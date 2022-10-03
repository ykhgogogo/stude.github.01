#  集合

##  集合的框架体系图

- 单例集合

Collection接口集合下面有两个重要的子接口Set和List

![image-20220921081615549](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220921081615549.png)

- 双列集合

Map接口下重要的实现子类

![image-20220921081905523](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220921081905523.png)

---

##  Set接口的常用方法：

### Set接口基本介绍：

- 无序，添加和取出顺序不一致，没有索引

- **不允许添加重复元素/对象（需要好好理解这个底层源码）**，所以最多一个null

- JDK中包含多个set接口的实现了

* Hashset创建的过程中，底层任然是调用HashMap（**HashMap底层时 数组+链表+红黑树**）

和list接口一样，set接口也是collection的子接口，因此常用方法和collection接口一样；同时set方法的遍历和collection方法一样。

**区别在于：不可以使用索引的方式获取**

---

##  HashSet底层机制说明（hash() + equals()）

- HashSet底层是HashMap

- 添加一个元素是，先得到hash值à会转换成à索引值

- 找到存储数据表table，看这个索引位置是否已经存放了元素

- 如果没有则直接加入

- 如果有，需要调用equals方法(equals比较的是内容，但是也可以重写为别的比较)，如果相同则放弃添加，如果不同则添加在最后（即next的过程）

- 在JDK8中，如果一条链表的元素个数到达8，并且table的大小大于等于64，则会进行树化（红黑树）

---

##  HashSet源码Debug分析

```java
HashSet hashSet = new HashSet();//Debug开始
hashSet.add("jack");
hashSet.add("ykh");
hashSet.add("jack");
System.out.println(hashSet);
```

1. 创建HashSet对象，底层是调用HashMap方法

```java
public HashSet() {
	map = new HashMap<>();//此时map对象为null
}
```

2. 进入add方法分析过程

```java
hashSet.add("jack");
//-->进入add方法

public boolean add(E e) {
    return map.put(e, PRESENT)==null;//涉及到map.put方法，并且传入e和PRESENT
    //e就是表示"jack",PRESENT则是定义好的一个常量，解释代码如下：
    //private static final Object PRESENT = new Object();
    //返回的是null，结果为真添加成功
}
//-->进入put方法中

//key就是e，即"jack";value就是PRESENT
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);//返回的null返回至add方法
}
//return语句中涉及到putVal方法和hash方法
//首先进入hash方法中，传入key
//-->进入hash方法中

static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
//key不是空，所以将key的hashCode按位与向右移动16位，最终得到的结果赋值给h
//如此操作的目的为了保证每次add元素返回的hash值不一样
//注意点：hash值并不完全等同于hashcode

//-->hash值返回后进入putVal方法
//下面代码较多慢慢理解
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;//为后面的代码定义临时变量
    if ((tab = table) == null || (n = tab.length) == 0)
        //table是位于hashmap中的属性，用于存放Node的结点的数组
        //此时hashmap中的数组table为null，执行以下代码
        n = (tab = resize()).length;
    	//进入resize()方法分析过程，此处为resize方法调用的返回处
    if ((p = tab[i = (n - 1) & hash]) == null)//用于判断tab[i]中是否有元素
        tab[i] = newNode(hash, key, value, null);
    	//newNode方法就是创建新Node结点，对象的参数(hash, item-->key,PRESENT:-->value, next:-->null)
    else {
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);//该方法是给hashmap的子类其他需求使用
    return null;//返回null到put方法中
}

//resize方法
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    //将table的null赋值给Node类型的oldTab对象
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    //oldTab == null为真，oldcap容量 = 0
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                 oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else {               // zero initial threshold signifies using defaults
        //if判断其他条件均不满足，走else这个过程
        newCap = DEFAULT_INITIAL_CAPACITY;
        //DEFAULT_INITIAL_CAPACITY默认容量是16
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        //static final int DEFAULT_INITIAL_CAPACITY = 1 << 4表示的含义就是位左移4次，等价于
        //1*2*2*2*2=16
        //newThr是临界容量 = 0.75 * 16；因此临界容量是12,起到缓冲作用
        //DEFAULT_LOAD_FACTOR表示临界因子为0.75
      }
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                  (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    //临界容量 = 12
    @SuppressWarnings({"rawtypes","unchecked"})
    Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    //HashMap的数组更新为newTab
    if (oldTab != null) {
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { // preserve order
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
    //直接跳转至此处分析，返回NNode类型的newTab对象
}
```

