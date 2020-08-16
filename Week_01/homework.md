## 1. 用 add first 或 add last 这套新的 API 改写 Deque 的代码
```
        Deque<String> deque = new LinkedList<>();
        deque.add("b");
        deque.addFirst("a");//这里addFirst的节点受位置影响
        deque.addLast("c");
        System.out.println(deque);

        String first = deque.pollFirst();
        System.out.println(first);

        while(deque.size()>0){
            System.out.println(deque.pop());
        }
        System.out.println(deque);

```
Deque 常用的Api
#### 1.添加 
-  void addFirst(E e);//添加头结点
-  void addLast(E e);//添加尾节点
-  boolean add(E e);//正常添加到队列末尾 

> 添加的时候如果空间不够都会抛出异常，
>```
>     * @throws IllegalStateException if the element cannot be added at this
>     *         time due to capacity restrictions
>     * @throws ClassCastException if the class of the specified element
>     *         prevents it from being added to this deque
>     * @throws NullPointerException if the specified element is null and this
>     *         deque does not permit null elements
>     * @throws IllegalArgumentException if some property of the specified
>     *         element prevents it from being added to this deque
>```
>只有add(E e)会返回添加结果
>
#### 2.获取
- E getFirst();//获取头结点，==为空抛异常==
- E getLast();//获取尾节点，==为空抛异常==
- E peekFirst();//获取头结点，可以为空
- E peekLast();//获取尾节点，可以为空
- E element(); //等同于getFirst()
> 以上方法只获取不出栈

- E removeFirst();
- E removeLast();//如果元素为空要报错
- E poll();//返回出栈元素
- E remove();
- E pollFirst();
- E pollLast();//可以返回空

> 以上方法获取并出栈

#### 3.遍历
- Iterator<E> iterator();
- Iterator<E> descendingIterator();

## 2.分析 Queue 和 Priority Queue 的源码
### a.Queue的源码
- Queue 是一个接口，定义队列这种数据结构的通用api
```java
interface Queue{
    boolean add(E e);
    boolean offer(E e);
    E element();
    E remove();
    E poll();
    E peek();
}
```
- 父类是Collection，表示这种数据类型是一种集合，可以使用集合的所有公开api
- 拥有子类
```
    LinkedList
    PriorityQueue
    java.util.concurrent.LinkedBlockingQueue
    java.util.concurrent.BlockingQueue
    java.util.concurrent.ArrayBlockingQueue
    java.util.concurrent.LinkedBlockingQueue
    java.util.concurrent.PriorityBlockingQueue
```
### b.Priority Queue的源码
- PriorityQueue 一个基于优先级的无界优先级队列。
- 元素按照其自然顺序进行排序，或者根据构造队列时提供的 Comparator 进行排序，具体取决于所使用的构造方法
- 该队列不允许使用 null 元素
- 不允许插入不可比较的对象(没有实现Comparable接口的对象)
- 是一个object数组，默认初始容量为11(为什么是11？)，随着不断向优先级队列添加元素，其容量会自动扩容，无需指定容量增加策略的细节
- 最大容量Integer.MAX_VALUE - 8，增长规则
```
    # 1.先扩容如果大于64扩容50%，否则就扩容到2(n+1)
    int newCapacity = oldCapacity + ((oldCapacity < 64) ? (oldCapacity + 2) : (oldCapacity >> 1));
    # 2.检查 newCapacity 超容没有，如果超容就根据设置的最小容量来计算 newCapacity
            (minCapacity > MAX_ARRAY_SIZE) ? Integer.MAX_VALUE :  MAX_ARRAY_SIZE;
```

## 3.删除排序数组中的重复项（Facebook、字节跳动、微软在半年内面试中考过）
> 给定一个排序数组，你需要在原地删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。 
 不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。 
来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array

思路：
- 1.暴力法 ：双重循环
```java
class test{
    public void rotate(int[] nums, int k) {
            int temp, previous;
            for (int i = 0; i < k; i++) {
                previous = nums[nums.length - 1];
                for (int j = 0; j < nums.length; j++) {
                    temp = nums[j];
                    nums[j] = previous;
                    previous = temp;
                }
            }
        }
}
```



## 4.旋转数组（微软、亚马逊、PayPal 在半年内面试中考过）
> 题目：给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。
- 弟子规(自递归)
```java
class Test{
    public void rotate(int[] nums, int k) {
            if (k <= 0) return;
            int temp = nums[nums.length - 1];
            for (int i = nums.length - 1; i > 0; i--) {
                nums[i] = nums[i - 1];
            }
            nums[0] = temp;
            rotate(nums, k - 1);
    }
}
```

