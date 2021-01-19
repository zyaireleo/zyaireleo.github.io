---
title: java从入门到入土
date: 2020-11-05 12:50:03
tags: jdk
category: Java
description: 主要记录一些jdk的新操作
---

### JDK8

#### 接口新特性和日期处理类

##### 人间迷惑之default关键字

- 在jdk1.8以前接⼝⾥⾯是只能有抽象⽅法，不能有任何⽅法的实现。jdk1.8⾥⾯打破了这个规定，引⼊了新的关键字default，使⽤default修饰⽅法，可以在接⼝⾥⾯定义具体的⽅法实现 
- 默认⽅法： 接⼝⾥⾯定义⼀个默认⽅法，这个接⼝的实现类实现了这个接⼝之后，不⽤管这个 default修饰的⽅法就可以直接调⽤，即接⼝⽅法的默认实
- 静态⽅法: 接⼝名.静态⽅法来访问接⼝中的静态⽅法

##### 用处不大之base64加解密API

- 早期java要使⽤Base64的方法

  - 使⽤JDK⾥sun.misc套件下的BASE64Encoder和BASE64Decoder这两个类

  ```java
 BASE64Encoder encoder = new BASE64Encoder();
 BASE64Decoder decoder = new BASE64Decoder();
 String text = "⼩滴课堂";
 byte[] textByte = text.getBytes("UTF-8");
 //编码
 String encodedText = encoder.encode(textByte);
 System.out.println(encodedText);
 //解码
 System.out.println(new String(decoder.decodeBuffer(encodedText),
"UTF-8")
  ```

  - 缺点：编码和解码的效率⽐较差，公开信息说以后的版本会取消这个⽅法 
  
  - Apache Commons Codec有提供Base64的编码与解码 
  
    缺点：是需要引⽤Apache Commons Codec
  
- jdk8

  不用引包，直接开干

  ```java
  Base64.Decoder decoder = Base64.getDecoder();
  Base64.Encoder encoder = Base64.getEncoder();
  String text = "⼩小滴课堂";
  byte[] textByte = text.getBytes("UTF-8");
  //编码
  String encodedText = encoder.encodeToString(textByte);
  System.out.println(encodedText);
  //解码
  System.out.println(new String(decoder.decode(encodedText), "UTF-
  8"));
  ```

##### 搞不明白之日期处理类

- 核心类

  ```
  LocalDate：不包含具体时间的⽇日期。
  LocalTime：不含⽇日期的时间。
  LocalDateTime：包含了日期及时间。
  ```

- 常用API

  太多，不po

- 时间日期格式化

  - JDK8之前：SimpleDateFormat来进行格式化，但SimpleDateFormat并不是线程安全的
  - JDK8之后：引入线程安全的日期与时间DateTimeFormatter

  ```
  LocalDateTime ldt = LocalDateTime.now();
  System.out.println(ldt);
  DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
  String ldtStr = dtf.format(ldt);
  System.out.println(ldtStr);
  ```

- 获取指定的日期时间对象

  ```
  LocalDateTime ldt = LocalDateTime.of(2020, 11, 11, 8, 20, 30);
  System.out.println(ldt);
  ```

- 计算日期时间差

  ```java
  LocalDateTime today = LocalDateTime.now();
  System.out.println(today);
  LocalDateTime changeDate = LocalDateTime.of(2020,10,1,10,40,30);
  System.out.println(changeDate);
  Duration duration = Duration.between( today,changeDate);//第二个参数减第⼀个参数
  System.out.println(duration.toDays());//两个时间差的天数
  System.out.println(duration.toHours());//两个时间差的⼩小时数
  System.out.println(duration.toMinutes());//两个时间差的分钟数
  System.out.println(duration.toMillis());//两个时间差的毫秒数
  System.out.println(duration.toNanos());//两个时间差的纳秒数
  ```

##### 我于人间全无敌之optional类

- 作用

​	主要解决的问题是空指针异常（NullPointerException），本质是⼀一个包含有可选值的包装类，这意味着 Optional 类既可以含有对象也可以为空

- 创建option实例

  - of()

    null 值作为参数传递进去,则会抛异常

    ```
    Optional<Student> opt = Optional.of(user);
    ```

  - ofNullable()

    如果对象即可能是 null 也可能是非 null，应该使用 ofNullable() ⽅方法

    ```
    Optional<Student> opt = Optional.ofNullable(user);
    ```

- 访问 Optional 对象的值

  -  get() ⽅法

    ```
    Optional<Student> opt = Optional.ofNullable(student);
    Student s = opt.get();
    ```

  - 如果值存在则isPresent()⽅法会返回true，调⽤get()⽅法会返回该对象⼀般使⽤get之前需要 先验证是否有值，不然还会报错

    ```java
    public static void main(String[] args) {
     	Student student = null;
     	test(student);
    }
    
    public static void test(Student student){
     	Optional<Student> opt = Optional.ofNullable(student);
     	System.out.println(opt.isPresent());
    }
    ```

