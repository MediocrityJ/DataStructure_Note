# 闫氏DP分析法——从集合角度思考DP问题

DP问题，本质上是求有限集合中的最值（集合中元素数量一般是指数级别）

一般为求集合中的最小值、最大值、数量或者存不存在

- 为什么用`dp`可以求解？

  > `dp`中的每个状态表示`f[i]`，不是单个元素，而是一个表示满足某个特定状态的**集合**，因此就不用枚举集合中的每一个元素了，而这个状态值，通常代表这个集合的**属性**，属性一般为`max、min、count、isexist`

- `dp`的步骤

  1. 状态表示（化零为整）

     - 集合
     - 属性（一般题目问的是什么，就是什么）

  2. 状态计算（化整为零）

     - 集合的划分

     - 子集通常满足两个性质
       - 不重复（不一定要满足，求数量的时候要满足，但如求极值的时候就不一定）
       - 不遗漏
     - 集合划分的依据
       - 寻找最后一个不同点
  
- `dp`数组下标从0还是从1开始的问题

  - 一般看转移方程中是否有`dp[i - 1]`，有的话为了简化边界，都会从1开始

## 1. 背包问题（选择问题）

- 选择问题的状态表示

  通常第一维为物品，后面的维度是限制，比如说体积的限制、重量的限制、可选择个数的限制等等

- ```c++
  // 下面的v都是Vi的简写，即第i个物品的体积
  // 0-1背包     f[i][j] = max(f[i - 1][j], f[i - 1][j - v] + w);
  // 完全背包     f[i][j] = max(f[i - 1][j], f[i][j - v] + w);
  // 多重背包		f[i][j] = max(f[i - 1][j - v] + w);
  ```
- 背包问题里初始化跟状态表示有关，体积<=j，==j初始化是不一样的

### 1.1 0-1背包

- **状态转移方程**

  F[i, j]只能被划分为两个子集，选择第i个物品，或者不选择。

  其中**F[i - 1,v - C<sub>i</sub>] + W<sub>i</sub>**，即选择第 i 件物品的情况下，需要判断`V[i]`是否小于当前体积，即**v - C<sub>i</sub>**不能为负。

<img src="DP方法总结.assets/image-20210424193409060.png" alt="image-20210424193409060" style="zoom:60%;" />

- 空间优化

  优化掉物品一维，只留下限制，但是**限制（即当前的这个维度）的遍历要从后往前遍历**

  <img src="DP方法总结.assets/image-20210424193815716.png" alt="image-20210424193815716" style="zoom:60%;" />

- 优化后的主体代码

  ```C++
  // n为物品数，m为体积
  for(int i = 1; i <= n; i++)
      for(int j = m; j >= v[i]; j--)  
          f[j] = max(f[j], f[j - v[i]] + w[i]);
  ```

### 1.2 完全背包

- 朴素思想的状态转移方程

  <img src="DP方法总结.assets/image-20210424202641432.png" alt="image-20210424202641432" style="zoom:60%;" />

  这里是将`F[i, v]`划分成了k个子集，不是0-1背包中仅仅分成两个子集就行了，第i件物品可以选0件、1件、……k件，直到kC<sub>i</sub> > v。

  如果就按这个状态转移方程，要三层循环

- **优化后的状态方程**

  <img src="DP方法总结.assets/image-20210424203124653.png" alt="image-20210424203124653" style="zoom:60%;" />

  与**0-1背包唯一的不同**就是右边等式第二项中前面是i，不是i-1，因此空间优化的时候要从前往后遍历

  **推导过程**：

  F[i, v] = max{F[i - 1, v], F[i - 1, v - C<sub>i</sub>] + W<sub>i</sub>, F[i - 1, v - 2C<sub>i</sub>] + 2W<sub>i</sub>, ……} (1)

  F[i, v - C<sub>i</sub>] = max{		  F[i - 1, v - C<sub>i</sub>], 		 F[i - 1, v - 2C<sub>i</sub>] + Wi, 	F[i - 1, v - 3C<sub>i</sub>] + 2W<sub>i</sub>, ……} (2)

  将(2)式加上一个W<sub>i</sub>，则(1)式中等式右边除了第一项F[i - 1, v]之外，后面的式子就等于(2)式+W<sub>i</sub>，即得到优化后的状态方程

  > (2)式的体积比(1)式少了一个C<sub>i</sub>，因此最后(2)式的多项式个数刚好比(1)式少了一个，即后面的刚好对齐