3. 第二次add的过程（与第一次不相同）
4. 第三次add的过程（与第一次相同）

```java
//put方法一样，进入到putVal方法中
//if ((tab = table) == null || (n = tab.length) == 0)//已经放入了两个Node到数组中，table不再为null
//if ((p = tab[i = (n - 1) & hash]) == null)//也不走这个if判断，因为第一次和这次添加的都是"jack",hash值是一样的，所以不等于null，走else语句
else {
    //高手在需要辅助变量的地方创建
    Node<K,V> e; K k;
    if (p.hash == hash && //先判断这次添加的"jack"的hash值和第一次添加的"jack"添加的hash值（p.hash）是否相同
        ((k = p.key) == key || (key != null && key.equals(k))))
        //再判断前后这两次是否为前一个对象||如果不是同一个对象，是否内容相同
        e = p;
    //如果if为true，不能加入    
    else if (p instanceof TreeNode)
        //判断p是不是一颗红黑树，如果是就通过红黑树方法添加
        e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
    else {
        //通过前面两种条件判断均不满足，添加内容不相同，只是链表不是红黑树
        //采用for循环依次比较的方式，并且是死循环
        //如果每次比较发现都不相同，会执行if ((e = p.next) == null)语句
        //把key接到链表的最后面，还会判断
        for (int binCount = 0; ; ++binCount) {
            if ((e = p.next) == null) {
                //p.next指向的Node同样被e指向，此时p指向前一个Node，e指向Node.next的Node
                p.next = newNode(hash, key, value, null);
                if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                    //TREEIFY_THRESHOLD是8，该if用于判断链表长度是否已经大于8
                    //如果达到8，就对当前链表进行树化
                    //进行树化还要进行判断，table小于64，只会进行数组扩容，不会马上树化
                    treeifyBin(tab, hash);
                break;
            }
            //如果循环判断过程中遇到相同，就会终止添加
            if (e.hash == hash &&
                ((k = e.key) == key || (key != null && key.equals(k))))
                break;
            p = e;
            //关键步骤之一，一次判断后，e指向的Node，同样被p指向，通过画图可以帮助理解
        }
    }
```

5. HashSet底层机制说明：扩容和转成红黑树

   5.1 HashSet底层是HashMap，第一次添加时，table数组扩容到16，临界值是12

   5.2 如果table数组使用到临界值12，就会扩容到32，新的临界值为48，依此类推

   5.3 如果一条链表的元素个数到达TREEIFY_THRESHOLD（默认为8），并且MIN_TREEIFY_CAPACITY（默认为64），就会进行树化，否则仍采取数组扩容机制

- [x] 代码演示：

```java
public static void main(String[] args) {
	HashSet hashSet = new HashSet();
    for (int i  = 0;i<100;i++){
    hashSet.add(i);
    }
}//该过程可以演示数组扩容的过程

//
HashSet hashSet = new HashSet();
for (int i = 0; i < 12; i++) {
	hashSet.add(new people(i));
    //在0-7添加过程中没有变化，当添加8的时候链表长度大于8，但是数组未大于64，所以会对数组进行扩容，达到32，8则会继续加入到原来链表中；加入9情况类似，会对数组进行扩容，扩容过程中也可能导致原来链表所在的索引值发生变化；当添加为10是，链表长度早已大于8并且，数组长度为64，满足转换成红黑树的条件
}

class people {
    private int i;

    public people(int i) {
        this.i = i;
    }

    @Override
    public int hashCode() {
        return 100;//通过重写hashCode方法返回同一个hash值，就可以确保都加入到同一个索引处的链表上
    }
}
```

6. 临界值12的说明：

