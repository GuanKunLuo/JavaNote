##### 类型转换

> Widening Primitive Conversion
>
> 拓宽基本类型转换
>
> 数据范围小的转为数据范围大的（19种），可以自动转换（隐式转换）

```java
byte -> short int long float double
short -> int long float double
char -> int long float double
int -> long float double
long -> float double
float -> double
```

> Narrowing Primitive Conversion
>
> 窄化基本类型转换
>
> 数据范围大的转为数据范围小的（22种），可能会丢失精度和范围，需要强制转换

```java
short -> byte char
char -> byte short
int -> byte short char
long -> byte short char int
float -> byte short char int long
double -> byte short char int long float
```

> Unary Numeric Promotion
>
> 一元数字提升
>
> 将 byte short char 类型的一元数字自动提升为 int 类型（拓宽基本类型转换）

* 数组的索引、创建数组时的数组长度
* 一元运算符 +
* 一元运算符 -
* 按位取反（~）
* 位移（<<、>>、>>>）

> Binary Numeric Promotion
>
> 二元数字提升
>
> 提升一个或者两个数字（拓宽基本类型转换）
>
> 如果有任意一个数字是double类型，那么另一个就会被转换为double类型
>
> 否则，如果任意一个数字是float类型，那么另一个就会被转换为float类型
>
> 否则，如果任意一个数字是long类型，那么另一个就会被转换为long类型
>
> 否则，两个数字都被转换为int类型

* *、/、%
* +、-
* <、<=、>、>=
* ==、!=
* &、^、|
* ?、:

> true false null 不是关键字，是字面量

##### 命名规则

* 不限制长度的`Java`字母、`Java`数字序列，但必须以`Java`字母开头（区分大小写）
* 不能使用关键字
* 不能使用上面的3个字面量

> `Java`字母
>
> Character.isJavaIdentifierStart() 方法返回 `true` 的代表 `Java` 字母
>
> Java 数字
>
> 0-9

##### 栈帧

> 栈帧随着方法的调用而创建，随着方法的结束而销毁，存储了方法的局部变量信息

##### 继承

>  在java中所有类的基类是Object
>
> 子类可以定义跟父类同名的成员变量（但不推荐）
>
> 子类重写方法的返回值类型必须<=父类的方法的返回值类型
>
> 重写和重载不看返回值类型，只看方法签名（方法名、方法参数类型、方法参数个数）
>
> super()、this()只用在构造方法中才可以调用
>
> 子类的构造方法必须先调用父类的构造方法，再执行后面的代码，如果没有显示的调用父类的构造方法，编译器会自动调用父类的无参构造方法，如果父类没有无参的构造方法，则编译器会报错

##### 注解（Annotation）

* *@Override* ：代表重写（可选），如果方法不是重写则报错
* *@SuppressWarning("警告类别")* ：抑制警告提示 

##### 访问权限

* `public` ：在任何地方都是可见的
* `protected` : 仅在自己的包中或者自己的子类中可见
* `无修饰符（package-private）` : 仅在自己的包中可见
* `private` : 仅在自己的类中可见

> 只有 `public` 和 `无修饰符`  可以修饰`顶级类` 
>
> 在一个java文件中最外层定义的类称之为`顶级类` 
>
> 一个文件java文件中，可以定义多个顶级类，并且`public` 只可以修饰一个类，并且该类必须与java文件名相同
>
> 上述4个访问权限不可以修饰局部类、局部变量
>
> 子类重写的方法权限>=父类的方法权限

#####  static

> `static` 常用来修饰类的成员： 成员变量、方法、嵌套类

**成员变量**

* 成员变量 被 `static` 修饰
  * 被称为类变量、静态变量、静态字段
  * 在程序运行过程中只占用一份固定的内存（存储在方法区）
  * 可直接用 `类名.变量名` 访问，也可以通过 `对象名.变量名` 访问（但不推荐）

* 成员变量没有被 `static` 修饰
  * 称为`实例变量` 
  * 在每个实例内部都有一份内存（堆空间）
  * 通过 `对象名.变量名` 访问

**方法**

* 被 `static` 修饰
  * 称为 `类方法` 、`静态方法` 
  * 可以通过类名访问，也可以通过对象名访问（但不推荐）
  * 内部不可以使用 `this` 关键字
  * 可以直接访问别的类方法、类变量，不可以访问实例变量、实例方法
* 没有被 `static` 修饰
  * 称为 `实例方法` 
  * 通过 `对象名.方法名` 访问
  * 内部可以使用 `this` 关键字
  * 可以直接访问别的类方法、类变量、实例变量、实例方法

静态导入

> 使用`静态导入` 后，就可以省略类名来访问静态成员（成员变量、方法、嵌套类）

```java
//静态导入：在import后加上static关键字
import static com.luo.Person.*;
public class Main {
	public static void main(String[] args) {
		System.out.println(count);
	}
}
```

##### 单例模式

