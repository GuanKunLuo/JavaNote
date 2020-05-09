##### 泛型

> 从`Java 5`开始，增加了`泛型`技术
>
> 将类型变为参数，提高代码复用率
>
> 修饰类名的泛型类型，只能用于实例方法，不能用于类方法（静态方法）

* `T`：Type
* `E`：Element（元素）
* `K`：Key
* `N`：Number
* `V`：Value
* `S`、`U`、`V`：2nd，3rd，4th types

**泛型的使用限制**

* 基本类型不能作为类型参数
* 不能创建类型参数的实例
* 不能定义类型为类型参数的静态变量，也不能用在静态方法上
* 类型参数不能跟`instanceof` 一起使用
* 不能创建带有类型参数的数组
  * `new Box<Integer>[4]`	
* 不能定义泛型的异常类
  * `public class MyException<T> extends Exception {}`	
* `catch` 的异常类型不能用类型参数
  * `try {} catch (T e) {}`

**泛型类型（Generic Type）**

> 使用了泛型的类或者接口
>
> 比如`java.util.Comparator`、`java.util.Comparable`

```java
//T 称为 类型参数（Type parameter）
public class Student<T> {   //类型的占位符
    private T score;

    public Student(T score) {
        this.score = score;
    }

    public T getScore() {
        return score;
    }
}

//调用
Student<Double> stu2 = new Student<Double>(98.4);
//从Java 7开始，可以省略右边<>中的类型
Student<String> stu1 = new Student<>("A+");
```

**多个类型参数**

```java
public class Student<N, S> {
    private N no;
    private S score;

    public Student(N no, S score) {
        this.no = no;
        this.score = score;
    }
}

Student<String, String> stu1 = new Student<>("E922","A++");
Student<Integer, Double> stu2 = new Student<>(18, 95.5);
```

**泛型类型的继承**

```java
public interface Collection<E> extends Iterable<E>
public interface List<E> extends Collection<E>
public class ArrayList<E> implements List<E>

Iterable<String> it = null;
Collection<String> col = null;
List<String> li = null;
ArrayList<String> al = null;

it = col;
col = li;
li = al;

//泛型的继承的条件是必须泛型的类型相同，下面的代码是错误的，因为两个类虽然是继承关系，但是两个类的泛型类型不同，所以也不能进行多态赋值
List<Object> list = null;
ArrayList<String> al = null;
//Error
list = al;

//E 继承自 父类型的E
public interface MyList<E, T> extends List<E> {
	void setNo(T no);
}
```

**原始类型**

> 没有传递具体的类型给泛型的类型参数
>
> 当使用了`原始类型` 时，编译器会给出`rawtypes` 警告（可以用`@SuppressWarnings("rawtypes")` 消除）

* 将`非原始类型` 赋值给`原始类型` 时，编译器没有任何警告和错误
* 将`原始类型` 赋值给`非原始类型` 时，编译器会给出`unchecked` 警告（可以用`@SuppressWarnings("unchecked")` 消除）
* `Class` 是`原始类型` ，`Class<Object>` 是`非原始类型`
* `原始类型` 的类型默认为`Object` 类型

**泛型方法**

> 使用 `<类型占位>` 修饰的方法，称为泛型方法

```java
//泛型方法语法示例
public <T> void test(T v1, T v2) {};
//类型推断
public static final <T> List<T> emptyList() {
  return (List<T>) EMPTY_LIST;
}
//通过返回值推断出List的类型
List<String> list1 = Collections.emptyList();	//List<String>

//泛型方法-构造方法，比较少用
public class Person<T> {
	private T age;
	public <E> Person(E name, T age) {
    
  }
}
//T必须跟随类泛型的类型
Person<Integer> p1 = new Person<>("Jack", 20);
Person<Double> p2 = new Person<>(666, 20.6);
Person<String> p3 = new Person<>(12.34, "80后");
```

**限制类型参数**

* 可以通过`extends` 对类型参数增加一些限制条件，比如`<T extends A>`
* `extends` 后面可以跟上类名、接口名，代表`T` 必须是`A` 类型，或者继承、实现A
* 可以同时添加多个限制，比如`<T extends A & B & C>`，代表`T` 必须同时满足 `A、B、C`
  * 接口必须放在类的后面，类只能放1个

```java
//规定Person的泛型类型，必须继承Number
public class Person<T extends Number> {
  private T age;
  public Person(T age) {
    this.age = age;
  }
  //继承Number类型的泛型后，可直接调用父类泛型的方法
  public int getAge() {
    return (age == null) ? 0 : age.intValue();
  }
}

//调用
Person<Long> p1 = new Person<>(100l);
System.out.println(p1.getAge());
Person<Double> p2 = new Person<>(13.4);
System.out.println(p2.getAge());
//Error，String类不继承自Number
Person<Sring> p3 = new Person<Sring>();
```

