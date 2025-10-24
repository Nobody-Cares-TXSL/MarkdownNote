﻿# 树
## 二叉树
### 二叉树术语 :
- 根节点(root node) : 没有父节点的节点
- 叶节点(leaf node) : 没有子节点的节点
- 边(edge) : 连接一个节点和另一个节点的线
- 节点所在层(level) : 根节点所在层为1, 根节点的子节点所在层为2, 以此类推
- 节点的度(degree) : 子节点的数量
- 二叉树的高度(height) : 从根节点到最远叶节点之间边的数量
- 节点深度(depth) : 从根节点到该节点之间边的数量
- 节点高度(height) : 从该节点到最远叶节点之间边的数量   

### 二叉树的类型:
- 满二叉树(full binary tree) 也称 完美二叉树(perfect binary tree) : 所有节点要么没有子节点, 要么有两个子节点 

| | 完美二叉树 |
| :--: | :--: |
| 第i层节点数 | 2^(i - 1) |
| 高度为h的树的叶节点数 | 2^h | 
| 高度为h的树的节点总数 | 2^(h + 1) - 1 |
| 节点总数为n的树的高度 | log2(n + 1) - 1 |
- 完全二叉树(complete binary tree) : 只有最底层节点未被填满,且最底层节点尽量靠左填充
- 完满二叉树(full binary tree) : 除叶节点外, 所有节点都有两个子节点
- 平衡二叉树(balanced binary tree) : 所有节点的左右子树高度差不大于1
- 二叉搜索树(binary search tree) : 所有节点的左子树小于根节点, 右子树大于根节点. 左右子树也为二叉搜索树
### 二叉树遍历
- 层序遍历(level order traversal) : 从根节点开始, 从上到下, 从左到右遍历所有节点 -> 时间复杂度: O(n)
```c++
// 层序遍历
void level_order(Tree* root) {
    // 创建一个队列，用于存储待访问的节点
    std::queue<Tree *> queue;
    // 将根节点入队
    queue.push(root);
    // 创建一个向量，用于存储遍历过程中的节点值
    std::vector<int> vec;
    // 遍历队列中的节点
    while (!queue.empty()) 
    {
        // 取出队列的第一个节点
        Tree* node = queue.front();
        // 将节点从队列中移除
        queue.pop();
        // 将节点的值添加到向量中
        vec.push_back(node->val);
        // 如果节点有左子树，则将左子树入队
        if (node->left!= nullptr) {
            queue.push(node->left);
        }
        // 如果节点有右子树，则将右子树入队
        if (node->right!= nullptr) {
            queue.push(node->right);
        }
    }
    // 打印元素
    for (int i : vec)
    {
        std::cout << i << " ";
    }
    std::cout << std::endl;
}
// 层序插入
void level_insert(Tree** root, int n) {
    // 如果根节点为空，创建一个新根节点
    if (*root == nullptr) {
        int x = rand() % 100 + 1;
        *root = new Tree(x);
    }
    std::queue<Tree*> queue;
    queue.push(*root);
    int count = std::pow(2, n + 1) - 1; // 修正层数计算
    int ans = count - 1;

    // 当队列不为空且还需要插入节点时，继续循环
    while (!queue.empty() && ans > 0) {
        Tree* node = queue.front();
        queue.pop();

        // 如果需要，插入左子节点
        if (node->left == nullptr && ans > 0) {
            int x = rand() % 100 + 1;
            node->left = new Tree(x);
            queue.push(node->left);
            ans--;
        }

        // 如果需要，插入右子节点
        if (node->right == nullptr && ans > 0) {
            int x = rand() % 100 + 1;
            node->right = new Tree(x);
            queue.push(node->right);
            ans--;
        }
    }
}
```
- 前中后序遍历 -> 时间复杂度: O(n)
```c++
static std::vector<int> vec;
// 前序遍历函数，用于遍历二叉树的前序遍历路径
void pre_order(Tree* root) {
    if (root == nullptr) {
        return;
    }
    vec.push_back(root->val);
    pre_order(root->left);
    pre_order(root->right);
}

// 中序遍历函数，用于遍历二叉树的中序遍历路径
void in_order(Tree* root) {
    if (root == nullptr) {
        return;
    }
    in_order(root->left);
    vec.push_back(root->val);
    in_order(root->right);
}

// 后序遍历函数，用于遍历二叉树的后序遍历路径
void post_order(Tree* root) {
    if (root == nullptr) {
        return;
    }
    post_order(root->left);
    post_order(root->right);
    vec.push_back(root->val);
}

// 深度优先搜索函数，用于遍历二叉树的前序、中序和后序遍历路径
void dfs(Tree* root) {
    std::cout << "前序遍历" << " ";
    pre_order(root);
    dfs_print();
    std::cout << std::endl;
    std::cout << "中序遍历" << " ";
    in_order(root);
    dfs_print();
    std::cout << std::endl;
    std::cout << "后序遍历" << " ";
    post_order(root);
    dfs_print();
    std::cout << std::endl;
}
```
### 二叉树的数组表示
> 完全二叉树非常适合用数组表示, None只会出现在最底层且靠右的位置,所以层序遍历之后None全在末尾, 可以直接省略
```c++
class Binary_Tree_Array
{
    public:
    Binary_Tree_Array(std::vector<int> arr) {
        tree_ = arr;
    } 
    ~Binary_Tree_Array() {}
    // 获取容量
    int size() {
        return tree_.size();
    }
    // 获取索引为i的值
    int val(int i) {
        if (i < 0 || i > size()) {
            return INT_MAX;
        }
        return tree_[i];
    }
    // 获取索引为i的左子节点索引
    int left(int i) {
        return 2 * i + 1;
    }
    // 获取索引为i的右子节点索引
    int right(int i) {
        return 2 * i + 2;
    }
    // 获取索引为i的父节点索引
    int parent(int i) {
        return (i - 1) / 2;
    }
    // 层序遍历
    void level_order() {
        if (!tree_.empty()) {
            for (int i : tree_)
            {
                std::cout << i << " ";
            }
            std::cout << std::endl;
        }
    }
    // 前中后序遍历
    void pre_order() {
        std::vector<int> vec_;
        dfs(0, "pre", vec_);
        std::cout << "pre : ";
        for (int i : vec_) 
        {
            if (i != INT_MAX) {
                std::cout << i << " ";
            }
        }
        std::cout << std::endl;
    }
    void in_order() {
        std::vector<int> vec_;
        dfs(0, "in", vec_);
        std::cout << "in : ";
        for (int i : vec_) 
        {
            if (i != INT_MAX) {
                std::cout << i << " ";
            }
        }
        std::cout << std::endl;
    }
    void post_order() {
        std::vector<int> vec_;
        dfs(0, "post", vec_);
        std::cout << "post : ";
        for (int i : vec_) 
        {
            if (i != INT_MAX) {
                std::cout << i << " ";
            }
        }
        std::cout << std::endl;
    }
    private:
    std::vector<int> tree_;
    void dfs(int i, std::string order, std::vector<int> &vec_) {
        if (val(i) == INT_MAX) {
            return;
        }
        if (order == "pre") {
            vec_.push_back(val(i));
        }
        dfs(left(i), order, vec_);
        if (order == "in") {
            vec_.push_back(val(i));
        }
        dfs(right(i), order, vec_);
        if (order == "post") {
            vec_.push_back(val(i));
        }
    }
};
```
### 二叉搜索树
- 插入 : 时间复杂度: O(logn)
- 查找 : 时间复杂度: O(logn)
- 删除 : 时间复杂度: O(logn)
+ 二叉搜索树的中序遍历是升序的
> 二叉搜索树删除操作完成后需保持二叉搜索树的性质"左子树 < 根节点 < 右子树"
> 删除操作根据节点的度分为 0, 1, 2 三种情况
> 节点度为0 : 直接删除
> 节点度为1 : 将待删节点替换为待删节点的子节点
> 节点度为2 : 找到左子树的最大节点或右子树的最小节点,标记该节点, 再删除左子树的最大节点或右子树的最小节点, 再将待删节点替换为标记节点
```c++
// 二叉搜索树
class Binary_Search_Tree
{
    public:
    // 插入
    void insert(int num) {
        if (tree_ == nullptr) {
            tree_ = new Tree(num);
            return;
        }
        Tree *cur = tree_, *pre = nullptr;
        // 循环查找插入位置
        while (cur != nullptr) 
        {
            if (cur->val == num) {
                return;
            }
            // 记录上一个节点
            pre = cur;
            if (cur->val < num) {
                cur = cur->right;
            } else {
                cur = cur->left;
            }
        }
        // 插入节点
        Tree* node = new Tree(num);
        if (pre->val < num) {
            pre->right = node;
        } else {
            pre->left = node;
        }
    }
    // 查找节点
    Tree* search(int num) {
        if (tree_ == nullptr) {
            std::cout << "无节点" << std::endl;
            return nullptr;
        }
        Tree* cur = tree_;
        while (cur != nullptr)
        {
            if (cur->val == num) {
                return cur;
            }
            if (cur->val > num) {
                cur = cur->left;
            } else {
                cur = cur->right;
            }
        }
        std::cout << "无节点" << std::endl;
        return nullptr;
    }
    // 删除节点
    void remove(int num) {
        // 根节点为空
        if (tree_ == nullptr) {
            return;
        }
        Tree* cur = tree_, *pre = nullptr;
        while (cur != nullptr)
        {
            // 找待删节点
            if (cur->val == num) {
                break;
            }
            pre = cur;
            if (cur->val > num) {
                cur = cur->left;
            } else {
                cur = cur->right;
            }
        }
        // 无删除节点
        if (cur == nullptr) {
            return;
        }
        // 删除节点的度为 0 或 1
        if (cur->left == nullptr || cur->right == nullptr) {
            // 节点的度为 0 / 1, node = nullpre / 子节点
            Tree* node = cur->left != nullptr ? cur->left : cur->right;
            // 是否为根节点
            if (cur != tree_) {
                // 左接左,右接右
                if (pre->left == cur) {
                    pre->left = node;
                } else {
                    pre->right = node;
                }
            } else {
                // 若为根节点,则重新指定根节点
                tree_ = node;
            }
            delete cur;
        } // 删除节点的度为 2
        else {
            // // 右树的最小节点
            // Tree* min_node = cur->right;
            // while (min_node->left != nullptr)
            // {
            //     min_node = min_node->left;
            // }
            // int temp_val = min_node->val;
            // // 递归删除
            // remove(temp_val);
            // cur->val = temp_val;

            // 左树的最大节点
            Tree* max_node = cur->left;
            while (max_node->right != nullptr)
            {
                max_node = max_node->right;
            }
            int temp_val = max_node->val;
            remove(temp_val);
            cur->val = temp_val;
        }
    }
    void bst_print() {
        std::vector<int> vec;
        in_order(vec, tree_);
        for (int i : vec) 
        {
            std::cout << i << " ";
        }
        std::cout << std::endl;
    }
    // 中序遍历
    void in_order(std::vector<int>& v, Tree* root) {
        if (root == nullptr) {
            return;
        }
        in_order(v, root->left);
        v.push_back(root->val);
        in_order(v, root->right);
    }
    private:
    Tree* tree_ = nullptr;
};
```
### AVL树
- AVL树即是二叉搜索树又是平衡二叉树,同时满足两类二叉树的性质,是一种平衡二叉搜索树.
- AVL树的相关操作需要获取节点高度,所有在节点中需要添加高度属性
- AVL树的插入和删除操作需更新节点高度, 并调整平衡因子.自底向上执行旋转操作,使失衡因子恢复平衡.
- 节点高度: 节点到叶子节点的最长路径长度. 叶子节点高度为 0, 空节点高度为 -1.
- 平衡因子 : 节点左子树高度 - 节点右子树高度. 空节点平衡因子为 0.
> 设平衡因子为f, 则平衡二叉树的平衡因子的绝对值不大于1, 即 |f| <= 1.
> 节点的平衡因子的绝对值 > 1的节点为"失衡节点".
>
以下为AVL树的相关操作
```c++
// AVL树节点
struct TreeNode
{
    int val{};
    int height = 0; // 节点高度
    TreeNode *left{};
    TreeNode *right{};
    TreeNode() = default;
    explicit TreeNode(int x) : val(x){}
};
class AVL_Tree
{
    public:
    AVL_Tree() {
        root = nullptr;
    }
    AVL_Tree(TreeNode* node) {
        root = node;
    }
    ~AVL_Tree() {}
        // 获取节点高度
    int height(TreeNode* node) {
        return node == nullptr ? -1 : node->height;
    }
    // 更新节点高度
    void up_data_height(TreeNode* node) {
        node->height = std::max(height(node->left), height(node->right)) + 1;
    }
    // 获取平衡因子
    int balance_factor(TreeNode* node) {
        // 空节点平衡因子为 0
        if (node == nullptr) {
            return 0;
        }
        // 节点平衡因子 = 左子树高度 - 右子树高度
        return height(node->left) - height(node->right);
    }
    // 右旋
    TreeNode* right_rotate(TreeNode* node) {
        TreeNode* child = node->left;
        TreeNode* grand_child = child->right;
        // 以child为原点,将node右旋
        child->right = node;
        node->left = grand_child;
        // 更新节点高度
        up_data_height(node);
        up_data_height(child);
        // 返回旋转后子树根节点
        return child;
    }
    // 左旋
    TreeNode* left_rotate(TreeNode* node) {
        TreeNode* child = node->right;
        TreeNode* grand_child = child->left;
        // 以child为原点,将node左旋
        child->left = node;
        node->right = grand_child;
        // 更新节点高度
        up_data_height(node);
        up_data_height(child);
        return child;
    }
    // 旋转操作
    TreeNode* rotate(TreeNode* node) {
        // 获取节点node的平衡因子
        int node_balance_factor = balance_factor(node);
        // 左偏树
        if (node_balance_factor > 1) {
            if (balance_factor(node->left) >= 0) {
                // 右旋
                return right_rotate(node);
            } else {
                // 先左旋后右旋
                node->left = left_rotate(node->left);
                return right_rotate(node);
            }
        }
        // 右偏树
        if (node_balance_factor < -1) {
            if (balance_factor(node->right) <= 0) {
                // 左旋
                return left_rotate(node);
            } else {
                // 先右旋后左旋
                node->right = right_rotate(node->right);
                return left_rotate(node);
            }
        }
        // 平衡树,无需旋转
        return node;
    }
    // 插入节点
    void insert(int val) {
        root =  insert_helper(root, val);
    }
    // 递归插入
    TreeNode* insert_helper(TreeNode* node, int val) {
        // 空节点,直接构造
        if (node == nullptr) {
            return new TreeNode(val);
        } 
        // 寻找正确插入点
        if (val < node->val) {
            node->left = insert_helper(node->left, val);
        } else if (val > node->val) {
            node->right = insert_helper(node->right, val);
        } else {
            // 有重复节点直接返回
            return node;
        }
        // 更新节点高度
        up_data_height(node);
        // 执行旋转操作,使该子树重新恢复平衡
        node = rotate(node);
        // 返回根节点
        return node;
    }
    // 删除节点
    void remove(int val) {
        root = remove_helper(root, val);
    }
    // 递归删除
    TreeNode* remove_helper(TreeNode* node, int val) {
        if (node == nullptr) {
            return nullptr;
        }
        // 寻找节点删除
        if (val < node->val) {
            node->left = remove_helper(node->left, val);
        } else if (val > node->val) {
            node->right = remove_helper(node->right, val);
        } else {
            if (node->left == nullptr || node->right == nullptr) {
                // 子节点数量为 0/1 的情况
                TreeNode* child = node->left != nullptr ? node->left : node->right;
                if (child == nullptr) {
                    // 子节点为 0, 直接删除
                    delete node;
                    return nullptr;
                } else {
                    // 子节点为 1, 删除并替换
                    delete node;
                    node = child;
                }
            } else {
                // 子节点数量为 2 的情况
                // 用左子树最大值节点替换
                TreeNode* left_child = node->left;
                // 找最大值节点
                while (left_child->right != nullptr) 
                {
                    left_child = left_child->right;
                }
                int max_val = left_child->val;
                // 递归删除左子树的最大值节点
                node->left = remove_helper(node->left, max_val);
                // 值替换
                node->val = max_val;
                // // 用右子树最小值节点替换
                // TreeNode* right_child = node->right;
                // while (right_child->left != nullptr) 
                // {
                //     right_child = right_child->left;
                // }
                // int min_val = right_child->val;
                // node->right = remove_helper(node->right, min_val);
                // node->val = min_val;
            }
        }
        // 更新节点高度
        up_data_height(node); 
        // 执行旋转操作,使该子树平衡
        node = rotate(node);
        // 返回根节点
        return node;
    }
    // 打印
    void AVL_print() {
        // vec存储节点的平衡因子
        std::vector<int> vec;
        in_order(root, vec);
        std::cout << std::endl;
        for (int i : vec) 
        {
            std::cout << i << " ";
        }
        std::cout << std::endl;
    }
    void in_order(TreeNode* node, std::vector<int>& v) {
        if (node == nullptr) {
            return;
        } 
        in_order(node->left, v);
        std::cout << node->val << " ";
        v.push_back(balance_factor(node));
        in_order(node->right, v);
    }
    private:
    TreeNode* root;
};
```
#### AVL树的旋转操作
- AVL树的旋转操作分为四种: 左旋, 右旋, 先左旋后右旋, 先右旋后左旋.
- 左右旋为对称操作
1. 左旋
<img width="504" alt="image" src="https://i-blog.csdnimg.cn/img_convert/7332c7f7f754b9b2976aeb76d6edea04.png">

