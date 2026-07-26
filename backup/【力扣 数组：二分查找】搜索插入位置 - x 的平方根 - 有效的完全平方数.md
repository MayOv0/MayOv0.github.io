
# [搜索插入位置](https://leetcode.cn/problems/search-insert-position/)
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
你可以假设数组中无重复元素。
示例 1:
输入: [1,3,5,6], 5
输出: 2
示例 2:
输入: [1,3,5,6], 2
输出: 1
示例 3:
输入: [1,3,5,6], 7
输出: 4
示例 4:
输入: [1,3,5,6], 0
输出: 0

## 二分法
既是有序序列还是无重复元素，可以使用二分查找

这里主要拆解下怎么通过最后的left，right还有middle(mid)得到插入位置
找不到target无非就是几种情况：

> **情况一：target = 3**
> ```
> 1 2 4 5
> ```
> left = 0, right = 3, mid = 1。3 > 2，所以 left = mid + 1 = 2
> ```
> 4 5
> ```
> left = 2, right = 3, mid = 2。3 < 4，所以 right = mid - 1 = 1
>
> 此时已经不满足条件出来了，此时要插入的位置下标为 **2**

---

> **情况二：target = 6**
> ```
> 1 2 4 5
> ```
> left = 0, right = 3, mid = 1。6 > 2，所以 left = mid + 1 = 2
> ```
> 4 5
> ```
> left = 2, right = 3, mid = 2。6 > 4，所以 left = mid + 1 = 3
> ```
> 5
> ```
> left = 3, right = 3, mid = 3。6 > 5，所以 left = mid + 1 = 4
>
> 此时已经不满足条件出来了，此时要插入的位置下标为 **4**

---

> **情况三：target = 5**
> ```
> 4 7
> ```
> left = 0, right = 1, mid = 0。5 > 4，所以 left = mid + 1 = 1
> ```
> 7
> ```
> left = 1, right = 1, mid = 1。5 < 7，所以 right = mid - 1 = 0
>
> 此时已经不满足条件出来了，此时要插入的位置下标为 **1**

可以看到插入位置的下标为 right+1 或者 left

```cpp
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1;
        int mid = 0;

        while (left <= right) {
            mid = (left + right) / 2;

            if (target < nums[mid]) {
                right = mid - 1;
            } else if (target > nums[mid]) {
                left = mid + 1;
            } else // target == nums[mid]
            {
                return mid;
            }
        }

        return left;
    }
};
```
时间复杂度：O(log n)
空间复杂度：O(1)

# [x 的平方根](https://leetcode.cn/problems/sqrtx/)
给你一个非负整数 x ，计算并返回 x 的 算术平方根 。
由于返回类型是整数，结果只保留 整数部分 ，小数部分将被 舍去 。
注意：不允许使用任何内置指数函数和算符，例如 pow(x, 0.5) 或者 x ** 0.5 。

示例 1：
输入：x = 4
输出：2
示例 2：
输入：x = 8
输出：2
解释：8 的算术平方根是 2.82842..., 由于返回类型是整数，小数部分将被舍去。
提示：
0 <= x <= 231 - 1

## 暴力解法
直接从0逐渐往x算，遇到i*i==x或者x>i^2 && x<(i+1)^2就可以返回i了
```cpp
class Solution {
public:
    int mySqrt(int x) {

        size_t i = 0;
        for (; i <= x; i++) {
            if (i * i == x) {
                return i;
            }
            else if ((i * i < x) &&
                (x < ((i + 1) * (i + 1)))) {
                return i;
            }
        }

        return i;
    }
};
```
## 二分法
通过二分法找一个值的平方==x，如果没有那出了循环后right的值就可以确定这个值。

找不到x无非就是几种情况：

> **情况一：x = 2**
> ```
> 0 1 2
> ```
> left = 0, right = 2, mid = 1。1^2 < 2，所以 left = mid + 1 = 2
> ```
> 2
> ```
> left = 2, right = 2, mid = 2。2^2 > 2，所以 right = mid - 1 = 1
>
> 此时已经不满足条件出来了，此时 x 的算术平方根的小数部分为 **1**

---

> **情况二：x = 5**
> ```
> 0 1 2 3 4 5
> ```
> left = 0, right = 5, mid = 2。2^2 < 5，所以 left = mid + 1 = 3
> ```
> 3 4 5
> ```
> left = 3, right = 5, mid = 4。4^2 > 5，所以 right = mid - 1 = 3
> ```
> 3
> ```
> left = 3, right = 3, mid = 3。3^2 > 5，所以 right = mid - 1 = 2
>
> 此时已经不满足条件出来了，此时 x 的算术平方根的小数部分为 **2**

---

> **情况三：x = 17**
> ```
> 0 1 2 … 17
> ```
> left = 0, right = 17, mid = 8。8^2 > 17，所以 right = mid - 1 = 7
> ```
> 0 1 … 7
> ```
> left = 0, right = 7, mid = 3。3^2 < 17，所以 left = mid + 1 = 4
> ```
> 4 … 7
> ```
> left = 4, right = 7, mid = 5。5^2 > 17，所以 right = mid - 1 = 4
> ```
> 4
> ```
> left = 4, right = 4, mid = 4。4^2 < 17，所以 left = mid + 1 = 5
>
> 此时已经不满足条件出来了，此时 x 的算术平方根的小数部分为 **4**

可以看到当最终的 mid 比 x 小，left 会加 1，right 就等于 x 算数平方根的小数部分；而当最终的 mid 比 x 大，right 会 -1，也正好就是 x 算数平方根的小数部分。
```cpp
class Solution {
public:
    int mySqrt(int x) {

        size_t left = 0;
        size_t right = x;

        size_t mid = 0;
        while (left <= right) {
            mid = (left + right) / 2;

            if (mid * mid == x) {
                return mid;
            }
            else if (mid * mid > x) {
                right = mid - 1;
            }
            else {
                left = mid + 1;
            }
        }

        return right;
    }
};
```
# [有效的完全平方数](https://leetcode.cn/problems/valid-perfect-square/)
给你一个正整数 num 。如果 num 是一个完全平方数，则返回 true ，否则返回 false 。
完全平方数 是一个可以写成某个整数的平方的整数。换句话说，它可以写成某个整数和自身的乘积。
不能使用任何内置的库函数，如  sqrt 。

示例 1：
输入：num = 16
输出：true
解释：返回 true ，因为 4 * 4 = 16 且 4 是一个整数。
示例 2：
输入：num = 14
输出：false
解释：返回 false ，因为 3.742 * 3.742 = 14 但 3.742 不是一个整数。

提示：
1 <= num <= 231 - 1
思路和上题几乎一样，在while循环内能得到结果的就返回true，否则返回false即可。
```cpp
class Solution {
public:
    bool isPerfectSquare(int num) {
        size_t left = 0;
        size_t right = num;

        size_t mid = 0;
        while (left <= right) {
            mid = (left + right) / 2;

            if (mid * mid == num) {
                return true;
            }
            else if (mid * mid > num) {
                right = mid - 1;
            }
            else {
                left = mid + 1;
            }
        }

        return false;
    }
};
```