# Java 8发布时间

2014年三月份

# Java 8新特性简介

* 速度更快

  数据结构更改：

  1. 比如HashMap从1.7的数组与链表，到1.8的数组与红黑树
  2. ConcurrentHashMap 从1.7的分段锁，到1.8的大量使用CAS实现无锁并发

  内存模型修改：

  1. 1.8中没有永久带了，而是一快新的空间，叫MetaSpace，并且这块空间直接使用的是物理地址！

* 代码更少（增加了新的语法Lambda表达式）

* 强大的Stream API

  让我们操作数据就像使用sql操作数据一样可以排序，筛选等等一起操作

* 便于并行

* 最大化减少空指针异常 Optional

* 类型推断加强

  ```java
  //在java 1.7 中的类型推断厂家如下
  List<Integer> array = new ArrayList<>();

  //在Java 1.8 中还可以如下这样，但是在1.7中是会报错的
  //定义一个方法如下
  public void show(Map<Integer, Integer> map){
      //TODO
  }
  //但是使用的时候可以这样,不用传HashMap中的泛型，JVM可以自己根据上下文推断
  show(new HashMap<>)；
  ```

  ​

#Java 8的主要新特性

## Lambda 表达式

为什么使用Lambda表达式？

让代码更加简洁和优雅!

###格式

1. Lambda 表达式的基础语法：Java8中引入了一个新的操作符 "->" 该操作符称为箭头操作符或 Lambda 操作符

箭头操作符将 Lambda 表达式拆分成两部分：

​	左侧：Lambda 表达式的参数列表

​	右侧：Lambda 表达式中所需执行的功能， 即 Lambda 体

2. Lambda 表达式需要“函数式接口”的支持

   函数式接口：接口中只有一个抽象方法的接口，称为函数式接口。 可以使用注解 @FunctionalInterface 修饰可以检查是否是函数式接口

### 实战

语法格式一：无参数，无返回值 ，如：

```java
() -> System.out.println("Hello Lambda!");
```

语法格式二：有一个参数，并且无返回值
```java
//(x) -> System.out.println(x)
Consumer<String> con = x -> System.out.println(x);
con.accept("didi！");
```

语法格式三：若只有一个参数，小括号可以省略不写

```java
x -> System.out.println(x)
```

语法格式四：有两个以上的参数，有返回值，并且 Lambda 体中有多条语句

```java
Comparator<Integer> com = (x, y) -> {
	System.out.println("函数式接口");
	return Integer.compare(x, y);
};
```

语法格式五：若 Lambda 体中只有一条语句， return 和 大括号都可以省略不写

```java
Comparator<Integer> com = (x, y) -> Integer.compare(x, y);
```

语法格式六：Lambda 表达式的参数列表的数据类型可以省略不写，因为JVM编译器通过上下文推断出，数据类型，即“类型推断”

```java
//全写
(Integer x, Integer y) -> Integer.compare(x, y);
//省略
(x, y) -> Integer.compare(x, y);
```

## 函数式编程

### 函数式接口

只有一个抽象方法的接口就是函数式接口

### @FunctionalInterface

在一个接口上面加上这个注解，那么如果在这个接口中定义多个接口就会编译错误！

### 常见内置函数式接口

#### Consumer<T>

> Consumer<T> : 消费型接口

void accept(T t);

```java
	//Consumer<T> 消费型接口 :
	@Test
	public void test1(){
		happy(10000, (m) -> System.out.println("你们刚哥喜欢大宝剑，每次消费：" + m + "元"));
	} 
	
	public void happy(double money, Consumer<Double> con){
		con.accept(money);
	}
```



#### Supplier<T>

> Supplier<T> : 供给型接口

T get(); 

```java
	//Supplier<T> 供给型接口 :
	@Test
	public void test2(){
		List<Integer> numList = getNumList(10, () -> (int)(Math.random() * 100));
		
		for (Integer num : numList) {
			System.out.println(num);
		}
	}
	
	//需求：产生指定个数的整数，并放入集合中
	public List<Integer> getNumList(int num, Supplier<Integer> sup){
		List<Integer> list = new ArrayList<>();
		
		for (int i = 0; i < num; i++) {
			Integer n = sup.get();
			list.add(n);
		}
		
		return list;
	}
```