```java
if (++size > threshold)
resize()
//该过程只有是添加了一个Node对象，size就会++，因此临界值12不是表示数组索引占用达到12
        for (int i = 0; i < 7; i++) {
            hashSet.add(new people(i));
        }
        for (int i = 0; i < 7; i++) {
            hashSet.add(new person(i));
        }

class people {
    private int i;

    public people(int i) {
        this.i = i;
    }

    @Override
    public int hashCode() {
        return 100;
    }
}

class person {
    private int i;

    public person(int i) {
        this.i = i;
    }

    @Override
    public int hashCode() {
        return 200;
    }
}
```

实例：

![image-20220920105257440](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220920105257440.png)

关键点：equals方法和hashCode方法的重写；可以直接生成两种方法的重写

- equals方法重写：

![image-20220920105524219](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220920105524219.png)

当勾选name和age时，两个均相同的时候，返回true；勾选某个属性就表示当该属性相同时返回true

- hashCode方法重写：

![image-20220920105702924](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220920105702924.png)

当勾选name和age时，两个均相同的时候，返回同一个hashCode；勾选某个属性就表示当该属性相同时返回同一个hashCode

---

## LinkedHashSet

### LinkedHashSet

- LinkedHashSet是HashSet的子类
- LinkedHashSet底层是一个LinkedHashMap（LinkedHashMap是HashMap的子类），底层维护了一个 **数组 + 双链表，而HashSet维护的是其中是单项链表**
- LinkedHashSet根据元素的hashCode值来决定元素的存储位置，同时使用链表维护元素的次序，使得元素看起来是以插入顺序保存的（遍历的结果与插入顺序一样）
- LinkedHashSet不允许添加重复的元素
- LinkedHashSet相较于HashSet更加有序

####  LinkedHashSet底层源码机制

1. 在LinkedHashSet中维护了一个hash表和双向链表（LinkedHashSet有head和tail）
2. 每一个节点有before和after属性，这样可以形成双向链表
3. 在添加一个元素时，先求hash值，在求出索引，确定该元素在table的位置（该过程和HashSet的原理过程一致），然后将添加的元素加入到双向链表（如果已经存在，不添加）；add加入第一个元素时，after和before均为null；当第二个元素加入完毕后第二个元素的before会指向第一个元素，第一个元素的after会指向第二个元素；由此实现两个元素形成双向链表，后续add过程类似
4. 还含有head和tail参数，head指向首结点，tail指向尾结点
5. **遍历LinkedHashSet也可以确保插入顺序和遍历顺序一致**

- [x] 注意点：LinkedHashSet中table数组的数据类型是HashMap$Node...，而在table索引处存储的数据类型是LinkedHashMap$Entry...，两处的数据类型不一致。


查看LinkedHashMap类源代码，含有Entry静态内部类：解释了数据结构不一致的原因，符合继承关系，同时Node可以通过HashMap类名直接访问，因此Node是静态内部类

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before, after;
    Entry(int hash, K key, V value, Node<K,V> next) {
        super(hash, key, value, next);
    }
}
```

HashMap中table数组的数据类型是HashMap$Node...，table数组索引处存放的数据类型还是HashMap$Node...

---

####  TreeSet（可以用于排序）

```java
public class treeSet {
    public static void main(String[] args) {
        //TreeSet treeSet = new TreeSet();
        //System.out.println(treeSet);//默认输出顺序是首字母从前往后排序
        //因此可以定义需要的排序方式
        TreeSet treeSet = new TreeSet(new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                //return ((String) o2).compareTo((String) o1);
                //通过匿名内部类自定义排序的方式，可以首字母从大到小后者从小到大
                //也可以按照字符串的长度排序
                return ((String) o2).length() - ((String) o1).length();
            }
        });
        treeSet.add("jack");
        treeSet.add("smith");
        treeSet.add("tom");
        treeSet.add("a");
        treeSet.add("ncd");//可以通过deBug方式追踪
        //发现如果排序按照字符串长度排序，则相同长度的不会重写和覆盖
        System.out.println(treeSet);
    }
}

--------------
//匿名内部类使用：
public TreeSet(Comparator<? super E> comparator) {
        this(new TreeMap<>(comparator));
}//继续追踪
//new TreeMap中将匿名内部类赋值给TreeMap中的comparator属性
public TreeMap(Comparator<? super K> comparator) {
        this.comparator = comparator;
}