**限制类型参数-示例**

```java
//限制类型参数用于泛型方法
static <T extends Comparable<T>> T getMax(T[] array) {
    if (array == null || array.length == 0) return null;
    T max = array[0];
    for (int i = 1; i < array.length; i++) {
        if (array[i] == null) continue;
        if (array[i].compareTo(max) <= 0) continue;
        max = array[i];
    }
    return max;
}

//调用
public static void main(String[] args) {
    Double[] ds = {4.1, 3.4, 7.4, 8.1};
    System.out.println(getMax(ds)); //8.1

    String[] strs = {"a", "b", "c", null};
    System.out.println(getMax(strs));   //c

    Integer[] is = {1,22,33,88,22,3};
    System.out.println(getMax(is)); //88
}
```

```java
public class Student<T extends Comparable<T>> implements Comparable<Student<T>> {
    private T score;

    public Student(T score) {
        this.score = score;
    }

    public T getScore() {
        return score;
    }

    public void setScore(T score) {
        this.score = score;
    }

    @Override
    public String toString() {
        return "Student{" +
                "score=" + score +
                '}';
    }

    @Override
    public int compareTo(Student<T> o) {
        if (o == null) return 1;
        if (score != null) return score.compareTo(o.score);
        //o.score != null, -1
        //o.score == null, 0
        return o.score != null ? -1 : 0;
    }
}

public static void main(String[] args) {
    Student[] studs = {
            new Student<>(11),
            new Student<>(88),
            new Student<>(55)
    };
    System.out.println(getMax(studs));
}
```

##### 通配符

> 在泛型中，`?` 被称为是通配符

* 通常用作变量类型、返回值类型的类型参数
* 不能用作泛型方法调用，泛型类型实例化、泛型类型定义的类型参数

```java
//泛型类型实例化，Error
new Box<?>();
//泛型方法，Error
Main.<?>set(xx, xx);
//泛型类型定义，Error
public class Student<? extends XXX> {}
```

**通配符-上界**（`<=`）

> 可以通过`extends` 设置类型参数的上界

``` java
//示例1
//类型参数必须是Number类型或者是Number的子类型
void testUpper(Box<? extends Number> box) {};

Box<Integer> p1 = null;
Box<Number> p2 = null;
//使用通配符定义变量
Box<? extends Number> p3 = null;
Box<? extends Integer> p4 = null;
testUpper(p1);
testUpper(p2);
testUpper(p3);
testUpper(p4);

//示例2
double sum(List<? extends Number> list) {
    double result = 0.0;
    for (Number number : list) {
        result += number.doubleValue();
    }
    return result;
}

List<? extends Number> list1 = Arrays.asList(11,22,33);
System.out.println(sum(list1));	//66.0
List<Integer> list2 = Arrays.asList(11,22,33);
System.out.println(sum(list2));	//66.0
```

**通配符-下界**（`>=`）

> 可以通过`super` 设置类型参数的下界，`super` 只能与`?` 搭配使用

```java
//示例1
void testLower(Box<? super Integer> box) {};

Box<Integer> p1 = null;
Box<Number> p2 = null;
//使用通配符定义变量
Box<? super Number> p3 = null;
Box<? super Integer> p4 = null;
testUpper(p1);
testUpper(p2);
testUpper(p3);
testUpper(p4);

//示例2
static void addNumbers(List<? super Integer> list) {
    for (int i = 0; i < 10; i++) {
        list.add(i);
    }
}
//调用
List<Integer> is = new ArrayList<>();
addNumbers(is);
System.out.println(is);

List<Number> ns = new ArrayList<>();
addNumbers(ns);
System.out.println(ns);
```

**通配符-无限制**

> `Class<?>`

```java
static void printList(List<?> list) {
    for (Object o : list) {
        System.out.print(o + " ");
    }
    System.out.println();
}
```

**通配符-继承**

> `? super -> ?`
>
> `? extends -> ?`
>
> 包含关系

**通配符-注意**

```java
//编译器在解析List<E>.set(int index, E element)时，无法确定E的真实类型，所以报错
void foo(List<?> list) {
    Object obj = list.get(0);
    list.set(0, obj);       //error
    list.set(0, list.get(0));   //error
}

//下面不报错
void foo(List<Object> list) {
    Object obj = list.get(0);
    list.set(0, obj);      
    list.set(0, list.get(0));
}

//解决办法，欺骗编译器
void foo(List<?> list) {
    foo2(list);
}

<T> void foo2(List<T> list) {
    T obj = list.get(0);
    list.set(0, obj);
    list.set(0, list.get(0));
}
```

##### Comparable Vs Comparator 

*`Comparable`*

* 如果数组元素本身具备可比较性（实现了`java.util.Comparable` 接口）
* 可以直接使用`Arrays.sort()` 方法进行排序

