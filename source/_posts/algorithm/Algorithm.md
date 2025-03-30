---
title: Algorithm
date: 2023-11-22
category:
  - 算法
tag:
  - java
sticky: false
---

# Algorithm



# 算法复杂性分析

![algorithm](/images/algorithm.png)


## 常数阶(1)
```java
public void sum(int n) {
    int sum = 0; // 执行一次
    sum = n*2; // 执行一次
    System.out.println(sum); // 执行一次
}
```

## 对数阶(logN)
多少个2相乘后其结果值会大于n，即2^x=n。由2^x=n可以得到x=logn，所以这段代码时间复杂度是O(logn)
```java
public void logarithm(int n) {
    int count = 1; // 执行一次
    while (count <= n) { // 执行logn次
        count = count*2; // 执行logn次
    }
}
```

## 线性阶(n)
```java
public void circle(int n) {
    for(int i = 0; i < n; i++) { // 执行n次
        System.out.println(i); // 执行n次
    }
}
```

## 对数阶(n*logN)
```java
public void logarithm(int n) {
    int count = 1;
    for(int i = 0; i < n; i++) { // 执行n次
        while (count <= n) { // 执行logn次
            count = count*2; // 执行nlogn次
        }
    }
}
```

## 平方阶(n2)
```java
public void square(int n) {
    for(int i = 0; i < n; i++){ // 执行n次
        for(int j = 0; j <n; j++) { // 执行n次
            System.out.println(i+j); // 执行n方次
        }
    }
}
```

# 常见算法

## 冒泡排序算法
```java
public static void bubbleSort(int[] input) {
    for (int i = 0; i < input.length - 1; i++) {
        for (int j = 0; j < input.length - i - 1; j++) {
            if (input[j] > input[j+1]) {
                int tmp = input[j];
                input[j] = input[j+1];
                input[j+1] = tmp;
            }
        }
    }
}
```

## 快速排序算法
```java
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        // Create an integer array
        int[] nums = new int[]{5, 7, 89, 6, 4};
        System.out.println("Unsorted array: " + Arrays.toString(nums));
        quicksort(nums, 0, nums.length - 1);
        System.out.println("\n\nSorted array: " + Arrays.toString(nums));
    }

    public static void quicksort(int[] arr, int low, int high) {
        if (arr == null || arr.length <= 0) return;

        if (high - low < 1) {
            // Base case where we have only one element left
            return;
        } else {
            int partitionIndex = partition(arr, low, high);

            // Recursive call to sort first half
            if (partitionIndex > low && partitionIndex < high) {
                quicksort(arr, low, partitionIndex - 1);
            }

            // Recursive call to sort last half
            if (partitionIndex + 1 < high) {
                quicksort(arr, partitionIndex + 1, high);
            }
        }
    }

    private static int partition(int[] arr, int low, int high) {
        int pivot = arr[(high + low) / 2];
        int start = low;
        int end = high;

        while (start <= end) {
            while (end > start && arr[end].compareTo(pivot) >= 0) {
                end--;
            }
            if (arr[start].compareTo(pivot) < 0) {
                swap(&arr[start], &arr[end]);
                start++;
            } else {
                end--;
            }
        }
        swap(&arr[start], &arr[high]);
        return start;
    }

    private static void swap(Object x, Object y) {
        if (x instanceof Integer) {
            Integer temp = (Integer) x;
            (y).intValue();
            x.intValue(temp.intValue());
            (y).intValue(temp.intValue() ^ 1);
        } else if (x instanceof Integer[]) {
                ((int[]) x)[0])];
            }
    }
}
```

## 二分查找算法
```java
public static int binarySearch(int[] arr, int key) {
    int low = 0;
    int high = arr.length - 1;
  
    while (low <= high) {
        int mid = (arr.length - 1) / 2;
  
        if (key < arr[mid]) {
            high = mid - 1;
        } else if (key > arr[mid]) {
            low = mid + 1;
        } else {
            return mid;
        }
    }
    return -1;
}
```

