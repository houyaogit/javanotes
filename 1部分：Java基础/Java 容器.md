# 	一 概览

## 容器



<img src="https://gitee.com/houyao123/my-resource/raw/master/img/1.png" style="zoom:70%;" />

## Collection

### List

Java 的 List 是非常常用的数据类型。List 是有序的 Collection。Java List 一共三个实现类：
分别是 ArrayList、Vector 和 LinkedList。

* ArrayList：ArrayList 是最常用的 List 实现类，内部是通过数组实现的，它允许对元素进行快速随机访问。数
  组的缺点是每个元素之间不能有间隔，当数组大小不满足时需要增加存储能力，就要将已经有数
  组的数据复制到新的存储空间中。当从 ArrayList 的中间位置插入或者删除元素时，需要对数组进
  行复制、移动、代价比较高。因此，它适合随机查找和遍历，不适合插入和删除。
* Vector：Vector 与 ArrayList 一样，也是通过数组实现的，不同的是它支持线程的同步，即某一时刻只有一个线程能够写 Vector，避免多线程同时写而引起的不一致性，但实现同步需要很高的花费，因此，
  访问它比访问 ArrayList 慢。
* LinkList：LinkedList 是用链表结构存储数据的，很适合数据的动态插入和删除，随机访问和遍历速度比较慢。另外，他还提供了 List 接口中没有定义的方法，专门用于操作表头和表尾元素，可以当作**堆**
  **栈、队列和双向队列**使用。

### Set 

Set 注重独一无二的性质,该体系集合用于存储无序(存入和取出的顺序不一定相同)元素，**值不能重**
**复**。对象的相等性本质是对象 hashCode 值（java 是依据对象的内存地址计算出的此序号）判断
的，如果想要让两个不同的对象视为相等的，就必须覆盖 Object 的 hashCode 方法和 equals 方
法。

* HashSet：基于哈希表实现，存入数据是按照哈希值，所以并不是按照存入的顺序排序，为保证存入的唯一性，存入元素哈希值相同时，会使用 equals 方法比较，如果比较出不同就放入同一个哈希桶里。
* TreeSet：基于红黑树实现，支持有序性操作，每增加一个对象都会进行排序，将对象插入的二叉树指定的位置。
* LinkHashSet（ HashSet+LinkedHashMap ）：继承于 HashSet、又是基于 LinkedHashMap 来实现的， 具有 HashSet 的查找效率 。

### Queue

## Map

1. HashMap： 根据键的 hashCode 值存储数据，大多数情况下可以直接定位到它的值，因而具有很快
   的访问速度，但遍历顺序却是不确定的。 HashMap 非线程安全，底层实现为数组+链表+红黑树。
2. ConcurrentHashMap：**支持并发操作的HashMap**，在JDK1.7和1.8实现线程安全的方式不同。
3. HashTable：Hashtable 是遗留类，不建议使用，很多映射的常用功能与 HashMap 类似，通过 synchronized 把整个（table）表锁住来实现线程安全的，效率十分低下。
4. TreeMap： 红黑树实现，可排序，需要对一个有序的key集合进行遍历时建议使用。
5. LinkHashMap： 是 HashMap 的一个子类， 增加了一条双向链表， **从而可以保存记录的插入顺序**，在用 Iterator 遍历 LinkedHashMap 时，先得到的记录肯定是先插入的，也可以在构造时带参数，按照访问次序排序。





# 二 源码分析

## ArrayList

### 1.定义

