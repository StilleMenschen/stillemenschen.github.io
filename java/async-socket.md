# 异步Socket

## 服务端

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.*;

public class SocketChannelServer extends Thread {
    private ServerSocketChannel serverSocketChannel;
    private Selector selector;
    private Map<SocketChannel, Deque<byte[]>> dataMap;
    private boolean isRunning = Boolean.TRUE;

    public SocketChannelServer(int port) {
        try {
            serverSocketChannel = ServerSocketChannel.open();
            serverSocketChannel.configureBlocking(Boolean.FALSE);
            serverSocketChannel.socket().bind(new InetSocketAddress(port));
            selector = Selector.open();
            serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
            dataMap = new HashMap<>();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void stopServer() {
        isRunning = Boolean.FALSE;
    }

    @Override
    public void run() {
        while (isRunning) {
            try {
                // Selects a set of keys whose corresponding channels are ready for I/O operations.
                selector.select();
                final Iterator<SelectionKey> keys = selector.selectedKeys().iterator();

                while (keys.hasNext()) {
                    final SelectionKey selectionKey = keys.next();

                    if (selectionKey.isAcceptable()) {
                        createChannel(serverSocketChannel, selectionKey);
                    } else if (selectionKey.isReadable()) {
                        doRead(selectionKey);
                    } else if (selectionKey.isWritable()) {
                        doWrite(selectionKey);
                    }
                    keys.remove();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    private void createChannel(final ServerSocketChannel serverSocketChannel, final SelectionKey selectionKey) throws IOException {
        final SocketChannel socketChannel = serverSocketChannel.accept();
        System.out.println("Accepted connection from " + socketChannel.getRemoteAddress());
        socketChannel.configureBlocking(Boolean.FALSE);
        socketChannel.write(ByteBuffer.wrap(("Welcome: " + socketChannel.getRemoteAddress() +
                "\nThe thread assigned to you is: " + Thread.currentThread().getId() + "\n").getBytes()));
        dataMap.put(socketChannel, new LinkedList<>()); // store socket connection
        System.out.println("Total clients connected: " + dataMap.size());
        socketChannel.register(selectionKey.selector(), SelectionKey.OP_READ); // selector pointing to READ operation
    }

    private void doRead(final SelectionKey selectionKey) throws IOException {
        System.out.println("Reading...");
        final SocketChannel socketChannel = (SocketChannel) selectionKey.channel();
        final ByteBuffer byteBuffer = ByteBuffer.allocate(1024); // pos=0 & limit=1024
        final int read = socketChannel.read(byteBuffer); // pos=numberOfBytes & limit=1024
        if (read == -1) { // if connection is closed by the client
            doClose(socketChannel);
        } else {
            byteBuffer.flip(); // put buffer in read mode by setting pos=0 and limit=numberOfBytes
            final byte[] buf = new byte[byteBuffer.limit()];
            byteBuffer.get(buf, 0, read);
            dataMap.get(socketChannel).add(buf); // find socket channel and add new byteBuffer queue
            selectionKey.interestOps(SelectionKey.OP_WRITE); // set mode to WRITE to send data
        }
    }

    private void doWrite(final SelectionKey selectionKey) throws IOException {
        System.out.println("Writing...");
        final SocketChannel socketChannel = (SocketChannel) selectionKey.channel();
        final Deque<byte[]> pendingData = dataMap.get(socketChannel); // find channel
        while (!pendingData.isEmpty()) { // start sending to client from queue
            final byte[] buf = pendingData.poll();
            System.out.println(new String(buf));
            socketChannel.write(ByteBuffer.wrap(buf));
        }
        selectionKey.interestOps(SelectionKey.OP_READ); // change the key to READ
    }

    private void doClose(final SocketChannel socketChannel) throws IOException {
        dataMap.remove(socketChannel);
        System.out.println("Connection closed by client: " + socketChannel.getRemoteAddress());
        socketChannel.close(); // closes channel and cancels selection key
    }

    public static SocketChannelServer startServer(final int port) throws IOException {
        final SocketChannelServer server = new SocketChannelServer(port);
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
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.SocketChannel;
import java.util.Iterator;

public class SocketChannelClient extends Thread {
    private final Selector selector;
    private final String message;
    private final SocketChannel socketChannel;

    public SocketChannelClient(final String message, final int port) throws IOException {
        this.message = message;
        final InetSocketAddress address = new InetSocketAddress(port);
        socketChannel = SocketChannel.open();
        socketChannel.configureBlocking(Boolean.FALSE);
        socketChannel.connect(address);
        selector = Selector.open();
        socketChannel.register(selector, SelectionKey.OP_CONNECT);
    }

    @Override
    public void run() {
        boolean isRunning = Boolean.TRUE;
        while (isRunning) {
            try {
                if (selector.select() > 0) {
                    final Iterator<SelectionKey> keyIterator = selector.selectedKeys().iterator();

                    while (keyIterator.hasNext()) {
                        final SelectionKey key = keyIterator.next();

                        if (key.isConnectable()) {
                            createChannel(key);
                        } else if (key.isReadable()) {
                            doRead(key);
                            isRunning = Boolean.FALSE;
                        } else if (key.isWritable()) {
                            doWrite(key);
                        }
                        keyIterator.remove();
                    }
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        try {
            System.out.println("Client is close " + socketChannel.getLocalAddress());
            socketChannel.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public void createChannel(final SelectionKey key) throws IOException {
        final SocketChannel channel = (SocketChannel) key.channel();
        if (channel.finishConnect()) {
            System.out.println("Connected to server: " + channel.getRemoteAddress());
            channel.configureBlocking(Boolean.FALSE);
            channel.register(key.selector(), SelectionKey.OP_WRITE);
        }
    }

    public void doRead(final SelectionKey key) throws IOException {
        System.out.println("Read to server...");
        final SocketChannel channel = (SocketChannel) key.channel();
        final ByteBuffer buffer = ByteBuffer.allocate(4096);
        final int dataLength = channel.read(buffer);
        final byte[] dataArray = new byte[dataLength];
        buffer.flip();
        buffer.get(dataArray, 0, dataLength);
        System.out.println(new String(dataArray));
    }

    public void doWrite(final SelectionKey key) throws IOException {
        System.out.println("Write to server...");
        final SocketChannel channel = (SocketChannel) key.channel();
        channel.write(ByteBuffer.wrap(message.getBytes()));
        channel.register(key.selector(), SelectionKey.OP_READ);
    }

    public static void startClient(final String message, final int port) throws IOException {
        final SocketChannelClient client = new SocketChannelClient(message, port);
        client.start();
    }
}
```

## 测试类

```java
import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;

import java.io.IOException;
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class SocketChannelTests {

    private static final int MAX_CLIENT = 5;
    private static SocketChannelServer server;
    private static CyclicBarrier cyclicBarrier;
    private static final Runnable clientRunner = () -> {
        try {
            final String msg = Thread.currentThread().getName().concat(" 这是一些文本消息");
            SocketChannelClient.startClient(msg, 51986);
            cyclicBarrier.await();
        } catch (IOException | InterruptedException | BrokenBarrierException e) {
            e.printStackTrace();
        }
    };

    @BeforeAll
    public static void setup() {
        try {
            server = SocketChannelServer.startServer(51986);
            cyclicBarrier = new CyclicBarrier(MAX_CLIENT);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Test
    public void message() {
        int threadCount = MAX_CLIENT;
        while (threadCount-- > 0) {
            new Thread(clientRunner, "Thread" + threadCount).start();
        }
        try {
            cyclicBarrier.await();
        } catch (InterruptedException | BrokenBarrierException e) {
            e.printStackTrace();
        }
    }

    @AfterAll
    public static void tearDown() {
        server.stopServer();
        server.interrupt();
    }
}
```

> 由于传输的数据包含了`Unicode`字符，运行此示例代码前请保证Java文件编码为`UTF-8`

Last Modified 2021-06-03