//剩余add过程类似于hashset，区别主要在于add过程中会调用匿名内部类中的compare方法并且重复的时候不进行覆盖
cmp = cpr.compare(key, t.key);//调用匿名内部类中的compare方法
//当条件相同 = 0时
else {
    V oldValue = t.value;
    if (replaceOld || oldValue == null) {
        t.value = value;
    }
    return oldValue;
}
```

**//根据排序的方式判断数据是否能够成功加入：如果按照字符顺序排序，只有完全相同时不能加入；如果按照字符串长度排序，长度相同时就无法加入（此处不能加入表示不会替换）**

---

##  Map

引言：在之前的Set学习中可以发现，输入的是K-V参数，其中Key表示输入的参数，Value则是采用默认的PRESENT。因此区别于Set，Map同样开用K-V的方式，只是将Value不再采用常量使用。

###  Map接口实现类的特点

- Map与Collection并列存在。用于保存具有映射关系的数据：Key-Value。Map需要放入这个两个数据，因此成为双列元素
- Map中的key和value可以是任何引用类型。用于保存具有映射关系的数据，会封装到HashMap$Node对象中
- Map中不允许重复，原因是HashSet一样，如果key相同-value不相同就会把原来的value替换（不同于HashSet）
- Map中的value是可以重复的，因为key不同时哈希值不相同不在table的同一个索引处
- Map中key可以为null，value也可以为null；key为null只能有一个，多个会发生替换；value可以为空，并且value为null可以是多个
- Map中key常用String类型数据，但是不是只能使用String，可以存放的数据类是Object
- Map中key和value之间存在单向一对一关系，即通过指定的key总能找到对应的value

```java
public class MapDemo01 {
    public static void main(String[] args) {
        Map hashMap = new HashMap();
        hashMap.put("ip1", "ykh");//k-v
        hashMap.put("ip2", "smith");
        hashMap.put("ip2", "jack");
        hashMap.put("ip3", "smith");
        hashMap.put(null, null);
        hashMap.put(null, "no");
        hashMap.put("ip4", "no");
        hashMap.put(new Object(), "1");
        System.out.println(hashMap);//{null=no, ip2=jack, java.lang.Object@119d7047=1, ip1=ykh, ip4=no, ip3=smith}
        System.out.println(hashMap.get("ip2"));//jack
    }
}
```

- Map存放数据key-value，一堆k-v是放在一个HashMap$Node中的，又因为Node实现了Entry接口，一些书表示一对k-v就是一个Entry（**较难理解**）

![image-20220920203329368](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220920203329368.png)

- 在HashMap的table数组中，各个索引位置存放的是Node数据类型的结点，结点中存放了很多数据，包括key-value；为了方便遍历，会创建EntrySet的集合，集合中存放的是一组一组的Map.Entry类型的数据，集合存放的内容是：EntrySet<Entry<key,value>> 即：transient Set<Map.Entry<K,V>> entrySet;但是K和V并不是存放新的数据，而是K指向在HashMap$Node中的K，V指向在HashMap$Node中的V，是指向关系；

- EntrySet中定义的Entry实际进行了向上转型，即

```java
Entry e  =   (Entry)HashMap$Node
```

- 而Node存在的位置还是处于HashMap$Node中，可以这样存放数据是因为Node implements Map.Entry(数组的多态)。这样设计还是为了方便遍历数据，因为**Map.Entry提供了方便获取数据得方法——getkey();和getvalue();**

- ```java
  public class MapDemo01 {
      public static void main(String[] args) {
          Map hashMap = new HashMap();
          hashMap.put("ip1", "ykh");//k-v
          hashMap.put("ip2", "smith");
          hashMap.put(new car(),new ip());
          Set set = hashMap.entrySet();
          System.out.println(set.getClass());//class java.util.HashMap$EntrySet
          for (Object o :set) {
              System.out.println(o.getClass());//class java.util.HashMap$Node
              Map.Entry e = (Map.Entry)o;
              System.out.println(e.getKey() +"  "+ e.getValue());
          }
          Set set1 = hashMap.keySet();//就是可以将所有Node结点中的key数据封装到Set类型的集合中，也可以单独取出
          System.out.println(set1.getClass());//得到运行类型：class java.util.HashMap$KeySet
          Collection values = hashMap.values();//将所有Node结点中的value数据封装到Collection类型的集合中，也可以单独取出
          System.out.println(values.getClass());//得到运行类型：class java.util.HashMap$Values
      }
  }
  class car{}
  class ip{}
  ```

- ![image-20220920210302775](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220920210302775.png)

- 我理解方便遍历可能的原因：将key存放在Set集合中，value存放在Collection集合中，因为可以调用iterator迭代器，因此方便遍历。并且是指向关系，实际数据的存储始终处于Node结点中。

---

###  Map接口的常用方法：

- put：添加元素
- remove：根据**键（key）**删除映射关系
- get：根据键获取**值（value）**
- size：获取元素个数
- clean：清除所有结点
- isEmpty：判断个数是否为0
- containsKey：查找键是否存在

---

###  Map接口遍历方法

遍历的关键点就是前面分析的图

![image-20220920203329368](C:\Users\MS\AppData\Roaming\Typora\typora-user-images\image-20220920203329368.png)

```java
public class MapDemo02 {
    public static void main(String[] args) {
        Map hashMap = new HashMap();
        hashMap.put("ykh", 9);
        hashMap.put("fs", 19);
        hashMap.put("213", 2);
        hashMap.put("q2", 4);
        hashMap.put("4s", 6);
        hashMap.put(null, 6);
        hashMap.put("ds2", null);
        //方式一：
        Set kayset = hashMap.keySet();
        //通过键kay的值获得value值，set类型的集合有两种遍历的方式，一种是增强for循环，一种是iterator迭代器
        System.out.println("===方式1.1===");
        for (Object o : kayset) {
            System.out.println(o + "  " + hashMap.get(o));
        }
        System.out.println("===方式1.2===");
        Iterator iterator = kayset.iterator();
        while (iterator.hasNext()) {
            Object next = iterator.next();
            System.out.println(next + "  " + hashMap.get(next));
        }
        //方式二：
        //把所有的value一整个集合取出
        System.out.println("===方式2.1===");
        Collection values = hashMap.values();
        //Collection类型的集合，所以所有的三种遍历方式均可以
        Iterator iterator1 = values.iterator();
        while (iterator1.hasNext()) {
            Object next = iterator1.next();
            System.out.println(next);
        }
        System.out.println("===方式2.2===");
        for (Object o : values) {
            System.out.println(o);
        }
        //方式三：
        System.out.println("===方式3.1===");
        Set set = hashMap.entrySet();
        for (Object o : set) {
            Map.Entry o1 = (Map.Entry) o;//通过向下转型把Object类型的数据转换从Map.Entry类型的数据从而可以访问其中的两个get方法
            System.out.println(o1.getKey() + "  " + o1.getValue());
        }
        System.out.println("===方式3.2===");//迭代器
        Iterator iterator2 = set.iterator();
        while (iterator2.hasNext()) {
            Object next = iterator2.next();//本质是从HashMap$Node类型的结点中取出数据
            System.out.println(next.getClass());
            System.out.println(next);
        }
    }
}
```

---

###  HashMap底层机制和源代码

- HashMap底层维护了Node类型的数组table，默认为null
- 当初创建对象时，将加载因子（loadfactor）初始化为0.75
- 当添加key-value时，通过key得到HashCode，然后将其计算得到哈希值，进而获得table表中对应的索引位置。如果该索引处没有元素，则可以直接添加。如果有元素，继续判断该索引处的元素和需要加入的是否相同，如果相同则直接**替换value**；如果不相等，需要判断是树结构还是链结构。如果添加过程中容量不足，则需要扩容。
- 第一次添加，table容量为16，临界值为12。
- 以后扩容为原来的两倍，**扩容机制和HashSet一样**
- 如果一条链长度超过8，并且table长度大于64时，会转换成红黑树
- 注意点：Hash值相同只是表示会在table数组中同一个索引处出现，并不代表key相同；深刻理解以下判断含义：
- ```java
  if (p.hash == hash &&
      ((k = p.key) == key || (key != null && key.equals(k))))
      //key的比较要根据数据类型分析，如果是引用数据类型则判断数据地址值
  ```

```java
    final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            n = (tab = resize()).length;
        if ((p = tab[i = (n - 1) & hash]) == null)
            tab[i] = newNode(hash, key, value, null);
        else {//当添加的一个结点Node和之前已经添加的一个Node的key相同
            Node<K,V> e; K k;
            if (p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k))))
                //e指向了原来的结点Node，转至if (e != null)
                e = p;
        	else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                //如果找到的是链表就循环比较
                for (int binCount = 0; ; ++binCount) {
                    if ((e = p.next) == null) {//这个if就是不存在一样的，在结尾加上Node
                        p.next = newNode(hash, key, value, null);
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);//进入方法中进一步判断是否满足扩容条件
                        break;
                    }
                    //这个if表示在循环过程中找到相同的
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    p = e;
                }
            }
            //value的替代在这里实现
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;//将传入的value替换了原本Node结点的value
                afterNodeAccess(e);
                return oldValue;//将完成替换的Node结点返回
            }
        }
        ++modCount;//++过程只有添加成功才会执行，替代过程经过上面return oldValue
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

