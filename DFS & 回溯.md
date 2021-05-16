### 经典回溯问题

另一种回溯技巧！每次选与不选两种选择，直接用两个`backTrace`进入下一层，还未总结

剪枝，一般就是减少`for`循环的次数

#### 排列问题

- 使用`isVisited`数组来标记选择列表
- 选择的范围是所有未被`visit`过的元素

###### 剑指38. 字符串的排列

<img src="DFS & 回溯.assets/image-20210515210724315.png" alt="image-20210515210724315" style="zoom:80%;" />

- 字符串中可能有重复字符，排序后剪枝

- 维护`visit`数组，回溯中每次下个字符的可能都从`[0..n]`遍历所有还没用到的字符

  ```java
  class Solution {
      List<String> res = new ArrayList<>();
      boolean[] isVisited;
      public String[] permutation(String s) {
          char[] cs = s.toCharArray();
          int n = cs.length;
          isVisited = new boolean[n];
          Arrays.sort(cs);
          backTrace(cs, new StringBuilder());
          String[] ans = res.toArray(new String[]{});
          return ans;
      }
  
      public void backTrace(char[] cs, StringBuilder path) {
          if (path.length() == cs.length) {
              res.add(new String(path));
              return;
          }
          for (int i = 0; i < cs.length; ++ i) {
              if (!isVisited[i]) {
                  if (i > 0 && !isVisited[i - 1] && cs[i] == cs[i - 1]) continue;
                  path.append(cs[i]);
                  isVisited[i] = true;
                  backTrace(cs, path);
                  isVisited[i] = false;
                  path.deleteCharAt(path.length() - 1);
              }
          }
      }
  }
  ```

- 还有一种以交换为基础的回溯方法

  - 实测效率和上面的差不多，还不如直接用上面的方法
  - 并且这种交换的方法得到的排列顺序不是按字典序的，顺序会有问题

  ```java
  class Solution {
      List<String> res = new ArrayList<>();
      char[] cs;
      public String[] permutation(String s) {
          cs = s.toCharArray();
          backTrace(0);
          String[] ans = res.toArray(new String[]{});
          return ans;
      }
  
      public void backTrace(int idx) {
          if (idx == cs.length) {
              res.add(new String(cs));
              return;
          }
          Set<Character> set = new HashSet<>();
          for (int i = idx; i < cs.length; ++ i) {
              if (set.contains(cs[i])) continue;
              set.add(cs[i]);
              swap(i, idx);
              backTrace(idx + 1);
              swap(i, idx);
          }
      }
  
      public void swap (int i, int j) {
          char temp = cs[i];
          cs[i] = cs[j];
          cs[j] = temp;
      }
  }
  ```

  

###### 46. 全排列

<img src="DFS & 回溯.assets/image-20210516140542910.png" alt="image-20210516140542910" style="zoom:80%;" />

- 维护`isVisited`的回溯，回溯中每次下个字符的可能都从`[0..n]`遍历所有还没用到的字符

- 回溯函数中无需下标，直接用`path`大小来判断是否退出

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<>();
      boolean[] isVisited;
      public List<List<Integer>> permute(int[] nums) {
          isVisited = new boolean[nums.length];
          backTrace(nums, new ArrayList<Integer>());
          return ans;
      }
  
      public void backTrace(int[] nums, List<Integer> path) {
          if (path.size() == nums.length) {
              ans.add(new ArrayList<Integer>(path));
              return;
          }
          for (int i = 0; i < nums.length; ++ i) {
              if (!isVisited[i]) {
                  isVisited[i] = true;
                  path.add(nums[i]);
                  backTrace(nums, path);
                  path.remove(path.size() - 1);
                  isVisited[i] = false;
              }
          }
      }
  }
  ```

- 以交换为基础的回溯

  - 每次等于固定`idx`位置（将`idx`上的字符与`idx`后面所有未固定的字符依次交换，获取所有可能性）

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<>();
      public List<List<Integer>> permute(int[] nums) {
          backTrace(nums, 0);
          return ans;
      }
  
      public void backTrace(int[] nums, int idx) {
          if (idx == nums.length) {
              List<Integer> res = new ArrayList<>();
              for (int num : nums) res.add(num);
              ans.add(res);
              return;
          }
          for (int i = idx; i < nums.length; ++ i) {
              swap(nums, i, idx);
              backTrace(nums, idx + 1);
              swap(nums, i, idx);
          }
      }
  
      public void swap(int[] nums, int i, int j) {
          int tmp = nums[i];
          nums[i] = nums[j];
          nums[j] = tmp;
      }
  }
  ```

  

###### 47.  全排列II

<img src="DFS & 回溯.assets/image-20210516140609366.png" alt="image-20210516140609366" style="zoom:80%;" />

