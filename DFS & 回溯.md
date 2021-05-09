

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