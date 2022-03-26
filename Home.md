# Welcome to the graph-partition wiki!

## 网站
- [工具网站kahypar](https://kahypar.org/)

## 基本概念
- Hypergraph
  - 是Graph的推广
  - 通常的graph中，一条边（edge）连接两个节点，但是在Hypergraph中，一条边可以连接的节点数大于等于2，边edge也叫做hyper edge或者叫做net
- k-way hypergraph partitioning
  - 是基本图分割问题的推广
  - 将节点集划分为k个不相交的节点块，每个节点块的大小都有一个上界，通常是$(1+\varepsilon)$倍的平均块大小
  - 目标函数
    - 有两种常用的目标函数，分别是cut-net和connectivity($\lambda - 1$ metric)
    - Cut-net metric
      - 是传统的图分割问题中edge-cut的推广，目标函数就是将那些连接了多个节点块的net的权重相加，目标是使其最小化
    - Connectivity metric
      - 每一个节点块都连接了若干net，对于任意一个节点块，设其连接的net的数量为$lambda$，目标函数就在原先的Cut-net的基础上，给每个net的权重分配一个系数，这个系数就是该net所对应的$lambda - 1$
- 符号化表述
  - Hypergraph, $H = (V,E,c,\omega)$, $V$ is vertex set, $E$ is edge(net) set, $c$ is weight of each node, $\omega$ is weight of each egde(net)
  - k-ways partition $\Pi = \{V_1, V_2, ..., V_k \}$,$$c(V_i) \le (1+\varepsilon) \lceil \frac{c(V)}{k} \rceil$$
  - Ovjective function (to minimized)
    - Cut-net
      - $\sum_{e \in cut}\omega(e)$
    - Connectivity
      - $\sum_{e \in cut}(\lambda-1)\omega(e)$

## KaHyPar简介
- 多层次超图分割框架
- 支持的metric
  - Cut-net
  - Connectivity
- 支持的方法
  - 递归二分（recursive bisection）
  - 直接进行k-way的分割（direct k-way partitioning）
- 多层次（Multi-level）， 分为下面的三个阶段
  - Coarsening
    - 将超图粗化处理，获取很多更小的超图的层次结构
  - Second phase
    - initial partition，获取最小的超图
  - Final phase
    - 取消粗化，在每一个层级上使用本地搜索（local search）的方法提高划分质量
  - [详细算法](https://github.com/kahypar/research-publications)
- 其他特性
  - 支持可变的节点块权重（experimental）
    - 划分时可以指定每个节点块的权重上界$B_k$
  - 固定节点（预分配节点至某个节点块）
    - 对原有的划分算法增加新的约束
    - 比如，指定某些节点，必须属于某个节点块，也就是说，这些节点不参与划分，在超图划分之前，被预分配到指定的节点块
    - 面对固定节点时，KaHyPar支持3种不同的收缩策略（contraction policies）
      - `free_vertex_only`
        - 如果收缩伙伴（contraction partner）是free vertex，则允许进行收缩
      - `fixed_vertex_allowed(default policy)`
        - 额外允许被预分配至同一节点块的fixed vertex进行收缩
      - `equivalent_vertices`
        - 只允许这样的收缩
          - 某个节点对中，两个节点都是free vertex
          - 某个节点对中，两个节点都是fixed vertex且属于同一个节点块
  - 进化算法框架
    - [详细算法](https://dl.acm.org/doi/10.1145/3205455.3205475)
  - 提高现有分割的质量