---

*`Comparator`*

* 可以在不修改类源代码的前提下，修改默认的比较方式（比如官方类、第三方类）

##### 集合（Collections）

> `java.util` 包中有个集合框架，提供了一大堆常用的数据结构
>
> `ArrayList`、`LinkedList`、`Queue`、`Stack`、`HashSet`、`HashMap` 等

**数组的局限性**

* 无法动态扩容
* 操作元素的过程不够面向对象

**`List`、`ArrayList`**

* 可以存储重复的元素
  * 元素之间 `equals` 可能返回 `true`
* 可以通过索引访问元素
* 记录元素的添加顺序
* `ArrayList` 的最小容量是10，每次扩容是上次容量的1.5倍
* `ArrayList` 是非线程安全的，对应`Vector` 是线程安全的

```java
//ArrayList 是 Java中的动态数组
//可以动态扩容的数组，封装了各种实用的数组操作
ArrayList list = new ArrayList();
list.add(11);
list.add(false);
//可以添加null值
list.add(null);
list.add(3.14);
//在list的0号索引位置插入"Jack"字符串
list.add(0,"Jack");
list.add('8');

//3   
System.out.println(list.indexOf(null));
//6
System.out.println(list.size());
//[Jack, 11, false, null, 3.14, 8]
System.out.println(list);
//清空集合,仅仅把集合中的每个内容全部变为null
list.clear();
//转换数组方法
List<Integer> list5 = new ArrayList<>();
list5.add(1);
list5.add(2);
//toArray方法默认返回Object[]，传入泛型类型可直接返回对应类型的数组
Integer[] integers = list5.toArray(new Integer[0]);
System.out.println(Arrays.toString(integers));

//遍历
//底层实现使用Iterator
Iterator<Integer> iterator = list.iterator();
while (iterator.hasNext()) {
  System.out.println(iterator.next());
}

for (Integer integer : list) {
  System.out.println(integer);
}

for (int i = 0; i < list.size(); i++) {
  System.out.println(list.get(i));
}

list.forEach(System.out::println);

//自定义Iterator
public class ClassRoom implements Iterable<String> {
    private String[] students;

    public ClassRoom(String... students) {
        this.students = students;
    }

    public String[] getStudents() {
        return students;
    }


    @Override
    public Iterator<String> iterator() {
        return new ClassRoomIterator();
    }

    private class ClassRoomIterator implements Iterator<String> {
        private int cursor;

        @Override
        public boolean hasNext() {
            return cursor < students.length;
        }

        @Override
        public String next() {
            return students[cursor++];
        }
    }
}

//调用遍历
ClassRoom cr = new ClassRoom("Jack","Rose");
for (String s : cr) {
    System.out.println(s);
}

//遍历删除集合元素
List<String> list = new ArrayList<>();
list.add("1");
list.add("2");
list.add("3");
list.add("4");
list.add("5");

//遍历元素的同时删除元素，使用iterator进行遍历
//如果在遍历集合元素的时候，使用了集合自带的方法修改集合的长度（比如add、remove等方法）
//那么可能会抛出java.util.ConcurrentModificationException异常
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    iterator.next();
    iterator.remove();
}

//将集合裁切到适合的大小，比如在调用clear()方法后，优化性能
list.trimToSize();
//提前扩容，而不用每次都动态扩容Copy，优化性能，也有构造方法
list1.ensureCapacity(list1.size() + 10000);
```

**LinkedList**

> `LinkedList` 是一个双向链表
>
> 实现了`List` 接口
>
> `API` 跟`ArrayList` 类似
>
> 元素中的节点内存地址不是连续的,添加一个元素相当于`new Node()`
>
> 会频繁的申请和销毁内存空间，但不会造成内存的浪费

* `LinkedList`：`size`、`first`、`last`
  * `size` ：集合的大小
  * `first` -> 指向第一个`节点（Node）`
  * `last` -> 指向最后一个`节点（Node）`
* `Node`：`item`、`next`、`prev`
  * `item` 存放元素
  * `next` -> 指向下一个`节点（Node）`, 最后一个`节点（Node）`指向`null`
  * `prev` -> 指向上一个`节点（Node）`，第一个节点（Node）指向`null`

**`LinkedList`、`ArrayList`**

*`效率对比`*

|                      | ArrayList  | LinkedList |
| :------------------: | :--------: | :--------: |
|       查找元素       |     高     |     低     |
|   添加元素（尾部）   |    相同    |    相同    |
|   添加元素（头部）   |     低     |     高     |
| 添加元素（任意位置） |    效率    |    相同    |
|       删除元素       | 与添加相同 | 与添加相同 |

**Set**

* 不可以存储重复的元素
  * 元素之间`equals` 不可能返回 `true`