- 空间再优化

  从前往后遍历

  <img src="DP方法总结.assets/image-20210424204649978.png" alt="image-20210424204649978" style="zoom:60%;" />

- 最终主体代码

  ```c++
  // n为物品数，m为体积
  // 注意下面两层循环的次序可以颠倒
  for(int i = 1; i <= n; i++)
      for(int j = v[i]; j <= m; j++)  
          f[j] = max(f[j], f[j - v[i]] + w[i]);
  ```

  

### 1.3 LC相关题目

#### 1.3.1 背包问题

##### (1) 0-1背包

###### Acwing-2 01背包问题

- 基本

```java
import java.util.*;

class Main{
    static int n, m;
    static int[][] dp;
    static int[] w;
    static int[] v;
    public static void main(String[] args) {
        Scanner input = new Scanner (System.in);
        n = input.nextInt();
        m = input.nextInt();
        v = new int[n + 1];
        w = new int[n + 1];
        dp = new int[n + 1][m + 1];
        for (int i = 1; i <= n; i++) {
            v[i] = input.nextInt();
            w[i] = input.nextInt();
        }
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j <= m; j++) {
                dp[i][j] = dp[i - 1][j];
                if (j >= v[i]) dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - v[i]] + w[i]);
            }
        }
        System.out.println(dp[n][m]);
    }
}
```

- 优化

```java
import java.util.*;

class Main{
    static int n, m;
    static int[] dp;
    static int[] w;
    static int[] v;
    public static void main(String[] args) {
        Scanner input = new Scanner (System.in);
        n = input.nextInt();
        m = input.nextInt();
        v = new int[n + 1];
        w = new int[n + 1];
        dp = new int[m + 1];
        for (int i = 1; i <= n; i++) {
            v[i] = input.nextInt();
            w[i] = input.nextInt();
        }
        for (int i = 1; i <= n; i++) {
            for (int j = m; j >= v[i]; j--) {
                dp[j] = Math.max(dp[j], dp[j - v[i]] + w[i]);
            }
        }
        System.out.println(dp[m]);
    }
}
```

###### 416. 分割等和子集



###### 474. 一和零



###### 494. 目标和



##### (2) 完全背包

###### AcWing-3 完全背包问题

- 朴素做法

```java
import java.util.*;

class Main{
    static int n, m;
    static int[][] dp;
    static int[] w;
    static int[] v;
    public static void main(String[] args) {
        Scanner input = new Scanner (System.in);
        n = input.nextInt();
        m = input.nextInt();
        v = new int[n + 1];
        w = new int[n + 1];
        dp = new int[n + 1][m + 1];
        for (int i = 1; i <= n; i++) {
            v[i] = input.nextInt();
            w[i] = input.nextInt();
        }
        for (int i = 1; i <= n; i++)
            for (int j = 0; j <= m; j++)
                for (int k = 0; k * v[i] <= j; k++) 
                    dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - v[i] * k] + k * w[i]);
        System.out.println(dp[n][m]);
    }
}
```

- 优化

```java
import java.util.*;

class Main{
    static int n, m;
    static int[][] dp;
    static int[] w;
    static int[] v;
    public static void main(String[] args) {
        Scanner input = new Scanner (System.in);
        n = input.nextInt();
        m = input.nextInt();
        v = new int[n + 1];
        w = new int[n + 1];
        dp = new int[n + 1][m + 1];
        for (int i = 1; i <= n; i++) {
            v[i] = input.nextInt();
            w[i] = input.nextInt();
        }
        for (int i = 1; i <= n; i++)
            for (int j = 0; j <= m; j++){
                dp[i][j] = dp[i - 1][j];
                if (j >= v[i]) dp[i][j] = Math.max(dp[i][j], dp[i][j - v[i]] + w[i]);
            }
        System.out.println(dp[n][m]);
    }
}
```

- 最终优化

