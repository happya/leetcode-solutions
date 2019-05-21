---
title: Two sum 总结
tags: leetcode
notebook: Leetcode
---

# Two Sum系列总结

## 1. Two sum

思路：
- 用一个字典`dict`存储待需的目标值及其下标，格式为`{目标数值: 目标数值下标}`。

- 对于访问的数据`x`，其目标值为`target-x`，如果该目标值存在，即可返回下标，即`dict[target-x]`。

- 如果不存在，那么当前数值可以作为将来找到数的目标数值，将其存入字典`{x:i}`，其中`i`为`x`的下标。

```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        to_find = {}
        for i, num in enumerate(nums):
            if target - num in to_find:
                return [i, to_find[target-num]]
            to_find[num] = i
```

```cpp
class Solution {
    public:
        vector<int> twoSum(vector<int>& nums, int target) {
            unordered_map<int,int> sum;
            vector<int> res;
            for(int i=0;i<nums.size();i++){
                int t=target-nums[i];
                if(sum.find(t)!=sum.end()){
                    res.push_back(sum[t]);
                    res.push_back(i);
                    return res;
                }
                else{
                    sum[nums[i]]=i;
                }
            }
            return res;
        }
    };
```
## 653: 2Sum-BST

思路：

### 1. $O(n)$ Time, $O(n)$ Space


> BST的中序遍历即为对其数据从小到大排序，用一个vector存储中序遍历值，问题退化为普通的BST。


```cpp
/**
* Definition for a binary tree node.
* struct TreeNode {
* int val;
* TreeNode *left;
* TreeNode *right;
* TreeNode(int x) : val(x), left(NULL), right(NULL) {}
* };
*/
class Solution {
    private:
        void inorder(TreeNode* root, vector<int>& nums){
        if(root==NULL) return;
        inorder(root->left,nums);
        nums.push_back(root->val);
        inorder(root->right,nums);
    }
    bool find(vector<int> nums, int target){
        int i=0,j=nums.size()-1;
        while(i<j){
            int sum=nums[i]+nums[j];
            if(sum==target)
                return true;
            else if(sum>target)
                j--;
                else i++;
        }
        return false;
}
public:
    bool findTarget(TreeNode* root, int k) {
        vector<int> nums;
        inorder(root,nums);
        return find(nums,k);
    }
};
```

### 2. O(n)Time O(h)Space


问：如果不用O(n)Space呢？写一个iterator，用双Pointer分别指向最左和最右节点，重载++运算符，让其分别找寻下一个合适的节点，整个写下来有点类似于分成两边的中序遍历，用stack存储其前驱节点信息。

```cpp
class BSTIterator{
    private:

    TreeNode* node; //当前访问节点
    bool forward; //表面前进的方向
    stack<TreeNode*> s;//存储待访问的节点的顺序
    int cur; //存储当前监控值，在2sum中需要用到的
    public:
        BSTIterator(TreeNode* root,bool forward_):node(root),forward(forward_){}
        int operator*(){return cur;}
        void operator++(int){
            while(node || !s.empty()){
                if(node) {//当前节点不为空，则存储该节点并继续往前走
                s.push(node);
                node=forward?node->left:node->right;
            }
            else{//当前节点为空，找出其前驱节点，继续走
                node=s.top();
                s.pop();
                cur=node->val;
                node=forward?node->right:node->left;
                break;
            }
        }
    }
};
class Solution {
    public:
    bool findTarget(TreeNode* root, int k) {
        if(root==NULL) return false;
        BSTIterator le(root,true);
        BSTIterator ri(root,false);
        le++;ri++;// 也可以在构造函数内添加(*this)++; le和ri分别初始化为最左和最右节点
        while(*le<*ri){
            cout<<*le<<" "<<*ri<<endl;
            if(*le+*ri==k) return true;
            else if(*le+*ri<k) le++;
            else ri++;
        }
        return false;
    }
};
```