* 不可以通过索引访问元素
* 不记录元素的添加顺序（`LinkedHashSet` 除外）

---

* `HashSet` 底层用了 `HashMap`
* `LinkedHashSet` 底层用了 `LinkedHashMap`
* `TreeSet` 底层用了`TreeMap`

```java
Set<String> set = new HashSet<>();
set.add("Jack");
set.add("Rose");
set.add("Kate");
set.add("Jack");
//[Kate, Rose, Jack]，HashSet不能有重复的元素，无序的（元素的哈希值、添加顺序影响了Set的顺序）
System.out.println(set);

//LinkedHashSet，可以去重并且保存了添加顺序
Set<String> set = new LinkedHashSet<>();
set.add("A");
set.add("B");
set.add("C");
set.add("A");
//[A,B,C]
System.out.println(set);
set.remove("B");
//[A,C]
System.out.println(set);

//TreeSet,必须具备可比较性,默认按照从小到大的顺序遍历元素
Set<String> set = new TreeSet<>();
set.add("B");
set.add("A");
set.add("C");
set.add("A");

//[A, B, C]
System.out.println(set);
//A
//B
//C
for (String s : set) {
    System.out.println(s);
}

//TreeSet 自定义比较
Set<String> set = new TreeSet<>((o1, o2) -> o2.compareTo(o1));
set.add("B");
set.add("A");
set.add("C");
set.add("A");
//[C, B, A]
System.out.println(set);
```

**Map**

* 不可以存储重复的 `key`，可以存储重复的`value`
* 不可以通过索引访问`key-value`
* 存储的是键值对（`key-value`），称作`映射`
* 无序的

```java
//HashMap（非线程安全）
Map<String, Integer> map = new HashMap<>();
map.put("Jack", 11);
map.put("Rose", 22);
map.put("Jim", 33);

//3
System.out.println(map.size());
//11
System.out.println(map.get("Jack"));
//{Rose=22, Jack=11, Jim=33}
System.out.println(map);

map.remove("Jack");
//{Rose=22, Jim=33}
System.out.println(map);

//--------------------------------------

//LinkedHashMap
Map<String, Integer> map = new LinkedHashMap<>();
map.put("Jack", 11);
map.put("Rose", 22);
map.put("Jim", 33);
//{Jack=11, Rose=22, Jim=33}
System.out.println(map);
map.remove("Jack");
//{Rose=22, Jim=33}
System.out.println(map);

Set<Entry<String, Integer>> entrys = map.entrySet();
for (Entry<String, Integer> entry : entrys) {
    //Rose:22
    //Jim:33
    System.out.println(entry.getKey() + ":" + entry.getValue());
}

//--------------------------------------

//TreeMap
//....与TreeSet相似
```

**`HashMap的遍历`**

```java
Map<String, Integer> map = new HashMap<>();
map.put("Jack", 11);
map.put("Rose", 22);
map.put("Jim", 33);

//entrySet方法
Set<Entry<String, Integer>> entrys = map.entrySet();
for (Entry<String, Integer> entry : entrys) {
    System.out.println(entry.getKey() + ":" + entry.getValue());
}

//Lambda表达式方式遍历
map.forEach((key, value) -> {
    System.out.println(key + ":" + value);
});

//只遍历value
Collection<Integer> values = map.values();
for (Integer value : values) {
    System.out.println(value);
}

//遍历所有key，通过key找到value
Set<String> keys =map.keySet();
for (String key : keys) {
    System.out.println(key + ":" + map.get(key));
}
```

**`java.util.Collections`**

> 是`Java` 提供的一个集合工具类，提供了很多`静态方法`

* `sort()`
* `max()`
* `min()`
* `addAll()`
* `reverse()`
* `reverseOrder)_`
* `shuffle()`
* `swap()`
* `fill()`
* `cop()`
* `replaceAll()`

ListIterator**

> `ListIterator` 继承自 `Iterator` ，在`Iterator` 的基础上增加了一些功能

* `boolean hasPrevious()`
* `E previpus()`
* `int nextIndex()`
* `int previousIndex()` 
* `void set(E e)`
* `void add(E e)`

##### 线程间通信

*3种方案*

- `Object.wait`
  - 释放`obj` 的内部锁，当前线程进入`WATTING` 或 `TIME_WAITING` 状态
- `Object.notify`
  - 随机唤醒1个因为`obj.wait` 进入`WATING` 或 `TIMED_WAITING` 状态的线程
- `Object.notifyAll`
  - 唤醒所有因为`obj.wait` 进入 `WAITING` 或 `TIMED_WAITING` 状态的线程

*调用条件*

> 若想再`线程A` 中成功调用 `obj.wait`、`obj.notify`、`obj.notifyAll` 方法
>
> `线程A` 必须要持有`obj` 的内部锁