```c++
// 左旋
    TreeNode* left_rotate(TreeNode* node) {
        TreeNode* child = node->right;
        TreeNode* grand_child = child->left;
        // 以child为原点,将node左旋
        child->left = node;
        node->right = grand_child;
        // 更新节点高度
        up_data_height(node);
        up_data_height(child);
        return child;
    }
```
2. 右旋
[![pVCPJJ0.png](https://i-blog.csdnimg.cn/img_convert/3c36ced00fcc54cebf2371f974953db0.png)](https://imgse.com/i/pVCPJJ0)

```c++
// 右旋
    TreeNode* right_rotate(TreeNode* node) {
        TreeNode* child = node->left;
        TreeNode* grand_child = child->right;
        // 以child为原点,将node右旋
        child->right = node;
        node->left = grand_child;
        // 更新节点高度
        up_data_height(node);
        up_data_height(child);
        // 返回旋转后子树根节点
        return child;
    }
```
3. 先左旋后右旋
[![pVCPYWV.png](https://i-blog.csdnimg.cn/img_convert/f10fb5f9707927d4af90e8881aa2e19a.png)](https://imgse.com/i/pVCPYWV)

4. 先右旋后左旋
[![pVCPtzT.png](https://i-blog.csdnimg.cn/img_convert/b1f0c70c1866b3a600317d17868e7692.png)](https://imgse.com/i/pVCPtzT)
- 通过AVL树的不同情况选择不同的旋转操作,使树重新恢复平衡.
以下为封装的旋转操作函数
```c++
// 旋转操作
    TreeNode* rotate(TreeNode* node) {
        // 获取节点node的平衡因子
        int node_balance_factor = balance_factor(node);
        // 左偏树
        if (node_balance_factor > 1) {
            if (balance_factor(node->left) >= 0) {
                // 右旋
                return right_rotate(node);
            } else {
                // 先左旋后右旋
                node->left = left_rotate(node->left);
                return right_rotate(node);
            }
        }
        // 右偏树
        if (node_balance_factor < -1) {
            if (balance_factor(node->right) <= 0) {
                // 左旋
                return left_rotate(node);
            } else {
                // 先右旋后左旋
                node->right = right_rotate(node->right);
                return left_rotate(node);
            }
        }
        // 平衡树,无需旋转
        return node;
    }
```
#### AVL树旋转的选择
| 失衡节点的平衡因子 | 子节点的平衡因子 | 应采用的旋转操作 |
| :--: | :--: | :--: |
| > 1(左偏树) | >= 0 | 右旋 |
| > 1(左偏树) | < 0 | 先左旋后右旋 |
| < -1(右偏树) | <= 0 | 左旋 |
| < -1(右偏树) | > 0 | 先右旋后左旋 |

