## Map(键值对，字典，)

```java
//把所有键转为字符串数组
map.keySet()；
```







## foreach循环

```java
//外部类实现Iterable接口重写iterator()方法
//内部类实现Iterator接口重写hasNext(),next()方法
public class SlowIntArrayList<T> implements Iterable<T> {4
    T[] elements;

    @SuppressWarnings("unchecked cast") // 这个可以不加，加了代码没有黄色警告
    public SlowIntArrayList() {
        elements = (T[]) new Object[0]; // 转型，不能用new T[10];原理以后讨论
    }
    //重写iterator
	@Override
    public Iterator<T> iterator() {
        return new InClass();
    }
	//成员内部类
    class InClass implements Iterator {
        int count = elements.length;
        //重写hasNext
        @Override
        public boolean hasNext() {
            return count > 0;
        }
 		//重写next
        @Override
        public Object next() {
            if (count <= 0){
                System.out.println("没有了");
            }
            count--;
            return get(count);
        }
    }
    //这是使用匿名内部类
    @Override
    public Iterator<T> iterator() {
        //返回一个匿名实现Iterator的类
        return new Iterator<T>() {
            int count = elements.length;
            @Override
            public boolean hasNext() {
                return count > 0;
            }

            @Override
            public T next() {
                count--;
                return get(count);
            }
        };
    }
}
```









## HashSet、HashMap与Hash值

**hashCode()**：一般具有相同属性的对象Hash值应该相同，否则在HashSet、HashMap中为不同的元素。

**equals()**：一般具有相同属性的对象应该相等，指的是内容相同，不同于：==表示对象内存地址一样。

**Objects中有用方法用于重写hashCode、equals**  

   - Objects.hashCode(xx) 单个属性哈希值  
   - Objects.hash(xx, yy) 多个属性算哈希值
   - Objects.equals(a, b) 判断对象是否相等，支持null

**HashSet、HashMap与hashCode**  

- 存储自定义对象、Map的键为自定义对象时必须重写类的hashCode方法
- 重写hashCode方法的类一般都需要重写equals方法   
- 哈希桶：HashSet、HashMap中使用哈希桶保存哈希值相同的元素，这些元素使用equals方法判断是否相同









## System的方法

###### 复制数组--System.arraycopy()

```java
System.arraycopy(原数组, 开始下标, 新数组, 开始下标, 长度);
```

```java
System.err.println("使用err标注错误输出流……");//输出是红色
System.setOut(System.err); // 设置输出流，之后的out输出流都会是红色也就是都是err标注错误输出流
System.out.println("这是out输出流……");
System.gc(); // 触发垃圾回收，基本用不上，用于调试
// 通用于Windows/Linux/Mac操作系统，获取用户的文件夹目等：跨平台
String property = System.getProperty("user.home");
System.out.println("System.getProperty(\"user.home\") = " + property);
 // 系统当前时间：
System.out.println("系统当前时间（毫秒）：" + System.currentTimeMillis());
System.out.println("系统当前时间（纳秒）：" + System.nanoTime());
// 退出JVM
System.exit(0); // 任意地方可以退出
```









## Math的方法

```java
System.out.println(Math.min(100, 102)); // 最小
System.out.println(Math.sqrt(100)); // 开方
System.out.println(Math.pow(100, 0.5)); // 求幂
//四舍五入
System.out.println(Math.round(1.4)); // 1
System.out.println(Math.round(1.5)); // 2
//取最大
System.out.println(Math.ceil(1.001)); // 2，天花板
//取最小
System.out.println(Math.floor(1.999)); // 1，地板
Math.random() //方法用于返回一个随机数，随机数范围为 0.0 =< Math.random < 1.0。
```









## java.math包

###### 基本数据类型存储不了时--BigInteger

```java
int a1 = 1234567890;
long a4 = 1234567890123456789L;
//当一个数太长了基本数据类型存储不了就使用BigInteger
BigInteger a2 = new BigInteger("12345678901234567890");
BigInteger a3 = new BigInteger("5432100");
a2 = a2.add(a3);
System.out.println(a2);
//小数
BigDecimal a5 = new BigDecimal("12345678901234567890.123123123");
```

![](C:\Users\18890\AppData\Roaming\Typora\typora-user-images\1641705563740.png)









## java.util包

