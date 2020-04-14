# 取余+hashmap降低时间复杂度

在某些需要存储临时数据的时候，最常用的是用长度为 n 的数组来存储， 然后过程中不断的遍历这个数组，于是每次都需要遍历n次。此时，在某些特殊问题上，可以采用取余的方式，将长度为n降低为一个具体数字k， 降低了时间复杂度和空间复杂度。

leetcode第523题为例

[题目链接](https://leetcode-cn.com/problems/continuous-subarray-sum/)  

![](https://user-gold-cdn.xitu.io/2020/1/29/16ff1213a35d964d?w=630&h=400&f=png&s=69466)


## 普通方法

普通方法(暂时不考虑除数为0的情况)一个长度为n的数组来存储前缀和，每次需要判断每一个前缀和与当前数的加和是否满足条件，满足条件返回，否则更新前缀和。代码如下：

```java
public boolean checkSubarraySum(int[] nums, int k) {
        int[] sums = new int[nums.length];
        for(int i = 0; i < nums.length; i++) {
            for(int j = 0; j <= i - 1; j++) {
                if((sums[j] + nums[i]) % k == 0) return true;//满足条件跳出
                sums[j] += nums[i];//不满足条件，更新前缀和
            }
            sums[i] = nums[i];//一次循环之后，需要加上当前数
        }
        return false;//最终返回false

    }
```



## 取余 + hashmap

该方法和上述方法唯一区别是采用一个hashmap来存储前缀和，同时前缀和对k取余，这样的话，就不用存储n个，而是最大为k的一个hashmap。  
对于本题而言：
无论何时，只要 sum%k的值已经被放入 HashMap 中了，代表着有两个索引 i和 j，它们之间元素的和是 k的整数倍。因此，只要 HashMap 中有相同的 sum%k，我们就可以直接返回true

代码如下：
```
public boolean checkSubarraySum(int[] nums, int k) {
        int sum = 0;
        HashMap <Integer, Integer> map = new HashMap <> ();
        map.put(0, -1);
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            sum = sum % k;
            if (map.containsKey(sum)) {
                if (i - map.get(sum) > 1)
                    return true;
            } else
                map.put(sum, i);
        }
        return false;
    }
```