``` java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

 ArrayList 是基于数组实现的，所以支持快速随机访问。 继承了AbstractList，实现了List。它是一个数组队列，提供了相关的添加、删除、修改、遍历等功能。 

**RandomAccess** 接口标识着该类支持快速随机访问（只是一个定义了类型的接口，无作用）。  **Cloneable 接口**，即覆盖了函数 clone()，**能被克隆**。  **java.io.Serializable 接口**，这意味着ArrayList**支持序列化**，**能通过序列化去传输**。 

 数组的默认大小为 10。 

### 2.扩容机制

**核心方法：**

```java
private void grow(int minCapacity) {
    // overflow-conscious code
    int oldCapacity = elementData.length;
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

 整个流程就是对旧数组位移运算得到新数组，然后把旧数组整个复制到新数组上，操作代价很高，新容量的大小为 `oldCapacity + (oldCapacity >> 1)`，也就是旧容量的 1.5 倍 。

  补充: 

**移位运算符简介**：移位运算符就是在二进制的基础上对数字进行平移。按照平移的方向和填充数字的规则分为三种:<<(左移)、>>(带符号右移)和>>>(无符号右移)。**作用**：**对于大数据的2进制运算,位移运算符比那些普通运算符的运算要快很多,因为程序仅仅移动一下而已,不去计算,这样提高了效率,节省了资源**  。

### 3.添加和删除

**在末尾添加元素：**

```java
public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }
```

这里看到ArrayList在末尾添加元素的实质就相当于为数组赋值。

**在指定位置添加元素：**

```java
public void add(int index, E element) {    
    rangeCheckForAdd(index);   	
    ensureCapacityInternal(size + 1);  // Increments modCount!! 
    System.arraycopy(elementData, index, elementData, index + 1,
                     size - index);    
    elementData[index] = element;    size++;}
```

 让数组自己复制自己实现让index开始之后的所有成员后移一个位置。

**删除指定元素：**

```java
public E remove(int index) {
    rangeCheck(index);
    modCount++;
    E oldValue = elementData(index);
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index, numMoved);
    elementData[--size] = null; // clear to let GC do its work
    return oldValue;
}
```

流程是把需要删除的元素右边的元素向左移动一位，覆盖了需要删除的元素。调用了arraycopy，所以操作代价也很高。
## CopyOnWriteArrayList

concurrent 并发包下的类，是ArrayList的线程安全解决方案， 通过ReentrantLock获取对象锁的方式来实现线程安全。  

**读写分离的特点**

读：

```java
@SuppressWarnings("unchecked")
private E get(Object[] a, int index) {
    return (E) a[index];
}
```

写：

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}