#### Function<T, R>

> Function<T, R> : 函数型接口

R apply(T t);

```java
	//Function<T, R> 函数型接口：
	@Test
	public void test3(){
		String newStr = strHandler("\t\t\t 我大尚硅谷威武   ", (str) -> str.trim());
		System.out.println(newStr);
		
		String subStr = strHandler("我大尚硅谷威武", (str) -> str.substring(2, 5));
		System.out.println(subStr);
	}
	
	//需求：用于处理字符串
	public String strHandler(String str, Function<String, String> fun){
		return fun.apply(str);
	}
```



#### Predicate<T>

> Predicate<T> : 断言型接口

boolean test(T t);

```java
	//Predicate<T> 断言型接口：
	@Test
	public void test4(){
		List<String> list = Arrays.asList("Hello", "atguigu", "Lambda", "www", "ok");
		List<String> strList = filterStr(list, (s) -> s.length() > 3);
		
		for (String str : strList) {
			System.out.println(str);
		}
	}
	
	//需求：将满足条件的字符串，放入集合中
	public List<String> filterStr(List<String> list, Predicate<String> pre){
		List<String> strList = new ArrayList<>();
		
		for (String str : list) {
			if(pre.test(str)){
				strList.add(str);
			}
		}
		
		return strList;
	}
```



##方法引用与构造器引用

### 方法引用：

若 Lambda 体中的功能，已经有方法提供了实现，可以使用方法引用

（可以将方法引用理解为 Lambda 表达式的另外一种表现形式）

#### 对象的引用 :: 实例方法名

```java
	//对象的引用 :: 实例方法名
	@Test
	public void test2(){
		Employee emp = new Employee(101, "张三", 18, 9999.99);
		
		Supplier<String> sup = () -> emp.getName();
		System.out.println(sup.get());
		
		System.out.println("----------------------------------");
		
		Supplier<String> sup2 = emp::getName;
		System.out.println(sup2.get());
	}
	
	@Test
	public void test1(){
		PrintStream ps = System.out;
		Consumer<String> con = (str) -> ps.println(str);
		con.accept("Hello World！");
		
		System.out.println("--------------------------------");
		
		Consumer<String> con2 = ps::println;
		con2.accept("Hello Java8！");
		
		Consumer<String> con3 = System.out::println;
	}
```

#### 类名 :: 静态方法名

```java
//类名 :: 静态方法名
@Test
public void test4(){
	Comparator<Integer> com = (x, y) -> Integer.compare(x, y);
	
	System.out.println("-------------------------------------");
	
	Comparator<Integer> com2 = Integer::compare;
}

@Test
public void test3(){
	BiFunction<Double, Double, Double> fun = (x, y) -> Math.max(x, y);
	System.out.println(fun.apply(1.5, 22.2));
	
	System.out.println("--------------------------------------------------");
	
	BiFunction<Double, Double, Double> fun2 = Math::max;
	System.out.println(fun2.apply(1.2, 1.5));
}
```
#### 类名 :: 实例方法名

```java
//类名 :: 实例方法名
@Test
public void test5(){
	BiPredicate<String, String> bp = (x, y) -> x.equals(y);
	System.out.println(bp.test("abcde", "abcde"));
	
	System.out.println("-----------------------------------------");
	
	BiPredicate<String, String> bp2 = String::equals;
	System.out.println(bp2.test("abc", "abc"));
	
	System.out.println("-----------------------------------------");
```


```java
	Function<Employee, String> fun = (e) -> e.show();
	System.out.println(fun.apply(new Employee()));
	
	System.out.println("-----------------------------------------");
	
	Function<Employee, String> fun2 = Employee::show;
	System.out.println(fun2.apply(new Employee()));
	
}
```
### 构造器引用

构造器的参数列表，需要与函数式接口中参数列表保持一致！