```java
/**
 * 饿汉式单例模式
 * @author luoguankun
 *
 */
public class Rocket {
	//私有的静态的实例变量
	private static Rocket instance = new Rocket();
	
	//构造方法私有化
	private Rocket() {}
	
	// 公共的静态的方法，返回唯一的哪个实例
	public static Rocket getInstance() {
		return instance;
	}
}

/**
 *  懒汉式单例模式
 * @author luoguankun
 *
 */
public class Rocket {
	private static Rocket instance = null;
	private Rocket() { };
  /**
	 * 并非线程安全的
	 * @return
	 */
	public static Rocket getInstance() {
		if (instance == null) instance = new Rocket();
		return instance;
	}
}
```

##### final

> 被`final` 修饰的类，不能被继承
>
> 被`final` 修饰的方法，不能被重写
>
> 被`final` 修饰的变量，只能进行1次赋值

**常量 static final**

```java
public static final double PI = 3.1415926...
```

##### 嵌套类

> `嵌套类/内部类` 是跟外部类的实例相关联

* 必须先创建外部类实例，然后再用外部类实例创建内部类实例

* 内部类不能定义`static` 成员 、`static` 方法、但可以定义常量

* 内部类可以直接访问外部类中的所有成员（即使被声明为private）

* 外部类可以直接访问内部类实例的成员变量、方法（即使被声明为private）用类变量名.成员

```java
public class Person {
	public static int count = 10;
	public class Hand {
		
	}
}

//创建内部类:外部类名.new 内部类名
Person person = new Person();
Hand hand = person.new Hand();


//内部类调用外部类同名成员属性的方法
public class OuterClass {
	private int x = 1;
	public class InnerClass {
		private int x = 2;
		public void show() {
			System.out.println(x);	//2
			System.out.println(this.x);	   //2
			System.out.println(OuterClass.this.x);	//1
		}
	}
}
```

##### 静态嵌套类

> 被 `static` 修饰的嵌套类
>
> 静态嵌套类在行为上就是一个顶级类，只是定义的代码写在了另一个类中

对比一般的顶级类，多了一些特殊权限：

1. 可以直接访问外部类中除实例变量、实例方法外的其他成员（即使被声明为`private` ）

##### 局部类

> 定义在代码块中的类，称为局部类
>

* 作用域仅为代码块中

* 局部类不能定义静态成员变量、方法，但可以定义编译时常量 `static final` 

* 局部类内部只能访问`final`或者`有效final`的局部变量，`有效final`代表从Java 8开始编译器自动判断的只赋值过一次的变量

* 局部类可以直接访问外部类中的所有成员（即使被声明为`private`）

* 局部类只有定义在实例相关的代码块中，才能直接访问外部类中的实例成员（实例变量、实例方法）

##### 抽象方法

> 被`abstract` 修饰的方法，称为抽象方法

* 只有方法声明，没有方法实现
* 不能是 `private` 权限（因为定义抽象方法的目的让子类去实现）
* 只能是实例方法，不能是类方法
* 只能定义在抽象类、接口中

##### 抽象类

> 被`abstract` 修饰的类，称为抽象类

* 可以定义抽象方法
* 不能实例化，但可以自定义构造方法
* 子类必须实现抽象父类中的所有抽象方法（除非子类也是一个抽象类）
* 可以像非抽象类一样定义成员变量、常量、嵌套类型、初始化块、构造方法、非抽象方法等，也就是说，抽象类也可以完全不定义抽象方法

> 使用场景：
>
> 抽取子类的`公共实现`到抽象父类中，要求子类必须要单独实现的定义成抽象方法

##### 接口

> 一系列方法声明的集合
>
> 用来定义规范、标准
>
> 在 Java 编程语言中，*interface (接口)* 是一个类似于类的引用类型，它 *只能* 包含常量，方法签名，默认方法，静态方法和嵌套类型。方法体仅适用于默认方法和静态方法。接口不能实例化 - 它们只能通过类 *implemented (实现)* 或通过其他接口 *extended (继承)*。
>
> 方法不是必需的。空接口可以用作类型和标记类，而无需任何特定的方法实现。有关有用的空接口的示例，请参阅 `java.io.Serializable.`

```java
//声明定义接口
public interface Test {
	public abstract void test1();
	public abstract void test2();
	public abstract void test3();
}

//实现接口
public class Person implements Test {
	@Override
	public void test1() {
		// TODO Auto-generated method stub

	}
	@Override
	public void test2() {
		// TODO Auto-generated method stub

	}
	@Override
	public void test3() {
		// TODO Auto-generated method stub

	}
}
```

> 接口的实际应用

```java
public interface JiaJiaoable {
	void jiaoBianCheng(Child child);
	void jiaoZuQiu(Child child);
}

public class Student implements JiaJiaoable {
	public void jiaoBianCheng(Child child) {
		System.out.println("Student教" + child.getName() + "编程");
	}
	
	public void jiaoZuQiu(Child child) {
		System.out.println("Student教" + child.getName() + "踢足球");
	}
}


public class Robot implements JiaJiaoable{
	public void jiaoBianCheng(Child child) {
		System.out.println("Robot教" + child.getName() + "编程");
	}
	
	public void jiaoZuQiu(Child child) {
		System.out.println("Robot教" + child.getName() + "踢足球");
	}
}

public class Child {
	private String name;
	private JiaJiaoable jiaJiao;
	
	public Child(String name) {
		this.name = name;
	}

	public String getName() {
		return name;
	}
	
	public void setJiaJiao(JiaJiaoable jiaJiao) {
		this.jiaJiao = jiaJiao;
	}

	public void study() {
		jiaJiao.jiaoBianCheng(this);
		jiaJiao.jiaoZuQiu(this);
	}
}

//Main
public static void main(String[] args) {
		Child child = new Child("Jack");
		child.setJiaJiao(new Student());
		child.setJiaJiao(new Robot());
		child.study();
	}
```

