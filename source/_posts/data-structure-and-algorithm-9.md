---
title: 数据结构与算法学习之红黑树
date: 2022-07-06 23:16:09
img: https://cdn.jsdelivr.net/gh/mahoo12138/js-css-cdn/hexo-images/study/algorithm/20210418160102.png
tags: 
- 数据结构与算法
categories: 
- 学习笔记
---

## B树

### 基本概念

B树（英语：B-tree）是一种自平衡多路搜索树，能够保持数据有序。这种资料结构能够让查找数据、顺序访问、插入数据及删除的动作，都在对数时间内完成；

与自平衡二叉查找树不同，B树适用于读写相对大的数据块的存储系统，例如磁盘。B树减少定位记录时所经历的中间过程，从而加快存取速度。

B树这种数据结构可以用来描述外部存储。这种资料结构常被应用在数据库和文件系统的实现上常用于文件系统和数据库的实现中；

---

不同于二叉树，每个B树的节点都可以存储两个以上的元素，每个节点都可以至多有 m 个**子节点**，我们称 m 为树的阶（order）。

### 性质

假设一个节点存储的元素为 x：

+ 根节点：1 ≤ x ≤ m - 1
+ 非根节点：⌈m/2⌉ - 1 ≤ x ≤ m - 1
+ 节点具有子节点，那么子节点个数为 y = x + 1
  + 根节点：2 ≤ y ≤ m
  + 非根节点：⌈m/2⌉ ≤ y ≤ m

例如，m = 3 时，所有节点都满足 2 ≤ y ≤ 3，常称为 (2, 3) 树，2-3树。

---

B树和二叉搜索树，在逻辑上是等价的多代节点合并，可以获得一个超级节点

+ 2代合并的超级节点，最多拥有4个子节点(至少是4阶B树)
+ 3代合并的超级节点，最多拥有8个子节点(至少是8阶B树)
+ n代合并的超级节点，最多拥有2"个子节点(至少是2阶B树)

m 阶的 B 树，最多需要 log~2~m 代合并；

### 搜索节点

与二叉搜索树的搜索类似：

+ 先在节点内部从小到大开始搜索元素；
+ 如果命中，搜索结束；
+ 如果未命中，再去对应的子节点中搜索元素，重复步骤 1；

### 添加节点

+ **新添加的元素必定是添加到叶子节点**；
+ 针对m阶高度h的B树，首先在B树中是否存在，如果不存在，即在叶子结点处插入；
  + 若该节点元素个数小于 m - 1，直接插入；
  + 若该节点元素个数等于 m - 1，引起节点分裂上溢；以该节点中间元素为分界，取中间元素（偶数个数，中间两个随机选取）插入到父节点中；
  + 重复上面动作，直到所有节点符合B树的规则；最坏的情况一直分裂到根节点，生成新的根节点，高度增加 1；

#### 分裂上溢

在添加时，如果待添加节点元素个数为 m - 1，会引起该节点的上溢，因为 m 阶 B 树每个节点至多存储 m - 1 个元素：

+ 上溢节点的元素个数必然是 m 个；
+ 取上溢节点最中间元素的位置为 k，将 k 位置的元素向上与父节点合并；
+ 再将 [0, k - 1] 和 [K + 1, m - 1]位置的元素分割成 2 个子节点；
  + 分裂后的两个节点的元素个数，必然都不会低于最低限制（- 1）
+ 一次分裂完毕后，有可能导致父节点上溢，依旧同样的步骤进行；

### 删除节点 

+ 待删除的是叶子节点的话，直接删除即可；
+ 待删除的节点是非叶子节点：（非叶子节点的前驱或者后继元素，必定在叶子节点中）
  + 先找到前驱或者后继节点，覆盖掉待删除的元素的值；
  + 再把前驱或后继节点删除，**真正的删除都是发生在叶子节点中的**；

#### 下溢合并

若删除后节点中元素数目小于 **⌈m/2⌉ -1**，则需要看其相邻兄弟节点是否丰满（元素个数大于⌈m/2⌉ - 1）；

+ 如果丰满，则向父节点借一个元素，让其兄弟节点**还**一个元素来满足条件；
+ 如果相邻兄弟都刚脱贫，即借了之后其结点数目小于⌈m/2⌉ - 1
+ 则将父节点中的元素，与该节及其其相邻的兄弟节点进行“合并”成一个节点，以此来满足条件。
+ 合并后的节点元素个数为  **⌈m/2⌉ + ⌈m/2⌉ - 2**，不超过 m - 1；
+ 该操作会导致父节点下溢，依然按照上述方法解决，下溢现象可能会一直往上传播；

## 红黑树
