###  226.Invert Binary Tree
```
/// 时间复杂度: O(n), n为树中节点个数
/// 空间复杂度: O(h), h为树的高度
public class Solution {
    public TreeNode invertTree(TreeNode root) {

        if(root == null)
            return null;

        TreeNode left = invertTree(root.left);
        TreeNode right = invertTree(root.right);

        root.left = right;
        root.right = left;

        return root;
    }
}
```
#### 思考：
100. same tree
101. symmetric tree
222. count complete tree nodes
110. balanced binary tree