- 兜底 orElse⽅法 

  - orElse()如果有值则返回该值，否则返回传递给它的参数值

    ```
    Student student1 = null;
    Student student2 = new Student(2);
    Student result = Optional.ofNullable(student1).orElse(student2);
    System.out.println(result.getAge());
    ```

    ```
    Student student = null;
    int result = Optional.ofNullable(student).map(obj-
    >obj.getAge()).orElse(4);
    System.out.println(result);
    ```

#### lamda表达式

- 在JDK8之前，Java是不⽀持函数式编程的，所谓的函数编程，即可理解是将⼀个函数（也称为“行为”）作为⼀个参数进行传递， ⾯向对象编程是对数据的抽象（各种各样的POJO类），⽽函数式编 程则是对行为的抽象（将行为作为⼀个参数进行传递）

- 创建线程

  - jdk8之前

    ```
    new Thread(new Runnable() {
     @Override
     public void run() {
     System.out.println("⼩滴课堂学习Java架构教程");
     }
     });
    ```

  - jdk8

    ```
     new Thread(() -> System.out.println("⼩滴课堂学习Java架构教程"));
    ```

- 集合容器里面的字符串排序 

  - 使用前

    ```
     List<String> list =Arrays.asList("aaa","ggg","ffff","ccc");
     	Collections.sort(list, new Comparator<String>() {
     		@Override
     		public int compare(String a, String b) {
     			return b.compareTo(a);
     		}
     	}
     );
     
     for (String string : list) {
     	System.out.println(string);
     }
    ```

  - 使用后

    ```
     List<String> list =Arrays.asList("aaa","ggg","ffff","ccc");
     	Collections.sort(list, (a,b)->b.compareTo(a)
     	);
     	
     for (String string : list) {
     	System.out.println(string);
     }
    ```

- lambda表达式 使⽤用场景(前提)：⼀一个接⼝口中只包含⼀一个⽅方法，则可以使⽤用Lambda表达式，这样
  的接⼝口称之为“函数接⼝口” 语法： (params) -> expression

  ```
  第⼀部分为括号内⽤逗号分隔的形式参数，参数是函数式接⼝⾥⾯⽅法的参数；第⼆部分为⼀个箭
  头符号：->；第三部分为⽅法体，可以是表达式和代码块
  参数列表 ：
   括号中参数列表的数据类型可以省略不写
   括号中的参数只有⼀个，那么参数类型和()都可以省略不写
  ⽅法体：
   如果{}中的代码只有⼀⾏，⽆论有返回值，可以省略{}，return，分号，要⼀起省略，其他
  则需要加上
  ```

  ​	好处： Lambda 表达式的实现⽅式在本质是以匿名内部类的⽅式进⾏实现，重构现有臃肿代码，更⾼的开发效率，尤其是集合Collection操作的时候，

- 自定义lambda接⼝编程

  ⾃定义lambda接⼝流程 

  - 定义⼀个函数式接⼝ 需要标注此接⼝ @FunctionalInterface，否则万⼀团队成员在接口上加 了其他⽅法则容易出故障 
  - 编写⼀个⽅法，输⼊需要操做的数据和接口
  - 在调用⽅法时传⼊数据 和 lambda 表达式，⽤来操作数据

  定义接口

  ```
  @FunctionalInterface
  public interface OperFunction<R,T> {
  	 R operator(T t1, T t2);
  }
  ```

  实现操作

  ```java
  public static void main(String[] args) throws Exception {
   	System.out.println(operator(20, 5, (Integer x, Integer y) -> {
   		return x * y;
   	}));
   	
   	System.out.println(operator(20, 5, (x, y) -> x + y));
  	System.out.println(operator(20, 5, (x, y) -> x - y));
  	System.out.println(operator(20, 5, (x, y) -> x / y));
   }
   public static Integer operator(Integer x, Integer y,OperFunction<Integer, Integer> of) {
       return of.operator(x, y);
   }	
  ```

#### JDK函数式编程

- Lambda表达式必须先定义接⼝，创建相关⽅法之后才可使⽤，这样做⼗分不便，其实java8已经内置了许多接口, 例如下⾯四个功能型接⼝，所以⼀般很少会由⽤户去定义新的函数式接⼝

- Java8的最⼤大特性就是函数式接口，所有标注了@FunctionalInterface注解的接口都是函数式接口

  ```
  Java8 内置的四⼤核⼼函数式接⼝
  
  Consumer<T> : 消费型接⼝：有⼊参，⽆返回值
   	void accept(T t);
   	
  Supplier<T> : 供给型接⼝：⽆⼊参，有返回值
  	 T get();
  	 
  Function<T, R> : 函数型接⼝：有⼊参，有返回值
   	R apply(T t);
   	
  Predicate<T> : 断⾔型接⼝：有⼊参，有返回值，返回值类型确定是boolean
  	 boolean test(T t);
  ```

  

##### 函数式编程之Function

- 传⼊⼀个值经过函数的计算返回另⼀个值 

  T：⼊参类型，R：出参类型 

  调⽤⽅法：R apply(T t)

##### 函数式编程之BiFunction

##### 函数式编程之Comsumer

##### 函数式编程之Supplier

##### 函数式编程之Predictate