- 和剑指38. 一个意思，和46的区别就多一个排序之后剪枝

- 剪枝条件

  > `if (i > 0 && !visited[i - 1] && nums[i] == nums[i - 1]) continue;`

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<List<Integer>>();
      boolean[] visited;
      public List<List<Integer>> permuteUnique(int[] nums) {
          Arrays.sort(nums);
          visited = new boolean[nums.length];
          backTrace(nums, new ArrayList<Integer>());
          return ans;
      }
  
      public void backTrace(int[] nums,ArrayList<Integer> path){
          if (path.size() == nums.length){
              ans.add(new ArrayList<Integer>(path));
              return;
          }
          for(int i = 0; i < nums.length; i++){
              if(!visited[i]){
                  if (i > 0 && !visited[i - 1] && nums[i] == nums[i - 1]) continue;
                  path.add(nums[i]);
                  visited[i] = true;
                  backTrace(nums, path);
                  visited[i] = false;
                  path.remove(path.size() - 1);
              }
          }
      }
  }
  ```

- 以交换为基础的回溯

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<List<Integer>>();
      boolean[] visited;
      public List<List<Integer>> permuteUnique(int[] nums) {
          visited = new boolean[nums.length];
          backTrace(nums, 0);
          return ans;
      }
  
      public void backTrace(int[] nums, int idx){
          if (idx == nums.length){
              List<Integer> res = new ArrayList<>();
              for (int num : nums) res.add(num);
              ans.add(res);
              return;
          }
          Set<Integer> set = new HashSet<>();
          for(int i = idx; i < nums.length; i++){
              if (!set.contains(nums[i])) {
                  set.add(nums[i]);
                  swap(nums, i, idx);
                  backTrace(nums, idx + 1);
                  swap(nums, i, idx);
              }
          }
      }
  
      public void swap(int[] nums, int i, int j) {
          int tmp = nums[i];
          nums[i] = nums[j];
          nums[j] = tmp;
      }
  }
  ```





###### 31. 下一个全排列

<img src="DFS & 回溯.assets/image-20210516144555703.png" alt="image-20210516144555703" style="zoom:80%;" />

- 最关键的一点，如何获取到下一个全排列

  - 序列中有满足升序的子序列，就可以得到下一个全排列

  - 先找出最大的索引 `i` 满足 `nums[i] < nums[i+1]`，如果不存在，就翻转整个数组（这种情况合并到下面找第一个大于`nums[i]`的下标中）
  - 因为我们需要使得新的排列尽量小，所以我们从后往前找第一个比`nums[i]`更大的数字
  - 再在`[i + 1..n - 1]`下标里倒序寻找第一个满足 `nums[minIndex] > nums[i]`的，注意此时`[i..n - 1]`一定是从大到小排好序的，即倒序的，因此倒序遍历得到第一个大的值
  - 交换`minIndex`与`i`下标上的数
  - 因为我们需要使得新生成的数列尽量小，因此需要对后面的数进行从小到大的排序
  - 注意此时`[i + 1..n - 1]`下标范围内的数还都是倒序的，可以直接翻转，图方便可以直接调用`Arrays.sort(int[], int start, int end)`实现范围排序
  - PS：如果序列是完全倒序的，那么下一个排列应该是生序序列，此时`i`的值会是`-1`

  ```java
  class Solution {
      public void nextPermutation(int[] nums) {
          int n = nums.length;
          int i;
          for (i = n - 2; i >= 0; -- i) {
              if (nums[i] < nums[i + 1]) {
                  int minIndex = i + 1;
                  for (int j = n - 1; j >= i + 1; -- j) {
                      if (nums[j] > nums[i]) {
                          minIndex = j;
                          break;
                      }
                  }
                  swap(nums, i, minIndex);
                  break;
              }
          }
          Arrays.sort(nums, i + 1, n);
          return;
      }
  
      public void swap(int[] nums, int i, int j) {
          nums[i] = nums[i] ^ nums[j];
          nums[j] = nums[i] ^ nums[j];
          nums[i] = nums[i] ^ nums[j];
      }
  }
  ```

- 自己实现`reverse`，也可以用39. 里面的倒序方法

  ```java
  class Solution {
      public void nextPermutation(int[] nums) {
          int n = nums.length;
          int i;
          for (i = n - 2; i >= 0; -- i) {
              if (nums[i] < nums[i + 1]) {
                  int minIndex = i + 1;
                  for (int j = n - 1; j >= i + 1; -- j) {
                      if (nums[j] > nums[i]) {
                          minIndex = j;
                          break;
                      }
                  }
                  swap(nums, i, minIndex);
                  break;
              }
          }
          // n - 1为最右边的下标
          reverse(nums, i + 1, n - 1);
          return;
      }
  
      public void swap(int[] nums, int i, int j) {
          nums[i] = nums[i] ^ nums[j];
          nums[j] = nums[i] ^ nums[j];
          nums[i] = nums[i] ^ nums[j];
      }
  
      public void reverse(int[] nums, int i, int j) {
          while (i < j) {
              swap(nums, i ++, j --);
          }
      }
  }
  ```

  