```java
Scanner scanner = new Scanner("一些字符串 字符串1 字符串2 ...");
scanner.useDelimiter(" "); // 分隔符，类似字符串的split分割方法
while (scanner.hasNext())
{
	System.out.println("下一个被扫描的：" + scanner.next());
}
```

![](C:\Users\18890\AppData\Roaming\Typora\typora-user-images\1641705282232.png)









## 工具类

### Files/Path/Paths   

Files必须和Path搭配使用：

###### 拷贝、移动文件--Files.copy()/move()

```java
//一个接口，相当于一个File对象
Path p = Paths.get("src/day28_IO/a.txt");
//复制a.txt到b.txt（b不能存在否则报错）
Files.copy(Paths.get("src/day28_IO/a.txt"), Paths.get("src/day28_IO/b.txt"));
//把"src/day28_IO/b.txt"（b.txt必须存在）移动到"src/day28_IO/test/b.txt"（b.txt不能存在）
Files.move(Paths.get("src/day28_IO/b.txt"), Paths.get("src/day28_IO/test/b.txt"));
```

###### 读取文件的所有行 Files.lines()/readAllLines()

```java
//读取文件的所有行返回一个Stream对象
Stream<String> stream = Files.lines(Paths.get("src/day28_IO/a.txt"),Charset.defaultCharset());
//使用toArray()把Stream对象转为字符串数组
System.out.println(Arrays.toString(stream.toArray()));
```



```java
	//文件大小(字节数)
	Files.size(p)
    // 遍历所有的文件对象   
    Files.walkFileTree()
    //是一个接口，相当于一个File对象： 
    Path
    //获取文件的根目录，比如系统盘 C:\      
    getRoot() 
    //构造一个相对路径  
    relativize()     
    //将Path转为一个真实的File对象  
    toFile() 
    //用于构造Path对象： 
    Paths   
    //返回一个Path对象
    get() 
```



### Objects方法

###### 判断null--Objects.equals(null, "abc")

```JAVA
boolean a1 = Objects.equals(null, "abc");
boolean a2 = Objects.equals(null, null);
System.out.println("Objects.equals(null, \"abc\") = " + a1);
System.out.println("Objects.equals(null, \"abc\") = " + a2);
```

![](C:\Users\18890\AppData\Roaming\Typora\typora-user-images\1641706176336.png)



### Arrays方法

```java
// 直接使用元素创建一个【只读】的List
List<String> list1 = Arrays.asList("a", "b", "c", "1");
// 可以修改
List<String> list2 = new ArrayList<>(Arrays.asList("a", "b"));
```

###### 排序--Arrays.sort(array)

```java
int[] array = {3, 2, 1};
System.out.println("原数组：" + Arrays.toString(array));
Arrays.sort(array);
System.out.println("排序后：" + Arrays.toString(array));
```

![](C:\Users\18890\AppData\Roaming\Typora\typora-user-images\1641707119730.png)



### Collections方法

###### 随机乱序--Collections.shuffle(list1)

```java
List<String> list1 = Arrays.asList("a", "b", "c", "d", "e");
Collections.shuffle(list1); // 随机乱序
System.out.println(list1);
```

![](C:\Users\18890\AppData\Roaming\Typora\typora-user-images\1641707378669.png)

###### 排序--Collections.sort(list1)

```java
List<String> list1 = Arrays.asList("a", "b", "c", "d", "e");
Collections.sort(list1);
System.out.println(list1);// 排序
```

![](C:\Users\18890\AppData\Roaming\Typora\typora-user-images\1641707428514.png)

###### 添加多个--Collections.addAll(list1, "添加的元素1", "2", "3")

```java
List<String> list1 = new ArrayList<>();
Collections.addAll(list1, "1", "2", "3");
System.out.println(list1);
```

![](C:\Users\18890\AppData\Roaming\Typora\typora-user-images\1641707619455.png)

###### 创建空容器--Collections.emptyList()

```java
// 创建空容器
List<String> list61 = new ArrayList<>(); // 空的
List<String> list62 = new ArrayList<>(); // 空的
System.out.println(list61 == list62);//false

// 只读
List<String> list31 = Collections.emptyList();
List<String> list32 = Collections.emptyList();
// list31.add("e");//无法添加
System.out.println(list31 == list32); //true// 重复利用
```









## String方法

###### 字符串转字符数组--str.toCharArray()

```java
String str = "abcdefghijkmli";
char[] chars = str.toCharArray();
```



###### 获取指定字符串下标

```java
sb.indexOf("@")
```