```java
import java.util.*;

class Main{
    static int n, m;
    static int[] dp;
    static int[] w;
    static int[] v;
    public static void main(String[] args) {
        Scanner input = new Scanner (System.in);
        n = input.nextInt();
        m = input.nextInt();
        v = new int[n + 1];
        w = new int[n + 1];
        dp = new int[m + 1];
        for (int i = 1; i <= n; i++) {
            v[i] = input.nextInt();
            w[i] = input.nextInt();
        }
        for (int i = 1; i <= n; i++) {
            for (int j = v[i]; j <= m; j++) {
                dp[j] = Math.max(dp[j], dp[j - v[i]] + w[i]);
            }
        }
        System.out.println(dp[m]);
    }
}
```

###### 322. 零钱兑换



###### 518. 零钱兑换 II





###### 139. 单词拆分

```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        int n = s.length();
        boolean[] dp = new boolean[n + 1];
        dp[0] = true;
        for (int i = 1; i <= n; i++) {
            for (String str : wordDict) {
                if (str.length() <= i && s.substring(i - str.length(), i).equals(str)){
                    // dp[i] |= dp[i - str.length()];
                    dp[i] = dp[i] || dp[i - str.length()];
                }
            }
        }
        return dp[n];
    }
}
```



###### 377. 组合总和 IV

与完全背包类似，但不同

>  给你一个由 **不同** 整数组成的数组 `nums` ，和一个目标整数 `target` 。请你从 `nums` 中找出并返回总和为 `target` 的元素组合的个数。请注意，顺序不同的序列被视作不同的组合。（`nums`中数字都为正数）
>
> 如果给定的数组中含有负数会发生什么？问题会产生何种变化？如果允许负数出现，需要向题目中添加哪些限制条件？

- 状态转移方程

  <img src="DP方法总结.assets/image-20210424235318625.png" alt="image-20210424235318625" style="zoom:67%;" />

  ```java
  class Solution {
      public int combinationSum4(int[] nums, int t) {
          // 因为 nums[i] 最小值为 1，因此构成答案的最大长度为 target
          int len = t;
          int[][] f = new int[len + 1][t + 1];
          f[0][0] = 1;
          int ans = 0;
          for (int i = 1; i <= len; i++) {
              for (int j = 0; j <= t; j++) {
                  for (int u : nums) {
                      if (j >= u) f[i][j] += f[i - 1][j - u];
                  }
              }
              ans += f[i][t];
          }
          return ans;
      }
  }
  ```

- 优化

  <img src="DP方法总结.assets/image-20210425003545187.png" alt="image-20210425003545187" style="zoom:67%;" />

```java
class Solution {
    public int combinationSum4(int[] nums, int target) {
        int[] dp = new int[target + 1];
        // dp[k]代表目标整数为k时的组合个数
        // 如果nums数组中有一个数，使得该数等于dp[k]代表的k这个组合数值，那么dp[k] += dp[0]，此时应该是+1，所以dp[0] = 1
        // 另一种判断dp[0] = 1的思路，dp[0]代表什么都不选，凑成总和为 0 的方案数就为 1
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            for (int num : nums) {
                if (i >= num) {
                    dp[i] += dp[i - num];
                }
            }
        }
        return dp[target];
    }
}
```

- 进阶

  > 如果存在负权值，答案可能会有无穷个。因为本身数值能够被选无限次，一旦存在负权，我们可以构造多个总和为 0 的方案，然后在此方案上构造出 `target`。
  >
  > 例如，考虑如下数据：`nums = [-1,1,2] `, `target = 2`，我们构造出无数个总和为 0 的方案：
  >
  > [-1,1]
  > [-1,1,-1,1]
  > [-1,1,-1,1,-1,1]
  > ...
  > 然后在这些方案的基础上，构造出`target`。
  >
  > 因此，如果允许出现负权，需要增加选择数量的限制。
  >
  > 可以考虑在二维解决方案的基础上去做，因为本质是一个「图论的有限步数到达具体节点」的问题，当我们期望从状态`dp[0][0]`到达`dp[m][n]`，但是中间存在总权值为 0 的环，那么我们可以通过进入无数次这样的环，来构成无限种方案。因此直接限制进环次数，或者增加总步数限制，就能从无限集合中解脱出来。
  >



##### (3) 多重背包 (0-1扩展)

即每个物品有数量限制`s[i]`

###### Acwing-4 多重背包问题