**接口中可以定义的内容**

* 抽象方法、常量、嵌套类型，从`Java 8` 开始还可以定义：默认方法、静态方法
* 上述可以定义的内容都是隐式 `public` 的， 因此可以省略 `public` 关键字
* 从`Java 9` 开始可以定义`private` 方法
* 接口中定义的属性都是`常量的` ，默认是省略`static final`修饰符的
* 抽象方法可以省略 `abstract`修饰符
* 不能自定义构造方法、不能定义（静态）初始化块、不能实例化

**接口的细节**

1. 接口名称可以在任何适用类型的地方使用
2. 一个类可以通过`implements` 实现一个或者多个接口
3. 实现接口的类必须实现接口中定义的所有抽象方法，除非它是个抽象类
4. 如果一个类实现的多个接口中有相同的抽象方法，只需要实现此方法一次
5. `extends` 和 `implements` 可以一起使用，但是`implements` 必须写在 `extends` 的后面
6. 当父类、接口中的方法签名相同时，那么返回值类型也必须相同
7. 一个接口可以通过`extends` 关键字继承一个或者多个接口
8. 当多个父接口中的方法签名一样时，那么返回值类型也必须一样

**抽象类和接口的对比**

* 抽象类
  * 继承  A extends D：A 是 D
  * 在紧密相关的类之间共享代码
  * 需要除`public` 之外的访问权限
  * 需要定义实例变量、非`final` 的静态变量
* 接口
  * 实现 A implements D: A 会 D 中的行为
  * 不相关的类实现相同的方法
  * 只是定义行为，不关心具体是谁实现了行为
  * 想实现类型的多重继承

##### 接口的升级问题

* 如果在某个接口中增加了新的抽象方法

  * 会导致大幅代码改动，以前实现的接口都需要实现新增的抽象方法

  * 若想不改动以前实现类的前提下进行接口升级，从`Java 8`开始，有2种方案

    * 默认方法（Default Method）

      * 用 `default` 修饰默认方法，并且添加实现
      * 默认方法只能是实例方法

      > 当一个类实现的接口中有默认方法时，这个类可以：
      >
      > 当一个接口继承的父接口中有默认方法时，这个接口可以:
      >
      > 1、啥也不干，沿用接口的默认实现
      >
      > 2、重新定义默认方法，覆盖默认方法的实现
      >
      > 3、重新声明默认方法，将默认方法声明为抽象方法（此类必须是抽象类）

      
  
      * 如果父类定义的非抽象方法与接口的默认方法相同时，最终将调用父类的方法
      * 如果父类定义的抽象方法与接口的默认方法相同时，要求子类实现此抽象方法
      * 可以通过`super` 关键字调用接口的默认方法
      * 如果（父）接口定义的默认方法与其他（父）接口定义的方法相同时，要求子类型实现此默认方法
      * 继承包含默认方法的接口时，可以执行以下操作：
        - 不提及默认方法，它允许继承的接口继承默认方法。
      - 重新声明默认方法，使其成为 `abstract`。
        - 重新定义默认方法，将重写该方法。

    * 静态方法（Static Method）
  
    * 接口中定义的静态方法只能通过接口名调用，不能被继承
      
    * 可以用接口名.方法名调用
  
  * 还可以选择创建一个新的接口继承原有接口来扩展接口方法
  
  

#####多态

> 具有多种形态
>
> 同一操作作用于不同的对象，产生不同的执行接口

**多态的体现**

* 父类（接口）类型指向子类对象
* 调用子类重写的方法

**`JVM`会根据或引用变量指向具体对象来调用对应的方法**

* 这个行为叫做：`虚方法调用`
* 类似于`C++` 中的虚函数调用

```java
public class Person {
	public int age = 1;
	
	static void jump() {
		System.out.println("Person-jump");
	}
	
	public void run() {
		System.out.println("Person-run");
	}
}

public class Student extends Person {
	public int age = 2;
	
	static void jump() {
		System.out.println("Student-jump");
	}
	
	public void run() {
		System.out.println("Student-run");
	}
	
	void saySomething() {
		System.out.println("Student-say");
	}
}

Person stu1 = new Student();
Student stu2 = new Student();
System.out.println(stu1.age);
System.out.println(stu2.age);
stu1.jump();
stu1.run();
stu2.saySomething();
((Student)stu1).saySomething();
System.out.println(((Student)stu1).age);
```

##### 匿名类（Anoymous Class）

> 当接口、抽象类的实现类，在整个项目中只用过一次，可以考虑使用匿名类

```java
Runable person = new Runable() {
  @Override
  public void run() {
    System.out.println("person-run");
  }
};

//调用
person.run();
```