###### 字符串截取

```java
//str.split("@")返回的是一个字符串数组类型
String str = "53285964@qq.com";
String[] strs = str.split("@");

//subString()
//1、只传一个参数：subString(int beginIndex)
//将字符串从索引号为beginIndex开始截取，一直到字符串末尾。（注意索引值从0开始）；
String sb = "asdfghj";
String str = sb.substring(2);
//2、传入两个参数：substring(int beginIndex, int endIndex)
//从索引号beginIndex开始到索引号endIndex结束（返回结果包含索引为beginIndex的字符不包含索引我endIndex的字符）
String sb = "53285964@qq.com";
String str = sb.substring(0, 8);
//3、根据某个字符截取字符串
//这里根据”@”截取字符串（也可以是其他子字符串）
String sb = "53285964@qq.com";
String str = sb.substring(0, sb.indexOf("@"));
```



###### 字符串转数字

```java
Integer.parseInt(strings[0])
```



###### 字节数组转字符串

```java
//new String(数组,开始下标, 结束下标, "编码格式")
new String(bytes, 0, len, "gbk")
```





## File类（操作文件）

###### 判断是不是目录（文件夹）

```java
list.isDirectory()
```



```java
//通过将给定路径名字符串转换成抽象路径名来创建一个新 File 实例。(就是把当前文件或目录当成对象)
File file = new File("D:\\user\\train\\java\\javaout\\src\\day27_IO");
//判断此路径名（也就是当前对象）表示的文件或目录是否存在。
file.exists();
//返回此路径名（也就是当前对象）下所有的目录、文件名的名称所组成字符串数组。
String[] list = file.list();
System.out.println(Arrays.toString(list));
```







## RandomAccessFile（  随机流 ）

###### 原理：

 首先把随机访问的文件对象看作存储在文件系统中的一个大型 byte 数组，然后通过指向该 byte 数组的光标或索引（即：文件指针 FilePointer）在该数组任意位置读取或写入任意数据。 

```java
//传入文件路径和权限（ "r"：可读，"w" ：可写，"rw"：可读性）
RandomAccessFile rFile = new RandomAccessFile("src/day27_IO/job05/words.txt", "rw");
int a;
while ((a = rFile.read()) != -1) {
    if (a == 49 || a == 50 || a == 51 || a == 52 || a == 53 || a == 54 || a == 55 || a == 56 || a == 57 || a == 48) {
        //seek(long pos) (设置下一次读取的位置)设置到此文件开头测量到的文件指针偏移量，在该位置发生下一个读取或写入操作。
        rFile.seek(rFile.getFilePointer() - 1);
        rFile.write('-');
    }
}
```

###### 获取当前指针位置--getFilePointer()

```java
rFile.getFilePointer()
```

###### 设置下一次读取的位置--seek()

```java
rFile.seek(rFile.getFilePointer() - 1);
```









## BufferedReader（包装字符输入流）

###### 读取一行--reader.readLine()

```java
//自动抛出异常并关闭
try (BufferedReader reader = new BufferedReader(new FileReader("src/day28_IO/job02/水果.txt"))) {
            String str;
            while ((str = reader.readLine()) != null){
                System.out.println(str);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
```







## FileWriter

###### 追加写入(会保留原来数据) 

```java
//当后面参数设置为true时就是追加，不会清空原来的数据
new FileWriter("文件名", true)
```







## 线程

###### 线程暂停

```java
//秒
TimeUnit.SECONDS.sleep(6);
//分钟
TimeUnit.MINUTES.sleep(1);
```



```java
//创建线程方式1
Thread thread = new MyThread();
//开启线程
thread1.start();
//等待前面线程运行结束（当后面的代码需要用到线程的数据时使用）
thread.join();
//创建线程方式2（拉姆达表达式）
Thread thread3 = new Thread(() -> System.out.println("拉姆达表达式：" + Thread.currentThread().getName()));
//创建线程方式2（匿名内部））
Thread thread4 = new Thread(new Runnable() {
    @Override
    public void run() {
        //Thread.currentThread().getName()当前运行线程名字
        System.out.println("匿名内部类：" + Thread.currentThread().getName());
    }
});
```



###### 线程状态

1) 总结你知道的阻塞的方法    

​	Thread.sleep(), Thread.join(),   Scanner,   线程执行内容需要锁但是被其他线程使用

2) 总结你所知道的能被中断的方法      