## 反转链表
```java
import java.lang.*;
class ListNode{
    public int val;
    public ListNode next;
    public ListNode prev;
}

    public ListNode reverseList(ListNode head){
        if(head == null || head.next == null){
            return head;
        }
        ListNode first = head;
        ListNode second = head.next;

        while(second != null){
            ListNode tempNext = second.next;
            // Swap next
            second.next = first;
            // Swap tail
            last = second;
            last.prev = first;

            // Move second node one position forward 
            first = second;
            // Move first node to previous node
            second = tempNext;
        }
        return head;
    }
```

## 动态规划
### 斐波那契数列
> 从第三项开始，每一项都等于前两项之和。具体来说，数列的前几项是：1、1、2、3、5、8、13、21、34、……

### 暴力递归
```java
    //暴力递归
    public static int fib(int number) {
        if (number == 1 || number == 2) {
            return number;
        }
        int count = fib(number - 1) + fib(number - 2);
        return count;
    }
```
复杂度为`2^n^`

```text
                       F5
            F4                   F3
      F3        F2=1         F2=1   F1=1
  F2=1  F1=1

分析暴力递归写法的执行流程
例如：我们计算f(5) 需要计算f(4) + f(3)
计算f(3) = f(3) + f(2)
发现没 f(3) 就要跑了两次。因此我们就想到可以用缓存把状态记录下来

```


### 第一次优化 记忆化递归(有备忘录的递归)：
```java
    public static int dfs(int n, int[] mem) {
        if (n == 1 || n == 2) {
            return n;
        }
        if (mem[n] != -1) {
            //缓存中存在对应集合时，直接返回
            return mem[n];
        }
        int count = dfs(n - 1, mem) + dfs(n - 2, mem);
        // 记录 dp[i]
        mem[n] = count;
        return mem[n];
    }

    // 记忆化递归
    public static int fib2(int n) {
        int[] mem = new int[n + 1];
        Arrays.fill(mem, -1);
        return dfs(n, mem);
    }
```
记忆化递归是一种`从顶到底`的方法，递归地将较大子问题分解为较小子问题，直至解已知的最小子问题（叶节点）。
之后，通过回溯逐层收集子问题的解，构建出原问题的解。

复杂度`n`

### 第二次优化 动态规划
动态规划是一种`从底至顶`的方法：从最小子问题的解开始，迭代地构建更大子问题的解，直至得到原问题的解。
由于动态规划不包含回溯过程，因此只需使用循环迭代实现，无须使用递归。
在以下代码中，我们初始化一个数组 dp 来存储子问题的解，它起到了与记忆化搜索中数组 mem 相同的记录作用：
```java
    //动态规划
    public static int fib3(int number) {
        if (number == 1 || number == 2) {
            return number;
        }
        int[] dp = new int[number + 1];
        // 初始状态：预设最小子问题的解
        dp[1] = 1;
        dp[2] = 2;
        // 状态转移：从较小子问题逐步求解较大子问题
        for (int i = 3; i <= number; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[number];
    }
```
复杂度`n`

### 第三次优化 动态规划(执行空间优化版本)

缓存表的最终值是前两项的和(dp[i]只与dp[i-1]和dp[i-2]有关)，因此无需数值，只用两个变量滚动前进即可
```java
    //迭代(优化空间版本)
    public static int fib4(int number) {
        if (number == 1 || number == 2) {
            return number;
        }
        int a = 1;
        int b = 2;
        for (int i = 3; i <= number; i++) {
            int tmp = b;
            b = a + b;
            a = tmp;
        }
        return b;
    }
```
复杂度`1`

### 爬楼梯
题目：https://leetcode.cn/problems/climbing-stairs/

```java
class Solution {
    public int climbStairs(int n) {
        int a = 1, b = 1, sum;
        for(int i = 0; i < n - 1; i++){
            sum = a + b;
            a = b;
            b = sum;
        }
        return b;
    }
}
```


### 打家劫舍
题目：https://leetcode.cn/problems/house-robber/

```java
class Solution {
    public int rob(int[] nums) {
        // 特殊判断
        if (nums == null || nums.length == 0) {
            return -1;
        }

        int a = 0;
        int b = 0;
        int result = 0;
        // 迭代处理
        for (int i = 0; i < nums.length; i++) {
            // 获取偷窃最高金额：偷窃当前房屋时金额和不偷窃当前房屋时金额中最高金额
            int c = Math.max(nums[i] + a, b);
            a = b;
            b = c;
            result = Math.max(result, c);
        }
        return result;
    }
}
```

