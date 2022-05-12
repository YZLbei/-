# 何为socket

抽象概念，通过socket发送或者接收数据

Socket=（IP地址：端口号）



# socket网络通信的过程

## 服务器端

1. 创建`ServerSocket`对象并绑定地址（ip）和端口号（port）

   server.bind(new InetSocketAddress(host,port))

2. 通过accept（）方法监听客户端的请求

3. 连接建立后，通过输入流读取客户端发送的请求信息

4. 通过输出流向客户端发送相应信息

5. 关闭相应资源

## 客户端

1. 创建`Socket`对象并且连接指定的地址（ip）和端口号（port）

   socket.connect(inetSocketAddress)

2. 建立连接后，通过输出流向服务器发送请求信息

3. 通过输入流获取服务器的响应信息

4. 关闭相应资源



# 网络通信实战

## 服务器端

```java
public class HelloServer {
    private static final Logger logger = LoggerFactory.getLogger(HelloServer.class);

    public void start(int port) {
        //1.创建 ServerSocket 对象并且绑定一个端口
        try (ServerSocket server = new ServerSocket(port);) {
            Socket socket;
            //2.通过 accept()方法监听客户端请求
            while ((socket = server.accept()) != null) {
                logger.info("client connected");
                try (ObjectInputStream objectInputStream = new ObjectInputStream(socket.getInputStream());
                     ObjectOutputStream objectOutputStream = new ObjectOutputStream(socket.getOutputStream())) {
                    //3.通过输入流读取客户端发送的请求信息
                    Message message = (Message) objectInputStream.readObject();
                    logger.info("server receive message:" + message.getContent());
                    message.setContent("new content");
                    //4.通过输出流向客户端发送响应信息
                    objectOutputStream.writeObject(message);
                    objectOutputStream.flush();
                } catch (IOException | ClassNotFoundException e) {
                    logger.error("occur exception:", e);
                }
            }
        } catch (IOException e) {
            logger.error("occur IOException:", e);
        }
    }

    public static void main(String[] args) {
        HelloServer helloServer = new HelloServer();
        helloServer.start(6666);
    }
}
```

## 客户端

```sql
/**
 * @author shuang.kou
 * @createTime 2020年05月11日 16:56:00
 */
public class HelloClient {

    private static final Logger logger = LoggerFactory.getLogger(HelloClient.class);

    public Object send(Message message, String host, int port) {
        //1. 创建Socket对象并且指定服务器的地址和端口号
        try (Socket socket = new Socket(host, port)) {
            ObjectOutputStream objectOutputStream = new ObjectOutputStream(socket.getOutputStream());
            //2.通过输出流向服务器端发送请求信息
            objectOutputStream.writeObject(message);
            //3.通过输入流获取服务器响应的信息
            ObjectInputStream objectInputStream = new ObjectInputStream(socket.getInputStream());
            return objectInputStream.readObject();
        } catch (IOException | ClassNotFoundException e) {
            logger.error("occur exception:", e);
        }
        return null;
    }

    public static void main(String[] args) {
        HelloClient helloClient = new HelloClient();
        helloClient.send(new Message("content from client"), "127.0.0.1", 6666);
        System.out.println("client receive message:" + message.getContent());
    }
}
```

# Socket的问题

`accept()`方法是阻塞方法，也就是说`ServerSocket`在调用`accept()`等待客户端的连接请求时会阻塞，直到收到客户端请求才会执行下面的代码



所以单纯要socket只能处理一个客户端的连接，如果需要管理多个客户端，**就要为请求的客户端单独创建一个线程**

```java
new Thread(() -> {
   // 创建 socket 连接
}).start();
```

但是会导致**资源浪费**

可以用**线程池**改进，减少浪费

```java
ThreadFactory threadFactory = Executors.defaultThreadFactory();
ExecutorService threadPool = new ThreadPoolExecutor(10, 100, 1, TimeUnit.MINUTES, new ArrayBlockingQueue<>(100), threadFactory);
threadPool.execute(() -> {
     // 创建 socket 连接
 });
```

但是改变不了BIO模型（同步阻塞）