###### 60. 排列序列

<img src="DFS & 回溯.assets/image-20210516193203434.png" alt="image-20210516193203434" style="zoom:80%;" />

- 采用纯暴力做法，直接列出所有全排列的可能，数出第`k`个，算法和46.的一样，只是要多个计数变量

  ```java
  class Solution {
      boolean[] visited;
      int cnt = 0;
      String ans;
      public String getPermutation(int n, int k) {
          StringBuilder path = new StringBuilder();
          visited = new boolean[n + 1];
          backTrace(path, n, k);
          return ans;
      }
  
      public void backTrace(StringBuilder path, int n, int k){
          if (cnt >= k) return;
          if (path.length() == n){
              cnt ++;
              if(cnt == k) ans = new String(path);
              return;
          }
          for (int i = 1; i <= n; ++ i){
              if (cnt >= k) break;
              if (!visited[i]) {
                  path.append((char)(i + '0'));
                  visited[i] = true;
                  backTrace(new StringBuilder(path), n, k);
                  visited[i] = false;
                  path.deleteCharAt(path.length()-1);
              }
          }
      }
  }
  ```

- 另一种暴力做法，每次求下一个全排列，循环调用`k`次，即调用31.中的算法`k - 1`次，求第1个排列就循环0次（速度比上一种暴力快很多）

  ```java
  class Solution {
      public String getPermutation(int n, int k) {
          int[] nums = new int[n];
          for (int i = 0; i < n; ++ i) nums[i] = i + 1;
          while (-- k > 0) nextPermutation(nums);
          StringBuilder ans = new StringBuilder();
          for (int num : nums) ans.append((char)(num + '0'));
          return ans.toString();
      }
  
      public void nextPermutation(int[] nums) {
          int n = nums.length;
          int i;
          for (i = n - 2; i >= 0; -- i) {
              if (nums[i] < nums[i + 1]) {
                  int minIndex = i + 1;
                  for (int j = n - 1; j >= i + 1; -- j) {
                      if (nums[j] > nums[i]) {
                          minIndex = j;
                          break;
                      }
                  }
                  swap(nums, i, minIndex);
                  break;
              }
          }
          Arrays.sort(nums, i + 1, n);
          return;
      }
  
      public void swap(int[] nums, int i, int j) {
          nums[i] = nums[i] ^ nums[j];
          nums[j] = nums[i] ^ nums[j];
          nums[i] = nums[i] ^ nums[j];
      }
  }
  ```

- 自己想的回溯做法，利用`visited`来标记当前数字是否被用过

  ```java
  class Solution {
      boolean[] visited;
      int[] factorials;
      StringBuilder ans = new StringBuilder();
      // 阶乘数组中的下标
      int index;
      public String getPermutation(int n, int k) {
          factorials = new int[n + 1];
          visited = new boolean[n + 1];
          index = n;
  
          factorials[0] = 1;
          for (int i = 1; i <= n; ++ i) factorials[i] = i * factorials[i-1];
  
          backTrace(n, k, visited);
          return ans.toString();
      }
  
      public void backTrace(int n, int k, boolean[] visited) {
          if(ans.length() == n){
              return;
          }
          index --;
          int a, b, cnt=0;
          // 本轮中第a个
          a = k / factorials[index] + (k % factorials[index] == 0 ? 0 : 1);
          // b是 k 减去本轮剪枝后，剩余数中要求的排列处于第 b 个位置
          b = k % factorials[index] == 0 ? factorials[index] : k - (a - 1) * factorials[index];
  
          for (int i = 1; i <= n; ++ i) {
              if (!visited[i]) {
                  cnt ++;
                  if (cnt == a) {
                      ans.append((char)(i + '0'));
                      visited[i] = true;
                      backTrace(n, b, visited);
                  }
              }
          }
      }
  }
  ```

  

- 官解：采用数学方法，和自己想的过程大同小异，但是不停的修改`ArrayList`大小还是有点耗时的，比自己想的方法慢一点，不过面试写这个容易一点

  ```java
  class Solution {
      public String getPermutation(int n, int k) {
          // 得到实际下标
          k --;
          
          StringBuilder ans = new StringBuilder();
          List<Integer> nums = new ArrayList<>();
          int[] factorials = new int[n + 1];
          factorials[0] = 1;
          int fact = 1;
          for (int i = 1; i <= n; ++ i) {
              nums.add(i);
              fact *= i;
              factorials[i] = fact;
          }
  
          for (int i = n - 1; i >= 0; -- i) {
              int index = k / factorials[i];
              ans.append(nums.remove(index));
              k -= index * factorials[i];
          }
          return ans.toString();
      }
  }
  ```

  



