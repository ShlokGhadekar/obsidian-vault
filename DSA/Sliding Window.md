[3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
**Input:** s = "abcabcbb"
**Output:** 3
**Explanation:** The answer is "abc", with the length of 3. Note that `"bca"` and `"cab"` are also correct answers.

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int left = 0;
        int right = 0;
        int length = 0;
        HashSet<Character> set = new HashSet<>();
        while(right<s.length()){
            char ch = s.charAt(right);
            if(!set.contains(ch)){
                set.add(ch);
                length = Math.max(length, right-left+1);
                right++;
            }
            else{
                set.remove(s.charAt(left));
                left++;
            }
        }
        return length;
    }
}
```
- expand right if not repeated, remove from left if repeated
- length is max of (length, right-left+1)

[209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
**Input:** target = 7, nums = [2,3,1,2,4,3]
**Output:** 2
**Explanation:** The subarray [4,3] has the minimal length under the problem constraint.

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
        int left = 0;
        int sum = 0;
        int ans = Integer.MAX_VALUE;
        for(int right = 0;right<nums.length;right++){
            sum+=nums[right];
            while(sum>=target){
                ans = Math.min(ans, right-left+1);
                sum = sum - nums[left];
                left++;
            }

        }
        return ans == Integer.MAX_VALUE ? 0 : ans;
    }
}
```

