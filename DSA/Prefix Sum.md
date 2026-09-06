[560. Subarray Sum Equals K](https://leetcode.com/problems/subarray-sum-equals-k/)

```java
class Solution {
    public int subarraySum(int[] nums, int k) {
        HashMap<Integer, Integer> prefixsum = new HashMap<>();
        int sum = 0, count = 0;
        for(int i=0;i<nums.length;i++){
            sum+=nums[i];
            if(sum==k) count++;
            int diff = sum - k;
            if(prefixsum.containsKey(diff)){
                count+=prefixsum.get(diff);
            }
            prefixsum.put(sum , prefixsum.getOrDefault(sum,0)+1);
        }
        return count;
    }
}
```
