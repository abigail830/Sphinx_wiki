0. Raw Socket Demo
======================

要点
^^^^^^^^^^
* **serverSocket.accept()** 此处会阻塞并等待新客户连接进入
* **inputStream.read** 此处是另外一处阻塞进行当前客户处理
* 如果使用单线程，在处理当前客户期间就无法处理新客户
* 当使用线程池的时候，每个线程会对应处理一个客户，当瞬间多个客户进入，就会产生多个线程

优化思考
^^^^^^^^^^
如何可以实现每个线程应对尽量多的客户？


代码例子
^^^^^^^^^^

.. code-block:: java
  
  public class RawSocketServer {
  
    public static void main(String[] args) throws IOException {
  
        final ExecutorService executorService = Executors.newCachedThreadPool();
        ServerSocket serverSocket = new ServerSocket(10090);
        System.out.println("Raw Socket Server start @ 10090");
  
        while(true) {
            final Socket socket = serverSocket.accept();
            System.out.println("New Connection Accepted");  
            executorService.execute(new Runnable() {
                @Override
                public void run() {
                    handle(socket);
                }
            });
        }
    }
  
    private static void handle(Socket socket) {
        try{
            byte[] bytes = new byte[1024];
            InputStream inputStream = socket.getInputStream();

            while(true){
                int read = inputStream.read(bytes);
                if(read != -1){
                    System.out.println(new String(bytes, 0, read));
                }else{
                    break;
                }
            }
        }catch (Exception ex){
            System.out.println("Exception happen when try to get InputStream");
        }finally {
            try{
                System.out.println("Close Socket");
                socket.close();
            }catch (IOException ioException){
                System.out.println("IOException when close socket");

            }
        }
    }
  }


.. index:: Netty

