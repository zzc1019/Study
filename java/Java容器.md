# Java容器

## 容器的概念

如果不知道程序运行时，会需要多少对象，或者需要更复杂的方式存储对象可以使用Java集合框架。

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210531235604324.png" alt="image-20210531235604324" style="zoom:67%;" />

接口4个 collection map list set

子类实现6个



## 容器API 



## Collection 接口

集合的基本操作：增加，删除，判断，取出

Colletcion存放的是单一值

特点：可以存放不同类型的数据，而数组只能存放固定类型的数据

​	API方法：

​		add：要求必须传入object对象，因此传入基本数据类型的时候，进行了自动装箱。

### List与Set的区别

Collection接口存储一组==不唯一==的，==无序==的的对象。

List接口存储一组==不唯一==的==有序==的对象。

Set接口中存储一组==唯一==，==无序==的对象。

Map接口存储一组键值堆对象，提供key到value到映射

## List接口



### List接口实现类

List特点：有序 不唯一

### ArrayList

ArrayList实现了长度可变的**数组**，在内存中分配连续的空间。

- 优点：遍历元素和随机访问元素的效率比较高
- 缺点：添加和删除需要大量移动元素，效率较低。

### LinkedList

LinkedList 采用**链表**存储方式

- 优点：插入、删除元素时，效率比较高

- 缺点：遍历和随机访问元素效率比较低

  单向链表

  | data | next |
  | ---- | ---- |

  双向链表

  | prev | data | next |
  | ---- | ---- | ---- |

  

### vector

#### vector 特点

1. Vector也是List接口的一个子类实现
2. Vector和ArrayList一样，底层都是使用数组进行实现的
3. 区别：
   - ArrayList是线程不安全的，效率高；vector是安全的，效率低（synchronized）
   - ArrayList扩容的时候，是扩容1.5倍；Verctor扩容是原来的两倍

## Set接口

### Set接口实现类

#### Set特点：无序 唯一

存入和取出的顺序不一定一致，set接口不存在get()方法。

Set set = new HashSet();

TreeSet ts = new TreeSet();

### HashSet

HashSet 采用Hashtable存储结构

- 优点：添加速度快，查询速度快，删除速度快
- 缺点：无序

LinkedHashSet

采用哈希表存储结构，同时使用链表维护次序

有序（添加顺序）

### TreeSet

采用二叉树（红黑树）的存储结构

- 优点：有序（排序后的升序）查询速度比List快
- 查询速度比HashSet慢

TreeSet 本身数据结构调用了 TreeMap 红黑树, 所以添加进的add() 需要能比较大小的数字 而不是各个类型都有

```java
HashSet hs = new HashSet();
hs.add(new Person("张三",20));
hs.add(new Person("李四",22));
hs.add(new Person("王五",23));
hs.add(new Person("李四",22));
// hashset中添加了重复的对象，不符合实际情况
// 在Person中重写 equals & hashcode
```

设置元素的时候，如果是自定义对象，会查找对象中的equals和hashcode方法，如果没有，比较的是地址，而不是属性值。

##### HashSet如何保证元素的唯一性

通过equals和hashcode两个方法。

如果元素的hashcode相同，会调用equals比较

如果hashcode不同，不对调用equals比较

#### AVL树 

能够自动平衡，保证每个节点的左右子节点高度之差绝对值为1。通过旋转保持平衡

#### 红黑树