#### 子集、组合问题

- 使用`start`变量标记当前遍历到的元素下标
- 选择的范围在当前下标`start`到末尾之间的元素

###### 78. 子集

<img src="DFS & 回溯.assets/image-20210516141508026.png" alt="image-20210516141508026" style="zoom:80%;" />

- 不是和全排列问题一样每次从0开始遍历了，每次回溯从`start`位置开始遍历

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<>();
      public List<List<Integer>> subsets(int[] nums) {
          backTrace(nums, new ArrayList<>(), 0);
          return ans;
      }
  
      public void backTrace(int[] nums, List<Integer> path, int start) {
          ans.add(new ArrayList<>(path));
          // 下一句可以不要
          if (start == nums.length) return;
          for (int i = start; i < nums.length; ++ i) {
              path.add(nums[i]);
              backTrace(nums, path, i + 1);
              path.remove(path.size() - 1);
          }
      }
  }
  ```

- 

###### 90. 子集II

<img src="DFS & 回溯.assets/image-20210516143256311.png" alt="image-20210516143256311" style="zoom:80%;" />

- 多了一步剪枝，和47. 的剪枝条件很类似

- 剪枝条件

  > `if (i > start && nums[i] == nums[i - 1]) continue;`

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<>();
      public List<List<Integer>> subsetsWithDup(int[] nums) {
          Arrays.sort(nums);
          backTrace(nums, new ArrayList<Integer>(), 0);
          return ans;
      }
  
      public void backTrace(int[] nums, ArrayList<Integer> path, int start) {
          ans.add(new ArrayList<>(path));
          for (int i = start; i < nums.length; i++) {
              if (i > start && nums[i] == nums[i - 1]) continue;
              path.add(nums[i]);
              backTrace(nums, path, i + 1);
              path.remove(path.size() - 1);
          }
      }
  }
  ```



###### 77. 组合

<img src="DFS & 回溯.assets/image-20210516163601766.png" alt="image-20210516163601766" style="zoom:80%;" />

- 和子集问题一样，只是回溯结束条件换了一下

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<>();
      public List<List<Integer>> combine(int n, int k) {
          backTrace(n, new ArrayList<>(), 1, k);
          return ans;
      }
  
      public void backTrace(int n, List<Integer> path, int start, int k) {
          if (path.size() == k) {
              ans.add(new ArrayList<>(path));
              return;
          }
          for (int i = start; i <= n; ++ i) {
              path.add(i);
              backTrace(n, path, i + 1, k);
              path.remove(path.size() - 1);
          }
      }
  }
  ```

- 剪枝优化

  - 每轮回溯里`i`可选择的范围很多情况下达不到`n`，因为进入回溯函数时已经选了`path.size()`个数，总共要选`k`个数，因此还剩`k - path.size()`个数要选，因此设本轮`i`最多选到的数为`x`，则`[x+1..n]`至少得有`k - path.size() - 1`个数，即`n - x = k - path.size() - 1`，得`x = n + path.size() - k + 1`

  - 注意这里数是从`1`开始的，不是`0`

    ```java
    class Solution {
        List<List<Integer>> ans = new ArrayList<>();
        public List<List<Integer>> combine(int n, int k) {
            backTrace(n, new ArrayList<>(), 1, k);
            return ans;
        }
    
        public void backTrace(int n, List<Integer> path, int start, int k) {
            if (path.size() == k) {
                ans.add(new ArrayList<>(path));
                return;
            }
            for (int i = start; i <= n + path.size() - k + 1; ++ i) {
                path.add(i);
                backTrace(n, path, i + 1, k);
                path.remove(path.size() - 1);
            }
        }
    }
    ```

    

###### 39. 组合总和

<img src="DFS & 回溯.assets/image-20210516171650227.png" alt="image-20210516171650227" style="zoom:80%;" />

- 尝试先把数组从大到小排序，注意`int[]`数组无法直接用`Arrays.sort()`函数实现倒序排序

- 多用了变量，分别用`target`、`sum`记录当前的和与目标之间的关系

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<>();
      public List<List<Integer>> combinationSum(int[] candidates, int target) {
          // Arrays.sort无法对int[]数组进行倒序排序，重写comparator只能对Integer类进行比较
          Arrays.sort(candidates);
          for (int i = 0; i < candidates.length >> 1; ++ i) {
              candidates[i] = candidates[i] ^ candidates[candidates.length - 1 - i];
              candidates[candidates.length - 1 - i] = candidates[i] ^ candidates[candidates.length - 1 - i];
              candidates[i] = candidates[i] ^ candidates[candidates.length - 1 - i];
          }
          backTrace(candidates, new ArrayList<>(), target, 0, 0);
          return ans;
      }
  
      public void backTrace(int[] candidates, List<Integer> path, int target, int sum, int start) {
          if (sum > target) return;
          if (sum == target) {
              ans.add(new ArrayList<>(path));
              return;
          }
          for (int i = start; i < candidates.length; ++ i) {
              path.add(candidates[i]);
              sum += candidates[i];
              backTrace(candidates, path, target, sum, i);
              sum -= candidates[i];
              path.remove(path.size() - 1);
          }
      }
  }
  ```

