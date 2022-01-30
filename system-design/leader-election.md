# 领导选举

## 领袖选举

集群中的节点（例如，一组服务器中的服务器）在其中选举一个所谓的“领导者”的过程，负责这些节点支持的服务的主要操作。
如果正确实施，领导者选举可以保证集群中的所有节点在任何给定时间都知道哪个是领导者，并且可以在领导者因任何原因死亡时选举新的领导者。

## 共识算法

一种用于让多个实体就单个数据值达成一致的复杂算法，例如一组机器中的“领导者”是谁。两种流行的共识算法（Consensus Algorithm）是 Paxos 和 Raft。

## Paxos 和 Raft

两种共识算法，当正确实施时，允许同步某些操作，即使在分布式环境中也是如此。

## Etcd

Etcd 是一个强一致性和高可用的键值对存储，通常用于在系统中实现领导者选举。了解更多：https://etcd.io/

## Zookeeper

ZooKeeper 是一个强一致性、高可用的键值对存储。它通常用于存储重要配置或执行领导者选举。了解更多：https://zookeeper.apache.org

## Demo

```python
import etcd3

import sys
import time
from threading import Event

# The current leader is going to be the value with this key.
LEADER_KEY = "/SystemExpert/leader"


# Entrypoint of the program.
def main(server_name):
    # Create a new client to etcd.
    client = etcd3.client(host="localhost", port=2379)

    while True:
        is_leader, lease = leader_election(client, server_name)
        if is_leader:
            print("I am the leader.")
            on_leadership_gained(lease)
        else:
            print("I am a follower.")
            wait_for_next_election(client)


# This election mechanism consists of all clients trying to put their name
# into a single key, but in a way that only works if the key does not
# exist (or has expired before) 。
def leader_election(client, server_name):
    print("New leader election happening.")
    # Create a lease before creating a key. This way, if this client ever
    # lets the lease expire, the keys associated with that lease will all
    # expire as well.
    # Here, if the client fails to renew lease for 5 seconds ( network
    # partition or machine goes down), then the leader election key will
    # expire.
    # https://help.compose.com/docs/etcd-using-etcd3-features#section-leases
    lease = client.lease(5)

    # Try to create the key with your name as the value. If it fails, then
    # another server got there first.
    is_leader = try_insert(client, LEADER_KEY, server_name, lease)
    return is_leader, lease


def on_leadership_gained(lease):
    while True:
        # As long as this process is alive and we're the leader,
        # we try to renew the lease. We don't give up leadership
        # unless the process / machine crashes or some exception
        # is raised.
        try:
            print("Refreshing lease; still the leader.")
            lease.refresh()
            # This is where the business logic would go.
            do_work()
        except Exception:
            # Here we most likely got a Client timeout (from
            # network issue). Try to revoke the current lease
            # so another member can get leadership.
            lease.revoke()
            return False
        except KeyboardInterrupt:
            print("\nRevoking lease; no longer the leader.")
            # Here we're killing the process. Revoke the lease and exit.
            lease.revoke()
            sys.exit(1)


def wait_for_next_election(client):
    election_event = Event()

    def watch_callback(resp):
        for event in resp.events:
            # For each event in the watch event, if the event is a deletion
            # it means the key expired / got deleted, which means the
            # leadership is up for grabs.
            if isinstance(event, etcd3.events.DeleteEvent):
                print("LEADERSHIP CHANGE REQUIRED")
                election_event.set()

    watch_id = client.add_watch_callback(LEADER_KEY, watch_callback)

    # While we haven't seen that leadership needs change, just sleep.
    try:
        while not election_event.is_set():
            time.sleep(1)
    except KeyboardInterrupt:
        client.cancel_watch(watch_id)
        sys.exit(1)

    # Cancel the watch; we see that election should happen aga in.
    client.cancel_watch(watch_id)


# Try to insert a key into etcd with a value and a lease. If the lease expires
# that key will get automatically deleted behind the scenes. If that key
# was already present , this will raise an exception.
def try_insert(client, key, value, lease):
    insert_succeeded, _ = client.transaction(
        failure=[],
        success=[client.transactions.put(key, value, lease)],
        compare=[client.transactions.version(key) == 0],
    )
    return insert_succeeded


def do_work():
    time.sleep(1)


if __name__ == '__main__':
    current_server_name = sys.argv[1]
    main(current_server_name)
```

```bash
python3 leader_election.py server1
python3 leader_election.py server2
python3 leader_election.py server3
```

Last Modified 2022-01-30
