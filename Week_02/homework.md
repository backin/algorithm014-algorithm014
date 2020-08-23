
# 作业
## 写一个关于 HashMap 的小总结。

## 有效的字母异位词（亚马逊、Facebook、谷歌在半年内面试中考过）

- 1. 可以先对字符串排序，然后对比字符串是否相等，来判断是不是异位词，因为要排序所以时间复杂度O(NlogN),有保存字符串的数组，所以空间复杂度O(N)
- 2. 比较两个字符串的字符数是不是一样的，要遍历每个字符所以，时间复杂度O(N)，开辟了一个存字符的数组大小26 是常数，所以空间复杂度是O(1)
```java
    class Solution {
        // 1.
        public boolean isAnagram(String s, String t) {
            if (s == null || t == null || s.length() != t.length()) return false;
            char[] charS = s.toCharArray();
            char[] charT = t.toCharArray();
            Arrays.sort(charS);
            Arrays.sort(charT);
            return new String(charS).equals(new String(charT));
        }
        // 2.
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
## 两数之和（近半年内，亚马逊考查此题达到 216 次、字节跳动 147 次、谷歌 104 次，Facebook、苹果、微软、腾讯也在近半年内面试常考）

- 1.暴力法双重循环 时间复杂度O(N^2),空间复杂度O(1)用来存结果
- 2.然后用map将遍历的值存取来，利用等式变换来找需要的值 时间复杂度O(N)，空间复杂度O(N)，map存储
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
## N 叉树的前序遍历（亚马逊在半年内面试中考过）
- 遇到树最先想到的就是递归，根据前中后序。安排
- 利用栈的特性，进行进栈出栈操作
```java
class Solution {
    public List<Integer> preorder(Node root) {
        // List<Integer> result=new LinkedList();
        // // helper(root,result);
        // method2(root,result);
        // return result;
        LinkedList<Node> stack = new LinkedList<>();
        LinkedList<Integer> output = new LinkedList<>();
        if (root == null) {
            return output;
        }

        stack.add(root);
        while (!stack.isEmpty()) {
            Node node = stack.pollLast();
            output.add(node.val);
            Collections.reverse(node.children);
            for (Node item : node.children) {
                stack.add(item);
            }
        }
        return output;
    }
    // 1、递归方式
    private void helper(Node root,List<Integer>temp){
        if(null==root){
            return;         
        }
        temp.add(root.val);  
        if(null==root.children)
            return;
        for(int i=0;i<root.children.size();i++){
            if(null!=root.children.get(i)){
                helper(root.children.get(i),temp);
            }
        }
    }
    // 2、迭代
    private void method2(Node root,List<Integer> temp){
       LinkedList<Node> stack = new LinkedList();
        if (null == root)
            return;

        stack.add(root);
        while(!stack.isEmpty()){
            Node node= stack.pollLast();
            temp.add(node.val);
            if(node.children==null)return;
            for(int i=node.children.size()-1;i>=0;i--){
                stack.add(node.children.get(i));
            }
        }      
        
    }    

}
```
## HeapSort ：自学 https://www.geeksforgeeks.org/heap-sort/

## 字母异位词分组（亚马逊在半年内面试中常考）
- 保存排好序的字符串到map中，然后对比排序后的就好了
```java
    class Solution {
        public List<List<String>> groupAnagrams1(String[] strs) {
            if (strs.length == 0) return new ArrayList();
            Map<String, List> ans = new HashMap<String, List>();
            for (String s : strs) {
                char[] ca = s.toCharArray();
                Arrays.sort(ca);
                String key = String.valueOf(ca);
                if (!ans.containsKey(key)) ans.put(key, new ArrayList());
                ans.get(key).add(s);
            }
            return new ArrayList(ans.values());
        }
        public List<List<String>> groupAnagrams(String[] strs) {
            Map<String, List<String>> temp = new HashMap<>();
            for (int i = 0; i < strs.length; i++) {
                char[] chars = strs[i].toCharArray();
                Arrays.sort(chars);
                String sortKey = String.valueOf(chars);
                List<String> value = temp.getOrDefault(sortKey, null);
                if (value == null) {
                    List<String> list = new ArrayList<>();
                    list.add(strs[i]);
                    temp.put(sortKey, list);
                }else{
                    value.add(strs[i]);
                }
            }
            return new ArrayList(temp.values());
        }
    }
```

## 二叉树的中序遍历（亚马逊、字节跳动、微软在半年内面试中考过）
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
## 二叉树的前序遍历（字节跳动、谷歌、腾讯在半年内面试中考过）
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
## N 叉树的层序遍历（亚马逊在半年内面试中考过）
```java
    public class LevelOrder {
        List<List<Integer>> res = new ArrayList<>();
    
        //2.dfs
        public List<List<Integer>> levelOrder(Node root) {
            if (root != null)
                dfs(root, 0);
    
            return res;
        }
    
        private void dfs(Node root, int level) {
            if (res.size() <= level) {
                res.add(new ArrayList<>());
            }
    
            res.get(level).add(root.val);
            for (Node n : root.children) {
                dfs(n, level + 1);
            }
        }
    
        //1.栈
        public List<List<Integer>> levelOrder1(Node root) {
    
            List<List<Integer>> result = new ArrayList<>();
            if (root == null) {
                return result;
            }
            Queue<Node> queue = new LinkedList<>();
            queue.add(root);
    
            while (!queue.isEmpty()) {
                int size = queue.size();
                List<Integer> level = new ArrayList<>();
                for (int i = 0; i < size; i++) {
                    Node curr = queue.poll();
                    level.add(curr.val);
                    List<Node> children = curr.children;
                    for (Node child : children) {
                        if (child != null) {
                            queue.add(child);
                        }
                    }
                }
                result.add(level);
            }
            return result;
        }
    }
```
## 丑数（字节跳动在半年内面试中考过）
1. 穷举
## 前 K 个高频元素（亚马逊在半年内面试中常考）


# 预习
## 爬楼梯
```java
class Solution {
    public int climbStairs(int n) { 
       
        return climbStairs4(n);

    }
    // 1、递归 时间复杂度O(2^n)（怎么来的我参考K叉数的遍历时间复杂度O(K^N)），超时
    public int climbStairs1(int n) { 
        if(n<=2)return n;
        return climbStairs(n-1)+climbStairs(n-2);

    }
    // 2、递归优化存储数组 时间复杂度 遍历n次所以是O(N),空间复杂度O(N)
    public int climbStairs2(int n) { 
        int res[]=new int[n];
        for(int i=1;i<=n;i++){
            if(i<=2){
                res[i-1]=i;
            }else{
                res[i-1]=res[i-2]+res[i-3];
            }
        }
        return res[n-1];
    }
    //3、 从上面我们可以知道我们只关注n上面的n-2和n-1。那么我们可以减少数组大小。节省空间
    public int climbStairs3(int n){
        if(n<=2)return n;  
        int res[]=new int[2];//只关心两个   
        int current=0;
        res[0]=1;
        res[1]=2;
        for(int i=3;i<=n;i++){
            current=res[0]+res[1];
            res[0]=res[1];
            res[1]=current;
                     
        }
        return current;
    }

    //4、既然都是2个数的数组那么用变量代替不是更棒
     public int climbStairs4(int n){
        if(n<=2)return n;  
     
        int current=0;
        int p=1;
        int q=2;
        for(int i=3;i<=n;i++){
            current=p+q;
            p=q;
            q=current;                     
        }
        return current;
    }

}
```

## 括号生成
## Pow(x, n)
## 子集
## N 皇后