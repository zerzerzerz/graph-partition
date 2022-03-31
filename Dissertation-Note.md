# Dissertation Note
- [Link](https://publikationen.bibliothek.kit.edu/1000105953)

## Abstract
- 问题定义：Balanced hypergraph partitioning
  - 给定一个超图H以及正整数k，将超图的节点集划分为k个不相交的节点块，每个节点块的大小都有上界；同时最小化目标函数（如cut-net或者connectivity）
- 算法概述
  - 启发式的三阶段多层次图割算法
    - coarsening（节点收缩）
    - initial partitioning
    - uncoarsening and refinement

## Introduction
### Motivation
- 传统的图分割
  - 用于关系网络的处理，通常作为一种预处理技巧
  - edge只能连接两个node，无法处理更为复杂的情况
  - 面对VLSI（very-large-scale integration）能力有限
- 超图与超图分割（HGP）
  - node->vertex
  - edge->hyperedge/net，可以连接两个及以上的vertex
  - 应用
    - VLSI设计，节约external wire带来的cost与delay
    - 数据分布式存储
    - 稀疏矩阵的计算

## Preliminaries
### Notation and Definitions
- ==各种基本定义详见PDF的Page 22==
- Hypergraph
  - vertex
  - hyperedge/net
  - 衍生出的各种定义
  - 超图的两种子结构
    - sub-hypergraph
    - section hypergraph
- Contractions and Uncontractions
  - vertex的收缩与扩张
- Representation of Hypergraph
  - 基于clique（net->完全图）
  - 基于bipartite（二分图表示法）
  - ![](fig/Dissertation-Note/超图的表示.jpg)
### k-way Hypergraph Partitioning Problem
- 两种常见的metric
  - cut-net适合2分的时候使用
  - connectivity适合$k$分$(k \ge 3)$的时候使用
- 在VLSI中，如果一个net连接了多个block，那么会消耗更多的资源，因此此时引入connectivity这种metric是更加合理的
- 算法分类
  - Recursive Bisection
    - 适合k是2的幂次的时候使用，如果k不满足，得到的块的大小将需要调整
  - Direct $k$-way partitioning
    - 直接堆initial hyper graph进行k-partitioning
### Related Problem Formulations
- 其他的metric
- Naturally Imbalanced Partitions
  - 用户自定义两个bound，修改之前的balance约束
- 其他的任务（Hyperedge partitioning）
- 多目标、多约束
### Computational Complexity
- 略
### Algorithmic Approaches
- Local Search
  - 基于neighborhood structure和history of search寻找新的解
  - neighborhood structure定义了移动方式，比如交换两个vertices所属的block，将某个vertex移动到另一个block
  - 基于是不是用history可以分为贪心算法和其他更复杂的算法（有点像DP）
- Hill Climbing/ Iterative Improvment (IIP)
  - 也是一种local search，从某个feasible solution出发，不断寻找更优的邻居解，直至收敛
- Spectral Partition
  - 像GNN/GCN里面的思路，对Graph的Laplacian矩阵进行分解（比如特征值分解）
- Multi-level Diagram
  - 启发式算法
  - 三阶段
    - coarsening
    - initial partitioning
    - uncoarsening/refinement
  - ![](fig/Dissertation-Note/多级算法.jpg)
- Memetic
  - 基因算法（遗传算法）
### Evaluation
- 略


## History of HGP
### Kernighan-Lin (KL) Algorithm
- 是各种IIP算法的基础
- 输入图和bisection，输出优化的bisection
- 中心思想就是计算gain并交换“分错”的vertex
- ![](fig/Dissertation-Note/KL算法.jpg)
- gain
  - 首先计算每个vertex的gain，设其属于A，则gain就是将其从A移动到B引起的internal edge和external edge的变化。如果有更多的external edge变成了internal edge，那么gain就大
  - $g(u) := \omega(\{ (u,v)| v \in \Gamma(u) \cap B \}) - \omega(\{(u,v)| v \in \Gamma(u) \cap A\})$
  - 为了保持bisection，每次需要交换（swap）一对节点
- Limitations
  - bisection（要求两个节点块的大小至多相差1）
  - vertex的权重为1
### Fiduccia-Mattheyses (FM) Algorithm
- 可以应用于bipartitioning ($\varepsilon$-balanced即可)
- 从swap vertices pair变为move single vertex
- Moving FM Gain
  - $I(v)$是与某个vertex $v$相连的net的集合
  - $\Lambda(e)$是与某个net $e$相交的节点块的集合
  - $\Phi(e, V_i) := \lvert e \cap V_i \rvert$也就是求某个net和节点块的交集的大小，也就是这个net在这个节点块中的number of pins
  - 设某个vertex属于节点块0，那么将其从0移动到1带来的gain就是$$g(v) := \omega(\{ e \in I(v) | \Phi(e,V_1) = \vert e \vert - 1 \}) - \omega(\{ e \in I(v) | \Phi(e,V_0) = \vert e \vert  \})$$
  - 前项就是从cut-net变成internal-net的数量，后项就是从internal-net变成cut-net的数量，和之前KL算法中的gain有相似之处
- Balanced bipartition
  - 放宽了balance的约束
- 与KL过程类似，从swap变成了move
### Single-Level Partitioning beyond KL/FM
- 基于KL和FM算法衍生出很多single-level的算法
- level体现在并没有对graph进行coarsening，直接对original graph进行分割和操作
### From Two-Level To Multi-Level Partitioning
- 介绍了一堆算法，但只是提了一下

## $n$-Level Hypergraph Partitioning
- 正式介绍Karlsruhe Hypergraph Partitioning也就是KaHyPar
### Data Structure
- 将Hypergraph表示为bipartite（二分图）G，G的节点集是超图的vertices和nets，如果$v \in e$，那么$(e,v)$就是G中的一条edge
- ![](fig/Dissertation-Note/超图的二分图表示的数据结构.jpg)
  - $V$数组表示了每个vertex属于的net
  - $E,A$两个数组表示了net连接的vertex，其中$E$的每个entry有两个分量，分别是这个net在$A$的from(f)和size(s)
- Contraction
  - 对于一个vertex-pair $(u,v)$，判断其contraction-partner $v$是直接delete还是relink
- Uncontraction
  - 判断是需要revert a delete or relink
### Computing $k$-way Partitions via Recursive Bipartitioning
#### 算法概述
  - Preprocessing
  - n-level coarsening, contracting vertices pair $(u,v)$
  - Initial bi-partitioning
  - Uncontracting and refinement
  - Recursive bi-partitioning on sub-structure
- ![](fig/Dissertation-Note/n-level%20recursive%20bipartitioning.jpg)
- 超图H的两种子结构
  - 设子节点集$V_i \subset V$ 
  - subhypergraph
    - $H_{V_i} = (V_i, \{e \cap V_i | e \in E, e \cap V_i \neq \emptyset\})$
    - 也就是说，只要一个net $e$与节点子集$V_i$交集非空，那么就可以添加到子超图中
  - section hypergraph
    - 与subhypergraph类似，但是必须$e \subseteq V_i$
    - $H \times {V_i} = (V_i, \{e | e \in E, e \subseteq V_i\})$
- 算法思想
  - 不需要初始的划分，只需要一个输入一个超图以及各种bound
  - contract/coarsening
  - initial partitioning
  - untract and refine
  - 根据选定的object来对子结构进行递归
    - cut-net使用section hypergraph，因为cut-net只看割集的数量，section中不含割集，这些割集的贡献已经被考虑过了
    - connectivity使用sub-hypergraph
### Preprocessing Phase
#### Pin Sparcification
- 一个超图可能有很多similar vertices，需要将其合并
- 定义similarity
  - Jaccard Coefficient
    - $J(u,v) = \frac{\vert I(u) \cap I(u) \vert}{\vert I(u) \cup I(v) \vert }$
  - $D(u,v) := 1 - J(u,v)$
- 计算
  - 可以通过遍历的方法去计算，但这样太慢了，提出locality sensitive hashing的方法降低时间复杂度
- Locality Sensitive Hashing (LSH)
  - 使用某种方式散列元素，使得相似的元素有更高的概率有相同的哈希值，反之更低的概率
- Min-Hash Fingerprint
  - 这里的hash function比较简单，$h(x) := ax + b \pmod{P}$
  - $h_i(v) := \min_{e}\{h_i(e) | e \in I(v), h_i \in \Sigma\}$
  - 选取$i$个不同的hash function，就可以求出$i$个hashing of vertex，然后得到一个vertex的finger print
  - 有着相同fingerprint的vertex分到一个cluster中
- 起初每个vertex都有自己的cluster，经过基于LSH的算法之后，将减少cluster的数量

#### Detecting Community Structure
- Motivation
  - Coarsening阶段greedy and local，需要引入更多的global information
- 先使用正常的community detection algorithm发现community，然后再每个community中进行vertex contraction来缩小net size
- Hypergraph Representation
  - 为了使用community detection，需要表示hypergraph
  - Clique
    - 不行，每个net变成一个完全图，添加过多的边，破坏了原有的稀疏性
  - Bipartite
- Weighting edge
  - 需要给bipartite中的edge赋权重，根据vertex-node和edge-node（net-node）的相对关系，选择不同的赋权方式，以实现更好的community detection

### Coarsening Phase
- Heavy edge rating function for vertices pair $(u,v)$
- 基于KasPar的Coarsening算法，进行generalization
- Coarsening Algorithm
  - 计算每对节点的rating，用一个优先级队列PQ维护
  - 每次取队首进行contraction
  - 从PQ中删除被合并的节点，重新计算rating（代表元u与其邻居的rating），重新选择contraction partner
- Lazy update strategy
  - 每次contraction之后将代表元的邻居节点置为invalid
  - 如果之后contraction取到了invalid的节点，再更新PQ
- 其他更加简单的Coarsening算法

### Portfolio-Based Initial Partitioning
- KaHyPar uses a portfolio of several algorithms to compute an initial solution
- 使用一系列算法求出一个initial partition

### Localized $2$-way and $k$-way FM Local Search
- 从这里开始就属于Refinement的部分了，Refinement是在uncontraction之后进行的
- FM Gain，移动之后，从cut变成internal的，减去，从internal变成cut的
#### $2$-way
- 使用时机
  - 每次uncontraction之后使用
- 初始化
  - 两个优先级队列，每个block一个，置为empty和disabled
  - 标记全部的节点为inactive和unmarked
- 开始
  - 激活所有的代表节点和刚刚因为uncontraction而新增的节点，同时需要确保被激活的节点都是border vertex
- 计算FM gain
  - 激活一个节点，也就意味着计算其FM gain，也就是将其从原来的block移动到其他所有的block来计算这次move的gain（因为这里是2-way因此target block只有1个）
  - 将gain入队至target block对应的PQ
- 寻找最优gain并move
  - 对于那些非空并且underloaded的PQ，将其置为enabled
  - 从全部的enabled的PQ中，找出gain最大的，实施这次move，并将被移动的节点（不妨设为$v$）置为inactive和marked（marked意味着这个节点不能再次被激活了）
- 更新$v$的邻居节点
  - 激活全部的邻居节点
  - 对于那些internal的邻居节点（不位于cut中），将其置为inactive并且把它们对应的move从全部的PQ中删除
  - 更新剩余的、active邻居节点
- 重复上述操作，也就是取PQs中的最高gain，直到全部的PQ都disabled
- 当local search结束时，和当前的最优解进行比较，如果更差，就reverse all moves然后重来（从initialization重新开始）
#### $k$-way