[红黑树练习](https://www.cs.usfca.edu/~galles/visualization/RedBlack.html)

自平衡二叉查找树和AVL树类似，通过旋转和变色保持平衡

- 节点都是红色和黑色的
- 根节点是黑色
- 每个叶节点是黑色的
- 每个红色节点的两个子节点都是黑色（从每个叶子到根的所有路径上不能有两个连续的红色节点）
- 从任意节点到其每个叶子到所有路径都包含相同数目的黑色节点
- 最长路径不超贵最短路径的2倍



<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210601122413161.png" alt="image-20210601122413161" style="zoom:50%;" />

## Iterator 接口

- 所有实现了Collection接口的容器类都有一个iterator方法，用以返回一个实现了Iterator 接口的对象。

- Iterator对象称作迭代器，用以方便的实现对容器内元素的遍历操作。

- 方法如下：

  ```java
  boolean hasNext();  //判断是否有元素没被遍历
  Object next();   //返回游标当前位置的元素，并将游标移动到下一个位置
  void remove();   //删除游标左侧的元素，
  ```

  

使用方法

```java
//遍历1
for(int i=0;i<list.size();i++)
{
  System.out.println(list.get(i));
}
//遍历2
Iterator it = list.iterator();
while(it.hasnext()){
  System.out.println(it.next());
}
//遍历3
//增强for循环
for(Object i:list)
{
  System.out.println(i);
}

```

保持同步

```java
ListIterator it= list.listIterator();
//it.hasPrevious(); 向前
while(it.hasNext()){
  Object o = it.next();
  if(o.equals(1)){
    it.remove();
  }
  System.out.println(o);
}
// 1 2 3 4
for(Object i:list)
{
  System.out.println(i);
}
//2 3 4
```



## Iterable 接口

所有类的集合都默认实现了Iterable的接口，意味着具备了增强for循环的能力

包含方法如下

```java
foreach();
iterator()
```



Iterable中的方法iterator(); 返回类型是Iterator接口



## Comparable 接口

### 比较器分类

#### 内部比较器

通过实现comparable接口进行比较

#### 外部比较器

定义在当前类中，implements Comparator <Person> 

```java
public class SetDemo implements Comparator<Person>{
  
  @override
  public int compare(Person o1,Person o2){
    
    if (o1.getAge()>o2.getAge()){return -1;}
    else if (o1.getAge()<o2.getAge()){return 1;}
    else return 0 ;
  }
  
  TreeSet ts = new TreeSet(new SetDemo());
} 
```

外部比较器可以定义为工具类，如果被比较的对象规则一致的话，可以复用

同时存在外部比较类和内部比较类，优先外部比较类 

## 泛型

代码中经常出现的<E> 代表element。

```java
List list = new ArrayList();
list.add(1);
list.add(true);
list.add("abc");
list.add(new Person());
```

当做一些集合的统一操作的时候，需要保证集合的类型是统一的，此时需要泛型来进行限制

给集合中元素设置相同的类型就是泛型的基本需求

```java
List<String> list = new ArrayList<String>(); //该集合中只能存放String类型
```

打印方式

方式1

```java
for(int i=0;i<list.size();i++){
  System.out.println(list.get(i));
}
```

方式2

```java
for(String iter:list){
  System.out.println(iter);
}
```



### 优点：

1. 数据安全
2. 获取数据的时候效率比较高

### 泛型的高阶应用

#### 泛型类

```java
public class Demo1<A>{
  private A a;
  private int id;
  //setter & getter
  public void show(){
    System.out.println("id:"+id+"A:"+a);
  }
}
```

使用:

传入任意类型在A的位置，即可定义私有变量的类型。

```java
Demo1<String> d1 = new Demo1<String>();
d1.setA("hello world");
d1.setId(1);

Demo1<Integer> d2 = new Demo1<Integer>();
d2.setA(34);
d2.setId(2);

Demo1<Person> d3 = new Demo1<Person>();
d3.setA(new Person("aaa",123));
d3.setId(3);
```

常见类泛型 <E>element <K>key <V>value

#### 泛型接口

```java
public interface Demo2<B>{
  B b;   //不能这么定义会报错
  //---------------------------------------------
  public B test1();
  Public void test2(B b);
}
//1.泛型接口的子类可以不设定是否是什么具体的类型，可以在实现子类对象的时候设定。
public class FanInterSub<C> implements Demo2<C>{
  @override
  public C test1(){
    return;
  }
  @override
  public void test2(C c){
    System.out.println(o);
  }   
}
public class Test{
  
  Demo2<String> d = new FanInterSub<String>(); 
  
}
//2. 子类实现泛型接口的时候，只在实现父类接口的时候指定父类泛型即可，测试方法中的泛型必须和子类一致
public class FanInterSub implements Demo2<Integer>{
  @override
  public C test1(){
    return;
  }
  @override
  public void test2(C c){
    System.out.println(o);
  }   
}
public class Test{
  
  Demo2 d2 = new FanInterSub<Integer>(); 
  
}
```



#### 泛型方法

在定义方法的时候，指定方法的返回值和参数是泛值

```java
public <Q> void show(Q q){//定义在返回类型前面
  System.out.println(q);
}
```

#### 泛型的上限

```java
ArrayList(Collection<? extends E>)
```

如果父类使用了，子类都可以直接使用

#### 泛型的下限

如果子类确定了，子类所有父类都可以直接传递参数使用。

## Map接口

特点：key-value映射

- HashMap

  Key无序，唯一（Set）

  Value 无序，不唯一（Collection）

- LinkedHashMap

  有序的HashMap

- TreeMap

  有序 速度没有Hash快

### HashMap

数组+链表+红黑树（JDK1.8）

数组+链表（JDK1.8）

![这里写图片描述](https://img-blog.csdn.net/20180902104845165?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTI2MzYzMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

方法

```java
isEmpty(); 判断是否为空
size();
clear();
containsKey();//判断key是否存在
containsValue();  //判断value是否存在
get(key);  //根据key获得value
remove(key); //删除一组

//遍历操作
Set<String> keys=map.keySet(); //获取keySet集合
for(String key:keys)
{
  System.out.println(key+"="+map.get(key))
}
//只能获取value不能获取key
Collection<Integer> values=map.values();
for(Iterator it:values)
{
  System.out.println(it);
}
//迭代器
Set<String> keys2=map.keySet()；
Iterator<String> it=keys.iterator();
while(it.hasNext())
{
  System.out.println();
}
//Map.entry
//表示的是一组K-V组合的映射关系，key和value成组出现
Set<Map.Entry<String, Integer>> entries = map.entrySet();
Iterator<Map.Entry<String, Integer>> iterator1 = entries.iterator();
while(iterator1.hasNext()){
            Map.Entry<String, Integer> next = iterator1.next();
            System.out.println(next.getKey()+"--"+next.getValue());
        }
```

#### 特点

key和value都允许为null

每一个entry都包含如下



| key | value | hash | next |
| ---- | :---: | ---- | ---- |

#### HashMap容量初始值为2的n次幂的原因

1. 方便进行与运算操作，hashmap中 取模操作采用是与运算 hash=value&(length-1)

2. 扩容前后只需要比较，新增加一位的大小

   原来是16 2^4=16 比较后四位 现在新增加一倍 比较第五位

   <img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210603214308173.png" alt="image-20210603214308173" style="zoom:50%;" />

   

#### JDK1.7 HashMap特点

数组+链表

1. 默认初始容量
2. 加载因子0.75
3. put操作
   - 设置值，计算hash
   - 扩容操作
   - 数据迁移操作

#### JDK1.8 HashMap特点



#### HashMap和HashTable的区别

1. HashMap线程不安全，效率高；HashTable线程安全，效率低
2. HashMap中key和value都可以为空，HashTable中key和value都不可以为空
3. HashTable中加了synchronized 其余的两者几乎相同
4. HashMap初始容量为16，HashTable初始容量为11

### LinkedHashMap

采用链表



### TreeMap

红黑树



## Collections工具类

Collections和Collection不同，前者是集合的操作类，后者是集合接口。

Collections 中常用的静态方法

```java
addAll(lst,"c","d","e");
Collections.sort(list);
Collections.sort(list,new Comparator String(){
  
  ……
  
})  //重写
binarySearch();//二分查找需要先进行排序操作，如果没有排序是查找不到的
Collections.sort(list);
System.out.println(Collections.binarySearch(list,"c"));

Collections.fill(list,"value"); //将所有的都覆盖为“value”

Collections.shuffle(list);  //随机排序

Collections.reverse(); //逆序
```

sort 自定义的sort

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210604111643215.png" alt="image-20210604111643215" style="zoom:50%;" />

Collections.sort(list);  //按字母排序



## Arrays

提供了数据操作的工具类，包含很多方法

​	集合和数组之间的转换

```java
int[] array = new int[]{1,2,3,4,5};
List ints=Arrays.asList(array);

Object[] Obj = ints.toArray();
```



## 总结

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210604110044011.png" alt="image-20210604110044011" style="zoom:50%;" />

<img src="/Users/zhichaozhang/Library/Application Support/typora-user-images/image-20210604110132435.png" alt="image-20210604110132435" style="zoom:50%;" />



## 面试题

### 集合和数组的比较

数组不是面向对象的，存在明显的缺陷，集合弥补了数组的一些缺点，比数组更灵活更实用，可以大大提高效率。

- 数组可以存放基本数据类型和对象，而集合只能存放对象
- 数组容易固定无法动态改变，集合类容易动态改变
- 数组无法判定实际存在多少元素，length只是容量，而集合的size() 返回的是元素个数
- 集合有多种实现方式和不同适应场合，数组仅以顺序表达
- 集合以类的方式存在，具有继承封装多态特性

### Collection和Collections的联系和区别

Collection是一个集合接口，有两个子接口List和Set

Collections是一个操作合集类，提供了一系列静态方法，实现对集合的搜索排序和线程安全化。

### ArrayList和LinkedList的联系和区别

ArrayList 遍历 随机访问元素效率高

LinkedList 插入删除元素效率高

### Vector和ArrayList的联系和区别

1. 联系

   实现原理相同，功能相同，都是长度可变的数组

2. 区别

   - Vector是早期JDK接口，ArrayList是代替Vector的新接口

   - Vector线程安全，ArrayList不安全

   - 扩容Vector增加两倍，ArrayList增加1.5倍





### HashMap和HashTable的联系和区别

1. 联系

   实现原理相同，功能相同，底层都是哈希表结构，查询速度快，在很多情况下可以互用

2. 区别

   - HashTable是早期JDK提供的接口，HashMap是新版的JDK提供的接口
   - HashTable是继承Dictionary类，HashMap继承Map接口
   - HashTable是线程安全，HashMap是线程不安全
   - HashTable不允许null，HashMap允许null





