* 匿名类不能定义除编译时常量以外的任何`static` 成员
* 匿名类只能访问`final` 或者 `有效final` 的局部变量
* 与局部类规则相同

**匿名类的用途**

1. 代码传递

```java
public class Times {
	public interface Block {
		void execute();
	}
	public static void test(Block block) {
		long begin = System.currentTimeMillis();
		block.execute();
		long end = System.currentTimeMillis();
		double duration = (end - begin) / 1000.0;
		System.out.println("耗时：" + duration + "s");
	}
}

Times.test(new Block() {
  @Override
  public void execute() {
    int age = 200000;
    for (int i = 0; i < age; i++) {
      System.out.println(i);
    }
  }
});
```

2. 回调

```java
public class Networks {
	public interface Block {
		void success(Object response);
		void failure();
	}
	
	public static void get(String url, Block callback) {
		//1、根据url发送一个异步请求（开启一条子线程）
		//....
		//2、请求完毕后
		boolean result = true;
		if (result) {
			Object responseObject = null;
			callback.success(responseObject);
		} else {
			callback.failure();
		}
	}
}

//调用
public class Main {
	public static void main(String[] args) {
		Networks.get("", new Block() {
			
			@Override
			public void success(Object response) {
				
			}
			
			@Override
			public void failure() {
				
			}
		});
	}
}
```

3. 过滤器

```java
public class Files {
	public interface Filter {
		boolean accept(String filename);
	}
	
	public static String[] getAllFileNames(String dir, Filter filter) {
		// 1.先获取dir文件夹下的所有文件名
		String[] results = {};
		
		// 2.进行过滤
		for (String filename : results) {
			if (filter.accept(filename)) {
				//将这个文件名装起来
				
			}
		}
		
		//3.返回所有装起来的文件名
		return results;
	}
}

//调用
public class Main {
	public static void main(String[] args) {
		Files.getAllFileNames("", new Filter() {
			@Override
			public boolean accept(String filename) {
				return filename.contai	ns("类");
			}
		});
	}
}
```

##### `Lambda Expression`

* `Lambda` 表达式是 `Java 8` 开始才有的语法
* 函数式接口（`Functional Interface`）： 只包含1个抽象方法的接口
  * 可以在接口上面加上`@FunctionalInterface` 注解，表示它是一个`函数式接口`
* 当`匿名类`实现的是`函数式接口`时，可以使用`Lambda` 表达式进行简化

```java
//Lambda语法
/*
	(参数列表) -> {
		return xxx;
	}
*/

Integer[] ages = {11,22,33,44,55,66,77,88,99};
Arrays.sort(ages, (a1, a2) -> a2 - a1);
System.out.println(Arrays.toString(ages));
```

* `Lambda` 只能访问`final` 或者 `有效final` 的局部变量
* `Lambda` 没有引入新的作用域

##### 匿名类 vs Lambda

* 作用域的区别：函数式接口的匿名类有自己的作用域，但是用`Lambda` 简化后的相当于没有引入新的作用域，所以要注意一些变量的重复定义，或者变量的访问域的区别

##### 方法引用

> 如果`Lambda` 中的内容仅仅是调用某个方法，可以使用`方法引用（Method Reference）` 来简化

|               种类               |              用法              |
| :------------------------------: | :----------------------------: |
|           引用静态方法           |  ClassName::staticMethodName   |
|      引用特定对象的实例方法      | ObjectName::instanceMethodName |
| 引用特定类型的任意对象的实例方法 |     ClassName::methodName      |
|           引用构造方法           |         ClassName::new         |
|    引用当前类中定义的实例方法    |    this::instanceMethodName    |
|     引用父类中定义的实例方法     |   super::instanceMethodName    |

```java
public static void main(String[] args) {
  Testable testable = (v1, v2) ->  Math.max(v1, v2);
  //类方法引用
  Testable testable2 = Math::max;
  System.out.println(testable.test(41, 40));
  System.out.println(testable2.test(50, 40));
}
```

**常用函数式接口**

> `java.util.function` 包中提供了很多常用的函数式接口

* `Supplier`
  * 有时使用`Supplier` 传参，可以避免代码的浪费执行（有必要的时候再执行）
* `Consumer`
* `Predicate`
* `Function`
* `......`

```java
//Supplier 定义
@FunctionalInterface
public interface Supplier<T> {
    T get();
}

//示例
public static void main(String[] args) {
	String str1 = null;
  
  //makeString
  //123
  System.out.println(getFirstNotEmptyString1("123", makeString()));
  //123
  System.out.println(getFirstNotEmptyString2("123", Main::makeString));
}

static String makeString() {
  System.out.println("makeString");
  return String.format("%d %d %d",1,2,3);
}

/* 方法一：返回第一个不为null的字符串 */
static String getFirstNotEmptyString1(String s1, String s2) {
  if (s1 != null && s1.length() > 0) return s1;
  if (s2 != null && s2.length() > 0) return s2;
  return null;
}

/*
	方法二：
  利用Supplier函数式接口作为方法参数，返回第一个不为null的字符串
  这样的好处是延迟执行
*/
static String getFirstNotEmptyString2(String s1, Supplier<String> s2Supplier) {
  if (s1 != null && s1.length() > 0) return s1;
  String s2 = s2Supplier.get();
  if (s2 != null && s2.length() > 0) return s2;
  return null;
}
```

