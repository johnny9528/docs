# 背包问题

## 1. 0-1背包问题

### 描述

```
有 N 件物品和一个容量是 V 的背包。每件物品只能使用一次。

第 i 件物品的体积是 vi，价值是 wi。

求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。
输出最大价值。
```

### 分析

设`dp[i][j]` 是前`i`件物品，总容量不超过`j`的最大收益。那么有：

* `i=0`， 没有任何物品，也就没有任何收益
* `j=0` ，背包什么都放不下， 没有任何收益

已经计算出了 `i-1`个物品的所有最大收益之后，有：

* 不选择第`i`个物品，那么有最大收益`dp[i][j]=dp[i-1][j]`, 或者第i个物品的体积已经大于 `j`了，也不能选择这个物品
* 选择第`i`个物品，会占据 `vi`的体积， 因此收益是 `dp[i][j]=dp[i-1][j-vi] + wi`

### 实现

```java
    /**
     * 0-1背包问题， n种物体，最多选择一次(要么选，要么不选)，求放入体积位v的背包的最大收益
     * @param n  物体的种类
     * @param v  背包的体积
     * @param weight n种物品占的体积的数组
     * @param value 每种物品的价值
     *
     * @return  最大收益
     */
    private int backpack01(int n, int v, int[] weight, int[] value) {
        int[][] dp = new int[n + 1][v + 1]; // dp[i][j] 代表前i种物品，最大体积位j的最大收益

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= v; j++) {
                int cost = weight[i - 1];
                if (cost > j) {
                    // 重量已经超了当前的最大体积
                    continue;
                }
                dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - cost] + value[i - 1]);
            }
        }
        return dp[n][v];
    }
```

### 优化

空间复杂度可以优化，由于后一行，只会和前一行有关系，因此一种思路是，将二维`dp`数组，优化为两个一维数组， 一个代表前一行，一个代表当前行。

或者， 根据转移表达式，可以发现，`dp[i][j]`只会与`dp[i]`这一行种小于`j`的索引有关系。因此，如果从后往前遍历，在没有修改`j`之前元素的情况下，就可以直接使用1个一维数组表示。

`dp[j]`代表的是容量不超过`j`的最大收益。因此有

```java
    private int backpack01_2(int n, int v, int[] weight, int[] value) {
        int[] dp = new int[v + 1]; // dp[i][j] 代表前i种物品，最大体积位j的最大收益

        for (int i = 1; i <= n; i++) {
            int cost = weight[i - 1];  // 当前第 i个物品的体积
            for (int j = v; j >= cost; j--) { // 从后往前， 从v 到 cost(因为cost必须小于j,否则装不下)
                dp[j] = Math.max(dp[j], dp[j - cost] + value[i - 1]);
                // 第一个 dp[j] 其实就是dp[i-1][j]， 是上一次算出来的dp[j]
                // dp[j-cost]， 其实就是dp[i-1][j-cost]， 是上一次算出来的dp[j-cost]
                // 因为是从后往前，之一轮在j之前并不会覆盖dp[j-cost]
            }
        }
        return dp[v];
    }
```



## 2. 0-1背包问题扩展问题

```
如果把条件限制到， 一定要把背包装满，不能只装一部分，求此时的最大收益
```

### 分析

0-1背包问题种的`dp[i][j]`是 前`i`个物品，体积不超过`j`的最大收益，即不一定要装满背包。那么此处，就要定义为 前`i`个物品，装满`j`的最大收益。大致思路一致，但是初始化的时候有区别。

比如 0-1背包种，`dp[1][10]`代表第1个物品， 体积不超过10的最大收益，比如 w有2，那么`dp[1][10]`实际上等于`dp[0][8] + v2`,

初始化的时候，`dp[0][8]`实际上初始化为了0

那么此种情况下，将不允许这种情况，因此就必须把`dp[0][t]`初始化为 负无穷，相当于断了这条通路， 只有`dp[0][0]=0`, 其他步骤完全一致。

因此，初始化的时候，除了`dp[row][0]=0`外，其他都得等于负无穷

### 实现

