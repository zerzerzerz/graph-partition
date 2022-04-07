# 论文阅读笔记
## Paper
- [On Joint Learning for Solving Placement and Routing in Chip Design](https://proceedings.neurips.cc/paper/2021/hash/898aef0932f6aaecda27aba8e9903991-Abstract.html)
- [Towards Machine Learning for Placement and Routing in Chip Design: a Methodological Overview](https://arxiv.org/abs/2202.13564)

## DeepPR
### Movitation
  - Placement
    - 将macros和standard cells布置在芯片上，macros是功能块，比如SRAM，standard cell就是基本的逻辑门(logic gate)
  - Routing
    - 通过布线(assign wires)连接各个元件
  - Objective
    - 优化关于Power, Performance, Area (PPA)的metric
    - 不触及约束条件
      - Placement density (放置密度)
      - Routing congestion (布线拥塞)
### Pipeline
  - Placement
    - 将一个电路用超图$H = (V,E)$进行表示，作为input
    - 首先确定macros的位置，之后确定其他cell的位置
  - Routing
    - 以placement的结果作为input，在各个元件之间进行布线
  - Placing macros sequentially, followed by a gradient-based placer to arrange cells

### Problem Formulation
- 关于Macros placement
  - 在芯片上确定macros的位置，无重叠，wirelength最小
  - 流程
    - 使用RL，每次确定一个macro的位置
    - 当所有的macros确定好位置后，将其固定，使用基于梯度的优化方法完成完整的解（也就是完成其他cell的放置）
  - Key elements of MDPs
    - state $s_t$: Global image $I$描述了整个chip canvas哪些位置是空闲的，netlist $H$描述了被放置的macros的详细信息
    - action $a_t$: chip canvas上空闲的位置
    - reward $r_t$: negative weighted sum of wirelength and routing congestion
### The Structure of Policy Network
- Current state用$n \times n$的image来表示
- 使用CNN对global image进行特征提取
- 使用GNN对netlist hypergraph获取node embedding

### Reward Design
#### Extrinsic
#### Intrinsic