```java
//Consumer 定义
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}

//示例
public static void main(String[] args) {
    int[] ints = {11, 22, 33, 44};
    //21 32 43 54
    ForEach(ints, (n) -> System.out.println(n + 10));
    //[11, 22, 33, 44]
    System.out.println(Arrays.toString(ints));
}

static void ForEach(int[] nums, Consumer<Integer> c) {
    if (nums == null || c == null) return;
    for (int n : nums) {
        c.accept(n);
    }
}
```

```java
//Predicate 定义
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

//示例
public static void main(String[] args) {
    int[] nums = {11,33,44,55,88,77,66};
    String str = join(nums, (n) -> (n & 1) == 0);
    System.out.println(str);
}

//拼接
static String join(int[] nums, Predicate<Integer> p) {
    if (nums == null || p == null) return null;
    StringBuilder sb = new StringBuilder();
    for (int n : nums) {
        if (p.test(n)) {
            sb.append(n).append("_");
        }
    }
    sb.deleteCharAt(sb.length() - 1);
    return sb.toString();
}
```

```java
//Function
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

//示例
public static void main(String[] args) {
    String[] strs = {"1","2","3"};
    int result = sum(strs, (s) -> Integer.valueOf(s));
    System.out.println(result);	//6
}

static int sum(String[] strs, Function<String, Integer> f) {
    if (strs == null || f == null) return 0;
    int result = 0;
    for (String str : strs) {
        result += f.apply(str);
    }
    return result;
}
```

##### 枚举

> 枚举是由一组预定义的常量构成
>
> 如果一个变量的取值只可能是固定的几个值，可以考虑使用枚举类型
>
> 枚举的本质就是类， 所有枚举类型最终都隐式继承自`java.lang.Enum` 
>
> 枚举定义完常量后，常量一定要写在前面，可以再定义成员变量、方法等内容（这时最后一个常量要以分号结束）
>
> 枚举的构造方法权限必须是 `无修饰符` 或者 `private` 
>
> Java 会主动调用构造方法初始化每一个常量，你不能主动调用构造方法
>

```java
public class Main {
  
	public static void main(String[] args) {
		Season summer = Season.SUMMER;
		System.out.println(summer.getMin());
		System.out.println(summer.getMax());
	}
	
	// 枚举类型
	public enum Season {
		SPRING(5,15), 
		SUMMER(25,31), 
		FALL(5,10), 
		WINTER(-10,5);
		
		private int min;
		private int max;
		private Season(int min, int max) {
			this.min = min;
			this.max = max;
		}
		
		public int getMax() {
			return max;
		}
		
		public int getMin() {
			return min;
		}
	}
}
```

##### 字符串

> `Java` 中用 `java.lang.String` 类代表字符串
>
> 底层使用`char[]` 存储字符数据，从`Java 9` 开始，底层使用`byte[]` 存储字符数据
>
> 所有字符串字面量（比如"abc"）都是`String` 类的实例
>
> `String` 对象一旦创建完毕，它的字符内容是不可以修改的

**字符串常量池(`String Constant Pool 简称SCP`)**

* 从 `Java 7` 开始属于堆空间的一部分（以前放在方法区）
* 当遇到字符串字面量时，会去查看`SCP`
  * 如果`SCP` 中存在于字面量内容一样的字符串对象`A` 时，就返回`A`
  * 否则，创建一个新的字符串对象`D` ，并加入到`SCP` 中，返回`D`

**intern方法**

* 如果`SCP` 中存在与`A` 内容一样的字符串对象`C` 时，就返回`C`
* 否则，将`A` 加入到`SCP` 中， 返回`A` 

**StringBuilder**

* 在进行大量字符串的改动操作时（比如拼接、替换）
* 使用`String`会非常消耗内存、降低程序性能
* 使用`StringBuilder` 可以节省内存、提高程序性能
* 常用方法：
  * append
  * insert
  * delete
  * replace
  * reverse
* `StringBuilder` 并不是 `String` 的子类或者父类
* `StringBuilder`、`String` 都实现了`CharSequence` 接口
* `StringBuiler` 的默认容量是`16` ，扩容后的新容量是原来的`2倍 + 2`

##### 日期

> `Java.util.Date` 是开发中经常用到的日期处理类

```java
//获得当前时间
Date now = new Date();
System.out.println(now);

//120 + 1970-01-01 00:00:00 GMT
//GMT 格林尼治时间 （英国）
//CST 中国时间 与格林尼治差8小时
Date date1 = new Date(120);
System.out.println(date1);

// 返回的是从1970-01-01 00:00:00 GMT到现在经历的毫秒数
System.currentTimeMillis();


//常用方法
Date d1 = new Date();
Date d2 = new Date();

d1.setTime(1000);
d2.setTime(2000);

System.out.println(d1.getTime());
System.out.println(d2.getTime());

System.out.println(d2.after(d1));			//true
System.out.println(d1.before(d2));		//true
System.out.println(d1.compareTo(d2));	//-1
```