final void setArray(Object[] a) {
    array = a;
}
```

写的操作需要加锁，防止并发写入导致数据丢失，不直接操作原数组，先copy一个数组进行操作，写完后setArray方法把新的复制数组赋值给旧数组。

 CopyOnWriteArrayList 在写操作的同时允许读操作，大大提高了读操作的性能，因此很适合读多写少的应用场景。 

缺点：

* 内存占用：在写操作时需要复制一个新的数组，使得内存占用为原来的两倍左右； 

* 数据不一致：读操作不能读取实时性的数据，因为部分写操作的数据还未同步到读数组中。 

所以 CopyOnWriteArrayList 不适合内存敏感以及对实时性要求很高的场景。 

## LinkedList

#### 1. 概览

内部私有类Node：

```java
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
}
```

定义：

```java
transient Node<E> first;
transient Node<E> last;
```

 补充：**transient**关键字标记的成员变量不参与序列化过程。 

综上可看出，LinkedList是由双向列表实现，使用Node存储节点信息，每个节点都有前节点（next），本节点（item），后节点（prev）。

#### 2.与 ArrayList 的比较

* ArrayList 基于动态数组实现，LinkedList 基于双向链表实现。 
* ArrayList插入删除元素时分两种情况：①把元素添加到末尾所需的时间复杂度是O(1)，②在指定位置添加删除元素时就需要移动整个数组，操作代价就比较大了。LinkedList 对于添加删除方法只需要断链然后更改指向，所需的消耗就很小了。
* ArrayList 支持高效的随机元素访问 而LinkedList 不支持。
* ArrayList的空 间浪费主要体现在在list列表的结尾会预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗比ArrayList更多的空间（因为要存放直接后继和直接前驱以及数据）。 

## HashMap

#### 1.整体原理分析 

 HashMap类中有一个非常重要的字段，就是  Node[] table，即哈希桶数组 

```java
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
```



 Node是HashMap的一个内部类，实现了Map.Entry接口，本质是就是一个映射(键值对)。 

hash:hashcode经过扰动函数得到的值， 然后通过 `(n - 1) & hash` 判断当前元素存放的位置，如果当前

位置存在hash和key值不相同的元素就使用拉链法解决冲突。

**“拉链法”** 就是：将链表和数组相结合。也就是说创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可。 

Node<K,V> next：next就是用于链表的指向。

所谓**扰动函数**指的就是 HashMap 的 hash 方法。使用 hash 方法也就是扰动函数是为了防止一些实现比较差的 hashCode() 方法 换句话说使用扰动函数之后可以减少碰撞。 

底层存储结构：

![2.png](https://gitee.com/houyao123/my-resource/raw/master/img/2.png)


#### 2.put方法分析


map.put("a","b")的整个流程：

 	1. 先判断散列表是否没有初始化或者为空，如果是就扩容
 	2. 根据键值 key 计算 hash 值，得到要插入的数组索引 
 	3. 判断要插入的那个数组是否为空：
 	  	1. 如果为空直接插入。
 	  	2. 如果不为空，判断 key 的值是否是重复（用 equals 方法）：
 	       	1. 如果是就直接覆盖
 	       	2. 如果不重复就再判断此节点是否已经是红黑树节点：
 	            	1. 如果是红黑树节点就把新增节点放入树中
 	            	2. 如果不是，就开始遍历链表：
 	                 	1. 循环判断直到链表最底部，到达底部就插入节点，然后判断是否大于链表长度是否大于8：
 	                      1. 如果大于8就转换为红黑树
 	                      2. 如果不大于8就继续下一步
 	                	2. 到底部之前发现有重复的值，就覆盖。
 	4. 判断是否需要扩容，如果需要就扩容。

下面上源码：

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    //tab: 引用当前hashMap的散列表
	//p: 表示当前散列表的元素
	//n: 表示散列表数组的长度
	//i: 表示路由寻址结果
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // table未初始化或者长度为0，进行扩容（采用了延时初始化，第一次put才会初始化散列表。）
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // 寻址找到的桶位为null
    //(n - 1) & hash 确定元素存放在哪个桶中，桶为空，新生成结点放入桶中(此时，这个结点是放在数组中)
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    // 寻址找到的桶位已经存在元素
    else {
        Node<K,V> e; K k;
        // 比较桶中第一个元素(数组中的结点)的hash值相等，key相等，也就是判断是否是重复的值。
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
                // 完全一致
            	//e:找到的一个与当前要插入的元素一直的元素
                e = p;
        // hash值不相等，即key不相等；且为红黑树结点
        else if (p instanceof TreeNode)
            // 放入树中
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        // 为链表结点
        else {
            // 在链表最末插入结点
            for (int binCount = 0; ; ++binCount) {
                // 到达链表的尾部
                if ((e = p.next) == null) {
                    // 在尾部插入新结点
                    p.next = newNode(hash, key, value, null);
                    // 结点数量达到阈值，转化为红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                // 判断链表中结点的key值与插入的元素的key值是否相等
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    // 相等，跳出循环
                    break;
                // 用于遍历桶中的链表，与前面的e = p.next组合，可以遍历链表
                p = e;
            }
        }
        // 表示在桶中找到key值、hash值与插入元素相等的结点
        if (e != null) { 
            // 记录e的value
            V oldValue = e.value;
            // onlyIfAbsent为false或者旧值为null
            if (!onlyIfAbsent || oldValue == null)
                //替换
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    // 结构性修改
    ++modCount;
    // 实际大小大于阈值则扩容
    if (++size > threshold)
        resize();
    // 插入后回调
    afterNodeInsertion(evict);
    return null;
} 
```

综上put的方法流程图为：