```java
//生产者
public class Producer implements Runnable{
    private Drop drop;

    public Producer(Drop drop) {
        this.drop = drop;
    }

    @Override
    public void run() {
        String[] foods = {"beef", "bread", "apple", "cookie"};

        for (int i = 0; i < foods.length; i++) {
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {}
            //将food[i]传递给Consumer
            drop.add(foods[i]);
        }
        // 告诉Consumer，不会再生产任何东西了
        drop.add(null);
    }
}

//消费者
public class Consumer implements Runnable{
    private Drop drop;

    public Consumer(Drop drop) {
        this.drop = drop;
    }

    @Override
    public void run() {
        String food = null;
        while ((food = drop.get()) != null) {
            System.out.format("Food Received: %s%n", food);
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {}
        }
    }
}

public class Drop {
    private String food;
    //empty
    //true：Consumer等待Producer
    //false: Producer等待Consumer
    private boolean empty = true;

    /**
     * 在Consumer线程中被执行
     * @return
     */
    public synchronized String get() {
        while (empty) {
            try {
                //Consumer线程会释放drop对象的内部锁，并且进入WATTING状态
                wait();
            } catch (InterruptedException e) {}
        }
        //重置标志符
        empty = true;
        //唤醒Producer
        notifyAll();
        //返回food
        return food;
    }

    /**
     * 在Producer线程中被执行
     * @param food
     */
    public synchronized void add(String food) {
        while (!empty) {
            try {
                //
                wait();
            } catch (InterruptedException e) {e.printStackTrace();}
        }

        //重置标志符
        empty = false;
        //food生产完毕
        this.food = food;
        //唤醒Consumer
        notifyAll();
    }
}

//调用
Drop drop = new Drop();
new Thread(new Consumer(drop)).start();
new Thread(new Producer(drop)).start();
```

**`ReentrantLock（可重入锁）`**

> 类全名：`java.util.concurrent.locks.ReentrantLock`

* 具有跟同步语句，同步方法一样的一些基本功能，但功能更加强大

```java
public class Station implements Runnable{
    private int tickets = 100;
    private ReentrantLock lock = new ReentrantLock();

    public boolean saleTicket() {
        try {
            //加锁，如果被别的线程获得锁，那么其他线程进入等待
            //lock是重用锁，可以调多遍，同一个线程可以获得多遍锁，锁多少遍，也要解锁多少遍
            lock.lock();
            lock.lock();

            //如果没有被别的线程获得锁，才获得这把锁
            //否则线程会继续往下走
            //需要判断是否需要unlock()
            //boolean flag = lock.tryLock();

            //检查是否有线程拥有这把锁
            //lock.isLocked();

            /*
            synchronized也是可重用锁
            synchronized ("1") {
                synchronized ("1") {
                    System.out.println("123");
                }
            }
            */

            if (tickets < 1) return false;
            tickets--;
            String name = Thread.currentThread().getName();
            System.out.println(name + "卖了1张.剩" + tickets + "张");
            return tickets > 0;
        } finally {
            //在 return 之前执行
            lock.unlock();
            lock.unlock();
        }
    }

    @Override
    public void run() {
        while (saleTicket());
    }
}
```

**线程池（Thread Pool）**

> 线程对象占用大量内存，在大型应用程序中，频繁地创建和销毁线程对象会产生大量内存管理开销
>
> 使用线程池可以大程度地减少线程创建、销毁所带来的开销

*线程池由`工作线程（Worker Thread）` 组成*

* 普通线程：执行完一个任务后，生命周期就结束了
* 工作线程：可以执行多个任务（任务没来就一直等，任务来了就干活）
  * 先将任务添加至队列（Queue）中，再从队列中取出任务提交到线程池中

*常用的线程池类型是`固定线程池（Fixed Thread Pool）`*

* 具有固定数量的正在运行的线程

```java
ExecutorService pool = Executors.newFixedThreadPool(5);
pool.execute(() -> {
    System.out.println(Thread.currentThread());
});
pool.execute(() -> {
    System.out.println(Thread.currentThread());
});
pool.execute(() -> {
    System.out.println(Thread.currentThread());
});
pool.execute(() -> {
    System.out.println(Thread.currentThread());
});
pool.shutdown();
```

##### I/O

> 全称是`Input/Output Stream`，译为`输入/输出流`
>
> `I/O`流的常用类型在`java.io` 包中
>
> `字节流（Byte Streams）`、`字符流（Character Streams）`、`缓冲流（Buffered Streams）`、`数据流（Data Streams）`、`对象流（Object Streams）`

* `应用程序`将数据写入到`文件、设备`等，称为`输出流`
* `文件、设备` 等将数据写入到 `应用程序`，称为`输入流`

---

###### File

> 一个`File`对象就代表一个文件或者目录（文件夹）

