# [415. 字符串相加](https://leetcode-cn.com/problems/add-strings/)

给定两个字符串形式的非负整数 num1 和num2 ，计算它们的和。

注意：

num1 和num2 的长度都小于 5100.
num1 和num2 都只包含数字 0-9.
num1 和num2 都不包含任何前导零。
你不能使用任何內建 BigInteger 库， 也不能直接将输入的字符串转换为整数形式。

## 分析

直觉就是用双指针，都从后往前移，每次计算两个位的整数之和。（注意到：如果是加法的话，那么进位最多只能是1）。这会遇到三种情况：1）两个位都有；2）只有1个位有值；3）两个位都没有值。第3）情况就是循环跳出的条件，即`while(i<=0 || j<=0)` ，当第1）和第2）情况时，有一种巧妙的思路，就是用0占位，也就是如果该位没有值，那么就用0替代它，这样两种情况就合并成了1）的情况。

## 代码

```java
    /**
     * @Title: 415. 字符串相加
     * @Description: 给定两个字符串形式的非负整数 num1 和num2 ，计算它们的和。
     * @Param: String num1, String num2
     * @return: String
     */
    public String addString(String num1, String num2){
        int m = num1.length();
        int n = num2.length();
        if (m==0 && n==0) return "";
        else if (m==0) return num2;
        else if (n==0) return num1;
        int i = m-1, j = n-1;
        int bonus = 0;
        StringBuilder sb = new StringBuilder();
        while (i>=0 || j>=0){
            int ni = i>=0?num1.charAt(i)-'0':0;
            int nj = j>=0?num2.charAt(j)-'0':0;
            int temp = ni+nj+bonus;
            bonus = temp/10;
            sb.append(temp%10);
            i -= 1;
            j -= 1;
        }
        if (bonus==1)sb.append(1);
        return sb.reverse().toString();
    }
```



## 难点

1. 循环跳出条件
2. 巧妙利用0占位



# [491. 递增子序列](https://leetcode-cn.com/problems/increasing-subsequences/)

给定一个整型数组, 你的任务是找到所有该数组的递增子序列，递增子序列的长度至少是2。

示例:

输入: [4, 6, 7, 7]
输出: [[4, 6], [4, 7], [4, 6, 7], [4, 6, 7, 7], [6, 7], [6, 7, 7], [7,7], [4,7,7]]
说明:

给定数组的长度不会超过15。
数组中的整数范围是 [-100,100]。
给定数组中可能包含重复数字，相等的数字应该被视为递增的一种情况。

## 分析



DFS中的path是怎么产生的？

初始path=[]，它有4种选择。

在选择了第0个元素后，它有后面的3种选择，继续选了3个里面的第一个元素之后，它有后面的2种选择，...；

在选择了第1个元素后，它有后面的2种选择，继续选择了2个里面的第一个元素之后，他有后面的1种选择，...；

这样形成path，一个一个的中间值。把中间值中符合条件的加入到result中，就是我们想要的结果。

![image-20200825174241103](.\images\image-20200825174241103.png)



## 代码

49 ms  beats 4%

```java
class Solution {
    Set<List<Integer>> resultList = new LinkedHashSet<>();

    public List<List<Integer>> findSubsequences(int[] nums) {
        List<Integer> path = new ArrayList<>();
        dfs_findSubsequences(nums, 0, path);
        System.out.println(resultList);
        return new ArrayList<>(resultList);
    }
    public void dfs_findSubsequences(int[] nums, int idx, List<Integer> path){
        if (path.size()>=2 && !resultList.contains(path)){
//            resultList.add(path); //这种写法，错误！因为在Java中这是引用类型。
            resultList.add(new ArrayList<>(path));
        }
        if (idx == nums.length){
            return;
        }

        for (int i = idx; i < nums.length; i++) {
            if (path.size()==0 || nums[i]>=path.get(path.size()-1)) {
                path.add(nums[i]);
                dfs_findSubsequences(nums, i + 1, path);
                path.remove(path.size() - 1);
            }
        }
    }
}
```

