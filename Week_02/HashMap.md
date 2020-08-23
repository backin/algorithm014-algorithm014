# HashMap 小结
## 什么是HashMap

之前有学过数组具有查询速度快，但是删除和插入效率低下的问题；而链表删除插入效率高，但是查询效率又不尽人意；于是HashMap出现了。HashMap是综合了数组和链表的特点，是数组+链表的数据结构，在jdk1.8之后又加入了红黑树

## 有什么特点
- 存的是什么？ 

HashMap类中有一个非常重要的字段，就是 Node[] table，说明存储元数据就是Node，

- 性能如何？

存取的时间复杂度可以达到O(1)


## 怎么用
- 存

主要方法：
    public V put(K key, V value)

下面看看put方法干了什么
    
    1. 判断键值对数组table[i]是否为空或为null，否则执行resize()进行扩容；
    
    2. 根据键值key计算hash值得到插入的数组索引i，如果table[i]==null，直接新建节点添加，转向6，如果table[i]不为空，转向3；
    
    3. 判断table[i]的首个元素是否和key一样，如果相同直接覆盖value，否则转向4，这里的相同指的是hashCode以及equals；
    
    4. 判断table[i] 是否为treeNode，即table[i] 是否是红黑树，如果是红黑树，则直接在树中插入键值对，否则转向5；
    
    5. 遍历table[i]，判断链表长度是否大于8，大于8的话把链表转换为红黑树，在红黑树中执行插入操作，否则进行链表的插入操作；遍历过程中若发现key已经存在直接覆盖value即可；
    
    6. 插入成功后，判断实际存在的键值对数量size是否超多了最大容量threshold，如果超过，进行扩容。
    
```java
    class HashMap{
    //其他方法省略了
    public V put(K key, V value) {
          // 对key的hashCode()做hash
          return putVal(hash(key), key, value, false, true);
      }
      
      final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                     boolean evict) {
          Node<K,V>[] tab; Node<K,V> p; int n, i;
          // 步骤①：tab为空则创建
         if ((tab = table) == null || (n = tab.length) == 0)
             n = (tab = resize()).length;
         // 步骤②：计算index，并对null做处理 
         if ((p = tab[i = (n - 1) & hash]) == null) 
             tab[i] = newNode(hash, key, value, null);
         else {
             Node<K,V> e; K k;
             // 步骤③：节点key存在，直接覆盖value
             if (p.hash == hash &&
                 ((k = p.key) == key || (key != null && key.equals(k))))
                 e = p;
             // 步骤④：判断该链为红黑树
             else if (p instanceof TreeNode)
                 e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
             // 步骤⑤：该链为链表
             else {
                 for (int binCount = 0; ; ++binCount) {
                     if ((e = p.next) == null) {
                         p.next = newNode(hash, key,value,null);
                            //链表长度大于8转换为红黑树进行处理
                         if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st  
                             treeifyBin(tab, hash);
                         break;
                     }
                        // key已经存在直接覆盖value
                     if (e.hash == hash &&
                         ((k = e.key) == key || (key != null && key.equals(k))))                                       break;
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
         // 步骤⑥：超过最大容量 就扩容
         if (++size > threshold)
             resize();
         afterNodeInsertion(evict);
         return null;
     }}
```

- 取

这里就比较简单了
主要用到两个方法
    1. public V get(Object key) 直接通过key获取对象
    2. public V getOrDefault(Object key, V defaultValue) 这个是上面方法的补充，如果取值为空，则设置为默认值
- 遍历

集合类的遍历一般通用的就有如果有迭代器可以用迭代器，没有就可以用for循环
这里HashMap中有一个table表存储的存储对象信息就是 entrySet，其返回的是一个Set对象，而Set是属于Collection的子类，所以可以使用迭代器iterator。如果只需遍历key可以使用keySet()
```java
class HashMap{
 public void test() {
        Map<String, String> test = new HashMap<>();
        test.put("1", "abc1");
        test.put("2", "abc12");
        test.put("3", "abc123");
        for (Map.Entry<String, String> entry : test.entrySet()) {
            System.out.println(String.format("%s:%s", entry.getValue(), entry.getValue());
        }   
        //遍历key
        for (String key : test.keySet()) {
            System.out.println(key);
        }
    }
}
```
## 需要注意的地方 
- 能够估量容量的时候，最好给确定的初始值，毕竟从源码可以看出，数组的扩容是一个费力的事情。
- HashMap是线程不安全的，不要在并发的环境中操作HashMap，建议使用ConcurrentHashMap。