```java
//UNIX、Linux、Mac系统
File file1 = new File("/Users/luoguankun/Documents/testio.rtf");

//Windows系统
// "/" 正斜杠	UNIX、Linux、Mac
File file1 = new File("F:/Files/1.txt");
// "/"
System.out.println(File.separator);		
File file1 = new File("F:" + File.separator +"Files" + File.separator + "1.txt");

// "\" 反斜杠  Windows
File file2 = new File("F:\\Files\\1.txt");

// 路径分隔符
// UNIX、Linux、Mac ： 冒号（:）
// Windows ： 分号（;）
System.out.println(File.pathSeparator);

//在Windows、Mac系统中，文件名和目录名不区分大小写
//在Unix、Linux中，文件名和目录名区分大小写
```

**常见操作方法**

```java
// /Users/luoguankun/Documents/testio.rtf
System.out.println(file1);
//获得文件名 testio.rtf
System.out.println(file1.getName());
//获得父路径 /Users/luoguankun/Documents
System.out.println(file1.getParent());
//获得父文件File对象 /Users/luoguankun/Documents
System.out.println(file1.getParentFile());
//获取路径 /Users/luoguankun/Documents/testio.rtf
System.out.println(file1.getPath());
//获取绝对路径 /Users/luoguankun/Documents/testio.rtf
System.out.println(file1.getAbsolutePath());
//获取绝对路径形式的文件 /Users/luoguankun/Documents/testio.rtf
System.out.println(file1.getAbsoluteFile());
//最后一次修改的时间（毫秒数）Sat Apr 25 12:58:19 CST 2020
System.out.println(new Date(file1.lastModified()));
//文件的大小（不支持目录）
System.out.println(file1.length());

boolean isAbsolute()
boolean exists()
boolean isDirectory()
boolean isFile()
boolean isHidden()
boolean canRead()
boolean canWrite()
```

###### 字符集（Character Set）

> 由字符组成的集合

*常见的字符集*

* `ASCII` : 128个字符（包括英文字母大小写、阿拉伯数字等）
* `ISO-8859-1`：支持欧洲的部分语言文字，在有些环境也叫`Latin-1`
* `GB2312`：支持中文（包括6763个汉字）
* `BIG5`：支持繁体中文（包括了13053个汉字）
* `GBK`：是对`GB2312`、`BIG5` 的扩充（包括了21003个汉字）,支持中日韩
* `GB18030`：是对`GBK` 的扩充（包括了27484个汉字）
* `Unicode`：包括了世界上所有的字符

*上面所有的字符集都已经包含了`ASCII`中的所有字符*

**字符编码**

> 每个字符集都有对应的字符编码，它决定了每个字符如何转成二进制存储在计算机中

* `ASCII`：单字节编码，编码范围是`0x00`~`0x7F` `（0~127）`
* `ISO-8859-1`：单字节编码，编码范围是`0x00`~`0xFF`
  * 其中`0x00`~`0x7F` 和`ASCII`一致，`0x80` ~ `0x9F` 是控制字符，`0xA0` ~ `0xFF`是文字符号
* `GB2312`、`BIG5`、`GBK`：采用双字节表示一个汉字
* `GB18030`：采用单字节、双字节、四字节表示一个字符
* `Unicode`：有`Unicode`、`UTF-8`、`UTF-16`、`UTF-32`等编码，最常用的是`UTF-8`编码
  * `UTF-8`采用单字节、双字节、三字节、四字节表示一个字符

```java
//编码
//[-27, -109, -120, -27, -109, -120, 108, 103, 107]
System.out.println(Arrays.toString("哈哈lgk".getBytes()));

//解码
//哈哈lgk
//编码和解码必须要相同的字符编码，否则会乱码
byte[] bytes = { -27, -109, -120, -27, -109, -120, 108, 103, 107 };
System.out.println(new String(bytes, "UTF-8"));
```

###### 字节流（Byte Streams）

* 一次只读写一个字节
* 最终都继承自`InputSteam`、`OutputStream`

*FileInputStream*

```java
InputStream fis = new FileInputStream("/Users/luoguankun/Documents/testio.txt");
//读取单个字节
int byte1 = fis.read();
System.out.println(byte1);
//读取指定长度的字节
byte[] bytes = new byte[100];
//返回文件内容实际长度
int len = fis.read(bytes);
//指定长度范围解码
String strs = new String(bytes, 0, len);
System.out.println(strs + "_");
fis.close();

//遍历全部字节并转换为字符串输出
try (
   	OutputStream fos = new FileOutputStream("");
) {
  	//一次读取1M
    byte [] bytes = new byte[1024];
    int len;
    while ((len = fis.read(bytes)) != -1) {
       System.out.println(new String(bytes, 0, len));
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
}
```

*FIleOutputStream*