## 难点

+ <font color=red>DFS中，需要用for循环。（中间值有多种选择的都需要for循环）</font>

  当idx=0时，把第0个元素加入到path中，然后对它后面的元素进行相同的操作。

  当idx=1时，把第1个元素加入到path中，然后对它后面的元素进行相同的操作。

+ <font color=red>把某个元素加入到path中后，在对它后面的元素执行相同的操作完了之后，要把这个元素从path中取出来。这样才能退回到添加这个元素之前的状态，选择其它可选元素。</font>

+ 对于Java而言，List<>是引用对象，所以在把path加到result中的时候，不能使用resultList.add(path)，而应该基于path新建一个对象<font color=red>resultList.add(new ArrayList<>(path))</font>，这样才不会出错。



## 优化

去重版

15ms beats 40%

```java
class Solution {
    Set<List<Integer>> resultList = new LinkedHashSet<>();

    public List<List<Integer>> findSubsequences(int[] nums) {
        List<Integer> path = new ArrayList<>();
        dfs_findSubsequences(nums, 0, path);
        // System.out.println(resultList);
        return new ArrayList<>(resultList);
    }
    public void dfs_findSubsequences(int[] nums, int idx, List<Integer> path){
        Set<Integer> set = new HashSet<>();

        if (path.size()>=2 && !resultList.contains(path)){
//            resultList.add(path); //这种写法，错误！因为在Java中这是引用类型。
            resultList.add(new ArrayList<>(path));
        }
        if (idx == nums.length){
            return;
        }

        for (int i = idx; i < nums.length; i++) {
            if (set.contains(nums[i])) {
                continue;
            }
            set.add(nums[i]);
            if (path.size()==0 || nums[i]>=path.get(path.size()-1)) {
                path.add(nums[i]);
                dfs_findSubsequences(nums, i + 1, path);
                path.remove(path.size() - 1);
            }
        }
    }
}
```

# [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![image-20200826095834740](.\images\image-20200826095834740.png)

示例:

输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
说明:
尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。

## 分析

+ <font color=red>DFS 画出树形图，标注上path就好做了。</font>

+ 仍旧是注意，当可选状态为多个的时候，你选择了一个元素之后对剩下的元素执行dfs，那么你执行完了之后要<font color=red>退回到之前的状态</font>。

## 代码

1ms beats 88%

```java
class Solution {
    List<String> result_findSubsequences = new ArrayList<>();
    public List<String> letterCombinations(String digits) {
        HashMap<Integer, List<Character>> map = new HashMap<>();
        map.put(2, Arrays.asList('a','b','c'));
        map.put(3, Arrays.asList('d','e','f'));
        map.put(4, Arrays.asList('g','h','i'));
        map.put(5, Arrays.asList('j','k','l'));
        map.put(6, Arrays.asList('m','n','o'));
        map.put(7, Arrays.asList('p','q','r','s'));
        map.put(8, Arrays.asList('t','u','v'));
        map.put(9, Arrays.asList('w','x','y','z'));
        if (digits.length()==0) return new ArrayList<>();
        dfs_letterCombinations(digits, 0, new StringBuilder(), map);
        return result_findSubsequences;
    }
    public void dfs_letterCombinations(String s, int idx, StringBuilder sb, HashMap<Integer, List<Character>> map){
        if (idx==s.length()){
            result_findSubsequences.add(sb.toString());
            return;
        }
        int n = s.charAt(idx) - '0'; //数字
        List<Character> list = map.get(n); //它的list
        for (int i = 0; i < list.size(); i++) {
            sb.append(list.get(i));
            dfs_letterCombinations(s, idx+1, sb, map);
            sb.deleteCharAt(sb.length()-1);
        }
    }
}
```



## 难点

无。dfs典型问题。