​	Thread.sleep,   Thread.join()

3) 总结目前你所知道的线程安全相关的方法、手段    

​	synchronized修饰方法或者代码块使线程同步,Thread.start()之后使用Thread.join()等待线程生命周期结束

4) 根据已学知识，说明以下几个线程状态之间转换产生的条件：

NEW -> RUNNABLE 条件是调用了start()方法  

RUNNABLE -> BLOCKED   调用了sleep()方法,线程执行内容需要锁但是还没拿到   

BLOCKED -> RUNNABLE    sleep()方法结束

RUNNABLE -> TERMINATED  当线程的run()方法执行完成时



###### 暂时释放锁和通知需要锁的线程

```java
myString.wait();//暂时释放锁，因为字符串为null
// 通知生产者
this.notifyAll();
```



###### 线程池

```java
//方法一
 // 线程池存放一堆可以重复利用（复用）的线程
ThreadPoolExecutor executor = new ThreadPoolExecutor(
    10, // 核心10个
    20, // 最大20个
    10, // 存活时间10s
    TimeUnit.SECONDS,
    new LinkedBlockingQueue<>(), // 存放任务的队列
    r -> new Thread(r), // 线程工厂
    new ThreadPoolExecutor.AbortPolicy() // 任务超出队列抛弃
);
// 这个方法是执行一个任务：会自动将任务提交给线程池，线程池安排线程自动执行
executor.execute(() -> System.out.println("线程池执行一个任务：" + Thread.currentThread().getName()));
 // 必须等待线程池结束：关门而不是关闭（停止）！
executor.shutdown(); // 注意：类似join，等待任务完成，实际上就是：无法再提交其他工作！
```

```java
//方法二
//创建一个可重用固定线程数的线程池
ExecutorService executor = Executors.newFixedThreadPool(2);
//可变尺寸的线程池（可根据需要创建新线程的线程池，但是在以前构造的线程可用时将重用它。）
ExecutorService executor = Executors.newCachedThreadPool();
//单任务线程池
ExecutorService pool = Executors.newSingleThreadExecutor();
//延迟连接池（可根据需要创建新线程的线程池，但是在以前构造的线程可用时将重用它们）
newScheduledThreadPool：
```



###### 阻塞队列

```java
        //创建阻塞队列
        BlockingQueue<String> queue = new ArrayBlockingQueue<>(1);
        //消费者
        Consumer consumer = new Consumer(queue);
        //生产者
        Producer producer = new Producer(queue);
        //创建线程池
        ExecutorService exe = Executors.newFixedThreadPool(2);
        //给线程池分配任务
        exe.execute(consumer::consumption);//调用消费者的消费方法
        exe.execute(() -> producer.production());//调用生产者的生产方法
        //等待线程结束，后面不能再添加任务
        exe.shutdown();
```

```java
    //消费者
public class Consumer {
	private BlockingQueue<String> queue;

    public Consumer(BlockingQueue<String> queue) {
        this.queue = queue;
    }

    //消费方法
    public void consumption() {
        System.out.println("开始消费");
        //里面有东西了，就打印出来
//        System.out.println(queue.element());
        //打印完后把字符串设置为空
        try {
            queue.take();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("消费完毕");
    }
}
```

```java
//生产者
public class Producer {
    private BlockingQueue<String> queue;

    public Producer(BlockingQueue<String> queue) {
        this.queue = queue;
    }

    //生产方法
    public void production(){
        System.out.println("开始生产");
        //设置字符串
        try {
            queue.put("★");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("生产完毕");
    }
}
```



###### 原子操作-- AtomicInteger【保证线程安全】

```java
//创建AtomicInteger类似Integr类型
AtomicInteger count = new AtomicInteger();
//加1
count.incrementAndGet();
```













## 服务端和客户端

###### 创建服务器--ServerSocket

```java
		//创建服务器
        ServerSocket serverSocket = new ServerSocket(8080);
        System.out.println("服务器开启..........");
        //等待客户访问
        Socket accept = serverSocket.accept();
        //创建一个输入流读取客户发送的东西
        InputStream inputStream = accept.getInputStream();
        //存储读取到的字节
        byte[] bytes = new byte[1024];
        //存取读到了多少个字节，-1为没读到
        int len;
        while ((len = inputStream.read(bytes)) != -1){
            //输出客户端发送来的东西
            System.out.println("收到消息：" + new String(bytes));
        }
        //关闭输入流
        inputStream.close();
```

