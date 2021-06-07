# DatagramSocket

## 服务端

启动 UDP 服务端在`51985`端口

```java
import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;
import java.net.SocketException;

public class DatagramSocketServer extends Thread {
    private final DatagramSocket socket;
    private final byte[] buf = new byte[256];

    public DatagramSocketServer(int port) throws SocketException {
        socket = new DatagramSocket(port);
    }

    @Override
    public void run() {
        boolean isRunning = true;
        while (isRunning) {
            DatagramPacket packet = new DatagramPacket(buf, buf.length);
            try {
                socket.receive(packet);
                final InetAddress address = packet.getAddress();
                final int port = packet.getPort();
                final String received = new String(packet.getData(), 0, packet.getLength());
                System.out.printf("Client: %s\n", received);
                packet = new DatagramPacket(buf, packet.getLength(), address, port);
                if (received.equals("end")) {
                    isRunning = false;
                    System.out.println("Server closing...");
                    socket.send(packet);
                    continue;
                }
                socket.send(packet);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        socket.close();
    }
}
```

## 客户端

```java
import java.io.IOException;
import java.net.*;
import java.util.Objects;

public class DatagramSocketClient {
    private final DatagramSocket socket;
    private final InetAddress address;
    private final int port;

    public DatagramSocketClient(String ip, int port) throws SocketException, UnknownHostException {
        this.port = port;
        socket = new DatagramSocket();
        address = InetAddress.getByName(ip);
    }

    public String sendMessage(String msg) throws IOException {
        Objects.requireNonNull(msg);
        byte[] buf = msg.getBytes();
        DatagramPacket packet = new DatagramPacket(buf, buf.length, address, port);
        socket.send(packet);
        packet = new DatagramPacket(buf, buf.length);
        socket.receive(packet);
        return new String(packet.getData(), 0, packet.getLength());
    }

    public void close() {
        socket.close();
    }
}
```

## 单元测试

```java
import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

public class DatagramSocketTests {

    public static DatagramSocketClient client;

    @BeforeAll
    public static void setup() {
        try {
            new DatagramSocketServer(51985).start();
            client = new DatagramSocketClient("localhost", 51985);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Test
    public void message() {
        try {
            String echo = client.sendMessage("hello server");
            assertEquals("hello server", echo);
            echo = client.sendMessage("server is working");
            assertNotEquals("hello server", echo);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @AfterAll
    public static void tearDown() {
        try {
            client.sendMessage("end");
            client.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

Last Modified 2021-05-30