**日期格式化处理**

> `java.text.SimpleDateFormat` 常用来进行日期的格式化处理

```java
SimpleDateFormat fmt = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
String str = fmt.format(new Date());
//2020-04-17 10:07:59
System.out.println(str);

Date date = fmt.parse(str);
//Fri Apr 17 10:08:39 CST 2020
System.out.println(date);
```

**Calendar**

> `java.util.Calendar` 也是开发中经常用到的日期处理类
>
> 功能比`Date` 更加丰富，`Date` 中很多过期的方法都迁移到了`Calendar` 中

```java
//表示当前时间
Calendar c = Calendar.getInstance();
//获得年
c.get(Calendar.YEAR);
//获得月（取值范围[0,11]. 0代表1月，递增到11代表12月）
c.get(Calendar.MONTH);
//一月中的第几天（取值范围[1,31]）
c.get(Calendar.DAY_OF_MONTH);
//一周中的第几天（取值范围[1,7]，1是星期天，2是星期一，7是星期六）
c.get(Calendar.DAY_OF_WEEK);
//一年中的第几天（取值范围[1, 366]）
c.get(Calendar.DAY_OF_YEAR);	
c.get(Calendar.HOUR);			//时
c.get(Calendar.MINUTE);			//分
c.get(Calendar.SECOND);			//秒
c.get(Calendar.MILLISECOND);	//毫秒

//常用方法
Calendar c = Calendar.getInstance();
//改时间为 2019年7月6日
c.set(2019, 6, 6);
//改时间为 2019年7月11日
c.add(Calendar.DAY_OF_MONTH, 5);
//改时间为 2019年9月11日
c.add(Calendar.MONTH, 2);

//设置Date对象
c.setTime(new Date());
//获得Date对象
c.getTime();

//设置毫秒数
c.setTimeInMillis(System.currentTimeMillis());
//获得毫秒数
c.getTimeInMillis();
```

##### 异常（Exception）

> 语法错误：导致编译失败，程序无法运行
>
> 逻辑错误： 编译可以通过
>
> 运行时错误： 在程序运行过程中产生的以外，会导致程序终止运行，也叫做`异常`
>
> `Java` 中所有的异常最终都继承自`java.lang.Throwable`

1. 检查型异常（checked Exception）
   * 这类异常一般难以避免，编译器会进行检查，如果开发者没有处理这类异常，编译器将会报错
   * 除`Error`、`RuntimeException` 以外的异常，都叫做检查型异常（直接继承自`Exception`的异常）
2. 非检查型异常（Unchecked Exception）
   * 这类异常一般可以避免、编译器不会进行检查
   * 如果开发者没有处理这类异常，编译器将不会报错
   * `Error`、`RuntimeException`

**常见的检查型异常**

* `java.io.FileNotFoundException` ：文件不存在
* `java.text.ParseException` ：字符串的格式不对
* `java.lang.InterruptedException`
* `java.lang.ClassNotFoundException` ：不存在这个类
* `java.lang.InstantiationException` ：初始化异常
* `java.lang.IllegalAccessException` ：非法访问异常

常见的非检查型异常

* `Error`
  * `java.lang.OutOfMemoryError` 
  * `java.lang.StackOverflowError` ：递归死循环
* `RuntimeException`
  * `java.lang.NullPointerException` ： 空指针异常
  * `java.lang.NumberFormatException` ：数字格式异常
  * `java.lang.ArrayIndexOutOfBoundsException` ：数组索引越界异常
  * `java.lang.ClassCastException` ：类型不匹配异常

**异常的处理**

> 程度产生了异常，一般我们会称之为：抛出了异常

*不管抛出的是检查型异常，还是非检查型异常：*

* 只要程序员没有主动去处理它，都会导致`Java` 程序终止运行

*两种方法处理异常*

1. `try-catch`

   ```java
   try {
       //代码1
       //代码2 （可能会抛出异常）,如果抛出异常，则代码3不会被执行
       //代码3
     } catch (异常A e) {
       // 当抛出【异常A】类型的异常时，会进入这个代码块
     	// 一般用下面的方法打印错误信息
       e.printStackTrace();
     } catch (异常B e) {
       // 当没有抛出【异常A】类型
       // 但是抛出【异常B】类型的异常时，会进入这个代码块
     } catch (异常C e) 
   		// 当没有抛出【异常A】类型、【异常B】类型
       // 但是抛出【异常C】类型的异常时，会进入这个代码块
     }
   
   //从Java7开始，单个catch可以捕获多种类型的异常
   //如果并列的几个异常类型之间存在父子关系，保留父类型即可
   //这里的e是隐式final的
   try {
     
   } catch (异常A | 异常B | 异常C e) {
   		//当抛出 A 或 B 或 C 类型的异常时，会进入这个代码块
   }
   ```

   * `finally`

   * 如果在执行`try` 或`catch` 时，JVM退出或者当前线程被中断、杀死，`finally` 可能不会执行

   * 如果`try` 或 `catch` 中使用了 `return`、`break`、`continue` 等提前结束语句，`finally` 会在 `return`、`break`、`continue` 之前执行

     ```java
     // try或catch正常执行完毕后，一定会执行 finally中的代码
     // 经常会在finally中编写一些关闭、释放资源的代码（比如关闭文件）
     try {
       
     } catch () {
       
     } catch () {
       
     } finally {
       
     }
     
     
     //只和try搭配使用
     try {
       
     } finally {
       
     }
     ```