- 从小到大排序，在`for`循环中判断是否可以进入下一层，减少了`for`循环的次数与判断的次数，这才是真剪枝

- `target`与`sum`的关系可以只用`target`一个变量来表示，即每次将`target`的值减去当前数的值

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<>();
      public List<List<Integer>> combinationSum(int[] candidates, int target) {
          Arrays.sort(candidates);
          backTrace(candidates, new ArrayList<>(), target, 0);
          return ans;
      }
  
      public void backTrace(int[] candidates, List<Integer> path, int target, int start) {
          if (target == 0) {
              ans.add(new ArrayList<>(path));
              return;
          }
          for (int i = start; i < candidates.length; ++ i) {
              if (candidates[i] > target) break;
              path.add(candidates[i]);
              backTrace(candidates, path, target - candidates[i], i);
              path.remove(path.size() - 1);
          }
      }
  }
  ```

  

###### 40. 组合总和II

<img src="DFS & 回溯.assets/image-20210516173332719.png" alt="image-20210516173332719" style="zoom:80%;" />

- `candidates`数组可以有重复数字了，并且每个数字只能用一次，因此与上一题有两个变化

  - 去重操作，`if (i > start && nums[i] == nums[i - 1]) continue;`

  - 可选择列表变化，下一层回溯不能从`i`开始了，只能从`i + 1`开始

    >  `backTrace(nums, path, target - nums[i], i + 1);`

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<>();
      public List<List<Integer>> combinationSum2(int[] candidates, int target) {
          Arrays.sort(candidates);
          backTrace(candidates, new ArrayList<>(), target, 0);
          return ans;
      }
  
      public void backTrace(int[] nums, List<Integer> path, int target, int start) {
          if (target == 0) {
              ans.add(new ArrayList<>(path));
              return;
          }
          for (int i = start; i < nums.length; ++ i) {
              if (nums[i] > target) break;
              if (i > start && nums[i] == nums[i - 1]) continue;
              path.add(nums[i]);
              backTrace(nums, path, target - nums[i], i + 1);
              path.remove(path.size() - 1);
          }
      }
  }
  ```

  

###### 216. 组合总和III

<img src="DFS & 回溯.assets/image-20210516174407133.png" alt="image-20210516174407133" style="zoom:80%;" />

- 天然单调增，都不用排序了

- 回溯出口改一下

- 循环里可以剪枝，和77. 组合的剪枝几乎一样

  ```java
  class Solution {
      List<List<Integer>> ans = new ArrayList<>();
      public List<List<Integer>> combinationSum3(int k, int n) {
          backTrace(n, k, 1, new ArrayList<>());
          return ans;
      }
  
      public void backTrace(int target, int k, int start, List<Integer> path) {
          if (target == 0 && path.size() == k) {
              ans.add(new ArrayList<>(path));
              return;
          }
          for (int i = start; i <= 10 + path.size() - k; ++ i) {
              if (i > target) break;
              path.add(i);
              backTrace(target - i, k, i + 1, path);
              path.remove(path.size() - 1);
          }
      }
  }
  ```



###### 377. 组合总和IV(非回溯)

<img src="DFS & 回溯.assets/image-20210516175417706.png" alt="image-20210516175417706" style="zoom:80%;" />

- `nums`长度有200，不是回溯能解决的，用`dp`

- 本题可以看成是爬楼梯问题，楼梯的阶数一共为`target`，一次可以走的步数为`nums[i]`。 一共有多少种走法？

- 具体见`DP`文件

  ```java
  class Solution {
      public int combinationSum4(int[] nums, int target) {
          int[] dp = new int[target + 1];
          // dp[k]代表目标整数为k时的组合个数
          // 如果nums数组中有一个数，使得该数等于dp[k]代表的k这个组合数值，那么dp[k] += dp[0]，此时应该是+1，所以dp[0] = 1
          // 另一种判断dp[0] = 1的思路，dp[0]代表什么都不选，凑成总和为 0 的方案数就为 1
          dp[0] = 1;
          for (int j = 1; j <= target; j++) {
              for (int num : nums) {
                  if (j >= num) {
                      dp[j] += dp[j - num];
                  }
              }
          }
          return dp[target];
      }
  }
  ```



