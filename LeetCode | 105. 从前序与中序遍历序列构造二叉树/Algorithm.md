># 根据一棵树的前序遍历与中序遍历构造二叉树。  
>  
>注意:  
>你可以假设树中没有重复的元素。  
>  
>例如，给出  
>前序遍历 preorder = [3,9,20,15,7]  
>中序遍历 inorder = [9,3,15,20,7]  
>返回如下的二叉树：  
> 
>```python  
>    3  
>   / \  
>  9  20  
>    /  \  
>   15   7 
>```    
根据`前中后序遍历`的定义不难看出，前序遍历的第一个节点一定是`root`，而第二个节点一定是`root`左子树的`root`。  
而在中序遍历中，对于每一个节点其序列中**左边的节点一定在树结构中位于该节点的左边。**  
在上题中前序序列第一个节点为`3`，也就是说`3`就是整个树的`root`，那么要构造树结构的话就需要找到`3`的`左子树`和`右子树`分别构建，我们将棵树构建的问题转换为了更小的**构建左右子树**的问题。  
那么怎么确定节点`3`的左右子树呢？根据中序遍历的定义(左根右)很容易就能想到，在中序序列中`3`这个节点左边的序列就包含了左子树的全部节点，右侧同理。  
这么看来问题就迎刃而解了，我们每次从前序序列中取出一个节点，在当前递归主体内生成新的节点，分别让当前新节点的`left`和`right`分别等于对中序当前节点的左右序列，和前序序列的下一节点的递归值就可以了。  
递归边界就是当前序序列遍历到末尾的时候。  
参考实现如下：
```python  
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        
        def build(val, inorder):
            root = TreeNode(val)    # 构造新的节点
            L, R = [], []
            L, R = inorder[:inorder.index(root.val)], inorder[inorder.index(root.val):]     # 切分左右子树的集合
            if preorder and preorder[0] in L:       # 判断递归条件
                root.left = build(preorder.pop(0), L)       # 递归左子树
            if preorder and preorder[0] in R:       # 判断递归条件
                root.right = build(preorder.pop(0), R)      # 递归右子树
            return root     # 将包含当前节点左子树和右子树的节点root返回给上一层作为上一层的左或右子树
        
        if not len(preorder):
            return None
        else:
            return build(preorder.pop(0), inorder)
```

可以让代码变得更简洁一些：  
```python  
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution(object):

    def buildTree(self, preorder, inorder):
        """
        :type preorder: List[int]
        :type inorder: List[int]
        :rtype: TreeNode
        """
        if len(inorder) == 0:
            return None
        root = TreeNode(preorder[0])        # 前序遍历第一个值为根节点
        mid = inorder.index(preorder[0])        # 因为没有重复元素，所以可以直接根据值来查找根节点在中序遍历中的位置
        root.left = self.buildTree(preorder[1:mid+1], inorder[:mid])        # 构建左子树
        root.right = self.buildTree(preorder[mid+1:], inorder[mid+1:])        # 构建右子树
        
        return root

# https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/solution/python-di-gui-xiang-jie-by-jalan/        
```