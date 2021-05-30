# Socket

## 服务端

通过开启子线程启动TCP服务端，绑定`51984`端口，TCP数据的结束标识为`\r\n`

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;

public class SocketServer extends Thread {
    private ServerSocket serverSocket;
    private final int port;
    private boolean isRunning = true;
    private static final int BLOCK_SIZE = 1024;
    private static final String END_FLAG = "\r\n";

    public SocketServer(int port) {
        this.port = port;
    }

    public void startServer() throws IOException {
        serverSocket = new ServerSocket(port);
        start();
    }

    @Override
    public void run() {
        while (isRunning) {
            try {
                final Socket clientSocket = serverSocket.accept();
                final PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);
                final BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
                char[] buf = new char[BLOCK_SIZE];
                int len;
                final StringBuilder builder = new StringBuilder();
                while ((len = in.read(buf, 0, BLOCK_SIZE)) != -1) {
                    builder.append(buf, 0, len);
                    // Receipt of this mark indicates the end of data transmission
                    if (END_FLAG.equals(builder.substring(builder.length() - END_FLAG.length()))) break;
                }
                final String message = builder.toString();
                out.print("Server: ".concat(message));
                out.print(END_FLAG);
                out.flush();
                System.out.println("Client: ".concat(message));
                in.close();
                out.close();
                clientSocket.close();
                // If the 'close' string is received, it means that the server is stopped
                if (message.contains("close")) {
                    serverSocket.close();
                    stopServer();
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    public void stopServer() throws IOException {
        isRunning = false;
        interrupt();
        System.out.println("Server is stop");
    }

    public static void main(String[] args) {
        final SocketServer server = new SocketServer(51984);
        try {
            server.startServer();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 客户端

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintStream;
import java.net.Socket;

public class SocketClient {

    private Socket clientSocket;
    private PrintStream out;
    private BufferedReader in;
    private final String ip;
    private final int port;
    private static final int BLOCK_SIZE = 1024;
    private static final String END_FLAG = "\r\n";

    public SocketClient(String ip, int port) {
        this.ip = ip;
        this.port = port;
    }

    public void startConnection() throws IOException {
        clientSocket = new Socket(ip, port);
        out = new PrintStream(clientSocket.getOutputStream(), true);
        in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
    }

    public String sendMessage(String msg) throws IOException {
        out.print(msg);
        out.print(END_FLAG);
        out.flush();
        char[] buf = new char[BLOCK_SIZE];
        int len;
        final StringBuilder builder = new StringBuilder();
        while ((len = in.read(buf, 0, BLOCK_SIZE)) != -1) {
            builder.append(buf, 0, len);
            // Receipt of this mark indicates the end of data transmission
            if (END_FLAG.equals(builder.substring(builder.length() - END_FLAG.length()))) break;
        }
        return builder.toString();
    }

    public void stopConnection() throws IOException {
        in.close();
        out.close();
        clientSocket.close();
    }

    public static void main(String[] args) {
        // If your computer does not support IPv6, please change the address to 127.0.0.1 or localhost
        final SocketClient client = new SocketClient("::1", 51984);
        try {
            client.startConnection();
            System.out.println(client.sendMessage("这是一些文本消息"));
            // System.out.println(client.sendMessage("close"));
            client.stopConnection();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

> 由于传输的数据包含了`Unicode`字符，运行此示例代码前请保证Java文件编码为`UTF-8`

Last Modified 2021-05-30