# [28. 实现 strStr()](https://leetcode-cn.com/problems/implement-strstr/)

实现 strStr() 函数。

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。

示例 1:

> 输入: haystack = "hello", needle = "ll"
> 输出: 2

示例 2:

> 输入: haystack = "aaaaa", needle = "bba"
> 输出: -1



## 分析

**方法一：子串逐一比较 - 线性时间复杂度**

最直接的方法 - 沿着字符换逐步移动滑动窗口，将窗口内的子串与 needle 字符串比较。

![image-20200829112915880](./images/strStr.png)



**方法二：双指针 - 线性时间复杂度**
上一个方法的缺陷是会将 haystack 所有长度为 L 的子串都与 needle 字符串比较，实际上是不需要这么做的。

首先，只有子串的第一个字符跟 needle 字符串第一个字符相同的时候才需要比较。

![image-20200829113011591](./images/strStr-2.png)

其次，可以一个字符一个字符比较，一旦不匹配了就立刻终止。      

​        ![image-20200829113221822](./images/strStr-3.png)

## 代码

3ms  beats 40 % 

```java
class Solution {
    public int strStr(String haystack, String needle) {
        if (needle.equals(""))return 0;
        int m = haystack.length(), n = needle.length();
        if (m < n) return -1;
        for (int i = 0; i < m; i++) {
            if (haystack.charAt(i)==needle.charAt(0) && m-i>=n){
                boolean flag = true;
                for (int j = 1; j < n; j++) {
                    if (haystack.charAt(i+j)!=needle.charAt(j)){
                        flag = false;
                        break;
                    }
                }
                if (flag==true)return i;
            }
        }
        return -1;
    }
}
```



## 难点



## KMP优化

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