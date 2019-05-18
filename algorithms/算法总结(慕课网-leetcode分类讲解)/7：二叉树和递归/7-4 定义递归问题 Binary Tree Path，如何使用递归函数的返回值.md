递归函数返回数组
### 257. Binary Tree Paths
- 注意判断终止条件是遇到孩子节点：`if(root.left == null && root.right == null)`
- 因为访问了root,为了防止传入空树访问到Null，对于一开始就要判断` if(root == null)`
- 递归调用函数的语义：获取从跟到叶子的所有路径
```java
/// 时间复杂度: O(n), n为树中的节点个数
/// 空间复杂度: O(h), h为树的高度
public class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        ArrayList<String> res = new ArrayList<String>();

        if(root == null)
            return res;

        if(root.left == null && root.right == null){
            res.add(Integer.toString(root.val));
            return res;
        }

        List<String> leftPaths = binaryTreePaths(root.left);
        for(String s: leftPaths){
            StringBuilder sb = new StringBuilder(Integer.toString(root.val));
            sb.append("->");
            sb.append(s);
            res.add(sb.toString());
        }

        List<String> rightPaths = binaryTreePaths(root.right);
        for(String s: rightPaths) {
            StringBuilder sb = new StringBuilder(Integer.toString(root.val));
            sb.append("->");
            sb.append(s);
            res.add(sb.toString());
        }

        return res;
    }
}
```

#### 练习
113. path sunII
129. sum root to leaf numbers