- 朴素做法

  - 状态转移方程和完全背包的朴素做法差不多

    F [i, j] = max(F[i - 1, j - v[i] * k] + w[i] * k)，k从0到s[i]

  ```java
  import java.util.*;
  
  class Main{
      static int n, m;
      static int[][] dp;
      static int[] w;
      static int[] v;
      static int[] s;
      public static void main(String[] args) {
          Scanner input = new Scanner (System.in);
          n = input.nextInt();
          m = input.nextInt();
          v = new int[n + 1];
          w = new int[n + 1];
          s = new int[n + 1];
          dp = new int[n + 1][m + 1];
          for (int i = 1; i <= n; i++) {
              v[i] = input.nextInt();
              w[i] = input.nextInt();
              s[i] = input.nextInt();
          }
          for (int i = 1; i <= n; i++)
              for (int j = 0; j <= m; j++)
                  for (int k = 0; k * v[i] <= j && k <= s[i]; k++) 
                      dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - v[i] * k] + k * w[i]);
          System.out.println(dp[n][m]);
      }
  }
  ```

- 无法用完全背包的思路优化——类比完全背包的(1)(2)式，(2)式最后多了一项`f[i-1,j-(s+1)v]+sw`

  ![image-20210425180027312](DP方法总结.assets/image-20210425180027312.png)

- 二进制的优化方式

  将枚举每个s的做法优化成枚举logS

  将s[i]以二进制拆分成不同的个数，即1,2,4,8……，这些个数的物品等价于组成了一个新的物品，并且这些新物品的价值从进制角度来讲可以覆盖掉原本所有的s[i]。时间复杂度从O(nms)优化到O(nm`logs`)

  将多重背包问题就优化成了一个新的等价的0-1背包问题

  每个物品按二进制拆分成多个物品

  ```java
  import java.util.*;
  
  
  class Main{
      static int n, m;
      static int[][] dp;
      static int[] w;
      static int[] v;
      static int[] s;
      // 如何确定len的长度
      // N * logS
      static final int len = 11010;
      public static void main(String[] args) {
          Scanner input = new Scanner (System.in);
          n = input.nextInt();
          m = input.nextInt();
          // 因为s的个数，即物品限定的个数可能很大，比如说设了个2000，那么二进制划分后是可能比物品个数要大的，因此要设大一点
          // 2000划分成二进制的话，到512为止，能组成的和为1023，剩下977，即1,2,4,8,16,32,64,128,256,512,977,共11个
          // N * logs得到len的长度，1000个物品，每个物品可以拆分成11个二进制表示下不同价值的物品，因此新物品的总数就可以算出来
          v = new int[len];
          w = new int[len];
          dp = new int[len][m + 1];
  
          // cnt的值最后可能是很大的，最大可以达到N*logS
          int cnt = 0;
  
          for (int i = 1; i <= n; i++) {
              int a,b,s;
              // a 为单位体积
              // b 为单位价值
              a = input.nextInt();
              b = input.nextInt();
              s = input.nextInt();
              int k = 1;
              // 注意下面的s是会变的！代表的是按二进制划分后剩下的数量，之前一直以为s是不变的，写成了while(k * 2 <= s)
              while (k <= s) {    //若s=2000，k为1,2,4……,512，while共循环10次，错错错，s是会变小的！当k为1024时，1024 <= 977不成立
                  cnt++;
                  v[cnt] = a * k;
                  w[cnt] = b * k;
                  s -= k;
                  k *= 2;
              }
              if (s > 0) {        // 若s=2000，再存一个剩下的977
                  cnt++;
                  v[cnt] = a * s;
                  w[cnt] = b * s;
              }
          }
          // n更新为新的物品的个数
          n = cnt;
  
          for (int i = 1; i <= n; i++) {
              for (int j = 0; j <= m; j++) {
                  dp[i][j] = dp[i - 1][j];
                  if (j >= v[i]) dp[i][j] = Math.max(dp[i][j], dp[i - 1][j - v[i]] + w[i]);
              }
          }
          System.out.println(dp[n][m]);
      }
  }
  ```