2. `try-with-resources` 

   > 从`Java 7` 开始推出的`try-with-resources` 语句（可以没有`catch`、`finally`）

   ```java
   try (资源1; 资源2; ...) {
     
   } catch(Exception e) {
     
   } finally {
     
   }
   ```

   * 可以在`try` 后面的小括号中声明一个或多个资源（`resource`）
   * 实现了`java.lang.AutoCloseable` 接口的实例，都可以称之为是资源
   * 不管`try` 中的语句是正常还是意外结束
   * 最终都会自动按顺序调用每一个资源的`close` 方法（`close` 方法的调用顺序与资源的生命顺序相反）

   ```java
   //示例
   try (
      	OutputStream fos = new FileOutputStream("");
       InputStream fis = new FileInputStream("")
   ) {
       byte [] bytes = new byte[1024];
       int len;
       while ((len = fis.read(bytes)) != -1) {
           fos.write(bytes, 0, len);
       }
   } catch (FileNotFoundException e) {
       e.printStackTrace();
   } catch (IOException e) {
       e.printStackTrace();
   }
   ```

3. `throws`

   > 作用：将异常抛给上层方法
   >
   > 如果异常最终抛给了`JVM`，那么整个`Java` 程序将终止运行

   ```java
   public class Main {
   	public static void main(String[] args) {
   		try {
   			method1();
   		} catch (ClassNotFoundException e) {
   			e.printStackTrace();
   		}
   	}
   	
   	static void method1() throws ClassNotFoundException {
   		method2();
   	}
   	
   	static void method2() throws ClassNotFoundException {
   		method3();
   	}
   	
   	static void method3() throws ClassNotFoundException {
   		Class cls = Class.forName("XXX");
   	}
   }
   ```

   * 当父类的方法没有`throws` 异常，子类的重写方法也不能`throws` 异常
   * 当父类的方法有`throws` 方法，子类的重写方法可以
     * 不`throws` 异常
     * `throws` 跟父类一样的异常
     * `throws` 父类异常的子类型

4. `throw`

   > 使用`throw` 可以抛出一个新建的异常

   ```java
   public void setAge(int age) throws Exception {
     if (age <= 0) {
       throw new Exception("age必须>0");
     } else {
       this.age = age;
     }
   }
   ```

5. `自定义异常`

   * 继承自 `Exception`
     * 使用起来代码会稍微复杂
     * 希望开发者重视这个异常、认真处理这个异常
   * 继承自 `RuntimeException`
     * 使用起来代码会更加简洁
     * 不严格要求开发者去处理这个异常

   **使用异常的好处**

   * 将错误处理代码与普通代码区分开
   * 能将错误信息传播到调用堆栈中
   * 能对错误类型进行区分和分组

   **断言类**

   ```java
   public class Asserts {
   	public static void test(Boolean result) {
   		if (result) return;
   		throw new IllegalArgumentException("条件不成立");
   	}
   }
   ```

##### 正则表达式

> 用非常精简的语法取代了复杂的验证逻辑，极大提高了开发效率

**单字符匹配**

|      语法      |                 含义                 |
| :------------: | :----------------------------------: |
|     [abc]      |               a、b、c                |
|     [^abc]     |      除了a、b、c以外的任意字符       |
|    [a-zA-Z]    |            从a到z、从A到Z            |
|   [a-d[m-p]]   |           [a-dm-p]（并集）           |
|  [a-z&&[def]]  |           d、e、f（交集）            |
| [a-z&&[ ^bc]]  |  [ad-z]（差集，从[a-z]中减去[bc]）   |
| [a-z&&[ ^m-p]] | [a-lq-z]（差集，从[a-z]中减去[m-p]） |

**预定义字符**