###### 创建客户端--Socket

```java
        //创建客户端
        Socket socket = new Socket("127.0.0.1", 8080);
        System.out.println("连接上服务器.....");
        //创建输出流往服务器发送东西
        OutputStream outputStream = socket.getOutputStream();
        System.out.println("开始发送...");
        outputStream.write("你好".getBytes(StandardCharsets.UTF_8));
        System.out.println("发送完毕...");
        //关闭输出流
        //outputStream.close();
        // 这里会通知告诉服务器，已经输出结束！所以可以不手动关闭输出流
        socket.shutdownOutput();
        // 关闭客户端
        socket.close();
```







## JDBC

###### 概念：

​	Java DataBase Connectivity Java 使用Java编程语言操作数据库
​	JDBC是官方定义的一套操作【所有】关系型数据库的规则即接口，不同数据库厂商实现同一套接口，并提供数据库驱动的jar包
​	MySQL的驱动：mysql-connector-java-8.0.26.jar，对应版本为8.0.26



###### JDBC开发步骤：

1. 导入jar包
	复制jar包到项目的lib目录下
	右键jar包 -> Add As Library
2. 注册驱动：Class.forName("com.mysql.cj.jdbc.Driver");
3. 获取数据库连接对象Connection：
	String url = "jdbc:mysql://localhost:3306/mydb?serverTimezone=Asia/Shanghai";
	Connection conn = DriverManager.getConnection(url, 用户名, 密码);
4. 定义sql语句：
	String sql = "insert into user values (xxx...)";
5. 获取执行sql语句的对象Statement：
	Statement statement = conn.createStatement();
6. 执行sql接收返回结果：
	int count = statement.executeUpdate(sql);
7. 处理结果：
	System.out.println(count >= 1 ? "插入数据成功！" : "插入数据失败！");
8. 释放资源
	statement.close();
	conn.close();



###### 说明：

1. 版本8.0中可以不用注册驱动：Class.forName(...)，添加依赖后会自动注册驱动：
   	java.sql.DriverManager.registerDriver(new Driver());
2. 旧版本使用的驱动是：com.mysql.jdbc.Driver
3. 必须有jar包添加到项目，不只是复制到项目文件夹
4. SQL连接语法：jdbc:mysql://ip地址(域名):端口号/数据库名称?参数1=值1&参数2=值2...
5. 如果旧版本出现乱码情况，添加URL参数：characterEncoding=utf8

###### 修改：

​	int statement.executeUpdate("update user set ...");
​	int statement.executeUpdate("delete from user ...");
​	int statement.executeUpdate("insert into user values...");
​	也可以执行create/alter/drop语句
​	返回值表示影响的行数，通过返回值判断SQL是否执行成功
​	获取最新的ID：select last_insert_id()

###### 查询：

​	ResultSet statement.executeQuery("select * from user");
​	返回游标对象ResultSet
​		ResultSet.next() 游标向下移动一行，判断是否还有数据
​		ResultSet.getXxx(int) 传入列的编号，从1开始
​		ResultSet.getXxx() 传入列的名称字符串
​	基本套路：while(resultSet.next()){ resultSet.getXxx("xxx");... }



```java
            //1.注册驱动【新版本可省略】
//            Class.forName("com.mysql.cj.jdbc.Driver");
            //2.获取数据库连接，如果时区有问题，加上：?serverTimezone=UTC
            String url = "jdbc:mysql://localhost:3306/mydb";
            String user = "root";
            String pass = "12345678";
            Connection conn = DriverManager.getConnection(url, user, pass);
            // 3.准备执行SQL语句，编写SQL语句
            String sql = "select * from user2";
            //4.创建一个Statement用来执行SQL语句
            Statement stmt = conn.createStatement();
            //5.使用Statement执行SQL语句,返回ResultSet结果集
            // stmt.execute();       // 执行SQL语句，返回是否成功，一般用来操作数据库、表等
            // stmt.executeQuery();  // 执行查询语句：查（select）
            // stmt.executeUpdate(); // 执行更新语句：增删改（insert/delete/update）
            ResultSet rs = stmt.executeQuery(sql);
            //6.获取所有的数据,使用next方法判断是否有下一个，有就移动到下一条，否则返回false
            while (rs.next()){
                //在JDBC中，数据下标从【1】开始
                /*int id = rs.getInt(1);//获取下标1的的一个数据，类型是int
                String username = rs.getString(2);
                String password = rs.getString(3);
                double money = rs.getDouble(4);*/

                // 在JDBC中，一般根据列名获取每一行数据的值
                int id = rs.getInt("id");//获取下标1的的一个数据，类型是int
                String username = rs.getString("username");
                String password = rs.getString("password");
                double money = rs.getDouble("money");
                System.out.printf("查询到一个用户：id：%s，用户名：%s， 密码：%s，余额：							%s%n",id, username, password, money);
            }
            //7.关闭游标和Statement
            rs.close();
            stmt.close();
            //8.关闭连接，释放资源
            conn.close();
```





