# LeetCode-Experience
Record of what I learn from leetcode

## 1.两数之和
Given an array of integers, return indices of the two numbers such that they add up to a specific target.
You may assume that each input would have exactly one solution, and you may not use the same element twice.
### Code
    
    public int[] twoSum(int[] nums, int target) {
        Map<Integer,Integer> m=new HashMap();//注意哈希表初始化时，Map<>中必须是Object，不能用简单数据类型；
        int tem;
        for(int i=0;i<nums.length;++i){
            tem=target-nums[i];
            if(m.containsKey(tem))
                return new int[]{m.get(tem),i};
            m.put(nums[i],i);//注意该句不可在if之前，题目要求是非重复元；
        }
        throw new IllegalArgumentException("No two sum solution");
    }

### Summary
(1)用哈希表来代替循环查找操作，即用n空间换n时间，这在之后的查找中都可以采用；  
(2)因为数组一遍过，当前之前的元素已经试过和更前的元素相加，无须再试，因此这里只用了一层循环。


## 2.找特别数
某数组中仅包含一个出现次数为奇数的数，其他元素出现次数均为偶数，找出该数。
### Code
    
    public int[] twoSum(int[] nums, int target) {
        int rst=0;
        for(int i=0;i<nums.length;++i）rst^=nums[i];
        return rst;
    }

### Summary
(1)位运算中的亦或运算^，两个一样的数亦或（a^a=0），如此对所有元素亦或运算，结果即为目标数；  
(2)a^b:二进制相同位取0，不同取1；  
   a&b:二进制有一个为0就为0；  
   a<<n:二进制左移n位，后面补0，相当于a*(2的n次方)；  
   a>>n:二进制右移n位，相当于a/(2的n次方)  
   '>>>':无符号右移n位，>>>与>>唯一的不同是它无论原来的最左边是什么数，统统都用0填充。  
       比如，byte是8位的，-1表示为byte型是11111111(补码表示法），b>>>4就是无符号右移4位，即00001111，这样结果就是15。
       
## 3.两数组中位数
There are two sorted arrays nums1 and nums2 of size m and n respectively.  
Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).  
You may assume nums1 and nums2 cannot be both empty.
### Code
    
    public double findMedianSortedArrays(int[] A, int[] B) {
        int m = A.length;
        int n = B.length;
        if (m > n) { // to ensure m<=n
            int[] temp = A; A = B; B = temp;
            int tmp = m; m = n; n = tmp;
        }
        int iMin = 0, iMax = m, halfLen = (m + n + 1) / 2;
        while (iMin <= iMax) {
            int i = (iMin + iMax) / 2;
            int j = halfLen - i;
            if (i < iMax && B[j-1] > A[i]){
                iMin = i + 1; // i is too small
            }
            else if (i > iMin && A[i-1] > B[j]) {
                iMax = i - 1; // i is too big
            }
            else { // i is perfect
                int maxLeft = 0;
                if (i == 0) { maxLeft = B[j-1]; }
                else if (j == 0) { maxLeft = A[i-1]; }
                else { maxLeft = Math.max(A[i-1], B[j-1]); }
                if ( (m + n) % 2 == 1 ) { return maxLeft; }

                int minRight = 0;
                if (i == m) { minRight = B[j]; }
                else if (j == n) { minRight = A[i]; }
                else { minRight = Math.min(B[j], A[i]); }

                return (maxLeft + minRight) / 2.0;
            }
        }
        return 0.0;
    }


### Summary
(1)整体构思，先确定两大条件：  
 1)要找的是一个划分，这个划分会将两数组左右两边划为等长的部分，由此nums1数组下标和nums2数组下标关系确立；  
 2)划分确定之后，因满足nums1[i]<nums1[i+1],nums1[i]<nums2[j+1],nums2[j]<nums[j+1],nums2[j]<nums1[i+1]。  
   由两数组有序，上述条件简化为nums1[i]<nums2[j+1],nums2[j]<nums1[i+1]；  
(2)边界条件和循环体：  
 1)边界条件：可能i==nums1.length()-1，或j==nums2.length()-1的情况，单独考虑；  
 2)循环体：二分查找，时间复杂度降为log(nums1.length())。根据需要满足的两个条件来确定循环每次运算的空间。  
 
