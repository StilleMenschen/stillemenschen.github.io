# 异步 Socket

## 服务端

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.atomic.AtomicInteger;

public class AsynchronousSocketChannelServer extends Thread {
    private static final AtomicInteger CLIENT_COUNT = new AtomicInteger();
    private static final int BLOCK_SIZE = 4096;
    private final CountDownLatch latch;
    private final AsynchronousServerSocketChannel channel;

    public AsynchronousSocketChannelServer(final int port) throws IOException {
        final ExecutorService service = Executors.newFixedThreadPool(5);
        final AsynchronousChannelGroup group = AsynchronousChannelGroup.withThreadPool(service);
        final InetSocketAddress address = new InetSocketAddress(port);
        channel = AsynchronousServerSocketChannel.open(group);
        channel.bind(address);
        latch = new CountDownLatch(1);
        System.out.println("Start server with port " + port);
    }

    @Override
    public void run() {
        channel.accept(this, new AcceptHandler());
        try {
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public void stopServer() throws IOException {
        latch.countDown();
        if (channel.isOpen())
            channel.close();
        System.out.println("Server is close");
    }

    public static synchronized AsynchronousSocketChannelServer startServer(final int port) throws IOException {
        final AsynchronousSocketChannelServer server = new AsynchronousSocketChannelServer(port);
        server.start();
        return server;
    }

    static class AcceptHandler implements CompletionHandler<AsynchronousSocketChannel, AsynchronousSocketChannelServer> {
        @Override
        public void completed(final AsynchronousSocketChannel result, final AsynchronousSocketChannelServer attachment) {
            System.out.println("Connected to client, current client " + AsynchronousSocketChannelServer.CLIENT_COUNT.incrementAndGet());
            attachment.channel.accept(attachment, this);
            final ByteBuffer readBuffer = ByteBuffer.allocate(BLOCK_SIZE);
            result.read(readBuffer, readBuffer, new ReadHandler(result));
        }

        @Override
        public void failed(Throwable exc, AsynchronousSocketChannelServer attachment) {
            if (exc instanceof AsynchronousCloseException && !attachment.channel.isOpen())
                System.err.println("Server is close, stop accept");
            else
                System.err.println(exc.getMessage());
        }
    }

    static class ReadHandler implements CompletionHandler<Integer, ByteBuffer> {
        private final AsynchronousSocketChannel channel;

        public ReadHandler(final AsynchronousSocketChannel channel) {
            this.channel = channel;
        }

        @Override
        public void completed(final Integer result, ByteBuffer attachment) {
            if (result > 0) {
                final byte[] buf = doRead(result, attachment);
                final String message = new String(buf);
                System.out.println("From client: " + message);
                if (message.contains("close")) {
                    try {
                        channel.close();
                        System.out.println("Server is close");
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
                doWrite(buf);
            }
        }

        @Override
        public void failed(Throwable exc, ByteBuffer attachment) {
            System.err.println(exc.getMessage());
            try {
                if (channel.isOpen())
                    channel.close();
                System.err.println("Server is close");
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        private byte[] doRead(final Integer result, final ByteBuffer buffer) {
            buffer.flip();
            final byte[] buf = new byte[result];
            buffer.get(buf, 0, result);
            return buf;
        }

        private void doWrite(byte[] buf) {
            final byte[] end = " Server".getBytes();
            final ByteBuffer writeBuffer = ByteBuffer.allocate(BLOCK_SIZE);
            writeBuffer.clear();
            writeBuffer.put(buf);
            writeBuffer.put(end);
            writeBuffer.flip();
            channel.write(writeBuffer, writeBuffer, new WriteHandler(channel));
        }
    }

    static class WriteHandler implements CompletionHandler<Integer, ByteBuffer> {
        private final AsynchronousSocketChannel channel;

        public WriteHandler(final AsynchronousSocketChannel channel) {
            this.channel = channel;
        }

        @Override
        public void completed(Integer result, ByteBuffer attachment) {
            if (attachment.hasRemaining()) {
                channel.write(attachment, attachment, this);
            } else {
                final ByteBuffer readBuffer = ByteBuffer.allocate(BLOCK_SIZE);
                channel.read(readBuffer, readBuffer, new ReadHandler(channel));
                System.out.println("Close connect, current client " + AsynchronousSocketChannelServer.CLIENT_COUNT.decrementAndGet());
            }
        }

        @Override
        public void failed(Throwable exc, ByteBuffer attachment) {
            System.err.println(exc.getMessage());
            try {
                if (channel.isOpen())
                    channel.close();
                System.err.println("Server is close");
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 客户端

```java
import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousSocketChannel;
import java.nio.channels.CompletionHandler;
import java.util.concurrent.CountDownLatch;

public class AsynchronousSocketChannelClient implements CompletionHandler<Void, AsynchronousSocketChannelClient>, Runnable {
    private static final int BLOCK_SIZE = 4096;
    private final AsynchronousSocketChannel channel;
    private final InetSocketAddress address;
    private final String message;
    private final CountDownLatch latch = new CountDownLatch(1);

    public AsynchronousSocketChannelClient(final String ip, final int port, final String message) throws IOException {
        this.message = message;
        address = new InetSocketAddress(ip, port);
        channel = AsynchronousSocketChannel.open();
    }

    @Override
    public void run() {
        channel.connect(address, this, this);
        try {
            latch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        try {
            channel.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void completed(Void result, AsynchronousSocketChannelClient attachment) {
        System.out.println("Successfully connected to the server");
        final ByteBuffer writeBuffer = ByteBuffer.allocate(BLOCK_SIZE);
        writeBuffer.clear();
        writeBuffer.put(message.getBytes());
        writeBuffer.flip();
        channel.write(writeBuffer, writeBuffer, new WriteHandler(channel, latch));
    }

    @Override
    public void failed(Throwable exc, AsynchronousSocketChannelClient attachment) {
        System.err.println(exc.getMessage());
        try {
            channel.close();
            System.err.println("Client is close");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    static class WriteHandler implements CompletionHandler<Integer, ByteBuffer> {
        private final AsynchronousSocketChannel channel;
        private final CountDownLatch latch;

        public WriteHandler(final AsynchronousSocketChannel channel, CountDownLatch latch) {
            this.channel = channel;
            this.latch = latch;
        }

        @Override
        public void completed(final Integer result, final ByteBuffer attachment) {
            if (attachment.hasRemaining()) {
                channel.write(attachment, attachment, this);
            } else {
                final ByteBuffer readBuffer = ByteBuffer.allocate(1024);
                channel.read(readBuffer, readBuffer, new ReadHandler(channel, latch));
            }
        }

        @Override
        public void failed(Throwable exc, ByteBuffer attachment) {
            System.err.println(exc.getMessage());
            try {
                channel.close();
                System.err.println("Client is close");
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    static class ReadHandler implements CompletionHandler<Integer, ByteBuffer> {

        private final AsynchronousSocketChannel channel;
        private final CountDownLatch latch;

        public ReadHandler(final AsynchronousSocketChannel channel, CountDownLatch latch) {
            this.channel = channel;
            this.latch = latch;
        }

        @Override
        public void completed(final Integer result, final ByteBuffer attachment) {
            attachment.flip();
            final byte[] bytes = new byte[result];
            attachment.get(bytes, 0, result);
            final String message = new String(bytes);
            System.out.println("From server: " + message);
            latch.countDown();
        }

        @Override
        public void failed(Throwable exc, ByteBuffer attachment) {
            System.err.println(exc.getMessage());
            try {
                channel.close();
                System.err.println("Client is close");
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 单元测试

```java
import org.junit.jupiter.api.AfterAll;
import org.junit.jupiter.api.BeforeAll;
import org.junit.jupiter.api.Test;

import java.io.IOException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class AsynchronousSocketChannelTests {
    private static AsynchronousSocketChannelServer server;
    private static ExecutorService pool;

    @BeforeAll
    public static void setup() {
        try {
            server = AsynchronousSocketChannelServer.startServer(51988);
            pool = Executors.newFixedThreadPool(5);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Test
    public void message() {
        try {
            for (int i = 0; i < 5; i++) {
                pool.execute(new AsynchronousSocketChannelClient("::1", 51988, "这是一些文本消息#" + i));
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @AfterAll
    public static void tearDown() {
        pool.shutdown();
        try {
            System.out.println("Await clients stopping... ");
            if (pool.awaitTermination(30, TimeUnit.SECONDS)) {
                System.out.println("All clients is stop");
            }
            server.stopServer();
        } catch (InterruptedException | IOException e) {
            e.printStackTrace();
        }
    }
}
```

>由于传输的数据包含了`Unicode`字符，运行此示例代码前请保证 Java 文件编码为`UTF-8`

Last Modified 2021-06-07