###### 93. 复原IP地址

<img src="DFS & 回溯.assets/image-20210516214422279.png" alt="image-20210516214422279" style="zoom:80%;" />

- 核心点

  1. 当前遍历下标`idx`满足以下两个条件时剪枝（`cnt`代表已经获取的整数个数）

     - `idx < s.length() - (4 - cnt) * 3`，即剩下的数字即使每3个一组也不够分

     - `idx > s.length() - (4 - cnt)` 即剩下的数字即使每1个一组也不够分
  2. 结束条件:`cnt == 4 && idx == s.length()`
  3. 当前遍历的数字为`0`时，继续回溯只有一种可能。不为`0`时，有三种可能，采用`for`循环选择

  ```java
  class Solution {
      List<String> ans = new ArrayList<>();
      public List<String> restoreIpAddresses(String s) {
          if (s.length() < 3 || s.length() > 12) return ans;
          backTrace(s, new StringBuilder(), 0, 0);
          return ans;
      }
  
      public void backTrace(String s, StringBuilder path, int idx, int cnt){
          if (idx < s.length() - (4 - cnt) * 3 || idx > s.length() - (4 - cnt)) return;
          if (cnt == 4 && idx == s.length()) {
              ans.add(new String(path.substring(0, path.length() - 1)));
              return;
          }
  
          if (s.charAt(idx) == '0') {
              path.append(s.charAt(idx)).append('.');
              backTrace(s, path, idx + 1, cnt + 1);
              path.delete(path.length() - 2, path.length());
          } else {
              for (int i = 1; i <= 3; ++ i) {
                  if (idx + i > s.length()) return;
                  String sub = s.substring(idx, idx + i);
                  if (Integer.valueOf(sub) <= 255) {
                      path.append(sub).append('.');
                      backTrace(s, path, idx + i, cnt + 1);
                      path.delete(path.length() - sub.length() - 1, path.length());
                  }
              }
          }
      }
  }
  ```



###### 1593. 拆分字符串使唯一子字符串的数目最大

<img src="DFS & 回溯.assets/image-20210517000441945.png" alt="image-20210517000441945" style="zoom:80%;" />

- 用`set`做去重，配合剪枝

  ```java
  class Solution {
      int ans = 1;
      Set<String> set = new HashSet<>();
      public int maxUniqueSplit(String s) {
          backTrace(s, 0);
          return ans;
      }
  
      public void backTrace(String s, int start) {
          if (start == s.length()) {
              ans = Math.max(ans, set.size());
              return;
          }
          // 如何剩下的元素即使1个1个分加上set的元素也不能大于已有的答案，直接不用循环
          for (int i = start; i < s.length() && set.size() + s.length() - i > ans; ++ i) {
              String sub = s.substring(start, i + 1);
              if (set.contains(sub)) continue;
              set.add(sub);
              backTrace(s, i + 1);
              set.remove(sub);
          }
      }
  }
  ```

  



#### N皇后问题

###### 51. N皇后

五种写法的N皇后

`dfs`内两层`for`循环即可

- 第一种写法，朴素判断

  - `dfs`函数参数为当前考虑的行下标
  - 用`sol`数组来记录每行皇后放的列数
  - `dfs`函数内先考虑每一列放置当前皇后，再判断以前放置好的行与当前行列的皇后是否会冲突
  - `dfs`函数当遍历到最后一行时，得到一个结果

  ```java
  class Solution {
      int[] sol;
      int n;
      List<List<String>> ans = new ArrayList<>();
      public List<List<String>> solveNQueens(int n) {
          this.n = n;
          sol = new int[n];
          dfs(0);
          return ans;
      }
  
      // dfs参数是当前考虑放皇后的行
      public void dfs(int row) {
          // 遍历将皇后放在row行col列的可能
          for (int col = 0; col < n; ++ col) {
              boolean isValid = true;
              // 与之前的每一行中已放置的皇后进行检查
              for (int r = 0; r < row; ++ r) {
                  if (sol[r] == col || sol[r] - col == r - row || sol[r] - col == row - r) {
                      isValid = false;
                      break;
                  }
              }
              if (!isValid) continue;
              sol[row] = col;
              if (row == n- 1) {
                  List<String> res = new ArrayList<>();
                  for (int i = 0; i < n; ++ i) {
                      StringBuilder tmp = new StringBuilder();
                      for (int j = 0; j < n; ++ j) {
                          if (sol[i] == j) tmp.append('Q');
                          else tmp.append('.');
                      }
                      res.add(tmp.toString());
                  }
                  ans.add(res);
              } else {
                  dfs(row + 1);
              }
          }
      }
  }
  ```

