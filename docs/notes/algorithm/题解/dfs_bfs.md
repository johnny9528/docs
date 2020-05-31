# dfs&bfs

## 22 括号生成

### 描述

[题目链接](https://leetcode-cn.com/problems/generate-parentheses/)

```
数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。
示例：
输入：n = 3
输出：[
       "((()))",
       "(()())",
       "(())()",
       "()(())",
       "()()()"
     ]
```

### 分析

方法1：暴力法， 利用递归获得所有的组合， 然后判断是否合法

方法2： 确保每次增加的都是合法的，只需要考虑条件：当前已经加入的右括号一定不大于左括号。

### 实现

#### 暴力法

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        if(n <= 0) return res;
        dfs(new char[2 * n], 0, res);
        return res;

        }
    public void dfs(char[] current, int number, List<String> res) {
        if(number == current.length) {
            if(isValid(current)) res.add(new String(current));
        }else {
            current[number] = '(';
            dfs(current, number + 1, res);
            current[number] = ')';
            dfs(current, number + 1, res);
        }
    }
    public boolean isValid(char[] current) {
        int balance = 0;
        for(char c : current) {
            if(c == '(') {
                balance += 1;
            }else {
                balance -= 1;
            }
            if(balance < 0) return false;
        }
        return balance == 0;
    }
}
```

#### dfs

```java
class Solution {
    public List<String> generateParenthesis(int n) {
        List<String> res = new ArrayList<>();
        if(n <= 0) return res;
        dfs("", n, n, res);
        return res;
    }
    public void dfs(String current, int leftSize, int rightSize, List<String> res) {
        //递归出口条件：左括号和右括号的剩余都为0，即全部加进去
        if(leftSize == 0 && rightSize == 0) {
            res.add(current);
        }else {
          
          	//左括号只要还有剩余， 就可以添加.注意res可能已经被修改了， 所以相当于全局的res。注意这个细节
            if(leftSize > 0) {
                dfs(current + '(', leftSize - 1, rightSize, res);
            }
          	//右括号的剩余数量一定要大于左括号
            if(rightSize > leftSize) {
                dfs(current + ')', leftSize, rightSize - 1, res);
            }
        }
    }
}
```







