# 机器人长期记忆与 Memory VLA 文献综述

> 更新日期：2026-07-15  
> 主题：Vision-Language-Action、长期记忆、部分可观测操作、TBPTT、全历史状态模型、事件/关键帧记忆

## 摘要

长时程机器人操作往往不满足马尔可假设：当前观测无法完整表示对象状态、任务进度、历史交互及已离开视野的证据。因此，VLA 策略需要在有限计算与显存预算下保留和读取有效历史。

本文按记忆表示与信息流程对相关工作分类，覆盖显式记忆库、检索与压缩、递归状态、完整轨迹建模、事件与关键帧记忆、对象中心表示、多模态记忆以及系统级终身记忆，并汇总对应评测基准。

## 1. 研究范围与分类框架

机器人记忆方法可从四个维度理解：“写入什么”决定记忆模态，“何时写入”决定时间粒度，“如何保留”决定容量与遗忘规则，“如何读取”决定历史信息与当前策略的融合方式。

依据核心机制，现有研究可分为显式记忆与检索、递归隐状态、全历史序列建模、稀疏事件记忆、结构化对象/空间记忆、语言与动作记忆，以及跨 episode 的经验与终身记忆。后文按这一谱系组织文献。

## 2. 代表性工作导读

| 方法类别 | 论文 | 年份 | 核心记忆机制 | 研究定位 |
|---|---|---:|---|---|
| 历史压缩 | [NativeMEM: Native Memory Compression for Long-Horizon Robotic Manipulation](https://arxiv.org/abs/2607.06678) | 2026 | 使用 VLA 自身视觉编码器，把每个历史“帧—视角”压缩为一个原生 token | 原生 token 形式的紧凑视觉历史 |
| 多模态记忆 | [LaMem-VLA: Dual Latent Memory in Vision-Language-Action Models](https://arxiv.org/abs/2607.07608) | 2026 | 短期视觉 vault、长期语义/动作 vault；检索、压缩后直接织入 VLA latent sequence | 双层潜在记忆与多模态压缩 |
| 全历史建模 | [Chronos: A Physics-Informed Full-History Framework for Non-Markovian Long-Horizon Manipulation](https://arxiv.org/abs/2606.30318) | 2026 | 每个物理时刻一个 state token，选择性 SSM 编码完整轨迹 | 非截断式全历史训练 |
| 事件记忆 | [EventVLA: Event-Driven Visual Evidence Memory for Long-Horizon VLA Policies](https://arxiv.org/abs/2606.20092) | 2026 | 初始/近期视觉 anchor + 预测未来效用的动态事件关键帧 | 学习式事件保留与瞬时证据建模 |
| 关键帧记忆 | [KEMO: Event-Driven Keyframe Memory for Long-Horizon Robot Manipulation with VLA Policies](https://arxiv.org/abs/2606.23589) | 2026 | 运动学事件检测、视觉差异过滤、关键帧交叉注意力读取 | 基于事件的稀疏视觉历史 |
| 工作/长期记忆 | [MemoAct: Atkinson–Shiffrin-Inspired Memory-Augmented Visuomotor Policy](https://arxiv.org/abs/2603.18494) | 2026 | 无损短期 bank + 压缩长期 bank + 相似度合并 | 认知模型启发的双层记忆 |
| 递归记忆 | [μVLA: On Recurrent Memory for Partially Observable Manipulation](https://arxiv.org/abs/2606.12497) | 2026 | 跨时刻传递少量 memory tokens | temporal TBPTT 与 recurrent memory |
| 递归记忆 | [ReMem-VLA: Empowering VLA with Memory via Dual-Level Recurrent Queries](https://arxiv.org/abs/2603.12942) | 2026 | frame-level 与 chunk-level recurrent queries，固定 EMA 传播 | 双时间尺度与 gradient-free recurrence |
| 显式记忆库 | [MemoryVLA: Perceptual-Cognitive Memory in VLA Models](https://arxiv.org/abs/2508.19236) | 2025 | working memory、perceptual-cognitive bank、检索与冗余合并 | 感知—认知显式记忆库 |
| 固定历史窗口 | [HAMLET: Switch your VLA Model into a History-Aware Policy](https://arxiv.org/abs/2510.00695) | 2025 | moment tokens、时间对比初始化、轻量历史聚合模块 | 短窗口历史聚合 |
| 前瞻记忆 | [Chameleon: Control-Indexed Prospective Memory for Visuomotor Manipulation](https://arxiv.org/abs/2603.24576) | 2026 | 写入 geometry-grounded embodied event memory，通过可微记忆栈形成面向控制的 prospective state | 可分离、可寻址的面向控制记忆 |
| 视觉/语言记忆 | [MEM: Multi-Scale Embodied Memory for Vision Language Action Models](https://arxiv.org/abs/2603.03596) | 2026 | 短期视频记忆 + 长期语言事件记忆 | 视觉与语义混合的分钟级记忆 |
| 工作/情景记忆 | [VPWEM: Non-Markovian Visuomotor Policy with Working and Episodic Memory](https://arxiv.org/abs/2603.04910) | 2026 | 近期 working window + 递归 episodic compressor | 无损工作记忆与有损情景记忆 |
| KV 记忆 | [TempoFit: Plug-and-Play Layer-Wise Temporal KV Memory](https://arxiv.org/abs/2603.07647) | 2026 | 缓存中间层 prefix K/V，相似度检索并施加时间衰减 | training-free 时间 KV 记忆 |

## 3. 显式记忆、检索与压缩

| 论文 | 年份 | 主要机制 | 历史范围/特点 |
|---|---:|---|---|
| [TFP: Temporally Conditioned Memory-Fusion Policies for Visuomotor Learning](https://arxiv.org/abs/2607.08283) | 2026 | 显式时间条件的 latent belief 与记忆融合 | 强调 elapsed-time-conditioned task dynamics |
| [HiMe: Hierarchical Embodied Memory for Long-Horizon VLA Control](https://arxiv.org/abs/2607.03449) | 2026 | Executor、Sentry、Planner 分层；Add/Update/Delete 动态知识管理 | 系统级快慢双过程记忆 |
| [WeaveLA: Event Driven Cross-Subtask Latent Memory Weaving](https://arxiv.org/abs/2606.17463) | 2026 | 子任务完成时写入，压缩后传递给下一子任务 action expert | 跨子任务事件记忆；强调分阶段训练 |
| [Remember What You Did? Learning Behavioral Memories for Partially Observable Object Manipulation](https://arxiv.org/abs/2606.21188) | 2026 | CAMP 使用过去动作的 DCT 重建训练递归记忆 | 从 action history 学习行为/进度记忆 |
| [DSSP: Diffusion State Space Policy with Full-History Encoding](https://arxiv.org/abs/2605.14598) | 2026 | Mamba history encoder + diffusion policy | 因果完整轨迹 scan |
| [Analytic Concept-Centric Memory for Agentic Embodied Manipulation](https://arxiv.org/abs/2606.29774) | 2026 | 对象语义部件、位姿、affordance、状态转移与技能记忆 | 结构化、可解释的 agentic memory |
| [Vesta: A Generalist Embodied Reasoning Model](https://arxiv.org/abs/2606.20905) | 2026 | 历史帧与运行中的文本子任务 cache | 更偏高层规划记忆 |
| [VQ-Memory for Robust Long-Horizon Manipulation](https://arxiv.org/abs/2603.09513) | 2026 | VQ-VAE 将历史关节状态离散为任务阶段 token | 轻量、抗噪的 proprioceptive memory |
| [Non-Markovian Long-Horizon Robot Manipulation via Keyframe Chaining](https://arxiv.org/abs/2603.01465) | 2026 | 选择历史关键帧并作为视觉 token 与当前帧交错输入 | 稀疏全历史视觉表示 |
| [BPP: Long-Context Robot Imitation Learning by Focusing on Key History Frames](https://arxiv.org/abs/2602.15010) | 2026 | 利用现成 VLM 问答选择语义关键帧 | 外部模型驱动的 sparse memory |
| [Recursive Belief Vision-Language-Action Models](https://arxiv.org/abs/2602.20659) | 2026 | 自监督 world-model 目标维护递归 latent belief | belief-centric recurrent memory |
| [EchoVLA: Synergistic Declarative Memory for VLA-Driven Mobile Manipulation](https://arxiv.org/abs/2511.18112) | 2025 | scene memory + episodic memory，粗到细检索融合 | 移动操作与空间语义记忆 |
| [MemER: Scaling Up Memory for Robot Control via Experience Retrieval](https://arxiv.org/abs/2510.20328) | 2025 | 经验库、关键帧化经验与检索 | episodic experience retrieval |
| [History-Aware Visuomotor Policy Learning via Point Tracking](https://arxiv.org/abs/2509.17141) | 2025 | 全历史对象点轨迹、对象级聚合 | 对象中心的运动/任务阶段记忆 |
| [PAM: Resolving State Ambiguity via Adaptive Working Memory Recoding](https://arxiv.org/abs/2512.24638) | 2025 | 多时间范围 query、working memory recoding、历史重建 | 最长约 300 帧的尺度自适应记忆 |
| [ContextVLA: Visual Context Compression for Efficient Multi-Frame VLA](https://arxiv.org/abs/2510.04246) | 2025 | 将历史观测压成单个 context token | 紧凑多帧基线 |
| [CronusVLA: A Vision-Language-Action Model with Multi-Frame Context](https://arxiv.org/abs/2506.19816) | 2025 | 单帧预训练、多帧后训练、历史特征缓存 | 短期多帧上下文，不是真正无限历史 |

## 4. 递归状态、TBPTT 与完整轨迹训练

| 论文 | 是否使用 TBPTT | 截断的维度 | 备注 |
|---|---|---|---|
| [μVLA](https://arxiv.org/abs/2606.12497) | **是，明确** | 机器人 episode 的物理时间轴 | 研究 TBPTT 长度、跨步梯度与 detached EMA |
| [AVA-VLA](https://arxiv.org/abs/2511.18960) | **是，明确** | 连续观测时间轴 | 短窗口展开；ReMem-VLA 引用其典型长度为 4 |
| [ReMem-VLA](https://arxiv.org/abs/2603.12942) | **是，但 horizon=1** | 物理时间轴 | 跨时刻长期传播由冻结 VLM 与固定 EMA 完成，不依赖跨步梯度 |
| [VPWEM](https://arxiv.org/abs/2603.04910) | **是，采用逐次写入 stop-gradient** | observation cache 与 summary cache 的递归时间轴 | 历史 token 每次写入 cache 时 `detach`；前向信息覆盖完整 episode，但梯度不会沿递归摘要链跨时间传播，功能上相当于极短 horizon 的 TBPTT |
| [Recurrent-Depth VLA](https://arxiv.org/abs/2602.07845) | **是，明确** | action head 的迭代推理深度 | 不是 episode 时间轴的长期记忆 TBPTT |
| [Chronos](https://arxiv.org/abs/2606.30318) | **否，明确采用完整轨迹反传** | 无截断 | 冻结/分块视觉编码，完整梯度保留在轻量 temporal-action stack |
| [MTIL: Encoding Full History with Mamba for Temporal Imitation Learning](https://arxiv.org/abs/2505.12410) | 未称为 TBPTT | 算法按完整轨迹累积损失 | Mamba-2 step recurrence；轨迹结束后更新 |
| [DSSP](https://arxiv.org/abs/2605.14598) | 未声明 TBPTT | 完整 demonstration scan | 更接近 trajectory-wise stateless full-history encoding |
| [Chameleon](https://arxiv.org/abs/2603.24576) | 未明确称为 TBPTT | sequence chunks | 对 chunk 尾部若干帧施加策略损失 |
| [HAMLET](https://arxiv.org/abs/2510.00695) | 未声明 | 固定历史窗口 | 主要是 moment-token 聚合，不是持续 recurrent state |
| [TempoFit](https://arxiv.org/abs/2603.07647) | 否 | 不训练 | 完全 training-free 的 KV memory retrofit |

### TBPTT 判断标准

- **Temporal TBPTT**：隐藏状态跨真实控制时刻传递，同时梯度仅回传最近的固定步数。
- **Detached streaming state**：状态跨 batch/segment 保留，但在边界处 `detach`；功能上相似于 TBPTT，代码可能没有直接命名为 `tbptt`。
- **Recurrent depth TBPTT**：截断同一次动作预测中的迭代 refinement，不等价于长期时间记忆。
- **Full-trajectory backpropagation**：损失可沿 temporal module 回传到完整轨迹早期，不属于 TBPTT。
- **Stateless full scan**：每个优化步骤重新扫描完整轨迹，通常不跨 minibatch 保存隐藏状态。

## 5. Memory Policy、系统级记忆与评测资源

### 5.1 记忆评测与数据集

| 论文/基准 | 年份 | 主要评测维度 |
|---|---:|---|
| [RoboMME: Benchmarking and Understanding Memory for Robotic Generalist Policies](https://arxiv.org/abs/2603.04639) | 2026 | temporal、spatial、object、procedural memory；16 个任务、14 种 memory-VLA 变体 |
| [RMBench: Memory-Dependent Robotic Manipulation Benchmark](https://arxiv.org/abs/2603.01229) | 2026 | 非马尔可夫操作、历史依赖、对象位置与任务进度 |
| [RoboTwin-MeM](https://arxiv.org/abs/2606.20092) | 2026 | 中间交互中短暂出现、之后消失的视觉证据 |
| [RuleSafe](https://arxiv.org/abs/2603.09513) | 2026 | 密码、逻辑锁、规则驱动的多阶段非马尔可夫任务 |
| [MemoryRTBench / MemoAct](https://arxiv.org/abs/2603.18494) | 2026 | working memory、long-term memory 与双层压缩策略 |
| [MIKASA-Robo](https://arxiv.org/abs/2606.12497) | 2026 | 部分可观测任务及 recurrent memory 泛化 |

### 5.2 Memory VLA / Memory Policy

| 论文 | 年份 | 主要机制 | 比较价值 |
|---|---:|---|---|
| [OptimusVLA: Global Prior Meets Local Consistency](https://arxiv.org/abs/2602.20200) | 2026 | Global Prior Memory 从相似轨迹检索动作先验；Local Consistency Memory 编码已执行动作和任务进度 | 同时覆盖跨示范检索与 episode 内动作记忆 |
| [SOMA: Spatial Memory for Out-of-Vision Manipulation in VLA](https://arxiv.org/abs/2605.22283) | 2026 | 多视角扫描构建持久空间语义记忆，动态修正并按指令检索 | 检验长期记忆是否能支持视野外对象定位，而非仅记住可见历史 |
| [MAP-VLA: Memory-Augmented Prompting for VLA in Robotic Manipulation](https://arxiv.org/abs/2511.09516) | 2025 | 从示范构建任务阶段 memory library；以可学习 soft prompts 表示并按轨迹相似度检索 | 区分跨示范软提示记忆与 episode 内在线记忆 |
| [DiM-WAM: World-Action Modeling with Diverse Historical Event Memory](https://arxiv.org/abs/2606.27677) | 2026 | 多个历史事件 memory banks 独立进行相似度合并；加入 bank identity、时间编码与任务进度监督 | 适合研究多 bank 身份、时间尺度编码和 progress supervision |
| [SeedPolicy: Horizon Scaling via Self-Evolving Diffusion Policy](https://arxiv.org/abs/2603.05117) | 2026 | SEGA 通过 gated attention 维护固定大小、随时间演化的 latent state | 可作为学习式 O(1) recurrent compression 基线 |
| [HiF-VLA: Hindsight, Insight and Foresight through Motion Representation](https://arxiv.org/abs/2512.09928) | 2025 | 用 motion 表示压缩历史变化，并联合 hindsight 与 future-motion reasoning | 以运动表征连接历史理解与未来预测 |
| [CycleManip: Enabling Cyclic Task Manipulation via Effective Historical Perception](https://arxiv.org/abs/2512.01022) | 2025 | cost-aware 历史采样与多任务历史理解，面向重复动作和正确终止时间 | 适合验证计数、周期和 elapsed-time memory |
| [CDP: Robust Autoregressive Visuomotor Policy Learning via Causal Diffusion](https://arxiv.org/abs/2506.14769) | 2025 | 以历史动作序列条件化 diffusion policy，并缓存跨时刻 attention K/V | action-history memory 与视觉记忆的互补基线 |
| [TraceVLA: Visual Trace Prompting Enhances Spatial-Temporal Awareness](https://arxiv.org/abs/2412.10345) | 2024 | 将状态—动作轨迹渲染成当前图像上的视觉 traces | 无需显式 latent bank 的历史压缩基线 |
| [LoHo-Manip: Long-Horizon Manipulation via Trace-Conditioned VLA Planning](https://arxiv.org/abs/2604.21924) | 2026 | task-management VLM 输出 done/remaining 语言记忆和 2D visual trace，短时 VLA 负责局部执行 | 高层显式进度记忆与低层端到端 latent memory 的比较 |

### 5.3 系统级与终身记忆

| 论文 | 年份 | 主要机制 | 范围 |
|---|---:|---|---|
| [RoboMemory: A Brain-inspired Multi-memory Agentic Framework for Lifelong Learning](https://arxiv.org/abs/2508.01415) | 2025 | 并行维护 spatial、temporal、episodic、semantic memory，并结合动态知识图谱与闭环规划 | 跨任务、终身与 agentic memory；超出单 episode policy memory，但适合讨论系统边界 |
| [AdaManip: Adaptive Articulated Object Manipulation Environments and Policy Learning](https://arxiv.org/abs/2502.11124) | 2025 | 面向不可直接观测内部机构，通过历史试探结果自适应操作 | 不是专门的 memory architecture，但提供典型 trial-and-error 非马尔可夫任务 |

### 5.4 相关综述

| 论文 | 年份 | 内容 | 用途 |
|---|---:|---|---|
| [Memory, Benchmark & Robots: MIKASA](https://arxiv.org/abs/2502.10550) | 2025 | MIKASA-Base 与包含 32 个桌面操作任务的 MIKASA-Robo | 评估不同记忆类型、历史跨度与部分可观测操作 |
| [Large VLM-based VLA Models for Robotic Manipulation: A Survey](https://arxiv.org/abs/2508.13073) | 2025 | 大型 VLM-based VLA 的架构、训练、world model、memory 与效率分类 | 用于完善 related-work taxonomy 和持续追踪相关方法 |
| [A Survey on VLA Models for Embodied AI](https://arxiv.org/abs/2405.14093) | 2024–2026 | 持续更新的 VLA 方法、数据、基准与能力综述 | 用于查漏补缺，不作为 memory 方法本身 |

## 6. 代表性方法谱系

- **无记忆与固定窗口**：Memory-free VLA 用于测量记忆的净收益；HAMLET 代表固定窗口与 moment-token 聚合。
- **密集历史压缩**：NativeMEM 代表逐帧紧凑 token；MemoryVLA 与 MemoAct 代表检索、冗余判定和长短期记忆合并。
- **稀疏事件记忆**：EventVLA 与 KEMO 根据未来效用、运动学事件或视觉变化保留关键帧。
- **递归记忆**：μVLA 使用 recurrent tokens 与 TBPTT；ReMem-VLA 以固定 EMA 实现不依赖跨步梯度的长期传播。
- **完整历史编码**：Chronos、MTIL 与 DSSP 利用轻量 SSM 或 Mamba 扫描轨迹，强调线性复杂度与长时程信息传递。

## 7. 相关方法分类与机制对照

本节按训练目标、状态表示和记忆模态统一整理相关方法，不按检索或加入文档的时间顺序区分。

### 7.1 长上下文与辅助训练目标

| 论文 | 年份 | 主要机制 | 与长期记忆研究的关系 |
|---|---:|---|---|
| [Learning Long-Context Diffusion Policies via Past-Token Prediction](https://arxiv.org/abs/2505.09561) | 2025 | 除未来动作外同时预测过去动作 token；短上下文视觉预训练后，用缓存视觉特征训练长上下文 policy head | 证明长时依赖的收益可能主要来自 policy head，而不一定来自视觉骨干 |
| [AURA-Mem: Action-Gated Memory for Robot Policies at Constant VRAM](https://arxiv.org/abs/2606.02775) | 2026 | 固定大小 fast-weight recurrent state；仅在 action-relevant surprise 时写入 | O(1) 状态显存与 learned write gate 基线 |
| [Gated Memory Policy](https://arxiv.org/abs/2604.18933) | 2026 | 学习何时读取历史、读取什么；交叉注意力构造 latent memory，并对历史动作注入噪声 | 对比始终读取与自适应门控读取 |
| [Action-Effect Memory Pretraining for Robot Manipulation](https://arxiv.org/abs/2606.12499) | 2026 | 以动作及其环境效果为记忆预训练信号 | 强调 interaction consequence，而非仅压缩观测 |
| [ChronoFlow-Policy: Unifying Past-Current-Future Interaction Flow](https://arxiv.org/abs/2606.31493) | 2026 | 用对象与夹爪的稀疏 3D keypoints 联合表示过去、当前和未来交互动态 | 可作为事件抽取与交互动力学辅助监督 |

### 7.2 状态空间、对象槽位与递归记忆

| 论文 | 年份 | 主要机制 | 备注 |
|---|---:|---|---|
| [Rethinking Progression of Memory State in Robotic Manipulation: An Object-Centric Perspective](https://arxiv.org/abs/2511.11478) | 2025 | Embodied-SlotSSM：持久对象槽位、slot-state-space dynamics 与 relational encoder | 结构化对象中心记忆，避免单一状态的时序干扰 |
| [AnoleVLA: Lightweight VLA with Deep State Space Models for Mobile Manipulation](https://arxiv.org/abs/2603.15046) | 2026 | 以 deep SSM 替代 Transformer 主干，维持线性复杂度的 recurrent state | 更偏轻量长上下文 VLA，但可作为 SSM backbone 对照 |
| [Recurrent Action Transformer with Memory](https://arxiv.org/abs/2306.09459) | 2023 | RATE 使用专用 memory embeddings 在 context segment 间递归传递 | 递归 memory-token 训练的基础方法，主要评测 offline RL |
| [ELMUR: External Layer Memory with Update/Rewrite for Long-Horizon RL](https://arxiv.org/abs/2510.07151) | 2025 | 每层外部 memory、双向 cross-attention、LRU replacement 或 convex blending | 超长稀疏依赖的结构化外部记忆参考 |

### 7.3 视觉、视频与语言记忆

| 论文 | 年份 | 主要机制 | 备注 |
|---|---:|---|---|
| [MemoryVAM: Integrating Memory into Video Action Model for Robot Manipulation](https://arxiv.org/abs/2606.20679) | 2026 | Perceiver Recap Compressor 将逐帧 CLIP 表征压为 memory tokens；Cue Gate 估计任务进度 | 把记忆同时注入 video backbone 和 action decoder |
| [Notes-to-Self: Scratchpad Augmented VLAs for Memory Dependent Manipulation Tasks](https://arxiv.org/abs/2602.21013) | 2026 | 显式自然语言 scratchpad 保存对象位置、计划和子目标进度 | symbolic/text memory 与 latent memory 的重要比较 |
| [Efficient Long-Horizon VLA Models via Static-Dynamic Disentanglement](https://arxiv.org/abs/2602.03983) | 2026 | DySta/SD-VLA 分离静态与动态视觉 token；静态 token 只保留一份，并由 recache gate 更新 | 不是完整长期记忆，但直接降低多帧冗余和推理成本 |
| [SAM2Act: Integrating Visual Foundation Model with a Memory Architecture for Robotic Manipulation](https://arxiv.org/abs/2501.18564) | 2025 | SAM2Act+ 使用 memory bank、encoder 与 attention 支持空间记忆 | MemoryBench 的来源和视觉 memory-bank 基线 |
| [EvoScene-VLA: Evolving Scene Beliefs Inside the Action Model](https://arxiv.org/abs/2605.21862) | 2026 | 在动作生成路径内部持续演化 scene belief | scene-state memory 与外部 bank 的对照方向 |

### 7.4 TBPTT 与梯度状态

| 论文 | TBPTT/梯度状态 | 说明 |
|---|---|---|
| [Past-Token Prediction](https://arxiv.org/abs/2505.09561) | 非递归 TBPTT 方案 | 直接输入缓存的长上下文 embedding，并用预测过去动作进行正则化 |
| [AURA-Mem](https://arxiv.org/abs/2606.02775) | 固定大小在线 recurrent state；论文重点不是 TBPTT 窗口比较 | 关键变量是 surprise-triggered write gate |
| [Embodied-SlotSSM](https://arxiv.org/abs/2511.11478) | SSM 顺序状态训练；论文未将其明确描述为 TBPTT | 记忆按对象槽位而不是单一全局状态组织 |
| [RATE](https://arxiv.org/abs/2306.09459) | segment 级 recurrent memory-token 训练 | 是理解跨段 memory token 和截断上下文训练的重要基础 |
| [MemoryVAM](https://arxiv.org/abs/2606.20679) | 显式 recap memory，不依赖无限递归计算图 | 核心是压缩历史和任务进度 cue |
| [Notes-to-Self](https://arxiv.org/abs/2602.21013) | 显式文本状态，无需通过视觉历史进行长距离反传 | 将长期信用分配部分转化为语言状态监督 |