```java
OutputStream fos = new FileOutputStream("/Users/luoguankun/Documents/testio.txt",true);
fos.write(75);
fos.write(76);
fos.write("哈哈lgk".getBytes());
fos.close();
```

###### 字符流（Character Streams）

* 一次只读写一个字符

* 最终都继承自`Reader`、`Writer`

* `FileReader`、`FileWriter` 只适合文本文件，比如`.txt`、`.java`等这类文件
* `FileWriter`默认是带缓冲

*FileReader*

```java
Reader reader = new FileReader("/Users/luoguankun/Downloads/1.txt");
//L
System.out.println((char)reader.read());
char[] chars = new char[1024];
int len = reader.read(chars);
//GK罗坤LGK罗冠坤
System.out.println(chars);
//10
System.out.println(len);
reader.close();

//但字符读取，间隔500毫秒打印
try (Reader reader = new FileReader("/Users/luoguankun/Downloads/1.txt")) {
    int c;
    while ((c = reader.read()) != -1) {
        System.out.print((char)c);
        Thread.sleep(500);
    }
}
```

*FileWriter*

```java
Writer writer = new FileWriter("/Users/luoguankun/Downloads/1.txt");
writer.write('L');
writer.write('G');
writer.write('K');
writer.write('罗');
writer.write('坤');
//写入字符串，本质是将字符串转换为字符数组
writer.write("LGK");
//写入字符数组
writer.write("罗冠坤".toCharArray());
writer.close();
```

###### 缓冲流（Buffered Streams）

* `字节流`、`字符流` 都是无缓冲的`I/O` 流，每个读写操作均由底层操作系统直接处理
  * 每个读写操作通常会出发磁盘访问，因此大量的读写操作，可能会使程序的效率大大降低
* 为了减少读写操作带来的开销，`Java`实现了缓冲的`I/O` 流

> 1. `缓冲输入流`: 从缓冲区读取数据，并且只有当缓冲区为空时才调用本地的输入API（Native Input API）
> 2. `缓冲输出流`: 将数据写入缓冲区，并且只有当缓冲区已满时才调用本地的输出API（Native Output API）

*下面表格中的4个缓冲流的默认缓冲区大小是`8192KB`，可以通过构造方法传参设置缓冲区的大小*

|            |      缓冲输入流       |       缓冲输出流       |
| :--------: | :-------------------: | :--------------------: |
| 缓冲字节流 | `BufferedInputStream` | `BufferedOutputStream` |
| 缓冲字符流 |   `BufferedReader`    |    `BufferedWriter`    |

> 缓冲流的常见使用方式：将`无缓冲流`传递给`缓冲流`的构造方法（将`无缓冲流`包装成`缓冲流`）
>
> 如果`无缓冲流` 比作是一个无装备的士兵，那么`缓冲流`就是一个强有力装备的士兵

```java
File file = new File("/Users/luoguankun/Downloads/1.txt");
InputStream is = new FileInputStream(file);
BufferedInputStream bis = new BufferedInputStream(is, 16384);
//只需要调用缓冲流的close方法，缓冲流的内部会调用非缓冲流的close方法
bis.close();

File file = new File("/Users/luoguankun/Downloads/1.txt");
Writer writer = new FileWriter(file);
BufferedWriter bw = new BufferedWriter(writer,16384);
bw.write("111");
bw.newLine();
bw.write("222");
bw.close();
```

*`flush`*

> 调用缓冲输出流的`flush` 方法，会强制调用本地的输出API，将缓冲区的数据真正写入到文件中
>
> 缓冲流的`close`方法，会自动调用`flush`方法

###### 文件编码转换

> 思路：
>
> 按照文件编码方式读取文件内容转换为字符串，然后将字符串按照目标文件编码格式进行写入

```java
//方式一
try (
        BufferedReader reader = new BufferedReader(
                new InputStreamReader(
                        new FileInputStream("/Users/luoguankun/Downloads/GBK.txt"),
                    "GBK"
            )
        );
        BufferedWriter writer = new BufferedWriter(
                new OutputStreamWriter(
                        new FileOutputStream("/Users/luoguankun/Downloads/UTF-8.txt"),
                                "UTF-8")
                )
        )
{
    char[] chars = new char[1024];
    int len;
    while ((len = reader.read(chars)) != -1) {
        writer.write(chars, 0, len);
    }
}
```

*从控制台读取输入内容*

```java
// 标准输入流（Standard inputStream）：控制台的输入，相当于在读取键盘的输入.System.in
// 标准输出流（Standard OutputStream）：控制台的输出，屏幕的输出.System.out
BufferedReader reader = new BufferedReader(
        new InputStreamReader(System.in));
String line;
while ((line = reader.readLine()) != null) {
    System.out.println(line);
}
reader.close();
```

**Scanner**

