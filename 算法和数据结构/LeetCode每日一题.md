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



#  [657. 机器人能否返回原点](https://leetcode-cn.com/problems/robot-return-to-origin/)

在二维平面上，有一个机器人从原点 (0, 0) 开始。给出它的移动顺序，判断这个机器人在完成移动后是否在 (0, 0) 处结束。

移动顺序由字符串表示。字符 move[i] 表示其第 i 次移动。机器人的有效动作有 R（右），L（左），U（上）和 D（下）。如果机器人在完成所有动作后返回原点，则返回 true。否则，返回 false。

注意：机器人“面朝”的方向无关紧要。 “R” 将始终使机器人向右移动一次，“L” 将始终向左移动等。此外，假设每次移动机器人的移动幅度相同。

示例 1:

输入: "UD"
输出: true
解释：机器人向上移动一次，然后向下移动一次。所有动作都具有相同的幅度，因此它最终回到它开始的原点。因此，我们返回 true。

示例 2:

输入: "LL"
输出: false
解释：机器人向左移动两次。它最终位于原点的左侧，距原点有两次 “移动” 的距离。我们返回 false，因为它在移动结束时没有返回原点。

## 分析

简单题。

我们只需按指令模拟机器人移动的坐标即可。起始时机器人的坐标为 (0,0)，在遍历完所有指令并对机器人进行移动之后，判断机器人的坐标是否为 (0,0)即可。

具体来说，我们用两个变量 xx 和 yy 来表示机器人当前所在的坐标为 (x,y)，起始时 x=0，y=0。接下来我们遍历指令并更新机器人的坐标：

> 如果指令是 U，则令 `y=y-1`
> 如果指令是 D，则令 `y=y+1`
> 如果指令是 L，则令 `x=x-1`
> 如果指令是 R，则令 `x=x+1`
> 最后判断 `(x,y)`是否为 `(0,0)` 即可。

## 代码

```java
class Solution {
    public boolean judgeCircle(String moves) {
        int n = moves.length();
        if (n==0) return true;
        int x = 0, y = 0;
        for (int i = 0; i < n; i++) {
            char c = moves.charAt(i);
            if (c=='U') y += 1;
            else if (c=='D') y -= 1;
            else if (c=='L') x -= 1;
            else if (c=='R') x += 1;
        }
        if (x==0 && y==0) return true;
        return false;
    }
}
```



## 难点

无。



# [557. 反转字符串中的单词 III](https://leetcode-cn.com/problems/reverse-words-in-a-string-iii/)



给定一个字符串，你需要反转字符串中每个单词的字符顺序，同时仍保留空格和单词的初始顺序。

示例：

> 输入："Let's take LeetCode contest"
> 输出："s'teL ekat edoCteeL tsetnoc"


提示：

在字符串中，每个单词由单个空格分隔，并且字符串中不会有任何额外的空格。

## 分析

开辟一个新字符串。然后从头到尾遍历原字符串，直到找到空格为止，此时找到了一个单词，并能得到单词的起止位置。随后，根据单词的起止位置，可以将该单词逆序放到新字符串当中。如此循环多次，直到遍历完原字符串，就能得到翻转后的结果。

## 代码

7ms beats 60%

```java
class Solution {
    public String reverseWords(String s) {
        StringBuilder sb = new StringBuilder();
        int n = s.length();
        int i = 0;
        while (i<n){
            int start = i;
            while (i<n && s.charAt(i)!=' ') {
                i++;
            }

            for (int j = i-1; j >=start; j--) {
                sb.append(s.charAt(j));
            }
            if (i!=n){
                sb.append(' ');
                i ++;
            }
        }
        return sb.toString();
    }
}
```



## 难点

细节。

+ 注意while里面的第一个`while (i<n && s.charAt(i)!=' ')`前边有个i<n的判断，因为这个while循环里有i自增的操作。

+ 第2个for循环是对每个单词进行反转。

+ 第3个if是判断是否到了末尾，注意i是不为空格时跳出来的，因此最后是停在了下标为n的位置。



# [841. 钥匙和房间](https://leetcode-cn.com/problems/keys-and-rooms/)

有 N 个房间，开始时你位于 0 号房间。每个房间有不同的号码：0，1，2，...，N-1，并且房间里可能有一些钥匙能使你进入下一个房间。

在形式上，对于每个房间 i 都有一个钥匙列表 rooms[i]，每个钥匙 rooms[i][j] 由 [0,1，...，N-1] 中的一个整数表示，其中 N = rooms.length。 钥匙 rooms[i][j] = v 可以打开编号为 v 的房间。

最初，除 0 号房间外的其余所有房间都被锁住。

你可以自由地在房间之间来回走动。

如果能进入每个房间返回 true，否则返回 false。

示例 1：

> 输入: [[1],[2],[3],[]]
> 输出: true
> 解释:  
> 我们从 0 号房间开始，拿到钥匙 1。
> 之后我们去 1 号房间，拿到钥匙 2。
> 然后我们去 2 号房间，拿到钥匙 3。
> 最后我们去了 3 号房间。
> 由于我们能够进入每个房间，我们返回 true。

示例 2：

> 输入：[[1,3],[3,0,1],[2],[0]]
> 输出：false
> 解释：我们不能进入 2 号房间。
> 提示：

`1 <= rooms.length <= 1000`
`0 <= rooms[i].length <= 1000`
所有房间中的钥匙数量总计不超过 3000。

## 分析

dfs经典问题，画出路径树图。

## 代码

93ms beats 5%

```java
class Solution {
    boolean flag = false;
    public boolean canVisitAllRooms(List<List<Integer>> rooms) {
        List<Integer> path = new ArrayList<>();
        path.add(0);
        dfs_canVisitAllRooms(rooms, 0, path);
        return flag;
    }
    public void dfs_canVisitAllRooms(List<List<Integer>> rooms, int roomId, List<Integer> path){
        boolean temp = true;
        for (int i = 0; i < rooms.size(); i++) {
            if (!path.contains(i)){
                temp=false;
                break;
            }
        }
        if (temp){
            flag=true;
            return;
        }
        List<Integer> room = rooms.get(roomId);
        for (int i = 0; i < room.size(); i++) {
            if (!path.contains(room.get(i))){
                path.add(room.get(i));
                dfs_canVisitAllRooms(rooms, room.get(i), path);
            }
        }
    }
}
```

## 难点

细节。在dfs()中调用`dfs(rooms, room.get(i), path)`时，这时由于我们是自始至终都要改变path的（而不是像LeetCode 491. 递增子序列中要记录下path的各个状态所以使用了`new ArrayList<>(path)`)，因此在本题里是<font color=red>直接用path，而不能new！</font>