![put方法流程图.png](https://gitee.com/houyao123/my-resource/raw/master/img/put%E6%96%B9%E6%B3%95%E6%B5%81%E7%A8%8B%E5%9B%BE.png)




tip： JDK1.7之前的put方法和现在流程不同的地方就是采用头插法插入元素。

#### 3.扩容（resize方法）

扩容会伴随着一次重新hash分配，并且会遍历hash表中所有的元素，是非常耗时的。在编写程序中，要尽量避免resize。 源码在下面，仔细阅读即可理解此方法。

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    //散列表已经被初始化了，是一次正常扩容
    if (oldCap > 0) {
        // 超过最大值就不再扩充了
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        // 没超过最大值，就扩充为原来的2倍
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY && oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold：翻倍
    }
     //散列表没有被初始化
    else if (oldThr > 0) // 初始化的容量是已经指定了的
        newCap = oldThr;
    else { 
        // 默认初始化容量
        newCap = DEFAULT_INITIAL_CAPACITY;//16
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);//12
    }
    // 计算新的resize上限
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ? (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        // 把每个bucket都移动到新的buckets中
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            //当前桶位有数据，但不清楚是什么数据。
            if ((e = oldTab[j]) != null) {
                //方便 GC
                oldTab[j] = null;
                //如果是单个元素
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                //如果是红黑树
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { //桶位已经形成链表
                    Node<K,V> loHead = null, loTail = null;//低位链表
                    Node<K,V> hiHead = null, hiTail = null;//高位链表
                    Node<K,V> next;
                    do {
                        next = e.next;
                        // 原索引，给低位链表赋值
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        // 原索引+oldCap，给高位链表赋值
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    // 原索引放到桶里
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    // 原索引+oldCap放到桶里
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

在扩容时看原hash值新增的那个bit位是1还是0就好了，是0的话索引没有变，是1的话索引变成“原索引+oldCap（旧数组大小）”，下图位resize（）方法示意图： 

![3.png](https://gitee.com/houyao123/my-resource/raw/master/img/3.png)




## ConcurrentHashMap

**JDK1.7：**

 ConcurrentHashMap 和 HashMap 实现上类似，最主要的差别是 ConcurrentHashMap 采用了分段锁（Segment），每个分段锁维护着几个桶（HashEntry），多个线程可以同时访问不同分段锁上的桶，从而使其并发度更高（并发度就是 Segment 的个数）。  **Segment 继承自 ReentrantLock ，默认的并发级别为 16 。** 

简单理解就是，ConcurrentHashMap 是一个 Segment 数组，Segment 通过继承
ReentrantLock 来进行加锁，所以每次需要加锁的操作锁住的是一个 segment，这样只要保证每
个 Segment 是线程安全的，也就实现了全局的线程安全。

结构如图：

![concurrentHashMap7.png](https://gitee.com/houyao123/my-resource/raw/master/img/concurrentHashMap7.png)



**JDK1.8：**

使用了 CAS 操作来支持更高的并发度，在 CAS 操作失败时使用内置锁 synchronized。  synchronized只锁定当前链表或红黑二叉树的首节点，这样只要hash不冲突，就不会产生并发，效率又提升N倍。 


## HashSet 

实现原理： HashSet底层由HashMap实现 ，值存放于HashMap的key上 ，HashMap的value统一为PRESENT 。

检查重复： 先对插入的元素的hashcode值和现有的元素的hashcode作比较，如果没有相符的hashcode，HashSet会假设对象没有重复出现，直接插入。但是如果发现有相同hashcode值的对象，这时会调用`equals（）`方法来检查hashcode相等的对象是否真的相同。  如果两者相同，HashSet就不会让加入操作成功 。
##  LinkedHashMap 

 ### LinkedHashMap 实现LRU（least recently used）

- 设定最大缓存空间 MAX_ENTRIES 为 3；
- 使用 LinkedHashMap 的构造函数将 accessOrder 设置为 true，开启 LRU 顺序；
- 覆盖 removeEldestEntry() 方法实现，在节点多于 MAX_ENTRIES 就会将最近最久未使用的数据移除。

```java
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private static final int MAX_ENTRIES = 3;

    protected boolean removeEldestEntry(Map.Entry eldest) {
        return size() > MAX_ENTRIES;
    }

    LRUCache() {
        super(MAX_ENTRIES, 0.75f, true);
    }
}
public static void main(String[] args) {
    LRUCache<Integer, String> cache = new LRUCache<>();
    cache.put(1, "a");
    cache.put(2, "b");
    cache.put(3, "c");
    cache.get(1);
    cache.put(4, "d");
    System.out.println(cache.keySet());
}
```

 ## TreeMap

### TreeMap 其 key 对象为什么必须要实现 Compare 接口

通过阅读 TreeMap 的 put 方法的源码发现：TreeMap 实现元素不重复就是通过调用 compareTo 方法，而要使用 compareTo 方法就必须实现Compare接口。

## BlockingQueue 的实现类

ArrayBlockingQueue 底层是数组，有界队列，如果我们要使用生产者-消费者模式，这是非常好的选择。

LinkedBlockingQueue 底层是链表，可以当做无界和有界队列来使用，所以大家不要以为它就是无界队列。

DelayQueue是一个无界阻塞队列，只有在延迟期满时才能从中提取元素。该队列的头部是延迟期满后保存时间最长的Delayed 元素。

SynchronousQueue 本身不带有空间来存储任何元素，使用上可以选择公平模式和非公平模式。

PriorityBlockingQueue 是无界队列，基于数组，数据结构为二叉堆，数组第一个也是树的根节点总是最小值。





# 集合框架面试题和解析

**为什么要设计出迭代器**

迭代器本质是一种设计模式，为了解决为不同的集合类提供统一的遍历操作接口。

## List、Set、Map 之间的区别

**List**

 1.可以允许重复的对象。
 2.可以插入多个null元素。
 3.是一个有序容器，保持了每个元素的插入顺序，输出的顺序就是插入的顺序。
 4.常用的实现类有 ArrayList、LinkedList 和 Vector。

**Set：**

1.不允许重复对象
2.无序容器，无法保证每个元素的存储顺序，TreeSet通过 Comparator 或者 Comparable 维护了一个排序顺序。
3.只允许一个 null 元素
4.Set 接口最流行的几个实现类是 HashSet、LinkedHashSet 以及 TreeSet。

**Map：**

1.Map不是collection的子接口或者实现类。Map是一个接口。
 2.Map 的 每个 Entry 都持有两个对象，也就是一个键一个值（键值对），Map 可能会持有相同的值对象但键对象必须是唯一的。
3.TreeMap 也通过 Comparator 或者 Comparable 维护了一个排序顺序。
4.Map 里你可以拥有随意个 null 值但最多只能有一个 null 键。
5.Map 接口最流行的几个实现类是 HashMap、LinkedHashMap、Hashtable 和 TreeMap。（HashMap、TreeMap最常用） 

## Arraylist 与 LinkedList 的区别

**1. 底层数据结构：** `Arraylist` 底层使用的是  动态数组 ；`LinkedList` 底层使用的是 **双向链表** 数据结构 

**2.插入删除数据方式：** ArrayList插入删除元素时分两种情况：①把元素添加到末尾所需的时间复杂度是O(1)，②在指定位置添加删除元素时就需要移动整个数组，操作代价就比较大了；LinkedList 对于添加删除方法只需要断链然后更改指向，所需的消耗就很小了。 

**3.是否支持快速随机访问：** ArrayList 支持高效的随机元素访问而LinkedList 不支持。 

**4. 内存空间占用：** ArrayList的空 间浪费主要体现在在list列表的结尾会预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗比ArrayList更多的空间（因为要存放直接后继和直接前驱以及数据）。

 ## ArrayList 和 Vector 的区别

`Vector`类的所有方法都是同步的。可以由两个线程安全地访问一个Vector对象、但是一个线程访问Vector的话代码要在同步操作上耗费大量的时间。Vector一次扩容为原来的2倍。

`Arraylist`不是同步的，所以在不需要保证线程安全时建议使用Arraylist，需要时使用 CopyOnWriteArrayList 。Arraylist一次扩容为1.5倍。

##  ArrayList 的扩容机制？

**前提：以无参数构造方法创建 ArrayList 时，实际上初始化赋值的是一个空数组。当真正对数组进行添加元素操作时，才真正分配容量。**

流程：

当我们要 add 进第1个元素到 ArrayList 时，数组长度为0 （因为还是一个空的 list），因为执行了 `ensureCapacityInternal()` 方法 ，所以更改了 minCapacity，此时为10（默认最小容量）。此时，就会执行grow 方法把容量扩大为10，然后一直add到第11个元素时，会对旧数组**位移运算**得到新数组，然后把旧数组整个复制到新数组上，是旧容量的 1.5 倍。 

## 如何实现数组和 List 之间的转换？

- List转换成为数组：调用ArrayList的toArray方法。
- 数组转换成为List：调用Arrays的asList方法。

 ## Array 和 ArrayList 有何区别？

- Array可以容纳基本类型和对象，而ArrayList只能容纳对象。 
- Array是指定大小的，而ArrayList大小是固定的。 
- Array没有提供ArrayList那么多功能，比如addAll、removeAll和iterator等。

## HashMap 和 Hashtable 的区别

1. **线程是否安全：** HashMap 是非线程安全的，HashTable 是线程安全的；HashTable 内部的方法基本都经过`synchronized` 修饰。（如果保证线程安全的话使用 ConcurrentHashMap ）；
2. **效率：** 因为线程安全的问题，HashMap 要比 HashTable 效率高一点。另外，HashTable 基本被淘汰，不要在代码中使用它；
3. **对Null key 和Null value的支持：** HashMap 中，null 可以作为键，这样的键只有一个，可以有一个或多个键所对应的值为 null。。但是在 HashTable 中 put 进的键值只要有一个 null，直接抛出 NullPointerException。
4. **初始容量大小和每次扩充容量大小的不同 ：** ①创建时如果不指定容量初始值，Hashtable 默认的初始大小为11，之后每次扩充，容量变为原来的2n+1。HashMap 默认的初始化大小为16。之后每次扩充，容量变为原来的2倍。②创建时如果给定了容量初始值，那么 Hashtable 会直接使用你给定的大小，而 HashMap 会将其扩充为2的幂次方大小；
5. **底层数据结构：** JDK1.8 以后的 HashMap 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为8）时，将链表转化为红黑树，以减少搜索时间。Hashtable 没有这样的机制。



## 说说 HashMap

1.内部存储结构：数组+链表+红黑树（JDK8）

2.默认容量16（数组长度），默认装载因子0.75。

3.key 和 value 对数据类型的要求都是泛型。

4.key 可以为 null，放在 table[0] 中。

5.hashcode：计算键的hashcode作为存储键信息的数组下标用于查找键对象的存储位置。equals：HashMap 使用 equals() 判断当前的键是否与表中存在的键相同。

## HashMap 的结构

在 JDK 1.7 中 HashMap 是以数组加链表的形式组成的，JDK 1.8 之后新增了红黑树的组成结构，当链表大于 8 并且容量大于 64 时，链表结构会转换成红黑树结构，添加红黑树是因为一旦链表过长，会严重影响 HashMap 的性能，而红黑树具有快速增删改查的特点，这样就可以有效的解决链表过长时操作比较慢的问题。

## HashMap 的长度为什么是2的幂次方

HashMap为了存取高效，要尽量较少碰撞，就是要尽量把数据分配均匀，每个链表长度大致相同，这个实现就在把数据存到哪个链表中的算法；这个算法实际就是取模，hash%length，计算机中直接求余效率不如位移运算，源码中做了优化hash&(length-1)， 

**hash%length==hash&(length-1)的前提是 length 是2的 n 次方；** 

## 什么是 HashMap 的加载因子？加载因子为什么是 0.75？

判断什么时候进行扩容的，假如加载因子是 0.5，HashMap 的初始化容量是 16，那么当 HashMap 中有 16*0.5=8 个元素时，HashMap 就会进行扩容。

这其实是出于容量和性能之间平衡的结果：

* 当加载因子设置比较大的时候，扩容的门槛就被提高了，扩容发生的频率比较低，占用的空间会比较小，但此时发生Hash冲突的几率就会提升，因此需要更复杂的数据结构来存储元素，这样对元素的操作时间就会增加，运行效率也会因此降低；
* 而当加载因子值比较小的时候，扩容的门槛会比较低，因此会占用更多的空间，多次扩容也会影响性能。
* HashMap的容量有一个固定的要求就是一定是2的幂次方。所以，如果负载因子是3/4的话，那么和capacity的乘积结果就可以是一个整数。


所以综合了以上情况就取了一个 0.5 到 1.0 的平均数 0.75 作为加载因子。

## put 方法流程

map.put("a","b")的整个流程：

      	1. 先判断散列表是否没有初始化或者为空，如果是就扩容
                	2. 根据键值 key 计算 hash 值，得到要插入的数组索引 
             	3. 判断要插入的那个数组是否为空：
                               	1. 如果为空直接插入。
                               	2. 如果不为空，判断 key 的值是否是重复（用 equals 方法）：
                       	1. 如果是就直接覆盖
                       	2. 如果不重复就再判断此节点是否已经是红黑树节点：
                       	1. 如果是红黑树节点就把新增节点放入树中
                                 	2. 如果不是，就开始遍历链表：
                                     1. 循环判断直到链表最底部，到达底部就插入节点，然后判断是否大于链表长度是否大于8：
                                        1. 如果大于8就转换为红黑树
                                        2. 如果不大于8就继续下一步
                                           2. 到底部之前发现有重复的值，就覆盖。
           	4. 判断是否需要扩容，如果需要就扩容。

## HashMap 的扩容机制

扩容时机：当`size`大于`threshold`的时候，并不一定会触发扩容机制，只要有一个新建的节点出现哈希冲突，则立刻`resize`。 

- size记录的是map中包含的Entry的数量
- 而threshold记录的是需要resize的阈值 且 `threshold = loadFactor * capacity`
- capacity 其实就是桶的长度

步骤：

* 数组，阈值都扩大一倍
* 如果旧数组不为空，开始遍历旧数组
* 遍历到的数组上只有一个元素，就直接迁移
* 如果是红黑树就使用 split 方法
* 如果是链表就把链表拆成两个，按照高位运算的结果放到新数组中并且保留顺序

#### JDK 1.8 在扩容方面对 HashMap 做了哪些优化？

1.7创建一个容量的新数组，重新计算每个元素在数组中的位置并且进行迁移。 

1.8中在扩容HashMap的时候，不需要像1.7中去重新计算元素的hash，只需要看看原来的hash值新增的哪个二进制数是1还是0就好了，如果是0的话表示索引没有变，是1的话表示索引变成“oldCap+原索引”，这样即省去了重新计算hash值的时间，并且扩容后链表元素位置不会倒置。 

## HashMap 1.7和1.8版本区别

* **数据结构：**1.7：数组+链表，1.8：数组+链表+红黑树
* **新节点插入方式：**1.7：头插法 ，1.8：直接在尾部插入
* **扰动运算次数：**1.7：运算多，1.8：运算少
* **插入和扩容的判断：**1.7：先扩容后插入，1.8：先插入后扩容
  * 为什么？1.8增加了判断是否为红黑树节点，先扩容的话不知道到底扩链表节点还是红黑树。

## HashMap为什么不直接使用hashCode()处理后的哈希值直接作为table的下标？

 `hashCode()`方法返回的是int整数类型，其范围为-(2 ^ 31)~(2 ^ 31 - 1)，  而HashMap的容量范围是在16（初始化默认值）~2 ^ 30，  HashMap通常情况下是取不到最大值的，并且设备上也难以提供这么多的存储空间，从而导致通过`hashCode()`计算出的哈希值可能不在数组大小范围内，进而无法匹配存储位置； 

**怎么解决呢？** 

1.HashMap自己实现了自己的`hash()`方法，通过两次扰动使得它自己的哈希值高低位自行进行异或运算，降低哈希碰撞概率也使得数据分布更平均； 

2.保证数组长度为2的幂次方的时候，使用`hash()`运算之后的值与运算（&）（数组长度 - 1）来获取数组下标的方式进行存储。

**那为什么是两次扰动呢？** 

这样就是加大哈希值低位的随机性，使得分布更均匀，从而提高对应数组存储下标位置的随机性&均匀性，最终减少Hash冲突，两次就够了，已经达到了高位低位同时参与运算的目的； 

## HashMap1.7为什么不安全？⭐

HashMap在rehash的时候，这个会重新将原数组的内容重新hash到新的扩容数组中，在多线程的环境下，存在同时其他put操作，如果hash值相同，把值插入同一个链表，会因为头插法的特性造成闭环，导致在get时会出现死循环，所以HashMap是线程不安全的。                       

#### 高并发下HashMap1.7的环是如何产生的

若当前线程一此时获得ertry节点，但是被线程中断无法继续执行，此时线程二进入transfer函数，并把函数顺利执行，此时新表中的某个位置有了节点，之后线程一获得执行权继续执行，在tranfer方法中会把next指向自己造成闭环，然后在get时会出现死循环。

## 为什么HashMap中String、Integer这样的包装类适合作为Key？⭐

String、Integer等包装类的特性能够保证Hash值的不可更改性和计算准确性，能够有效的减少Hash碰撞的几率

1. 都是final类型，即不可变性，保证key的不可更改性，不会存在获取hash值不同的情况
2. 内部已重写了`equals()`、`hashCode()`等方法，遵守了HashMap内部的规范，不容易出现Hash值计算错误的情况。

**如果我想要让自己的Object作为K应该怎么办呢？** 

 重写`hashCode()`和`equals()`方法 。 **重写`hashCode()`是因为需要计算存储数据的存储位置**， **重写`equals()`方法** **目的是为了保证key在哈希表中的唯一性**； 



## ConcurrentHashMap线程安全的实现方式/数据结构⭐

在JDK1.7版本中，ConcurrentHashMap维护了一个Segment数组，Segment这个类继承了重入锁ReentrantLock，在该类里面维护了一个 HashEntry<K,V>[] table数组，在写操作put，remove，扩容的时候，会对Segment加锁，所以仅仅影响这个Segment，不同的Segment还是可以并发的，所以解决了线程的安全问题，同时又采用了分段锁也提升了并发的效率。在JDK1.8版本中，ConcurrentHashMap摒弃了Segment的概念，而是直接用Node数组+链表+红黑树的数据结构来实现，并发控制使用Synchronized和CAS来操作，整个看起来就像是优化过且线程安全的HashMap。 

详见java容器总结篇。

## BlockingQueue是什么？

Java.util.concurrent.BlockingQueue是一个队列，在进行检索或移除一个元素的时候，它会等待队列变为非空；当在添加一个元素时，它会等待队列中的可用空间。BlockingQueue接口是Java集合框架的一部分，主要用于实现生产者-消费者模式。我们不需要担心等待生产者有可用的空间，或消费者有可用的对象，因为它都在BlockingQueue的实现类中被处理了。Java提供了集中BlockingQueue的实现，比如ArrayBlockingQueue、LinkedBlockingQueue、PriorityBlockingQueue,、SynchronousQueue等。

 ## 在 Queue 中 poll()和 remove()有什么区别？

poll() 和 remove() 都是从队列中取出一个元素，但是 poll() 在获取元素失败的时候会返回空，但是 remove() 失败的时候会抛出异常。



* 可以用for循环直接删除ArrayList的特定元素吗？可能会出现什么问题？怎样解决？   
* 异常或漏删元素，ArrayList底层结构是数组类型，数组的特点是删除其中某个元素时，后面的所有元素索引都会前移，此时for循环的指针却会下移，因此会略过下一个元素。解决方法可以是删除将指针回调一次，或者使用迭代器遍历。