- 后面的几种写法，见52. 都没有保存皇后的路径，没法用于51. 



###### 52. N皇后II

<img src="DFS & 回溯.assets/image-20210516230857567.png" alt="image-20210516230857567" style="zoom: 50%;" />

> 参考：https://zhuanlan.zhihu.com/p/22846106	

- 第一种写法

  ```java
  class Solution {
      int ans = 0;
      int[] sol;
      int n;
      public int totalNQueens(int n) {
          this.n = n;
          sol = new int[n];
          dfs(0);
          return ans;
      }
  
      // dfs参数是当前考虑放皇后的行
      public void dfs(int row) {
          // 遍历将皇后放在row行col列的可能
          for (int col = 0; col < n; ++ col) {
              boolean isValid = true;
              // 与之前的每一行中已放置的皇后进行检查
              for (int r = 0; r < row; ++ r) {
                  if (sol[r] == col || sol[r] - col == r - row || sol[r] - col == row - r) {
                      isValid = false;
                      break;
                  }
              }
              // 
              if (!isValid) continue;
              sol[row] = col;
              if (row == n- 1) {
                  ans ++;
              } else {
                  dfs(row + 1);
              }
          }
      }
  }
  ```

- 第二种写法

  - 提前使用三个布尔数组来判断当前行列能否放皇后

  ```java
  class Solution {
      int ans = 0;
      boolean[] cols;    // 列
      boolean[] lowHigh; // 副对角线
      boolean[] highLow; // 主对角线
      int n;
      public int totalNQueens(int n) {
          this.n = n;
          cols = new boolean[n];
          lowHigh = new boolean[2 * n - 1];
          highLow = new boolean[2 * n - 1];
          dfs(0);
          return ans;
      }
  
      // dfs参数是当前考虑放皇后的行
      public void dfs(int row) {
          // 遍历将皇后放在row行col列的可能
          for (int col = 0; col < n; ++ col) {
              int i = row + col, j = n - 1 + col - row;
              if (cols[col] || lowHigh[i] || highLow[j]) continue;
              if (row == n - 1) ans ++;
              else {
                  cols[col] = lowHigh[i] = highLow[j] = true;
                  dfs(row + 1);
                  cols[col] = lowHigh[i] = highLow[j] = false;
              }
          }
      }
  }
  ```

- 第三种方法

  - 将布尔数组用一个`int`型数表示，即位图的概念
  - 标记占用时对应的位本来为 0，而清除标记时对应的位本来为 1。

  ```java
  class Solution {
      int ans = 0;
      int cols;    // 列
      int lowHigh; // 副对角线
      int highLow; // 主对角线
      int n;
      public int totalNQueens(int n) {
          this.n = n;
          dfs(0);
          return ans;
      }
  
      // dfs参数是当前考虑放皇后的行
      public void dfs(int row) {
          // 遍历将皇后放在row行col列的可能
          for (int col = 0; col < n; ++ col) {
              int i = row + col, j = n - 1 + col - row;
              // 任意一位为1，则不可能
              if ((((cols >> col) | (lowHigh >> i) | (highLow >> j)) & 1) != 0) continue;
              if (row == n - 1) ans ++;
              else {
                  // 标记占用
                  cols ^= (1 << col); lowHigh ^= (1 << i); highLow ^= (1 << j);
                  dfs(row + 1);
                  // 清除标记
                  cols ^= (1 << col); lowHigh ^= (1 << i); highLow ^= (1 << j);
              }
          }
      }
  }
  ```

- 第四种写法

  - 在每一行中不用遍历判断每列是否可能，而是直接用位运算得出所有可能的列

  - 具体见注释

    ```java
    class Solution {
        int ans = 0;
        int cols;    // 列
        int lowHigh; // 副对角线
        int highLow; // 主对角线
        int n;
        public int totalNQueens(int n) {
            this.n = n;
            dfs(0);
            return ans;
        }
    
        // dfs参数是当前考虑放皇后的行
        public void dfs(int row) {
            // available上所有的1代表剩下的皇后在该行可以放的列的位置
            // lowHigh = row + col; highLow = n - 1 - row + col
            // 当col属于[0..n - 1]时，可以把这两个变量二进制上的位数与cols中对应
            // 以撇为例：第 row 行的各个格子属于第 row 至 row + n - 1 撇，它们是否被占用的信息，储存在 pie 这个变量的第 row 至 row + n - 1 位。把 pie 右移 row 位，那么这些信息就移到了第 0 至 n - 1 位，就跟 shu 中的信息对齐了。捺同理。
            int available = (((1 << n) - 1) & ~(cols | (lowHigh >> row) | (highLow >> (n - 1 - row))));
            while (available != 0) {
                // p 为最右边的1代表的数
                int p = available & -available;
                // available 去掉最右边的1，即在当前行中选取一个可以放的列的情况
                available ^= p;
                if (row == n - 1) ans ++;
                else {
                    // 标记占用
                    cols ^= p; lowHigh ^= (p << row); highLow ^= (p << (n - 1 - row));
                    dfs(row + 1);
                    // 清除标记
                    cols ^= p; lowHigh ^= (p << row); highLow ^= (p << (n - 1 - row));
                }
            }
        }
    }
    ```

