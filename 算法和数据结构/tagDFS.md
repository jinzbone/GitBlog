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

# [257. 二叉树的所有路径](https://leetcode-cn.com/problems/binary-tree-paths/)

给定一个二叉树，返回所有从根节点到叶子节点的路径。

说明: 叶子节点是指没有子节点的节点。

示例:

```markdown
输入:

   1
 /   \
2     3
 \
  5

输出: ["1->2->5", "1->3"]

解释: 所有根节点到叶子节点的路径为: 1->2->5, 1->3
```

## 分析

dfs经典问题。

出口是：到达叶子结点->把叶子结点的值加到path中，并把path加到result中，return

## 代码

2ms beats 98%

```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> result = new ArrayList<>();
        dfs_binaryTreePaths(result, root, new StringBuilder(""));
        return result;
    }
    public void dfs_binaryTreePaths(List<String> result, TreeNode root, StringBuilder path){
        if (root == null) return;
        path.append(root.val);
        if (root.left == null && root.right == null) {
            result.add(path.toString());
            return;
        }
        path.append("->");
        if (root.left != null) {
            dfs_binaryTreePaths(result, root.left, new StringBuilder(path));
        }
        if (root.right != null) {
            dfs_binaryTreePaths(result, root.right, new StringBuilder(path));
        }
    }
}
```

## 难点

<font color = red>出口是：到达叶子结点。</font>

# [46. 全排列](https://leetcode-cn.com/problems/permutations/)

给定一个 没有重复 数字的序列，返回其所有可能的全排列。

示例:

> 输入: [1,2,3]
> 输出:
> [
>   [1,2,3],
>   [1,3,2],
>   [2,1,3],
>   [2,3,1],
>   [3,1,2],
>   [3,2,1]
> ]

## 分析

dfs经典问题。

需要用result保存全部的时候，注意保存现场，并在下一次的dfs之后，恢复状态。

## 代码

3ms beats 50%

```java
class Solution {
    List<List<Integer>> result_permute = new ArrayList<>();
    public List<List<Integer>> permute(int[] nums) {
        List<Integer> path = new ArrayList<>();
        dfs_permute(nums, path);
        return result_permute;
    }
    public void dfs_permute(int[] nums, List<Integer> path){
        if (path.size() == nums.length){
            result_permute.add(path);
            return;
        }
        for (int i = 0; i < nums.length ; i++) {
            if (!path.contains(nums[i])){
                path.add(nums[i]);
                dfs_permute(nums, new ArrayList<>(path));
                path.remove(path.size()-1);
            }
        }
    }
}
```

## 难点

