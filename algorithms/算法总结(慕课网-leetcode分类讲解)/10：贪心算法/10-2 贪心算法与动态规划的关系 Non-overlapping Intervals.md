# 10-2 贪心算法与动态规划的关系 Non-overlapping Intervals

# 435

![1542721216109](assets/1542721216109.png)

转换题目思想：最多保留多少个区间。

![1542721339400](assets/1542721339400.png)

**对于区间问题的常用排序方法：按照起始点排序**

## 动态规划

对于组合问题，都可以思考一下能不能用动态规划解决。很像最长上升子序列问题（可转换为动态规划）

```java
import java.util.Arrays;
import java.util.Comparator;

/// 435. Non-overlapping Intervals
/// https://leetcode.com/problems/non-overlapping-intervals/description/
/// 动态规划
/// 时间复杂度: O(n^2)
/// 空间复杂度: O(n)
public class Solution1 {
    // Definition for an interval.
    public static class Interval {
        int start;
        int end;
        Interval() { start = 0; end = 0; }
        Interval(int s, int e) { start = s; end = e; }
    }

    public int eraseOverlapIntervals(Interval[] intervals) {
        if(intervals.length == 0)
            return 0;

        Arrays.sort(intervals, new Comparator<Interval>() {
            @Override
            public int compare(Interval o1, Interval o2) {
                if(o1.start != o2.start)
                    return o1.start - o2.start;
                return o1.end - o2.end;
            }
        });

        // memo[i]表示以intervals[i]为结尾的区间能构成的最长不重叠区间序列
        int[] memo = new int[intervals.length];
        Arrays.fill(memo, 1);
        // i=0的情况就是只有一个子区间，这样就自己已经构成了一个序列，并且这个序列的结果已经银行在了memo中，答案就是1.
        for(int i = 1 ; i < intervals.length ; i ++)
            // memo[i]
            for(int j = 0 ; j < i ; j ++)
                if(intervals[i].start >= intervals[j].end)
                    memo[i] = Math.max(memo[i], 1 + memo[j]);

        int res = 0;
        for(int i = 0; i < memo.length ; i ++)
            res = Math.max(res, memo[i]);

        return intervals.length - res;
    }

    public static void main(String[] args) {
        Interval[] interval1 = {new Interval(1,2),
                                new Interval(2,3),
                                new Interval(3,4),
                                new Interval(1,3)};
        System.out.println((new Solution1()).eraseOverlapIntervals(interval1));
    }
}
```

![1542722126751](assets/1542722126751.png)

## 贪心算法

![1542722220509](assets/1542722220509.png)

```java
import java.util.Arrays;
import java.util.Comparator;

/// 435. Non-overlapping Intervals
/// https://leetcode.com/problems/non-overlapping-intervals/description/
/// 贪心算法
/// 时间复杂度: O(n)
/// 空间复杂度: O(n)
public class Solution2 {
    // Definition for an interval.
    public static class Interval {
        int start;
        int end;
        Interval() { start = 0; end = 0; }
        Interval(int s, int e) { start = s; end = e; }
    }

    public int eraseOverlapIntervals(Interval[] intervals) {
        if(intervals.length == 0)
            return 0;
		// 按结尾排序
        Arrays.sort(intervals, new Comparator<Interval>() {
            @Override
            public int compare(Interval o1, Interval o2) {
                if(o1.end != o2.end)
                    return o1.end - o2.end;
                return o1.start - o2.start;
            }
        });
		
        //取结尾最早的那个元素，也就是第0个，是一定要的
        int res = 1;
        // 记录前面的区间编号
        int pre = 0;
        // 从第一个开始比较
        for(int i = 1 ; i < intervals.length ; i ++)
            if(intervals[i].start >= intervals[pre].end){
                res ++;
                pre = i;
            }

        return intervals.length - res;
    }

    public static void main(String[] args) {
        Interval[] interval1 = {new Interval(1,2),
                new Interval(2,3),
                new Interval(3,4),
                new Interval(1,3)};
        System.out.println((new Solution2()).eraseOverlapIntervals(interval1));
    }
}
```