> 在`Java` 中，以1个反斜杠（`\`） 开头的字符会被当做转义字符处理
>
> 因此，为了在正则表达式中完整地表达预定义字符，需要以2个反斜杠开头，比如"\\\d"

| 语法 |        含义        |
| :--: | :----------------: |
|  .   |      任意字符      |
|  \d  |   [0-9]（数字）    |
|  \D  |  [^0-9]（非数字）  |
|  \s  | [\t\n\f\r]（空白） |
|  \S  |  [^\s]（非空白）   |
|  \w  |  [a-zA-Z]（单词）  |
|  \W  |  [^\w]（非单词）   |

**量词（Quantifier）**

| 贪婪（Greedy） | 勉强（Reluctant） | 独占（Possessive） |           含义            |
| :------------: | :---------------: | :----------------: | :-----------------------: |
|      X{n}      |       X{n}?       |       X{n}+        |         X出现n次          |
|     X{n,m}     |      X{n,m}?      |      X{n,m}+       |        X出现n到m次        |
|     X{n,}      |      X{n,}?       |       X{n,}+       | X{0,1}（X出现0次或者1次） |
|       X?       |        X??        |        X?+         | X{0,1}（X出现0次或者1次） |
|       X*       |        X*?        |        X*+         |   X{0,}（X出现任意次）    |
|       X+       |        X+?        |        X++         |   X{1,}（X至少出现1次）   |

**`Pattern`、`Matcher`**

> `String` 的 `matches` 方法底层用到了`Pattern`、`Matcher` 两个类
>
> `Pattern` - `java.util.regex.Pattern`
>
> `Pattern.matches(regex,"...")`

**`Matcher` 常用方法**

* `matches()` : 如果整个`input` 与 `regex`匹配，则返回`true`

* `find()`
  * 如果从`input` 中找到了与`regex` 匹配的子序列，就返回`true`
  * 如果匹配成功，可以通过`start` 、`end`、`group` 方法获取更多信息
  * 每次的查找范围会先剔除此前已经查找过得范围
* `start()` : 返回上一次匹配成功的开始索引
* `end()` : 返回上一次匹配成功的结束索引
* `group()` : 返回上一次匹配成功的`input` 子序列

```java
public static void main(String[] args) {
		String input = "123_444_555_666_789";
		String regex = "\\d{3}";
		
		Pattern p = Pattern.compile(regex);
		Matcher m =  p.matcher(input);
		while (m.find()) {						//true
			System.out.println(m.group());	//123
			// [0, 3) length 3 - 0 = 3
			System.out.println(m.start());	//0
			System.out.println(m.end());	//3
		}		
	}
```

**贪婪（Greedy）**

1. 先吞掉整个`input` 进行匹配
   * 若匹配失败，则吐出最后一个字符
2. 然后再次尝试匹配，重复此过程，直到匹配成功

**勉强（Reluctant）**

1. 先吞掉`input` 的第一个字符进行匹配
   * 若匹配失败，则再吞掉一个字符
2. 然后再次尝试匹配，重复此过程，直到匹配成功

**独占（Possessive）**

1. 吞掉整个`input` 进行唯一的一次匹配

**捕获组**

```java
Strign regex = "(dog){3}";
"dogdogdog".matches(regex); 		//true
```

**捕获组-反向引用（Backreference）**

> 可以使用反斜杠（`\`）+ 组编号（从1开始）来引用组的内容

```java
String regex = "(\\d\\d)\\1";
"1212".matches(regex);	//true
"1234".matches(regex);	//false

String regex = "([a-z] {2})([A-Z] {2})\\2\\1";
"mjPKPKmj".matches(regex);	//true
"mjPKmjPK".matches(regex);	//false
```

* `((A)(B(C)))` 一共有4个组，可以从左到右数左括号

  * 编号1：`((A)(B(C)))`
  * 编号2：`(A)`
  * 编号3：`(B(C))`
  * 编号4：`(C)`

  ```java
  String regex = "((I)( Love( You)))\\3{2}";
  "I Love You Love You Love You".matches(regex); //true
  ```

**边界匹配符（Boundary Matcher）**

> `边界匹配符`匹配的是位置，不是字符
>
> 除开英文字母大小写、阿拉伯数字、下划线、其他国家的正常文字，以外的字符

* 终止符
  * \r、\n、\r\n
* 输入： 整个字符串
* 一行： 以终止符（或整个输入的结尾）结束的字符串片段
  * 如果输入的是`"dog\ndog\rdog"`
  * 那么3个`dog` 都是一行

| 语法 |              含义              |
| :--: | :----------------------------: |
|  \b  |            单词边界            |
|  \B  |           非单词边界           |
|  ^   |           一行的开头           |
|  $   |           一行的结尾           |
|  \A  |           输入的开头           |
|  \z  |           输入的结尾           |
|  \Z  | 输入的结尾（结尾可以有终止符） |
|  \G  |        上一次匹配的结尾        |

**常用模式**

|      模式       |                     含义                     | 等价于 |
| :-------------: | :------------------------------------------: | :----: |
|     DOTALL      |  单行模式（.可以匹配任意字符，包括终止符）   |  (?s)  |
|    MULTILINE    | 多行模式（^、$才能真正匹配一行的开头和结尾） |  (?m)  |
| CASE_INSENSTIVE |                 不区分大小写                 |  (?i)  |

```java
String regex = ".";
String input = "\r\n";
Pattern p = null;
Matcher m = null;

//默认“.”匹配符是不支持匹配\r\n的
p = Pattern.compile(regex,0);
m = p.matcher(input);
System.out.println(m.find());	//false

//使用DOTALL模式后，"."匹配符可以匹配\r\n
p = Pattern.compile(regex,Pattern.DOTALL);
m = p.matcher(input);
System.out.println(m.find());	//true

//设置多行模式后，\r\n才真正被启用，所以"."匹配符识别不了
p = Pattern.compile(regex,Pattern.MULTILINE);
m = p.matcher(input);
System.out.println(m.find());	//false

p = Pattern.compile(regex,Pattern.MULTILINE | Pattern.DOTALL);
m = p.matcher(input);
System.out.println(m.find());	//true
```