##### 预编译和事务

###### SQL漏洞：

​	SQL注入攻击
​	select * from user2 where username='abc' and password='abc' or '1'='1'
​	使用预编译



###### 预编译--避免SQL注入攻击

​	PreparedStatement Connection.prepareStatement(String sql)
​	PreparedStatement 预编译SQL：

1. 避免SQL注入攻击
2. 提高效率
3. 简化开发，取代Statement
   使用?作为占位符：select * from user where username=? and password=?;
   传递占位符参数：PreparedStatement.setXxx(占位符编号, 值)，占位符编号从1开始
   基本原理：先发送SQL到服务器进行预编译，并不会直接执行，等所有语句发完后再统一执行

```java
String sql = "update user2 set money=money-100 WHERE id=?";
Connection conn = DriverManager.getConnection(url, user, pass);
//创建一个预编译Statement：PreparedStatement
PreparedStatement stmt = conn.prepareStatement(sql);
//设置参数，SQL中下标从1开始
stmt.setInt(1, 1);
//可以执行，和普通Statement没区别，SQL语句
int i = stmt.executeUpdate();
```



###### 管理事务：

​	开启事务：Connection.setAutoCommit(boolean autoCommit) 设为false开启事务
​	提交事务：commit()，所有SQL都执行完提交事务
​	回滚事务：rollback()，在异常发生后，catch语句中回滚事务

```java
//取消自动事务
conn.setAutoCommit(false);
//手动提交
conn.commit();
//事务回滚
conn.rollback();
```





###### properties--配置文件

​	配置文件：jdbc.properties
​	配置内容：

#表示注释，不会被读取，配置值不需要使用双引号

​		url=jdbc:mysql://localhost:3306/mydb
​		username=root
​		password=12345678
​	标准配置：
​		jdbc.driver=com.mysql.cj.jdbc.Driver
​		jdbc.url=jdbc:mysql://localhost:3306/mydb
​		jdbc.username=root
​		jdbc.password=12345678

```properties
# 注释， Properties实质上相当于是一个MAp<String, String>
# 键值对采用：key=value的方式配置，无所谓类型，都不要加"双引号
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/mydb
username=root
password=12345678
```

​	读取配置：
​		Properties pro = new Properties();
​		InputStream inputStream = JdbcUtils.class.getResourceAsStream("jdbc.properties");
​		properties.load(inputStream);
​		String url = properties.getProperty("jdbc.url");
​    使用配置：
​		Class.forName(driver);
​		DriverManager.getConnection(url, username, password);

```java
//获取配置文件流
InputStream stream = JdbcUtils.class.getResourceAsStream("/jdbc.properties");
//创建配置文件
Properties properties = new Properties();
//加载配置文件流
properties.load(stream);
//读取配置的值
String driver = properties.getProperty("driver");
url = properties.getProperty("url");
user = properties.getProperty("username");
pass = properties.getProperty("password");
//使用配置
//加载驱动
Class.forName(driver);
DriverManager.getConnection(url, user, pass);
```







###### try...with...resources--用法

```java
使用try...with...resources
	try中必须创建临时变量；变量类型必须实现AutoCloseable接口
	try (Connection conn = DriverManager.getConnection(...);
		Statement stmt = conn.createStatement();
		ResultSet rs = stmt.executeQuery(sql)){
	}

```





###### driver.properties--（阿里巴巴）配置文件

详细看javaout/src/sql04/dao/JDBCUtils

```java
//获取一个流来读取文件
InputStream stream = Driver01.class.getResourceAsStream("/driver.properties");
//加载文件
Properties properties = new Properties();
properties.load(stream);
//Druid读取配置
DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
//从连接池获取连接
Connection conn = dataSource.getConnection();
// 后面的步骤一样
// 增删改查
```



































