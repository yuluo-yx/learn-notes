# java 集合的常用方法

## List

链表的优点和缺点：

- 优点：因为链表上的元素在空间存储上内存地址不连续，所以随机增删元素的时候不会有大量元素位移，因此随机增删效率高。**在以后的开发中如果有大量的增删集合中的元素时，建议使用LinkedList**
- 缺点：不能通过数学表达式的计算查找元素的内存地址，每一次查找都是从头开始遍历，所以LinkedList的查找效率比较低。

### ArrayList

把检索发挥到极致 （末尾添加元素的效率很高，底层数组发挥的作用）

扩容特点：10 --> 15 --> 15 * 1.5  （1.5倍扩容）

ArrayList是非线程安全的，可以通过`java.util.Collections的synchronized()`方法转换为线程安全的。

### LinkedList

把随机增删发挥到极致 （双向链表）

> 添加元素都是往集合末尾添加，所以ArrayList比LinkedList用的多

## Vector

- 底层也是数组；初始化容量为10，以两倍的速度进行扩容（double）
- vector中的所有方法都有synchronized关键字修饰，是线程安全的

## Set

### HashSet

由哈希表（实际为`HashMap`实例）支持

特点：**无序不可重复**，加入HashSet的元素实际上是存储到了HashMap的key部分

### TreeSet

特点：**无序（指的是输进去的顺序和输出的顺序不同并且没有下标）不可重复，存储的元素可以按照大小自动排序，可排序集合，放到TreeSet中的元素实际上是放到了TreeMap集合的key部分了。**

底层结构是一个二叉树

可以按照字典顺序对String进行排序 （升序）

### LinkedHashSet

哈希表和链表实现了`Set`接口，具有可预测的迭代次序

## Map

`java.util.Map`

- Map和collection没有继承关系
- Map集合以Key和Value的方式存储数据：键值对
- map中的key起主导作用，value是key的一个附属品

### Map接口中的常用方法

V put(K Key, V Value)

`向Map集合中中添加键值对`

V get(Object key)

`通过key获取value`

void clear()

`清空map集合`

boolean containsKey(Object key)

`判断map集合中是否包含某个key`

boolean containsValue(Object value)

`判断map集合中是否包含某个value`

boolean isEmpty()

`判断map集合中的元素个数是否为0`

Set(K) keySet()

`获取map集合中所有的key，（所有的键是一个Set集合）`

int size()

`获取map集合中键值对的个数`

V remove(Object key)

`通过key删除键值对`

Collection<V> values()

`获取map集合中的所有value，返回一个Collection`

Set<Map.Entry<K,V>> entrySet()

**Map集合通过entrySet转换为的set集合的类型为Map.Entry<K, V>（为Map类中的静态内部类）**

`将map集合转换为Set集合`

### Map集合的遍历方式

- 第一种

```java
Map<Integer, String> map = new HashMap<>();

map.put(1, "yuluo");
map.put(2, "huakai");
map.put(3, "zhangsan");

// map集合的遍历
/*
第一种方式：获取所有的key，通过遍历key来遍历value
 */
Set<Integer> keys = map.keySet();

// 通过迭代器
Iterator<Integer> it = keys.iterator();
while (it.hasNext()) {
    Integer key = it.next();
    String value = map.get(key);
    System.out.println(key + "=" + value);
}

// foreach
for (Integer key : keys) {
    System.out.println(key + "=" + map.get(key));
}
```

- 第二种

```java
// 第二种:Set<Map.Entry<Integer, String>>
Set<Map.Entry<Integer, String>> set = map.entrySet();

// 利用迭代器
Iterator<Map.Entry<Integer, String>> it2 = set.iterator();
while (it2.hasNext()) {
    Map.Entry<Integer, String> node = it2.next();
    Integer key = node.getKey();
    String value = node.getValue();
    System.out.println(key + "=" + value);
}
// foreach  适合大数据量的处理
for (Map.Entry<Integer, String> node : set) {
    System.out.println(node.getKey() + "--->" + node.getValue());
}
```

## 哈希表数据结构

哈希表：数组和单向链表的结合体

- 数组在查询方面效率很高，随机增删效率低
- 单向链表在随机增删方面效率高，在查询方面效率很低
- 哈希表融合了两种数据结构的优点。

### HashMap

- HashMap集合底层是哈希表/散列表的数据结构，非线程安全的。
- key的部分特点
  - 无序，不可重复
  - 不可重复使用equals方法保证，如果key重复了，value会被覆盖掉。
- HashMap集合的默认容量是16.默认加载因子是0.75，（默认加载因子：当HashMap集合底层数组的容量达到了75%，数组开始扩容）
- HashMap集合初始化容量必须是2的倍数，官方推荐
  - 提高存取效率，**达到散列均匀**
- java8对HashMap集合的改进
  - 如果单向链表超过8个，会将单向链表变成红黑树数据结构
  - 当红黑树上的结点小于6个，会重新变成单向链表
  - **为了提高检索效率**

### HashMap和HashTable

HashMap是非线程安全的，允许key和value的值为null（但是key的值只能有一个为nul，key重复value会被覆盖）。

HashTable的初始化容量为11，默认加载因子为0.75f，（扩容是原容量\*2 + 1）是线程安全的，不允许key和value的值为null.

properties继承了HashTable类，是线程安全的。

`Map map = new HashTable();`

## Collection工具类

sort(List list)

sort(list list, Comparable Object)

**对list集合中的元素进行排序时，要确保list集合中的元素实现了Comparable接口**

synchronizedList()

通过构造方法实现set和list之间的转换

`List<String> myList = new ArrayList<>(set);`

## 锁和阻塞机制

- 悲观锁：

  - synchronized 的底层是通过队列的形式实现的，可以自己加锁和释放锁
  - ReentrantLock 需要自己添加
    - `ReetrantLock lock = new Reentrantlock();`
    - `加锁：lock.lock()`
    - `解锁：lock.unLock()`

- 乐观锁

  - cas + 自旋

  

  

  

  