```java
    private int backpack01_3(int n, int v, int[] weight, int[] value) {
        int[][] dp = new int[n + 1][v + 1]; // dp[i][j] 代表前i种物品，最大体积位j的最大收益
        // 初始化
        for (int[] eachRow : dp) {
            Arrays.fill(eachRow, Integer.MIN_VALUE);
        }
        for (int i = 0; i <= n; i++) {
            dp[i][0] = 0;
        }
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= v; j++) {
                int cost = weight[i - 1];
                if (cost > j) {
                    // 重量已经超了当前的最大体积
                    dp[i][j] = dp[i - 1][j];
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - cost] + value[i - 1]);

                }
            }
        }

        for (int[] temp : dp) {
            System.out.println(Arrays.toString(temp));
        }
        return dp[n][v];
    }
```

​	



## 3. 完全背包问题

### 描述

```
有 N 种物品和一个容量是 V 的背包，每种物品都有无限件可用。

第 i 种物品的体积是 vi，价值是 wi。

求解将哪些物品装入背包，可使这些物品的总体积不超过背包容量，且总价值最大。
输出最大价值。
```

### 分析

0-1背包问题是能选择0次或者1次，完全背包问题是可以选择，0，1，2...任意次

设`dp[i][j]` 是前`i`件物品，总容量不超过`j`的最大收益。那么有：

* `i=0`， 没有任何物品，也就没有任何收益
* `j=0` ，背包什么都放不下， 没有任何收益

已经计算出了 `i-1`个物品的所有最大收益之后，有：

`dp[i][j] = dp[i-1][j](不选择)、dp[i-1][j-vi] + wi(选择一次)、dp[i-1][j - 2 * vi] + 2 * wi(选择两次)、...... `

最大收益是上面的最大值，当然，选择也不是任意次，不能超过背包总容量的次数

### 实现

```java
    /**
     *  完全背包问题， 每种物品可以无限次的选取
     * @param n  物体的种类
     * @param v  背包的体积
     * @param weight n种物品占的体积的数组
     * @param value 每种物品的价值
     *
     * @return  最大收益
     */
    private int backpack02_1(int n, int v, int[] weight, int[] value) {
        int[][] dp = new int[n + 1][v + 1]; // dp[i][j] 代表前i种物品，最大体积位j的最大收益
        for (int i = 1; i <= n; i++) {
            int cost = weight[i - 1];  // 当前物品的体积
            int profile = value[i - 1]; // 当前物品的收益
            for (int j = 1; j <= v; j++) {
                for (int k = 0; k * cost <= j; k++) {
                    // 个数选择从0到任意
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - k * cost] + k * profile);
                }
            }
        }
        return dp[n][v];
    }
```

### 优化

仍然是对空间复杂度的优化。和0-1背包思路很类似。0-1背包是从后往前，此处从前往后即可。

因为比如 现在要求本轮`dp[10]`的最大收益， 比如说`wi=3`, 那么，那么如果从前往后的话，一定是计算好了`dp[7]`的结果，`dp[7]`代表的是， 可能已经取了`t`个`i`物品了， 那么要计算`dp[10]`， 和0-1背包类似，我要么再取一个`i`物品，要么不取。因此有

```
    private int backpack02_2(int n, int v, int[] weight, int[] value) {
        int[] dp = new int[v + 1]; // dp[j] 代表当前轮， 最大体积位j的最大收益

        for (int i = 1; i <= n; i++) {
            int cost = weight[i - 1];
            int profile = value[i - 1];
            for (int j = cost; j <= v; j++) {
            	// 从前往后
                dp[j] = Math.max(dp[j], dp[j - cost] + profile);
            }
        }
        return dp[v];
    }
```



## 4. 多重背包问题

### 描述

```
有 N 种物品和一个容量是 V 的背包。

第 i 种物品最多有 si 件，每件体积是 vi，价值是 wi。

求解将哪些物品装入背包，可使物品体积总和不超过背包容量，且价值总和最大。
输出最大价值。
```

### 分析

基本过程和完全背包问题(没有优化的版本)以模一样, 仅仅是个数的选择有限制而已，加一个限制条件即可

### 实现

```java
    /**
     * 多重背包问题
     * 
     * @param n  物体的种类
     * @param v  背包的体积
     * @param weight n种物品占的体积的数组
     * @param value 每种物品的价值
     * @param counts 每种物品的个数
     * 
     * @return
     */
    private int multiplyBackpack_1(int n, int v, int[] weight, int[] value, int[] counts) {
        int[][] dp = new int[n + 1][v + 1]; // dp[i][j] 代表前i种物品，最大体积位j的最大收益
        for (int i = 1; i <= n; i++) {
            int cost = weight[i - 1];  // 当前物品的体积
            int profile = value[i - 1]; // 当前物品的收益
            int maxCount = counts[i - 1];
            for (int j = 1; j <= v; j++) {
                for (int k = 0; k * cost <= j && k <= maxCount; k++) {
                    // 个数选择从0到最大个数， 和完全背包问题，就只有这儿不同
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - k * cost] + k * profile);
                }
            }
        }
        return dp[n][v];
    }
```