---

###  HashTable

####  HashTable的基本介绍：

- 存放的元素都是键值对，K-V
- **HashTable的键和值都不能为null，否则会抛出异常NullPointException**
- HashTable使用方法基本上和HashMap一样
- HashTable是线程安全的（synchronized）
- Hashtable的底层是table数组，是Hashtable$Entry[] 初始化大小是11，临界值是8；扩容机制时（2 * n + 1）

```java
int newCapacity = (oldCapacity << 1) + 1;
//扩容语句
```

- 当K值相同时，V会被替换

---

### Properties

####  基本介绍：

- Properties类继承自Hashtable类并且实现了Map接口，也是使用一种键值对的形式来保存数据。
- 使用特点和Hashtable类似
- Properties还可以用于从xxx.properties文件中，加载数据到Properties类中并进行读取和修改
- 工作和xxx.properties文件通常作为配置文件

```java
//增put
properties.put("jcak",13);
System.out.println(properties);
//删remove
properties.remove("jcak");
System.out.println(properties);
//改put
properties.put("tom","V改成xxx内容");
System.out.println(properties);
//查get      System.out.println(properties.get("joj"));
```

-----

###  TreeMap

TreeSet底层还是调用了TreeMap源码，TreeMap的实际过程和TreeSet几乎一致，区别在于传入的是键值对，同样根据指定的compare方法对Key排序，如果比较的是Key的长度，那么当长度相同时键值对没法加入，但是会对相同长度的Key进行覆盖。