**格式**：

```
类名 :: new
```

**实例**：

```java
	//构造器引用
	@Test
	public void test7(){
		Function<String, Employee> fun = Employee::new;
		
		BiFunction<String, Integer, Employee> fun2 = Employee::new;
	}
	
	@Test
	public void test6(){
		Supplier<Employee> sup = () -> new Employee();
		System.out.println(sup.get());
		
		System.out.println("------------------------------------");
		
		Supplier<Employee> sup2 = Employee::new;
		System.out.println(sup2.get());
	}
```

### 数组引用

**格式**：

```
类型[] :: new
```

**实例**：

```java
	//数组引用
	@Test
	public void test8(){
		Function<Integer, String[]> fun = (args) -> new String[args];
		String[] strs = fun.apply(10);
		System.out.println(strs.length);
		
		System.out.println("--------------------------");
		
		Function<Integer, Employee[]> fun2 = Employee[] :: new;
		Employee[] emps = fun2.apply(20);
		System.out.println(emps.length);
	}
```



## Stream API

### 中间操作：

| 操 作      | 类 型  | 返回类型      | 操作参数           | 函数描述符         |
| -------- | ---- | --------- | -------------- | ------------- |
| filter   | 中间   | Stream<T> | Predicate<T>   | T -> boolean  |
| map      | 中间   | Stream<R> | Function<T, R> | T -> R        |
| limit    | 中间   | Stream<T> |                |               |
| sorted   | 中间   | Stream<T> | Comparator<T>  | (T, T) -> int |
| distinct | 中间   | Stream<T> |                |               |

### 终端操作 ：

| 操 作     | 类 型  | 目 的                                 |
| ------- | ---- | ----------------------------------- |
| forEach | 终端   | 消费流中的每个元素并对其应用 Lambda。这一操作返回 void   |
| count   | 终端   | 返回流中元素的个数。这一操作返回 long               |
| collect | 终端   | 把流归约成一个集合，比如 List、 Map 甚至是 Integer。 |



## 接口的默认方法和静态方法

### 默认方法

Java 8中允许接口中包含具有具体实现的方法，该方法称为“默认方法”，默认方法使用 **default** 关键字修饰。 

接口默认方法的” 类优先” 原则
若一个接口中定义了一个默认方法，而另外一个父类或接口中
又定义了一个同名的方法时

1. 选择父类中的方法。如果一个父类提供了具体的实现，那么接口中具有相同名称和参数的默认方法会被忽略。


2. 接口冲突。如果一个父接口提供一个默认方法，而另一个接口也提供了一个具有相同名称和参数列表的方法（不管方法是否是默认方法）， 那么必须覆盖该方法来解决冲突 



### 静态方法

Java8 中，接口中允许添加静态方法。 

## 新时间日期API

### LocalDate

java 8 中表示日期的类

#### 获取此对象

```java
LocalDate.of(2014, 3, 18);		//获取一个固定时间的日期
LocalDate.now();				//获取今天的日期
LocalDate.parse("2014-03-18");	//通过格式转化
```

#### 操作此对象

```java
LocalDate date = LocalDate.now();
int year = date.getYear();
Month month = date.getMonth();
int day = date.getDayOfMonth();
DayOfWeek dow = date.getDayOfWeek();
int len = date.lengthOfMonth();
boolean leap = date.isLeapYear();
//使用TemporalField读取LocalDate的值
int year = date.get(ChronoField.YEAR);
int month = date.get(ChronoField.MONTH_OF_YEAR);
int day = date.get(ChronoField.DAY_OF_MONTH);
```

### LocalTime

java 8中表示时间的类,操作类似`LocalDate`，具体的差异也会列出来

#### 获取此对象

```java
LocalTime time = LocalTime.parse("13:45:20");		//获取一个固定时间的日期
```

### LocalDateTime 

这个复合类，是LocalDate和LocalTime的合体 ，它同时表示了日期和时间，但不带有时区信息，你可以直接创建，也可以通过合并日期和时间对象构造 。





## 其他新特性