### 优化

讲一下大致思路， 多重背包问题，可以延展成为0-1背包问题。 比如，`i`物品有 15个， 价值为4，那么就相当于15个 价值为4的物品而已。

如果把15延展为15个1的话，其实也没意义。

如果把15分成 1， 2， 4， 8， 那么你想要任意个物品`i`， 1， 2， 4， 8都能组合起来， 比如9， 就是8 + 1而已。因此将15个i物品分成

1个  `4(1 * 4)`， 1个`8(2 * 4)`， 1个`16(4 * 4)`， 1个`32(8 * 4)`， 即一个价值为4的物品，一个价值为8的物品， 一个价值为16的物品，一个价值为32的物品即可。空间复杂度指数级下降。

15恰好是 2的4次方减1. 那么如果是13这种，就分为 1， 2， 4， 6这种即可



## 5. 二维费用的背包问题

### 描述

```
有 N 件物品和一个容量是 V 的背包，背包能承受的最大重量是 M。

每件物品只能用一次。体积是 vi，重量是 mi，价值是 wi。

求解将哪些物品装入背包，可使物品总体积不超过背包容量，总重量不超过背包可承受的最大重量，且价值总和最大。
输出最大价值
```

### 分析

仅仅是增加一个重量即可，加一维即可，思路和过程基本上同理.

优化思路也是一致的。

### 实现

```java
    /**
     * 二维背包问题

     */
    private int twoDimenBackpack(int n, int v, int w, int[] weight, int[] volume, int[] value) {
        int[][][] dp = new int[n + 1][v + 1][w + 1];
        
        for (int i = 1; i <= n; i++) {
            int weightCost = weight[i - 1];
            int volumeCost = volume[i - 1];
            int profile = value[i - 1];
            
            for (int j = 1; j <= v; j++) {
                for (int k = 1; k <= w; k++) {
                    if (weightCost > j || volumeCost > k) {
                        dp[i][j][k] = dp[i - 1][j][k];
                    } else {
                        dp[i][j][k] = Math.max(dp[i - 1][j][k], dp[i - 1][j - weightCost][k - volumeCost] + profile);
                    }
                }
            }
        }
        return dp[n][v][w];
    }
```



## 6. 求满足最大收益的方案总数

### 描述

```
基于之前的，现在不求最大收益，求最大收益的方案数量
```

### 分析

基本思路不变，多构造一个数组，用于存放方案数即可

`dp2[i][j]`表示前i个，体积不超过`j`的方案数

但需要注意的是初始化， 当什么都不选的时候，也是一种方案，因此`dp2[0][t]`需要设置为`1`

### 实现

```java
    /**
     * 最大收益的方案数
     */
    private int howManyWays(int n, int v, int[] weight, int[] value) {
        int[][] dp = new int[n + 1][v + 1];
        int[][] counts = new int[n + 1][v + 1];

        Arrays.fill(counts[0], 1);// 不选择任何东西，是一种方案
        for (int i = 1; i <= n; i++) {
            int cost = weight[i - 1];
            int profile = value[i - 1];
            for (int j = 1; j <= v; j++) {
                if (cost > j) {
                    //不能放进去
                    dp[i][j] = dp[i - 1][j];
                    counts[i][j] = counts[i - 1][j];
                } else {
                    // 选择或者不选择，两种方式
                    int way1 = dp[i - 1][j]; // 不选
                    int way2 = dp[i - 1][j - cost] + profile; // 选择

                    dp[i][j] = Math.max(way1, way2); // 更新收益

                    if (way1 == way2) {
                        // 两种都可以
                        counts[i][j] = counts[i - 1][j] + counts[i - 1][j - cost];
                    } else if (way1 > way2) {
                        // 选择 不选的方案
                        counts[i][j] = counts[i - 1][j];
                    } else {
                        counts[i][j] = counts[i - 1][j - cost];
                    }
                }
            }
        }
        return counts[n][v];
    }
```



