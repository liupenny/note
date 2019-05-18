## 优先队列也是队列，只是跟普通队列取出，放入思路不同。
## 堆的底层实现：白板编程

### 347. Top K Frequent Elements

#### 思路1：排序
统计字符和出现次数，排序 (nlogn)后取前k个，瓶颈在于排序

#### 思路2：长度为k的优先队列
维护一个长度为k的优先队列，(nlogk),因为是在长度为k的队列中操作，所以复杂度是logk


```
// 时间复杂度: O(nlogk)
// 空间复杂度: O(n + k)
    private class PairComparator implements Comparator<Pair<Integer, Integer>>{

        @Override
        public int compare(Pair<Integer, Integer> p1, Pair<Integer, Integer> p2){
            if(p1.getKey() != p2.getKey())
                return p1.getKey() - p2.getKey();
            return p1.getValue() - p2.getValue();
        }
    }

    public List<Integer> topKFrequent(int[] nums, int k) {

        if(k <= 0)
            throw new IllegalArgumentException("k should be greater than 0");

        // 统计每个元素出现的频率
        HashMap<Integer, Integer> freq = new HashMap<Integer, Integer>();
        for(int i = 0 ; i < nums.length ; i ++)
            if(freq.containsKey(nums[i]))
                freq.put(nums[i], freq.get(nums[i]) + 1);
            else
                freq.put(nums[i], 1);
        
        //统计出不重复的字符数应该大于k
        if(k > freq.size())
            throw new IllegalArgumentException("k should be less than the number of unique numbers in nums");

        // 扫描freq,维护当前出现频率最高的k个元素
        // 在优先队列中,按照频率排序,所以数据对是 (频率,元素) 的形式
        PriorityQueue<Pair<Integer, Integer>> pq = new PriorityQueue<Pair<Integer, Integer>>(new PairComparator());
        for(Integer num: freq.keySet()){
            int numFreq = freq.get(num);
            if(pq.size() == k){
                if(numFreq > pq.peek().getKey()){
                    pq.poll();
                    pq.add(new Pair(numFreq, num));
                }
            }
            else
                pq.add(new Pair(numFreq, num));
        }

        ArrayList<Integer> res = new ArrayList<Integer>();
        while(!pq.isEmpty())
            res.add(pq.poll().getValue());

        return res;
    }

    private static void printList(List<Integer> nums){
        for(Integer num: nums)
            System.out.print(num + " ");
        System.out.println();
    }
```

如果k和n差不多，维护一个(nlog(n-k))的元素，即维护一个长度n-k的优先队列能大幅提高效率。

#### 练习
23. Merge K sorted Lists（重要）  
k分归并排序，k取多少的时候性能最好？