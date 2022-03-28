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