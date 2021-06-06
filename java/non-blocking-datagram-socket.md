# 非阻塞 DatagramSocket

## 服务端

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.net.SocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.DatagramChannel;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.util.Iterator;

public class DatagramChannelServer extends Thread {
    private final DatagramChannel datagramChannel;
    private final Selector selector;

    static class ClientMessage {
        public ByteBuffer buffer = ByteBuffer.allocate(256);
        public SocketAddress address;
    }

    public DatagramChannelServer(final int port) throws IOException {
        datagramChannel = DatagramChannel.open();
        final InetSocketAddress address = new InetSocketAddress(port);
        datagramChannel.configureBlocking(false);
        datagramChannel.socket().bind(address);
        selector = Selector.open();
        datagramChannel.register(selector, SelectionKey.OP_READ, new ClientMessage());
    }

    @Override
    public void run() {
        boolean isRunning = Boolean.TRUE;
        while (isRunning) {
            try {
                if (selector.select() > 0) {
                    final Iterator<SelectionKey> keys = selector.selectedKeys().iterator();

                    while (keys.hasNext()) {
                        final SelectionKey selectionKey = keys.next();

                        if (selectionKey.isReadable()) {
                            final DatagramChannel channel = (DatagramChannel) selectionKey.channel();
                            final ClientMessage clientMessage = (ClientMessage) selectionKey.attachment();
                            clientMessage.buffer.clear();
                            clientMessage.address = channel.receive(clientMessage.buffer);
                            System.out.println("Server: " + clientMessage.address);
                            final String message = new String(clientMessage.buffer.array());
                            if (message.length() > 0 && message.contains("close")) {
                                isRunning = Boolean.FALSE;
                                continue;
                            }
                            if (clientMessage.address != null) {
                                selectionKey.interestOps(SelectionKey.OP_WRITE);
                            }
                        } else if (selectionKey.isWritable()) {
                            final DatagramChannel channel = (DatagramChannel) selectionKey.channel();
                            final ClientMessage clientMessage = (ClientMessage) selectionKey.attachment();
                            clientMessage.buffer.put(" Server".getBytes());
                            clientMessage.buffer.flip();
                            final int len = channel.send(clientMessage.buffer, clientMessage.address);
                            if (len != 0) {
                                selectionKey.interestOps(SelectionKey.OP_READ);
                            }
                        }
                        keys.remove();
                    }
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        System.out.println("Server is close");
        datagramChannel.socket().close();
    }

    public void stopServer() throws IOException {
        datagramChannel.close();
        selector.close();
        interrupt();
    }

    public static DatagramChannelServer startServer(final int port) throws IOException {
        final DatagramChannelServer server = new DatagramChannelServer(port);
        server.start();
        return server;
    }
}
```

## 客户端

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.DatagramChannel;

public class DatagramChannelClient {
    private final DatagramChannel client;
    private final InetSocketAddress address;

    public DatagramChannelClient(final String ip, final int port) throws IOException {
        address = new InetSocketAddress(ip, port);
        client = DatagramChannel.open();
        client.connect(address);
    }

    public void sendMessage(String message) throws IOException {
        final ByteBuffer buffer = ByteBuffer.allocate(256);
        buffer.put(message.getBytes());
        buffer.flip();
        client.send(buffer, address);
        buffer.clear();
    }

    public void getMessage() throws IOException {
        final ByteBuffer buffer = ByteBuffer.allocate(256);
        client.receive(buffer);
        buffer.flip();
        final byte[] bits = new byte[buffer.remaining()];
        buffer.get(bits, 0, buffer.remaining());
        System.out.println(new String(bits));
    }

    public void close() throws IOException {
        client.close();
    }
}
```

## 单元测试

```java
import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;

public class DatagramChannelTests {
    private static DatagramChannelClient client;
    private static DatagramChannelServer server;

    @BeforeAll
    public static void setup() {
        try {
            server = DatagramChannelServer.startServer(51987);
            client = new DatagramChannelClient("::1", 51987);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @Test
    public void message() {
        try {
            client.sendMessage("这是一些文本消息");
            client.getMessage();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    @AfterAll
    public static void tearDown() {
        try {
            client.sendMessage("close");
            client.close();
            server.stopServer();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

> 由于传输的数据包含了`Unicode`字符，运行此示例代码前请保证 Java 文件编码为`UTF-8`

Last Modified 2021-06-07