> `java.util.Scanner` 是一个可以使用正则表达式来解析基本类型和字符串的简单文本扫描器
>
> 它默认利用空白（`空格`\\`制表符`\\`行终止符` ）作为分隔符将输入分割成多个`token`（部分）

```java
Scanner(InputStream source);
Scanner(Readable source)
Scanner(File source);
Scanner(String source);

Scanner s = new Scanner("jack rose kate");
while (s.hasNext()) {
    System.out.println(s.next());
}
//jack
//rose
//kate
s.close();

//File
Scanner s = new Scanner(new File("/Users/luoguankun/Downloads/utf-8.txt"));

System.out.println(s.nextInt());
System.out.println(s.nextDouble());
System.out.println(s.next("[a-z]{2}\\d{2}");

//自定义分隔符
s.useDelimiter("\\s*\\d+\\s*");
                   
//标准输入
Scanner s = new Scanner(System.in);
System.out.println("请输入第1个整数：");
int n1 = s.nextInt();
System.out.println("请输入第2个整数：");
int n2 = s.nextInt();
System.out.format("%d + %d = %d%n", n1, n2, n1 + n2);
s.close();
```

**`PrintStream`、`PrintWriter`**

> 这两个类可以实现格式化输出
>
> `System.err`、`System.out` 是`PrintStream` 类型的实例，属于标准输出流，是字节流，内部使用字符流对象模拟字符流的许多功能
>
> `PrintWriter` 格式化的输出流到文件一般使用`PrintWriter`

*3个常用方法*

* `print()`
* `println()`
* `format()`

**`print`、`write` 的区别**

* `write(97)` 写入的字符是\``a`\`
* `print(97)` 写入的字符串是\"`97`\"

###### 数据流

> 支持基本数据类型、字符串类型的I/O操作

* `DataInputStream`
* `DataOutputStream`

```java
int x = 10;
int y = 2003;
double height = 1.75;
String str = "lgk";

DataOutputStream dos = new DataOutputStream(new FileOutputStream("/Users/luoguankun/Downloads/77.txt"));
dos.writeInt(x);
dos.writeInt(y);
dos.writeDouble(height);
dos.writeUTF(str);
dos.close();

//10
//2003
//1.75
//lgk
DataInputStream dis = new DataInputStream(new FileInputStream("/Users/luoguankun/Downloads/77.txt"));
System.out.println(dis.readInt());
System.out.println(dis.readInt());
System.out.println(dis.readDouble());
System.out.println(dis.readUTF());
dis.close();
```

###### 对象流

> 支持引用类型的I/O操作
>
> 只有实现了`java.io.Serializable` 接口的类才能使用对象流，否则抛出`NotSerializableException` 异常
>
> ``java.io.Serializable`` 是一个`标记接口`，不要求实现任何方法

* `ObjectInputStream`
* `ObjectOutputStream`

```java
Person p = new Person("Jack", 20, 99);
ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("/Users/luoguankun/Downloads/person.txt"));
oos.writeObject(p);
oos.close();

//Person{name='Jack', age=20, level=99}
ObjectInputStream ois = new ObjectInputStream(new FileInputStream("/Users/luoguankun/Downloads/person.txt"));
//每一次readObject()按写入顺序读取一次对象
Person p = (Person) ois.readObject();
ois.close();
System.out.println(p);
```

**序列化、反序列化**

> 要求对象实现`java.io.Serializable`接口

* 序列化
  * 将`对象` 转换为 `二进制`
  * 利用`ObjectOutputStream` 实现对象序列化
* 反序列化
  * 将`二进制` 还原为 `对象`
  * 利用`ObjectInputStream` 实现对象反序列化

**`transient`关键字** 

> 被`transient` 修饰的实例变量不会被序列化

```java
private transient int age;
```

**`serialVersionUID`**

> 每一个可序列化类都有一个`serialVersionUID` ，相当于类的版本号
>
> 默认情况下会根据类的详细信息计算出`serialVersionUID` 的值，根据编译器实现的不同可能千差万别
>
> 一旦类的信息发生修改，`serialVersionUID` 的值就会发生改变，建议每一个可序列化的类都自定义`serialVersionUID` ，不要使用默认值

```java
//如果一个类在序列化到文件后，对类的成员做了改动，比如删掉某个成员变量，那么反序列化时，会抛出InvalidClassException异常
Exception in thread "main" java.io.InvalidClassException: com.luo.Person; local class incompatible: stream classdesc serialVersionUID = 6957892401830891112, local class serialVersionUID = -4167166217683851109
at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
at java.io.ObjectInputStream.readObject(ObjectInputStream.java:431)
at com.luo.Main.main(Main.java:17)
  
//自定义serialVersionUID		必须是static final long  建议private
private static final long serialVersionUID = -4167166217683851109L;
```