```java
public class treeMap {
    public static void main(String[] args) {
        TreeMap treeMap = new TreeMap(new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                //return ((String) o2).compareTo((String) o1);
                return ((String) o2).length() - ((String) o1).length();
            }
        });
        treeMap.put("YKH", 123);
        treeMap.put("YKL", 123);
        treeMap.put("YKH", 111);
        treeMap.put("TOM", 26);
        treeMap.put("JACK", 78);
        System.out.println(treeMap);//{JACK=78, YKH=26}
        //默认依旧按照Key的首字母大小排序
        //自定义即可改变排序方式
    }
}
```

---

###  Collections工具类

```java
package com.hspedu;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;

public class collections {
    public static void main(String[] args) {
        ArrayList arrayList = new ArrayList();
        arrayList.add("jack");
        arrayList.add("tom");
        arrayList.add("king");
        arrayList.add("smith");
        arrayList.add("smith");
        Collections.sort(arrayList);//自然排序
        System.out.println(arrayList);//[jack, king, smith, tom]
        Collections.reverse(arrayList);//反转
        System.out.println(arrayList);//[tom, smith, king, jack]
        for (int i = 0; i < 5; i++) {
            Collections.shuffle(arrayList);//随机排序
            System.out.println(arrayList);//结果均不一样
        }
        //自定义排序方式
        Collections.sort(arrayList, new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                return ((String) o1).length() - ((String) o2).length();
            }
        });
        System.out.println(arrayList);//[tom, jack, king, smith]
        //Max根据自然排序给定集合中的最大值;也可以根据compare方法给出最大值
        System.out.println(Collections.max(arrayList));//tom
        Object maxelement = Collections.max(arrayList, new Comparator() {
            @Override
            public int compare(Object o1, Object o2) {
                return ((String) o1).length() - ((String) o2).length();
            }
        });
        System.out.println(maxelement);//smith
        //min的两种方式一样
        //frequency返回指定集合中指定元素的数目
        System.out.println(Collections.frequency(arrayList, "smith"));//2
        //copy将数组完全拷贝,需要定义des的数组长度和目标数组一致，否则会排除索引越界的异常
        ArrayList des = new ArrayList();
        for (int i = 0; i < arrayList.size(); i++) {
            des.add("");
        }
        Collections.copy(des, arrayList);
        System.out.println(des);
        //replaceAll替换集合中所有xx为xx2
        Collections.replaceAll(arrayList, "smith", "rose");
        System.out.println(arrayList);//[tom, king, jack, rose, rose]
    }
}
```