- 空间优化

  ```java
  import java.util.*;
  
  
  class Main{
      static int n, m;
      static int[] dp;
      static int[] w;
      static int[] v;
      static int[] s;
      // 如何确定len的长度
      // N * logS
      static final int len = 11010;
      public static void main(String[] args) {
          Scanner input = new Scanner (System.in);
          n = input.nextInt();
          m = input.nextInt();
          // 因为s的个数，即物品限定的个数可能很大，比如说设了个2000，那么二进制划分后是可能比物品个数要大的，因此要设大一点
          // 2000划分成二进制的话，到512为止，能组成的和为1023，剩下977，即1,2,4,8,16,32,64,128,256,512,977,共11个
          // N * logs得到len的长度，1000个物品，每个物品可以拆分成11个二进制表示下不同价值的物品，因此新物品的总数就可以算出来
          v = new int[len];
          w = new int[len];
          dp = new int[m + 1];
  
          // cnt的值最后可能是很大的，最大可以达到N*logS
          int cnt = 0;
  
          for (int i = 1; i <= n; i++) {
              int a,b,s;
              // a 为单位体积
              // b 为单位价值
              a = input.nextInt();
              b = input.nextInt();
              s = input.nextInt();
              int k = 1;
              // 注意下面的s是会变的！代表的是按二进制划分后剩下的数量，之前一直以为s是不变的，写成了while(k * 2 <= s)
              while (k <= s) {    //若s=2000，k为1,2,4……,512，while共循环10次，错错错，s是会变小的！当k为1024时，1024 <= 977不成立
                  cnt++;
                  v[cnt] = a * k;
                  w[cnt] = b * k;
                  s -= k;
                  k *= 2;
              }
              if (s > 0) {        // 若s=2000，再存一个剩下的977
                  cnt++;
                  v[cnt] = a * s;
                  w[cnt] = b * s;
              }
          }
          // n更新为新的物品的个数
          n = cnt;
  
          for (int i = 1; i <= n; i++) {
              for (int j = m; j >= v[i]; j--) {
                  dp[j] = Math.max(dp[j], dp[j - v[i]] + w[i]);
              }
          }
          System.out.println(dp[m]);
      }
  }
  ```



##### (4) 分组背包问题 (0-1扩展)

###### Acwing-9 分组背包问题

- 物品分组，每个组里面要么不选该组的物品，要么只能选组中的一个物品

- 状态表示有一点不同，之前的`f[i, j]`代表从前i个物品中选，且总体积不大于j的所有选法。而这里的`f[i, j]`代表从前i**组**中选

- 状态转移方程

  <img src="DP方法总结.assets/image-20210425211722378.png" alt="image-20210425211722378" style="zoom:67%;" />

  <img src="DP方法总结.assets/image-20210425212358024.png" alt="image-20210425212358024" style="zoom:67%;" />

  <img src="DP方法总结.assets/image-20210425214707696.png" alt="image-20210425214707696" style="zoom: 33%;" />

  ```java
  import java.util.*;
  
  class Main{
      static int n, m;
      static int[] dp;
      static int[][] w;
      static int[][] v;
      static int[] s;
      public static void main(String[] args) {
          Scanner input = new Scanner (System.in);
          n = input.nextInt();
          m = input.nextInt();
          v = new int[n + 1][];
          w = new int[n + 1][];
          dp = new int[m + 1];
          s = new int[n + 1];
          for (int i = 1; i <= n; i++) {
              s[i] = input.nextInt();
              v[i] = new int[s[i] + 1];
              w[i] = new int[s[i] + 1];
              for (int j = 1; j <= s[i]; j++) {
                  v[i][j] = input.nextInt();
                  w[i][j] = input.nextInt();
              }
          }
          for (int i = 1; i <= n; i++) {
              for (int j =m; j >= 0; j--) {
                  for (int k = 1; k <= s[i]; k++){
                      if (v[i][k] <= j)
                          dp[j] = Math.max(dp[j], dp[j - v[i][k]] + w[i][k]);   
                  }
              }
          }
          System.out.println(dp[m]);
      }
  }
  ```

  

## 2. 区间DP

### LC相关题目

###### Acwing-282. 石子合并

>  将N堆石子合并成为一堆。
>
> 每次只能合并相邻的两堆，合并的代价为这两堆石子的质量之和，合并后与这两堆石子相邻的石子将和新堆相邻，合并时由于选择的顺序不同，合并的总代价也不相同。
>
> 问题是：找出一种合理的方法，使总的代价最小，输出最小代价。

<img src="DP方法总结.assets/image-20210426132248703.png" alt="image-20210426132248703" style="zoom: 33%;" />

1. 状态表示

   - f[i, j] 表示将[i, j]合并成一堆的方案的集合
   - 属性为min