## 4.最长回文
Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.
### Code
    
    public String longestPalindrome(String s) {
        if (s == null || s.length() < 1) return "";
        int start = 0, end = 0;
        for (int i = 0; i < s.length(); i++) {
            int len1 = expandAroundCenter(s, i, i);
            int len2 = expandAroundCenter(s, i, i + 1);
            int len = Math.max(len1, len2);
            if (len > end - start) {
                start = i - (len - 1) / 2;
                end = i + len / 2;
            }
        }
        return s.substring(start, end + 1);
    }
    
    private int expandAroundCenter(String s, int left, int right) {
        int L = left, R = right;
        while (L >= 0 && R < s.length() && s.charAt(L) == s.charAt(R)) {
            L--;
            R++;
        }
        return R - L - 1;
    }

### Summary
对每一/二个字符进行中心扩展（回文中心可以是双字符）。


## 5.Z型输出
The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this:   
(you may want to display this pattern in a fixed font for better legibility)  
P   A   H   N  
A P L S I I G  
Y   I   R  
And then read line by line: "PAHNAPLSIIGYIR"
### Code
    public String convert(String s, int numRows) {
        if (numRows == 1) return s;

        StringBuilder ret = new StringBuilder();
        int n = s.length();
        int cycleLen = 2 * numRows - 2;

        for (int i = 0; i < numRows; i++) {
            for (int j = 0; j + i < n; j += cycleLen) {
                ret.append(s.charAt(j + i));
                if (i != 0 && i != numRows - 1 && j + cycleLen - i < n)
                    ret.append(s.charAt(j + cycleLen - i));
            }
        }
        return ret.toString();
    }

### Summary
自己的解法是先按格式放在一个二维矩阵里，实则浪费了很多空间和赋值时间。  
事实上此题只是探求输出顺序和逻辑结构之间的对应关系。

## 6.电话数字字母组合
Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent.
A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.
### Code
    Map<String, String> phone = new HashMap<String, String>() {
        {
            put("2", "abc");
            put("3", "def");
            put("4", "ghi");
            put("5", "jkl");
            put("6", "mno");
            put("7", "pqrs");
            put("8", "tuv");
            put("9", "wxyz");
        }
    };

    List<String> output = new ArrayList<String>();

    public void backtrack(String combination, String next_digits) {
            // if there is no more digits to check
            if (next_digits.length() == 0) {
            // the combination is done
            output.add(combination);
        }
        // if there are still digits to check
        else {
        // iterate over all letters which map 
        // the next available digit
            String digit = next_digits.substring(0, 1);
            String letters = phone.get(digit);
            for (int i = 0; i < letters.length(); i++) {
            String letter = phone.get(digit).substring(i, i + 1);
            // append the current letter to the combination
            // and proceed to the next digits
            backtrack(combination + letter, next_digits.substring(1));
            }
        }
    }

    public List<String> letterCombinations(String digits) {
        if (digits.length() != 0)
        backtrack("", digits);
        return output;
    }

### Summary
用递归载值。自己想到的方法也是用递归，但没有直接用字符串方式，而是用第i个将要输出的字符串进行定位。

## 7.4数之和

### Code
    public List<List<Integer>> fourSum(int[] nums, int target) {
		List<List<Integer>> res = new ArrayList<>();
		
		if (nums.length == 0) return res;
		
        Arrays.sort(nums);
        
        List<Integer> tempList = new ArrayList<>();
        
        find(nums, 0, 0, target, nums[nums.length - 1], tempList, res);
        return res;
    }
    
    private void find(int[] nums, int start, int sum, int target, int maxVal, List<Integer> tempList, List<List<Integer>> res)
    {
        if (tempList.size() == 4)
        {
            if (sum == target)
                res.add(new ArrayList<Integer>(tempList));
            
            return;
        }
        
        if (start == nums.length) return;
        
        for (int i = start; i < nums.length; i++)
        {
            if (i > start && nums[i] == nums[i-1])
                continue;
            
            //nums[i] is too small
            if (sum + nums[i] + maxVal * (4 - tempList.size() - 1) < target)
            	continue;  
            
            //nums[i] is too big
            if (sum + nums[i] * (4 - tempList.size()) > target)
            	continue; 
            
            tempList.add(nums[i]);
            find(nums, i+1, sum + nums[i], target, maxVal, tempList, res);
            
            tempList.remove(tempList.size() - 1);
        }
    }

### Summary
(1)递归的方式，最后一步有种DFS的感觉，但事实上只是在遍历指针start之后的所有元素；  
(2)这里的4数可以换成n数。
