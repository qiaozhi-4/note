# Lambda表达式



- λ 希腊字母表中排序第十一位的字母，英语名称为 Lamda

- 避免匿名内部类定义过多

- 其实质属于函数式编程的概念

- 去掉了一堆没有意义的代码,只留下核心逻辑

  > (params)-> expression[表达式]
  >
  > (params) -> statement[语句]
  >
  > [(params)-> {statements}





带参的简化方式的演变案例

```java
 /**
 *总结：
 *1.lambda表达式只能有一行代码的情况下才能简化成为一行，如果有多行，那么就用代码块包裹
 *2.前提是接口为函数式接口，接口中有且仅有一个抽象方法
 *3.多个参数也可以去掉参数类型，要去掉就都去掉，必须加上括号
 */
public class TestLambda2 {
	//静态内部类
	/*
	 * static class Love implements ILove{
	 * 
	 * @Override public void love(int a) { // TODO Auto-generated method stub
	 * System.out.println("i love you -->"+a); } }
	 */

	public static void main(String[] args) {
		
		
		//局部内部类
		/*
		 * class Love implements ILove{
		 * 
		 * @Override public void love(int a) { // TODO Auto-generated method stub
		 * System.out.println("i love you -->"+a); } }
		 */
		
//		ILove love = new Love();
		//匿名内部类
		/*
		 * ILove love = new ILove() {
		 * 
		 * @Override public void love(int a) { // TODO Auto-generated method stub
		 * System.out.println("i love you -->"+a); } };
		 */
		//lambda简化
		//ILove love = (int a) -> {System.out.println("i love you -->"+a);};
		//lambda 有参 进一步简化
		ILove love = null;
		
		//简化1 参数类型
		//love = (a) -> {System.out.println("i love you -->"+a);};
		
		//简化2 简化括号
		//love = a -> {System.out.println("i love you -->"+a);};
		
		//简化3 简化花括号
		love = a -> System.out.println("i love you -->"+a);
		love.love(3000);
	}
}

interface ILove{
	void love(int a);
}

//实现类
/*
 * class Love implements ILove{
 * 
 * @Override public void love(int a) { // TODO Auto-generated method stub
 * System.out.println("i love you -->"+a); } }
 */
```



总结：
 1.lambda表达式只能有一行代码的情况下才能简化成为一行，如果有多行，那么就用代码块包裹
 2.前提是接口为函数式接口，<font color=red>接口中有且仅有一个抽象方法</font>
 3.多个参数也可以去掉参数类型，要去掉就都去掉，必须加上括号





> @FunctionalInterface

一个标志性注解，被该注解修饰时的**接口**只能声明一个抽象方法



#### 函数式接口

我们知道使用Lambda表达式的前提是需要有函数式接口，而Lambda表达式使用时不关心接口名，抽象方法名。只关心抽象方法的参
数列表和返回值类型。因此为了让我们使用Lambda表达式更加的方便，在JDK中提供了大量常用的函数式接口

主要是在java.util.function包中



##### Supplier

无参有返回值 用来生产数据

```java
@FunctionalInterface
public interface Supplier<T> {

    T get();
}
```

实例 找出最大值

```java
    private static void fun1(Supplier<Integer> supplier) {
        Integer max = supplier.get();
        System.out.println(max);
    }

    public static void main(String[] args) {
        fun1(() -> {
                    int[] arr = {99, 88, 77, 111, 44, 1};
                    Arrays.sort(arr);
                    return arr[arr.length - 1];
                }
        );
    }
```



##### Consumer

有参无返回值 用来消费数据

```java
@FunctionalInterface
public interface Consumer<T> {

    void accept(T t);

    default Consumer<T> andThen(Consumer<? super T> after) {
        // 进行参数验证。遇到要判断对象是否为空，空的时候报空指针异常。
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}

```

默认方法

如果一个方法的参数和返回值全部是Consumer类型,那么就可以实现消费一个数据的时候,，首先做一个操作，然后再做一个操作，实现组合，而这个方法就是Consumer接口中的default方法andThen方法

实例 大小写转换 调用默认方法

```java
    public static void fun1(Consumer<String> c1, Consumer<String> c2) {
        String str = "HELLO world";
        /*c1.accept(str);c2.accept(str);*/
        // c1.andThen(c2).accept(str); 先小写再大写
        c2.andThen(c1).accept(str); // 先大写再小写
    }
    public static void main(String[] args) {
        fun1((str1) -> {
            System.out.println(str1.toLowerCase()); // hello world
        },(str2) ->{
            System.out.println(str2.toUpperCase()); // HELLO WORLD
        });
    }
```



##### Function

有参有返回值的接口，Function接口根据一个类型的数据得到另一个类型的数据，前者称为前置条件，后者称为后置条件。有参有返回值。

```java
@FunctionalInterface
public interface Function<T, R> {

    R apply(T t);


    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }

    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }

    static <T> Function<T, T> identity() {
        return t -> t;
    }
}
```

默认方法

andThen（）和Consumer接口的默认方法使用一致 将当前Function和另外一个需要后执行的Function组合起来产生一个新的Function

```java
public static void testAfterCombineFunction() {
        /**
         * 功能描述：
         *  求一个数的平方的5倍是多少
         *
         *  这个功能可以拆分成两个简单的Function，一个求某数的平方，一个求某数的5倍大小
         */

        Function<Integer, Integer> squareFunction = i -> i * i;
        Function<Integer, Integer> fiveTimesFunction = i -> i * 5;

        Function<Integer, Integer> composedFunction = squareFunction.andThen(fiveTimesFunction);

        System.out.println("5的平方的再5倍是：" + composedFunction.apply(5)); // 125 5的平方的5倍

    }
```



compose（）将当前Function和另外一个需要先执行的Function组合起来产生一个新的Function

```java
public static void testBeforeCombineFunction() {
        /**
         * 功能描述：
         *  求一个数的平方的5倍是多少
         *
         *  这个功能可以拆分成两个简单的Function，一个求某数的平方，一个求某数的5倍大小
         */

        Function<Integer, Integer> squareFunction = i -> i * i;
        Function<Integer, Integer> fiveTimesFunction = i -> i * 5;

        Function<Integer, Integer> composedFunction = squareFunction.compose(fiveTimesFunction);

        System.out.println("5的5倍的再平方是：" + composedFunction.apply(5)); // 625 5的5倍的平方

    }
```



identity静态方法，返回一个Function，这个Function的功能十分简单，就是原封不动返回入参

```java
    @AllArgsConstructor
    @Data
    private static class User {
        private String name;
        private int age;

        @Override
        public String toString() {
            return String.format("我叫%s，今年%d岁", this.name, this.age);
        }
    }

    public static void testIdentityFunction() {
        Function<User, User> identityFunction = Function.identity();
        User param = new User("张三", 25);

        User result = identityFunction.apply(param);
        System.out.println(result); // 我叫张三，今年25岁
    }
```



##### Predicate

有参且返回 对某种数据类型的数据进行判断，结果返回一个boolean值

```java
@FunctionalInterface
public interface Predicate<T> {

    boolean test(T t);
    
    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }

    default Predicate<T> negate() {
        return (t) -> !test(t);
    }

    default Predicate<T> or(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) || other.test(t);
    }

    static <T> Predicate<T> isEqual(Object targetRef) {
        return (null == targetRef)
                ? Objects::isNull
                : object -> targetRef.equals(object);
    }
}

```

默认方法

```java
    public static void main(String[] args) {
        fun1(p1 -> {return p1.contains("w");}, p2 -> {return p2.contains("h");});
    }

    private static void fun1(Predicate<String> p1, Predicate<String> p2) {
        // p1 包含w p2 包含h
        // 并且 &&
        boolean b1 = p1.and(p2).test("hello");
        // 或者 ||
        boolean b2 = p1.or(p2).test("hello");
        // 取反 !
        boolean b3 = p1.negate().test("hello");
        System.out.println(b1); // false
        System.out.println(b2); // true
        System.out.println(b3); // true
    }
```





**Lambda表达式和匿名内部类的区别**

 所需类型不同

- 匿名内部类：可以是接口，也可以是抽象类，还可以是具体类
- Lambda表达式：只能是接口

 

 使用限制不同

- 如果接口中有且仅有一个抽象方法，可以使用Lambda表达式，也可以使用匿名内部类
- 如果接口中多于一个抽象方法，只能使用匿名内部类，而不能使用Lambda表达式

 

 **实现原理不同**

- 匿名内部类：编译之后，产生一个**单独**的.class字节码文件

- Lambda表达式：编译之后，**没有**一个单独的.class字节码文件。对应的字节码会在运行的时候动态生成

  > 在类中新增了一个方法，这个方法的方法体就是Lambda表达式中的代码
  > 还会形成一个匿名内部类，实现接口，重写抽象方法
  > 在接口中重写方法会调用新生成的方法



#### 方法引用

在使用Lambda表达式的时候，也会出现代码冗余的情况。

如果在Lambda表达式中要执行的代码和我们另一个方法中的代码是一样的，这时候就没有必要重写一份逻辑了，可以“引用”重复代码

```java
public class Test01 {
    public static void main(String[] args) {
        // lambda表达式冗余
        getSum(a ->{
            int count = 0;
            for (int i : a) {
                count += i;
            }
            System.out.println(count);
        });

        // 方法引用
        getSum(Test01::getTotal);
    }

    private static void getSum(Consumer<int[]> comparable) {
        int[] a = {1, 2, 3, 4};
        comparable.accept(a);
    }

    private static void getTotal(int[] a) {
        int count = 0;
        for (int i : a) {
            count += i;
        }
        System.out.println(count);
    }
}
```



> 方法引用的语法格式

符号表示  `::`

应用场景  如果Lambda表达式所要实现的方案，已经有其他方法存在相同的方案，那么则可以使用方法引用。



常见的方法引用方式

- instanceName::methodName对象::方法名
  一个类中已经存在了一个成员方法，可以通过对象名引用成员方法

  ```java
          Date now = new Date();
          Supplier<Long> supplier = () ->  now.getTime();
          System.out.println(supplier.get());
  
          // 方法引用 对象::方法
          Supplier<Long> supplier1 = now::getTime;
          System.out.println(supplier1.get());
  ```

  注意事项
  被引用的方法，参数要和接口（函数式接口）中的抽象方法的参数一样
  当接口抽象方法有返回值时，被引用的方法必须有返回值

  

- ClassName:staticMethodName类名::静态方法

  ```java
          Supplier<Long> supplier1 = () -> System.currentTimeMillis();
          System.out.println(supplier1.get());
  
          // 方法引用 类名::静态方法
          Supplier<Long> supplier2 = System::currentTimeMillis;
          System.out.println(supplier2.get());
  ```

  

- ClassName:methodName类名::普通方法
  类名引用实例方法是有前提的，实际上是拿第一个参数作为方法的调用者

  ```java
          Function<String,Integer> function1 = (str) -> str.length();
          System.out.println(function1.apply("hello"));
  
          // 方法引用 类名::实例方法
          Function<String,Integer> function2 = String::length;
          System.out.println(function2.apply("hello"));
  
          BiFunction<String,Integer,String> biFunction = String::substring;
          System.out.println(biFunction.apply("hello", 2)); // llo
  ```

  

- ClassName::new  类名::new 调用的构造器
  由于构造器的名称和类名完全一致，所以构造器引用使用`::new`的格式

  ```java
  public class Student {
      private String name;
      private String age;
      ...
  }
  ```

  

  ```java
          Supplier<Student> supplier1 = () -> new Student();
          System.out.println(supplier1.get());
  
          // 方法引用 类名::构造器
          Supplier<Student> supplier2 = Student::new;
          System.out.println(supplier2.get());
  
          BiFunction<String,String,Student> biFunction = Student::new;
          System.out.println(biFunction.apply("张三","18")); // Student{name='张三', age='18'}
  ```

  



- TypeName[ ]::new  String[ ]::new 调用数组的构造器

  ```java
          Function<Integer, String[]> function1 = (len) -> {return new String[len];};
          System.out.println(function1.apply(3).length);
  
          // 方法引用 数组::构造器
          Function<Integer, String[]> function2 = String[]::new;
          System.out.println(function2.apply(3).length);
  ```



> 小结

方法引用是对lambda表达式符合特定情况下的一种缩写方式，使得lambda表达式更加的精简。



```java
        Stream.of("aa", "bb", "cc", "aa").toArray(String[]::new); // 方法引用
        
        Stream.of("aa", "bb", "cc", "aa").toArray(s -> new String[s]); // lambda表达式
        
        Stream.of("aa", "bb", "cc", "aa").toArray(new IntFunction<String[]>() { // 内部类
            @Override
            public String[] apply(int value) {
                return new String[value];
            }
        });
```





# Stream API



### 集合处理数据的弊端

当我们在需要对集合中的元素进行操作时，处理获取，添加，修改外，最常用的操作就是集合遍历

```java
        List<String> list = Arrays.asList("寻血猎犬","直布罗陀","班加罗尔","命脉","恶灵","x亡灵");
        for (String s : list) {
            if (s.startsWith("恶")) {
                System.out.println(s);
            }
        }
```

以上代码针对不同的需求都会一次次的循环，在Jdk1.8中的Stream API可以更高效的解决。

> stream

```java
        List<String> list = Arrays.asList("寻血猎犬","直布罗陀","班加罗尔","命脉","恶灵","x亡灵");
        // 元素结尾为'灵' 长度为3 结果遍历
        list.stream().filter(s -> s.endsWith("灵") ).filter(s -> s.length() == 3 ).forEach(System.out::println); // x亡灵
```



### Stream流式思想



==注意：Stream和IO流没有任何关系==

Stream流式思想类似于工厂车间的“生产流水线”，**Stream流不是一种数据结构，不保存数据**，而是对数据进行加工处理。Stream可以看做是流水线的一个工序。在流水线上，通过多个工序让原材料加工成一个商品。

![img](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/c13a4ebad535d1ed1a4eec104c88dde5919cb932.png@942w_471h_progressive-16544003188361.webp)

Stream API 能让我们快速的完成许多复杂的操作，如筛选、切片、映射、查找、去除重复、统计、匹配和归约



### Stream流的获取方式

> 根据Collection获取

java.util.collection接口中加入了default方法stream，也就是说Collection接口下的所有实现类都可以通过stream方法来获取Stream流。

```java
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }
```



```java
        new HashSet<>().stream();
        new ArrayList<>().stream();
        new Vector<>().stream();
```



但是Map接口没有实现Collection接口，可以根据Map获取对应的key，value的集合。

```java
        Map<String,Object> map = new HashMap<>();
        map.keySet().stream(); // key
        map.values().stream(); // value
        Stream<Map.Entry<String, Object>> stream = map.entrySet().stream(); // EntrySet
```



> 通过Stream的of方法

在实际开发中还是会不可避免的操作到数组中的数据，由于数组对象不可能添加默认方法，所以Stream接口提供了静态方法of

```java
    public static<T> Stream<T> of(T t) {
        return StreamSupport.stream(new Streams.StreamBuilderImpl<>(t), false);
    }
```

```java
        Stream<Integer> integerStream = Stream.of(1, 2, 3, 4);
        integerStream.forEach(System.out::print); // 1234

        Integer[] integers = {4,5,6,7,8};
        Stream.of(integers).forEach(System.out::print); // 45678

        // 注意：基本数据类型是不行的会被当做一个整体
        int[] ints = {4,5,6,7,8};
        Stream.of(ints).forEach(System.out::print); // [I@682a0b20
```



### 常用方法

![img](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/2103224ff04c0c5fd523fd9c785b2e078c5a1d64.png@942w_375h_progressive-16544003188363.webp)

**终结方法：** 返回值类型不再是Stream 类型的方法，不再支持链式调用。

**非终结方法： ** 返回值类型仍然是Stream类型的方法，支持链式调用。



> 注意

Stream只能操作一次

Stream方法返回的是新的流

**Stream不调用终结方法，中间的操作不会执行**



#### forEach

forEach用来遍历流中的数据的

```java
    void forEach(Consumer<? super T> action);
```

该方法接收一个Consumer接口，会将每一个流元素交给函数处理



#### count

Stream流中的count方法来统计其中的元素个数

```java
    long count();
```

```
    System.out.println(Stream.of("aa", "aaa", "aba", "bcd", "efg").filter(s -> s.contains("a")).count()); // 3
```



#### filter

filter方法的作用是用来过滤数据的。返回符合条件的数据

![img](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/1c0695c2660b52605981ee5adaab13c8c536c68a.png@942w_308h_progressive-16544003188365.webp)

可以通过filter方法将一个流转换成另一个子集流

```
    Stream<T> filter(Predicate<? super T> predicate);
```

该接口接收一个Predicate函数式接口参数作为筛选条件



#### limit

![img](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/ba7a428482f6af66801f7ec3b39abedb7308ac46.png@942w_338h_progressive-16544003188367.webp)



limit方法可以对流进行截取处理，截取前n个数据(下标从1开始)

```java
    Stream<T> limit(long maxSize);
```

参数类型是long类型的数值，如果集合当前长度大于参数就进行截取，否则不操作

```java
    Stream.of("aa", "aaa", "aba", "bcd", "efg").limit(3).forEach(System.out::print); // aa aaa aba
```





#### skip

![img](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/1b029646e5345f8051bf047a45a48193add1fe8e.png@942w_365h_progressive-16544003188369.webp)

如果希望跳过**前面的几个元素**，可以使用skip获得一个截取之后的新流：(下标从1开始)

```java
    Stream<T> skip(long n);
```

```java
    Stream.of("aa", "aaa", "aba", "bcd", "efg").skip(3).forEach(System.out::print); // bcd efg
```



#### map

```java
    <R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

![img](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/87ae3824bb4e05149cb88e5b77f2dc9b4e429e05.png@942w_290h_progressive-165440031883611.webp)

该接口需要一个Function函数式接口参数，可以将当前流中的T类型数据转换为另一种R类型的数据

```java
    Stream.of("aa", "aaa", "aba", "bcd", "efg").map(String::toUpperCase).forEach(System.out::print); // AAAAAABABCDEFG
```



#### sorted

数据排序

```java
    Stream<T> sorted();
    Stream<T> sorted(Comparator<? super T> comparator);
```



可以根据数据的自然数据排序，也通过比较器指定对应的比较规则

```java
    // 根据数据的自然数据排序
    Stream.of(88, 99, 222, 3, 1).sorted().forEach(System.out::print); // 1 3 88 99 222
    Stream.of("aa", "aaa", "aba", "bcd", "efg").sorted().forEach(System.out::print); // aa aaa aba bcd efg
    // 降序 通过比较器指定对应的比较规则
    Stream.of(88, 99, 222, 3, 1).sorted((o1, o2) -> o2 - o1).forEach(System.out::println); // 222 99 88 3 1
```



#### distinct

去掉重复数据

```java
    Stream<T> distinct();
```

对于基本数据类型是可以直接去重的，对于自定义类型，需要==重写hashCode和equals==方法才能去重

```java
    Stream.of(88, 888, 88, 11, 11).sorted((o1, o2) -> o2 - o1).distinct().forEach(System.out::print); // 888 88 11
    // 自定义类型 没有重写hashCode和equals方法
    Stream.of(new Student("张三", "18"), new Student("张三", "18"))
                .distinct().forEach(System.out::print); // Student{name='张三', age='18'}Student{name='张三', age='18'}
    // 重写之后
    Stream.of(new Student("张三", "18"), new Student("张三", "18"))
                .distinct().forEach(System.out::print); // Student{name='张三', age='18'}
```



#### reduce

![image-20220605121624730](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/image-20220605121624730.png)

将所有数据归纳得到一个数据

```java
    T reduce(T identity, BinaryOperator<T> accumulator);
```

使用：

```java
        // identity 默认值 求和
        Integer sum = Stream.of(1, 2, 3, 4).reduce(0,
                (x, y) -> {
                    System.out.printf("x=%d y=%d%n", x, y);
                    return x + y;
                }); // 10
        /*
        x=0 y=1
        x=1 y=2
        x=3 y=3
        x=6 y=4
        */
        // 最大值
        Integer max = Stream.of(1, 2, 3, 4).reduce(0,
                (x, y) -> x > y ? x : y
        ); // 4
```



#### map 和 reduce

在实际开发过程中经常会将map和reduce一起使用

```java
        // 年龄和
        Integer ageSum = Stream.of(new Student("张三", 18), new Student("李四", 20), new Student("王五", 22))
                .map(Student::getAge /*p -> p.getAge()*/)
                .reduce(0, Integer::sum /*(x, y) -> x + y*/); // 60

        // 最大年龄
        Integer max = Stream.of(new Student("张三", 18), new Student("李四", 20), new Student("王五", 22))
                .map(Student::getAge)
                .reduce(0, Integer::max); // 22

        // 统计 字符a 出现的次数
        Integer strSum = Stream.of("a", "a", "b", "c", "e")
                .map(s -> s.contains("a") ? 1 : 0)
                .reduce(0, Integer::sum); // 2
```



#### mapToInt

![image-20220605130841873](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/image-20220605130841873.png)

使用

```java
        // Interge占用的内存比int多很多，在Stream流操作中会自动装箱和拆箱操作
        // 为了提高代码效率 可以先将流中的Integer数据转换为int数据，再操作
        Integer arr[] = {1, 2, 3, 4, 5, 6};
        Stream.of(arr)
                .mapToInt(Integer::intValue)
                .filter(s -> s > 3) // 如果是Integer类型这边就会有拆箱装箱处理
                .forEach(System.out::println); // 4,5,6
```



#### concat

将两个流合并成为一个流

Stream的静态方法concat

```java
        Stream<String> stream1 = Stream.of("a", "b", "c");
        Stream<String> stream2 = Stream.of("e", "f", "g");
        Stream.concat(stream1,stream2).forEach(System.out::println); // a b c e f g
```





### Stream结果收集

**collect**

#### 结果收集到集合



```java
        // 收集到list集合中
        List<String> list = Stream.of("aa", "bb", "cc", "aa").collect(Collectors.toList()); // [aa, bb, cc, aa]
        // 收集到set集合中
        Set<String> set = Stream.of("aa", "bb", "cc", "aa").collect(Collectors.toSet()); // [aa, bb, cc]
```





```java
        // 获取的类型为具体的实现 譬如：ArrayList HashSet
        ArrayList<String> arrayList = Stream.of("aa", "bb", "cc", "aa").collect(Collectors.toCollection( ArrayList::new/*() -> new ArrayList<>()*/));
        HashSet<String> hashSet = Stream.of("aa", "bb", "cc", "aa").collect(Collectors.toCollection(HashSet::new));
```



> Collectors.toCollection()方法源码

```java
    public static <T, C extends Collection<T>>
    Collector<T, ?, C> toCollection(Supplier<C> collectionFactory) {  // 生产者的函数式接口
        return new CollectorImpl<>(collectionFactory, Collection<T>::add, // r1 r2
                                   (r1, r2) -> { r1.addAll(r2); return r1; },
                                   CH_ID);
    }
```



#### 结果收集到数组

Stream中提供了toArray方法来将结果放到一个数组中，返回值类型是Object[],如果要指定返回的类型，可以使用重载方法`toArray(IntFunction<A[]> generator)`

```java
        Object[] objects = Stream.of("aa", "bb", "cc", "aa").toArray(); //  数组中的元素类型为 Object
        System.out.println(Arrays.toString(objects)); // [aa, bb, cc, aa]
        // 指定数组元素的类型
        String[] strings = Stream.of("aa", "bb", "cc", "aa").toArray(String[]::new);
        System.out.println(Arrays.toString(strings)); // [aa, bb, cc, aa]
```



#### 聚合运算

使用stream流处理数据后，可以像数据库的聚合函数一样对某个字段一样对某个字段进行操作，如获得最大最小值，求和，平均值，统计数量。

```java
        // 最大年龄
        Optional<Student> maxAge = Stream.of(new Student("张三", 18), new Student("李四", 24), new Student("王五", 22))
                .collect(Collectors.maxBy((p1, p2) -> p1.getAge() - p2.getAge()));
        System.out.println("最大年龄" + maxAge); // Optional[Student{name='李四', age=24}]
        // 年龄之和
        Integer sumAge = Stream.of(new Student("张三", 18), new Student("李四", 24), new Student("王五", 22))
                .collect(Collectors.summingInt(Student::getAge/*s -> s.getAge()*/));
        System.out.println("年龄之和" + sumAge); // 64
        // 年龄平均值
        Double avgAge = Stream.of(new Student("张三", 18), new Student("李四", 24), new Student("王五", 22))
                .collect(Collectors.averagingInt(Student::getAge));
        System.out.println("平均年龄" + avgAge); // 21.333333333333332
        // 统计数量
        long count = Stream.of(new Student("张三", 18), new Student(null, 24), new Student("王五", 22)).count();
        Long collect = Stream.of(new Student("张三", 18), new Student("李四", 24), new Student("王五", 22))
                .collect(Collectors.counting());
        System.out.println(collect + " " + count); // 3 3
```



#### 分组操作



可以根据某个属性将数据分组

```java
        // 根据账号名称分组
        Map<String, List<Student>> listMap = Stream.of(new Student("张三", 18), new Student("李四", 24), new Student("张三", 16), new Student("李四", 12))
                .collect(Collectors.groupingBy(Student::getName));

        // forEach
        listMap.forEach((k, v) -> System.out.println("k=" + k + " v=" + v));
        // Stream流 forEach
        listMap.entrySet().stream().forEach(System.out::println);
        //k=李四 v=[Student{name='李四', age=24}, Student{name='李四', age=26}]
        //k=张三 v=[Student{name='张三', age=18}, Student{name='张三', age=22}]

        // 根据年龄分组
        Map<String, List<Student>> listMap1 = Stream.of(new Student("张三", 18), new Student("李四", 24), new Student("张三", 16), new Student("李四", 12))
                .collect(Collectors.groupingBy(s -> s.getAge() >= 18 ? "成年" : "未成年"));
        listMap1.entrySet().stream().forEach(System.out::println);
        // 未成年=[Student{name='张三', age=16}, Student{name='李四', age=12}]
        // 成年=[Student{name='张三', age=18}, Student{name='李四', age=24}]
```



> 多级分组

```java
        // 先根据name分组 再根据age分组
        Map<String, Map<String, List<Student>>> map = Stream.of(
                        new Student("张三", 18),
                        new Student("张三", 16),
                        new Student("张三", 22),
                        new Student("李四", 24),
                        new Student("张三", 16),
                        new Student("李四", 12))
                .collect(Collectors.groupingBy(Student::getName, Collectors.groupingBy(s -> s.getAge() >= 18 ? "成年" : "未成年")));
        map.entrySet().stream().forEach(System.out::println);
        // 李四={未成年=[Student{name='李四', age=12}], 成年=[Student{name='李四', age=24}]}
        // 张三={未成年=[Student{name='张三', age=16}, Student{name='张三', age=16}], 成年=[Student{name='张三', age=18}, Student{name='张三', age=22}]}
```



#### 分区操作



`Collectors.partitioningBy()`会根据值是否为True，把集合中的数据分割为两个列表，一个true列表，一个false列表

![image-20220605165632523](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/image-20220605165632523.png)

```java
        Map<Boolean, List<Student>> map = Stream.of(
                        new Student("张三", 19),
                        new Student("李四", 24),
                        new Student("张三", 16),
                        new Student("李四", 12))
                .collect(Collectors.partitioningBy(student -> student.getAge() > 18));
        map.entrySet().stream().forEach(System.out::println);
        //false=[Student{name='张三', age=16}, Student{name='李四', age=12}]
        //true=[Student{name='张三', age=19}, Student{name='李四', age=24}]
```



#### 拼接操作

`Collectors.joining()`会根据指定的连接符，将所有元素连接成一个字符串



```java
        String collect = Stream.of(
                        new Student("张三", 19),
                        new Student("李四", 24),
                        new Student("张三", 16),
                        new Student("李四", 12))
                .map(Student::getName)
                /*.collect(Collectors.joining()); // 张三李四张三李四*/
                /*.collect(Collectors.joining("_")); // 张三_李四_张三_李四*/
                .collect(Collectors.joining("  ","< "," >")); // < 张三  李四  张三  李四 > 
```



### 并行的Stream流



#### 串行的Stream流

以上使用的stream的流都是串行的，在一个线程上面执行

```java
        Stream.of(1, 2, 3, 4, 5)
                .filter(s -> {
                    System.out.println(Thread.currentThread());
                    return s >= 2;
                }).count();
```

运行结果

```
Thread[main,5,main]
Thread[main,5,main]
Thread[main,5,main]
Thread[main,5,main]
Thread[main,5,main]
```



#### 并行流

parallelStream其实就是一个并行流，通过默认的ForkJoinPool，可以提高多线程任务的速度。



##### 获取并行流



> 通过List接口中的`parallelStream`方法获取

```java
        List<Integer> list = new ArrayList<>();
        Stream<Integer> integerStream = list.parallelStream();
```



> 通过已有的串行流转换为并行流`parallel`

```java
        Stream<Integer> parallel = Stream.of(1, 2, 3).parallel();
```



##### 并行流的操作



```java
        Stream.of(1, 2, 3, 4, 5, 6).parallel()
                .filter(s -> {
                    System.out.println(Thread.currentThread() + " " + s);
                    return s > 0;
                }).count();
```

 运行结果

```java
Thread[ForkJoinPool.commonPool-worker-13,5,main] 3
Thread[ForkJoinPool.commonPool-worker-11,5,main] 1
Thread[ForkJoinPool.commonPool-worker-9,5,main] 2
Thread[ForkJoinPool.commonPool-worker-4,5,main] 5
Thread[ForkJoinPool.commonPool-worker-2,5,main] 6
Thread[main,5,main] 4
```



在for循环，串行的Stream流和并行的Stream流之中**处理比较多的数据**的情况下parallelStream的效率是最高的。
Stream并行处理的过程会分而治之，也就是将一个大的任务切分成了多个小任务,这表示每个任务都是一个线程操作。



#### 线程安全问题

在多线程的处理下，肯定会出现数据安全问题。



解决办法：



> 同步锁

```java
        List<Integer> listNew = new ArrayList<>();
        IntStream.rangeClosed(1,1000) // 生成数据1-1000
                .parallel()
                .forEach(i -> {
                    synchronized (listNew){
                        listNew.add(i);
                    }
                });
        System.out.println(listNew.size());
```



> 线程安全容器

```java
        // Vector也可以使用但是已经过时
        CopyOnWriteArrayList<Integer> listNew = new CopyOnWriteArrayList<>();
        IntStream.rangeClosed(1,1000) // 生成数据1-1000
                .parallel()
                .forEach(i -> {
                        listNew.add(i);
                });
```



> 通过Stream中的toArray/collect（线程安全 可以查看文档注释）操作

```java
        List<Integer> listNew = new ArrayList<>();
        List<Integer> list = IntStream.rangeClosed(1, 1000) // 生成数据1-1000
                .parallel()
                .boxed()// 返回由该流的元素组成的Stream ，每个元素都装箱为Integer
                .collect(Collectors.toList());
```



### Fork/Join框架



parallelStream使用的是Fork/join框架。Fork/join框架自JDK7引入，可以将一个大的任务拆分成为很多小的任务来异步执行。

> 注意包含三个模块

- 线程池 ForkjoinPool
- 任务对象 ForkjoinTask
- 执行任务线程 ForkjoinWorkerThread

![image-20220605192900328](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/image-20220605192900328.png)



#### Fork/Join原理 分治法

ForkJoinPool主要用来使用分治法(Divide-and-Conquer Algorithm)来解决问题。典型的应用比如快速排序算法ForkJoinPool需要使用相对少的线程来处理大量的任务。比如要对1000万个数据进行排序，那么会将这个任务分割成两个500万的排序任务和一个针对这两组500万数据的合并任务。以此类推，对于500万的数据也会做出同样的分割处理，到最后会设置一个阈值来规定当数据规模到多少时，停止这样的分割处理。比如，当元素的数量小于10时，会停止分割，转而使用插入排序对它们进行排序。那么到最后，所有的任务加起来会有大概2000000+个。问题的关键在于，对于一个任务而言，只有当它所有的子任务完成之后，它才能够被执行。

![image-20220605193214194](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/image-20220605193214194.png)



#### Fork/Join原理 工作窃取算法

Fork/Joir最核心的地方就是利用了现代硬件设备多核，在一个操作时候会有空闲的cpu,那么如何利用好这个空闲的
cpu就成了提高性能的关键，而这里我们要提到的工作窃取(work-stealing)算法就是整个Fork/Join框架的核心理念
Fork小oin工作窃取(work-stealing)算法是指某个线程从其他队列里窃取任务来执行。

![image-20220605194024351](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/image-20220605194024351.png)

那么为什么需要使用工作窃取算法呢？假如我们需要做一个比较大的任务，我们可以把这个任务分割为若干互不依赖的子任务，为了减少线程间的竞争，于是把这些子任务分别放到不同的队列里，并为每个队列创建一个单独的线程来执行队列里的任务，线程和队列一对应，比如A线程负责处理A队列里的任务。但是有的线程会先把自己队列里的任务干完，而其他线程对应的队列里还有任务等待处理。干完活的线程与其等着，不如去帮其他线程干活，于是它就去其他线程的队列里窃取一个任务来执行，而在这时它们会访问同一个队列，所以为了减少窃取任务线程和被窃取任务线程之间的竞争，通常会使用双端队列，被窃取任务线程永远从双端队列的头部拿任务执行，而窃取任务的线程永远从双端队列的尾部拿任务执行。
工作窃取算法的优点是充分利用线程进行并行计算，并减少了线程间的竞争，其缺点是在某些情况下还是存在竞争比如双端队列里只有一个任务时。并且消耗了更多的系统资源，比如创建多个线程和多个双端队列。
上文中已经提到了在JDK8引入了自动并行化的概念。它能够让一部分java代码自动地以并行的方式执行，也就是我们使用了ForkloinPool的ParallelStream.对于ForkJoinPool通用线程池的线程数量，通常使用默认值就可以了，即运行时计算机的处理器数量。可以通过设置系统属性 java.util.concurrent.ForkJoinPool.common.parallelism=N(N为线程数量)，来调整ForkJoinPool的线程数量，可以尝试调整成不同的参数来观察每次的输出结果。



> 案例

需求：使用Fork/Join计算1-10000的和，当一个任务的计算数量大于3000的时候拆分任务，数量小于3000的时候就计算。

<img src="https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/image-20220605201022706.png" alt="image-20220605201022706" style="zoom:50%;" />

```java
public class StreamTest005 {
    public static void main(String[] args) {

        ForkJoinPool pool = new ForkJoinPool();
        SumRecursiveTask task = new SumRecursiveTask(1,10000l);
        Long result = pool.invoke(task); // 线程池处理任务
        System.out.println(result);

    }
}

// 继承处理任务的类
class SumRecursiveTask extends RecursiveTask<Long> {

    // 定义一个拆分的临界值
    private static final long THRESHOLD = 3_000l;

    // 通过构造函数传入起始和结束坐标
    private final long start;
    private final long end;

    public SumRecursiveTask(long start, long end) {
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        // 得到长度
        long length = end - start;

        if (length <= THRESHOLD) { // 说明任务小于临界值不需要拆分
            // 处理任务 计算
            long sum = 0;
            for (long i = start; i <= end; i++) {
                sum += i;
            }
            System.out.printf("计算：%d ---> %d,的结果为：%d%n", start, end, sum);
            return sum;
        } else {
            // 需要拆分
            long middle = (start + end) / 2;
            System.out.printf("拆分：左边 %d ---> %d ，右边 %d ---> %d%n", start, middle, middle + 1, end);

            // 将拆分之后的任务继续代入 继承处理任务
            SumRecursiveTask left = new SumRecursiveTask(start, middle);
            left.fork(); // 在当前任务正在运行的线程池中异步执行此任务 爷爷类的方法
            SumRecursiveTask right = new SumRecursiveTask(middle + 1, end);
            right.fork();
            return left.join() + right.join(); // 返回计算结果
        }
    }
}

```

执行结果

```txt
拆分：左边 1 ---> 5000 ，右边 5001 ---> 10000
拆分：左边 1 ---> 2500 ，右边 2501 ---> 5000
拆分：左边 5001 ---> 7500 ，右边 7501 ---> 10000
计算：1 ---> 2500,的结果为：3126250
计算：2501 ---> 5000,的结果为：9376250
计算：7501 ---> 10000,的结果为：21876250
计算：5001 ---> 7500,的结果为：15626250
50005000
```





# Optional



以前对null的处理，一般都要使用if...else... 判断是否为null，代码显得很冗余

Optional类是解决空指针的问题，Optional是一个没有子类的工具类，可以为一个null的容器对象，它主要的作用就是为了避免Null检查，防止空指针异常。

![image-20220606123149478](https://yyb-note-images.oss-cn-shenzhen.aliyuncs.com/img/Typora/image-20220606123149478.png)



### Optional的创建方式



> 通过of方法 of方法不支持null值

```java
        Optional<String> op1 = Optional.of("java");
        // Optional<String> op2 = Optional.of(null); NullPointerException
```



> 通过ofNullable方法 支持null值

```java
        Optional<String> op3 = Optional.ofNullable("java");
        Optional<String> op4 = Optional.ofNullable(null);
```



> 通过empty方法 直接创建一个空的Optional对象

```java
        Optional<Object> op5 = Optional.empty();
```



### 常用方法



> get() 如果Optional有值则返回，否者抛出NoSuchElementException异常

> isPresent() 判断是否包含值，包含返回true，不包含返回false



```java
        Optional<Object> op2 = Optional.empty();
        // 这样写和之前判断null还是一样 没有意义？
        if (op2.isPresent()){
            op2.get();
        }else {
            System.out.println("没有值！！！");
        }
```



> orElse(T t) 如果调用的对象包含值，就返回该值，否则返回t

```java
        Optional<String> op1 = Optional.of("java");
        Optional<String> op2 = Optional.empty();

        System.out.println(op1.orElse("python")); // java
        System.out.println(op2.orElse("python")); // python
```



> orElseGet(Supplier s) 如果调用对象包含值，就返回该值，否者返回lambda表达式中的返回值

```java
        op2.orElseGet(() -> "为空哦！") // 为空哦！
```



> orElseThrow() 如果存在，则返回包含的值，否则抛出由提供的供应商创建的异常。



> ifPresent(Consumer<? super T> consumer) 该方法没有返回值，如果**存在值**，就调用Consumer接口函数的accept方法对数据进行处理 accept也没有返回值。



案例 对比 （感觉没啥区别，可能是例子不够好）

```java
    // 将Student对象中的 name 转大写并返回
    public String getName(Student student) {
        if (student != null) {
            String name = student.getName();
            if (name != null) {
                return name.toUpperCase();
            }
            return null;
        }
        return null;
    }

    public String getNameOptional(Optional<Student> op){
        if (op.isPresent()){
            String msg = op.map(Student::getName) // map 和Stream流中的方法相似
                    .map(String::toUpperCase)
                    .orElse(null);
            return msg;
        }
        return null;
    }
```