2. 状态计算

   - 设s[i]数组为**前缀和**数组，方便计算合并的代价
- f[i, j] = min(f[i, k] + f[k + 1, j] + s[j] - s[i])，其中k属于[i, j-1]

- `f[i, j]`表示一段区间的dp，一般都是先遍历长度，然后遍历左端点，根据长度得到右端点

- 代码实现

  ```java
  import java.util.*;
  
  public class Main {
      static int n;
      static int[] s;
      static int[][] f;
      
      public static void main(String[] args){
          Scanner input = new Scanner(System.in);
          n = input.nextInt();
          s = new int[n + 1];
          f = new int [n + 1][n + 1];
          for (int i = 1; i <= n; i++) {
              s[i] = input.nextInt();
              s[i] += s[i - 1];
          }
          // f[i, j]表示一段区间这样的dp，一般都是先遍历长度，然后遍历左端点，根据长度得到右端点
          // len从2开始遍历，当数组长度为1时，不需要合并石子，即代价为0，初始化默认就是0，不用单独处理
          for (int len = 2; len <= n; len++) {
              for (int i = 1; i + len - 1 <= n; i++) {
                  int j = i + len - 1;
                  // 这里要接初始化
                  f[i][j] = 100000000;
                  for (int k = i; k < j; k++) {
                      // 前缀和求[i-j]的和是s[j]-s[i-1]，别忘了-1
                      f[i][j] = Math.min(f[i][j], f[i][k] + f[k + 1][j] + s[j] - s[i - 1]);
                  }
              }
          }
          System.out.println(f[1][n]);
      }
  }
  ```





## 3. 线性DP

只要DP的状态转移是线性的，前一个推后一个，或者后一个推前一个这种，就可以算作是线性DP，背包在二维空间也是线性的



### LC相关题目

##### 单序列线性DP

###### 300. 最长递增子序列(LIS)

<img src="DP方法总结.assets/image-20210426120314304.png" alt="image-20210426120314304" style="zoom:33%;" />

- 状态计算：根据递增子序列中倒数第二个数的情况（不一定是原数组中第i - 1个数）来划分子集，划分的子集有的不一定存在，当`nums[j] >= nums[i]`时，就不存在这个划分的子集。

> DP数组定义：` dp[i] ` 代表以 `nums[i]`为结尾的最长递增子序列长度
>
> Base case：` dp[..] = 1`
>
> 状态转移方程： ` dp[i] = max(dp[i],dp[j]+1) ` （遍历 i 之前的所有 j ，如果 nums[ j ] < nums[ i ]，找出最大的dp[ j ] + 1）
>
> 最终求解： dp数组中最大的那个

- 朴素DP

  ```java
  // 21.2 写
  public int lengthOfLIS(int[] nums) {
    // dp方法
    int[] dp = new int [nums.length];
    Arrays.fill(dp,1);
    for(int i = 1;i<nums.length;i++){
      for(int j=0;j<i;j++){
        if(nums[j]<nums[i])
          dp[i] = Math.max(dp[i],dp[j]+1);	// 等于找出i之前每个dp[j]+1中的最大值
      }
      // res = Math.max(dp[i],res); 这里会出错，错误case：只要一个输入 [1]，应该输出1，但输出为0，因为没进for循环
    }
    // 这里最好不要把下面遍历nums数组找到最大值的过程写到上面的双重for循环里，会出现边界错误，比如，输入为空、输入的值都一样等等
    int res = 0;
    for(int i=0;i<nums.length;i++){
      res = Math.max(dp[i],res);
    }
    return res;
  }
  
  // 21.4.26
  class Solution {
      public int lengthOfLIS(int[] nums) {
          int ans = 0;
          int[] dp = new int[nums.length + 1];
          for (int i = 0; i < nums.length; i++) {
              // 初始化移到这里
              dp[i] = 1;
              for (int j = 0; j < i; j++) {
                  if (nums[j] < nums[i]) {
                      dp[i] = Math.max(dp[i], dp[j] + 1);
                  }
              }
          }
          for (int i = 0; i < nums.length; i++) {
              ans = Math.max(ans, dp[i]);
          }
          return ans;
      }
  }
  ```