---

###  开发中如何选择集合实现类

主要根据业务要求特点：

- 先判断存储的类型（一组对象还是一组键值对）
- 一组对象(单例集合)：Collection接口
  - 运行重复：List
    - 增删多：LinkedList [底层维护一个双向链表]
    - 改查多：ArrayList [底层维护Object类型的可变数组]
  - 不允许重复：Set接口
    - 无序：HashSet [底层时HashMap，维护了一个哈希表 即（数据+链表+红黑树）]
    - 排序：TreeSet
    - 插入和取出顺序一致：LinkedHashSet，维护数组+双向链表
- 一组键值对：Map
  - 键无序：HashMap[底层是哈希表（JDK7：数组+链表）；（JDK8：数组+链表+红黑树）]
  - 键排序：TreeMap
  - 键插入顺序和取出顺序一致：LinkedHashMap
  - 读取文件：properties

----



```java
TreeSet treeSet = new TreeSet();
        treeSet.add(new wo());
class wo{}
//会出现类型转换异常，因为treeSet添加的过程中会比较key值，比较的时候会涉及向上转型(Comparable<? super K>)k1，由于wo并没有实现Comparable接口，所以没法转换
//解决方法1：实现comparable接口重写抽象方法
class wo implements Comparable{
    @Override
    public int compareTo(Object o) {
        return 0;
    }
}
//解决方法2：使用匿名内部类
```

---

```java
package com.hspedu.homework;

import java.util.HashSet;
import java.util.Objects;

public class homework05 {
    public static void main(String[] args) {
        HashSet set = new HashSet();
        person p1 = new person("aa", 1001);
        person p2 = new person("bb", 1002);
        set.add(p1);
        set.add(p2);
        p1.setName("cc");
        set.remove(p1);//p1再创建添加过程中的hash值是i1，但是id经过修改后成为了cc
        //remove方法计算过程中计算的是（1001，"cc"）对应的hash值i2，和i1明显不同，所以
        //remove没法找到p1进行删除
        System.out.println(set);//两个对象
        set.add(new person("cc", 1001));
        //此时创建对象加入的person是根据（"cc",1001）计算的到的hash值i3再计算的到table索引对应的位置
        //i3与i1并不相同，所以可以加入table表中
        System.out.println(set);//三个对象
        set.add(new person("aa", 1001));
        //（"aa", 1001）计算得到的hash值i4与i1相同，所以再table表i中会比较i4和i1
        //由于重写了equal方法，并且i1中的aa已经被替换成了cc，所以i4可以加入其中，接在
        //i1的后面
        System.out.println(set);//四个对象
/*[person{name='bb', id=1002}, person{name='cc', id=1001}]
[person{name='bb', id=1002}, person{name='cc', id=1001}, person{name='cc', id=1001}]
[person{name='bb', id=1002}, person{name='cc', id=1001}, person{name='cc', id=1001}, person{name='aa', id=1001}]*/
    }
}

class person {
    private String name;
    private int id;

    public person(String name, int id) {
        this.name = name;
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        person person = (person) o;
        return id == person.id && Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, id);
    }

    @Override
    public String toString() {
        return "person{" +
                "name='" + name + '\'' +
                ", id=" + id +
                '}';
    }
}
```