- 3次反转
```java
class Test{
    public static void rotate(int[] nums, int k) {
        reverse(nums, 0, nums.length - 1);//全返
        k = k % nums.length;
        reverse(nums, 0, k - 1);//反转头
        reverse(nums, k, nums.length - 1);//反转尾
    }
    public static void reverse(int[] nums, int start, int end) {
        while (start < end) {
            int temp = nums[start];
            nums[start++] = nums[end];
            nums[end--] = temp;
        }
    }
}
```

    ## 5.合并两个有序链表（亚马逊、字节跳动在半年内面试常考）
>将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。
```java
class Test{
  public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
       if(l1==null){
           return l2;
       }else if(l2==null){
           return l1;
       }else if(l1.val<l2.val){
           l1.next=mergeTwoLists(l1.next,l2);
           return l1;
       }else {
           l2.next=mergeTwoLists(l1,l2.next);
           return l2;
       }        
    }
}
```

## 6.合并两个有序数组（Facebook 在半年内面试常考）
```java
class Solution {
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        // l 表示从后向前当前插入位置
        int l = m + n - 1;
        // i 标记数组 nums1 当前比较位置
        int i = m - 1;
        // j 标记数组 nums2 当前比较位置
        int j = n - 1;
        //将数组 nums 2 全部数据插入到数组 nums1 为止
        while (j >= 0 ) {
            //当数组 nums1 当前可比较数据位 i 大于等于 0 并且数组 nums1 当前位置值比 nums2 当前位置值大时，将数组 nums1 当前位置数组 插入到 l 位置 ，i 位指针向前挪一位
            //否则将 nums2 当前位置数据插入到 l 位置，j 位指针向前挪一位
            //当前插入位置 l 向前挪一位
            nums1[l--] =  (i >= 0 && nums1[i] >= nums2[j]) ? nums1[l] = nums1[i--] : nums2[j--];
        }
    }
}
```

## 7.两数之和（亚马逊、字节跳动、谷歌、Facebook、苹果、微软在半年内面试中高频常考）
```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public int[] twoSum(int[] nums, int target) {
       return twoSum2(nums,target);
    }

    //双重循环 时间复杂度O(n^2)
    public int[] twoSum1(int[] nums, int target) {
        
        for(int i=0;i<nums.length-1;i++){
            for(int j=i+1;j<nums.length;j++){
                if(nums[i]+nums[j]==target){
                    return new int[]{i,j};
                }
            }
        }
        return new int[]{0,0};
    }

    //target是两个数之和，那么把每个数都看成一个因子，用一个map存储起来。每次去看
    public int[] twoSum2(int[] nums, int target) {
        Map<Integer, Integer> result = new HashMap();
        for (int i = 0; i < nums.length; i++) {
            int fator = target - nums[i];
            if (result.containsKey(fator)) {
                return new int[]{result.get(fator), i};
            }
            result.put(nums[i], i);
        }
        return null;
    }
}
```
## 8.移动零（Facebook、亚马逊、苹果在半年内面试中考过）
```java
class Solution {
    public void moveZeroes(int[] nums) {
        moveZeroes1(nums);
    }
    //直接在原数组上操作
    public void moveZeroes1(int[] nums) {
        int j=0;
        for(int i=0; i<nums.length;i++){
            if(nums[i]!=0){
                nums[j]=nums[i];
                if(i!=j){
                    nums[i]=0;
                }
              j++;  
            }            
        }
    }
}
```

## 9.加一（谷歌、字节跳动、Facebook 在半年内面试中考过）
```java
class Solution {
    public int[] plusOne(int[] digits) {
        for (int i = digits.length - 1; i >= 0; i--) {
            digits[i]++;
            digits[i] = digits[i] % 10;
            if (digits[i] != 0) return digits;//如果最高位等于零了那么说明进位了，要额外处理，反之就结束
        }
        digits = new int[digits.length + 1];//弥补最高位进位的bug
        digits[0] = 1;
        return digits;

    }
}
```

## 有效的字母异位词
```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s == null || t == null || s.length() != t.length()) return false;

        int chars[] = new int[26];
        char[] charArray = s.toCharArray();
        for (int i = 0; i < charArray.length; i++) {
            chars[charArray[i] - 'a']++;
            chars[t.charAt(i) - 'a']--;
        }
        for (int i = 0; i < chars.length; i++) {
            if (chars[i] != 0) return false;
        }

        return true;
    }
}
```

## 94. 二叉树的中序遍历
```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
       List<Integer> result=new ArrayList();
       helper(root,result);
       return result;
    }
    private void helper(TreeNode root,List<Integer> result){
        if(root==null) { 
         return;
        }
        helper(root.left,result);
        result.add(root.val);       
        helper(root.right,result);
    }
}
```

## 剑指 Offer 40. 最小的k个数
- 最简单的解法
```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        int[] a = new int[k];
        Arrays.sort(arr);
        for (int i = 0; i < k; i++) {
            a[i] = arr[i];
        }
        return a;
    }
}
```