- 二分优化

  ```java
  // 21.2写
  public int lengthOfLIS(int[] nums) {
    // 二分
    int res = 0;
    int[] table = new int [nums.length];
    for(int i=0;i<nums.length;i++){
      int l = 0, r = res;
      while(l<r){
        int mid = l+r>>1;
        if(table[mid]>=nums[i])r=mid;
        else l=mid+1;
      }
      if(r==res)res++;
      table[l]=nums[i];
    }
    return res;
  }
  ```

  ```java
  // 21.4.26
  class Solution {
      public int lengthOfLIS(int[] nums) {
          // tail数组记录长度为i的子序列中的最后一个数字
          // 可以用贪心反证的方法证明tail数组一定是严格单调递增的，
          // 如tail[4]代表长度为4的递增子序列中最后一个数，一定小于tail[5]（长度为5的递增子序列中最后一个数）
          int[] tail = new int [nums.length + 1];
          // len代表存在的递增子序列的最大长度，即二分查找的右边界
          int len = 0;
          for (int i = 0; i < nums.length; i++) {
              int l = 0, r = len;
              while (l < r) {
                  int mid = l + r >> 1;
                  // 在tail数组中找第一个大于等于nums[i]的
                  if (tail[mid] >= nums[i]) r = mid;
                  else l = mid + 1;
              }
              // 如果找到的是已经存在的递增子序列长度的最大值，如在长度为[1,2,3,4]的递增子序列，找到可以插在长度为4的后面
              // 那么就生成了长度为5的子序列
              if (r == len) len++;
              // 不然，就更新tail数组
              // 同样如在长度为[1,2,3,4]的递增子序列，找到可以插在长度为2的后面，那么长度为3的子序列最后一个数就要更新为nums[i]
              // 二分查到的就是要插入的这个位置，右半部分的第一位
              tail[r] = nums[i];
          }
          return len;
      }
  }
  ```

  



##### 双序列线性DP

###### 1143. 最长公共子序列(LCS)

<img src="DP方法总结.assets/image-20210426123523467.png" alt="image-20210426123523467" style="zoom: 33%;" />

1. 状态表示

   - f[i, j] 代表字符串 A[1-i] 和 B[1-j] 的公共子序列的集合
   - 公共子序列长度的最大值

2. 状态计算

   - 寻找最后一个不同点

     A[i]、B[j]是否包含在子序列里面，可以将f[i, j]划分成四份，可以用00,01,10,11来代表四种情况，即都不在，有一个在，两个都在

     00这种情况` f[i-1][j-1]`

     01:`f[i-1][j]`，10:`f[i][j-1]`；注意这里01、10和这两个f并不是完全对应的，01代表B[j]一定要在子序列里，而`f[i-1][j]`代表A[1 到 i-1]和B[1-j]的公共子序列集合，不一定包含B[j]，即01代表的集合是`f[i-1][j]`所代表集合的子集。但是，注意这里`dp`求的是极值，因此所划分的区间之间是可以重复的，即使01和10用`f[i-1][j]`和`f[i][j-1]`表示，集合的范围扩大了，但是重复计算极值是可以接受的。

- 代码实现

  ```java
  class Solution {
      public int longestCommonSubsequence(String text1, String text2) {
          int len1 = text1.length();
          int len2 = text2.length();
          char[] cs1 = text1.toCharArray();
          char[] cs2 = text2.toCharArray();
          int[][] dp = new int[len1 + 1][len2 + 1];
          for (int i = 1; i <= len1; i++) {
              for (int j = 1; j <= len2; j++) {
                  if (cs1[i - 1] == cs2[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;
                  else dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
              }
          }
          return dp[len1][len2];
      }
  }
  ```

- `if (cs1[i - 1] == cs2[j - 1]) dp[i][j] = dp[i - 1][j - 1] + 1;`

- y总代码<img src="DP方法总结.assets/image-20210426124321397.png" alt="image-20210426124321397" style="zoom: 67%;" />

- 为什么当相等时可以不用判断`dp[i - 1][j]`和`dp[i][j - 1]`与`dp[i - 1][j - 1] + 1`的大小关系？

  > 因为`dp[i - 1][j - 1] + 1`一定满足>=`dp[i - 1][j]`或`dp[i][j - 1]`，这两个最多也就和`dp[i - 1][j - 1] + 1`相等，毕竟只差一个

###### 72.编辑距离



###### 10.正则表达式匹配





###### 44.通配符匹配