- 第五种方法

  - 将三个位图变量由全局变量改成`dfs`函数形参，则不需要清楚标记了
  - 把三个变量的含义改成，在当前行有哪些列所在的撇或捺或列本身已被占用，这样在计算当前行可用列时就不再需要移位运算了
  - 在递归至下一行时，`lowHigh` 需要右移 1 位 —— 当前行第 3 列所在的撇，是下一行第 2 列所在的撇；同理，`highLow` 则需要左移 1 位。

  ```java
  class Solution {
      int ans = 0;
      int n;
      public int totalNQueens(int n) {
          this.n = n;
          dfs(0, 0, 0, 0);
          return ans;
      }
  
      // dfs参数是当前考虑放皇后的行
      public void dfs(int row, int cols, int lowHigh, int highLow) {
          int available = ((1 << n) - 1) & ~(cols | lowHigh | highLow);
          while (available != 0) {
              // p 为最右边的1代表的数
              int p = available & -available;
              // available 去掉最右边的1，即在当前行中选取一个可以放的列的情况
              // 下面这句写成available &= (available - 1);一样的
              available ^= p;
              if (row == n - 1) ans ++;
              else {
                  // 下面的或改成异或也是对的，一样，p在的那一位两边的位都是0和1之间的操作
                  // 右移用无符号有符号也是一样的，都是补0
                  dfs(row + 1, cols | p, (lowHigh | p) >> 1, (highLow | p) << 1);
              }
          }
      }
  }
  ```

  











### 还未区分的题

###### 698. 划分为k个相等的子集

- ```java
  class Solution {
      boolean ans = false;
      public boolean canPartitionKSubsets(int[] nums, int k) {
          int target = 0, sum = 0, residue;
          for (int num : nums) {
              sum += num;
          }
          target = sum / k;
          residue = sum % k;
          if (residue != 0) return false;
  
          Arrays.sort(nums);
          for (int i = 0; i < nums.length / 2; ++ i) {
              nums[i] = nums[i] ^ nums[nums.length - 1 - i];
              nums[nums.length - 1 - i] = nums[i] ^ nums[nums.length - 1 - i];
              nums[i] = nums[i] ^ nums[nums.length - 1 - i];
          }
          if (nums[0] > target) return false;
  
          int[] sums = new int[k];
          dfs(nums, sums, 0, 0, target, 0);
          return ans;
      }
  
      public void dfs(int[] nums, int[] sum, int u, int used, int target, int cnt) {
          if (ans) return;
          if (cnt == sum.length) {
              ans = true;
              return;
          }
          if (used < sum.length) {
              sum[used] = nums[u];
              if (nums[u] == target) dfs(nums, sum, u + 1, used + 1, target, cnt + 1);
              else dfs(nums, sum, u + 1, used + 1, target, cnt);
              sum[used] = 0;
          }
          for (int i = 0; i < used; ++ i) {
              if (sum[i] + nums[u] <= target) {
                  sum[i] += nums[u];
                  if (sum[i] == target) dfs(nums, sum, u + 1, used, target, cnt + 1);
                  else dfs(nums, sum, u + 1, used, target, cnt);
                  sum[i] -= nums[u];
              }
          }
      }
  }
  ```

- 





###### 1723. 完成所有工作的最短时间

- 参考三叶代码

- ```java
  class Solution {
      int ans = Integer.MAX_VALUE;
      public int minimumTimeRequired(int[] jobs, int k) {
          int[] sum = new int[k];
          dfs(jobs, 0, 0, sum, 0);
          return ans;
      }
  
      public void dfs(int[] jobs, int u, int used, int[] sum, int max) {
          if (max > ans) return;
          if (u == jobs.length) {
              ans = max;
              return;
          }
  
          if (used < sum.length) {
              sum[used] = jobs[u];
              dfs(jobs, u + 1, used + 1, sum, Math.max(sum[used], max));
              sum[used] = 0;
          }
  
          for (int i = 0; i < used; ++ i) {
              sum[i] += jobs[u];
              dfs(jobs, u + 1, used, sum, Math.max(sum[i], max));
              sum[i] -= jobs[u];
          }
      }
  }
  ```

- 