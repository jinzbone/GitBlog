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