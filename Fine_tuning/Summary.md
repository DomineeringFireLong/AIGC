# 微调技术
## 定义
微调是指在预训练大型语言模型(Pretrained Language Model, PLM)基础上，使用特定领域或任务的数据进行进一步训练，使模型适应特定需求的技术。  

## 作用
- 领域适应：预训练模型具有通用知识，但特定领域知识不足  
- 任务适应：预训练目标(如语言建模)与下游任务目标(如分类)不一致  
- 性能提升：针对特定任务优化可以显著提高模型表现  

## 分类
1. 全参数微调(Full Fine-tuning)
2. 参数高效微调方法(Parameter-Efficient Fine-tuning, PEFT)
3. 其他微调方法
## 方法介绍

1. FT
- 原理：更新模型所有参数，是最直接的微调方式。
- 优点：理论上能达到最佳性能,实现简单直接.
- 缺点：计算资源消耗大、容易过拟合(尤其小数据集时)、需要存储每个任务的完整模型副本。

2. 适配器微调(Adapter)
- 原理：在Transformer层间插入小型全连接网络(适配器)，只训练这些新增参数。
    [Transformer Layer]
        |
    [Adapter Down-Proj] (d->h)
        |
    [Adapter Up-Proj] (h->d)  

        |
    [LayerNorm]  

- 优点：参数效率高(仅新增2-4%参数)、模块化设计，易于切换任务。
- 缺点：增加推理延迟(额外计算)、需要谨慎选择插入位置。


3.  前缀微调(Prefix Tuning)
- 原理：在输入前添加可学习的"前缀"token，通过这些前缀来指导模型行为。  
Input: [PREFIX] + [TASK INPUT]  
- 变体：  
    Prompt Tuning：仅输入层添加可学习token
    P-Tuning：使用LSTM/MLP生成更复杂的连续prompt

- 优点：完全不修改原始模型、前缀可解释性强
- 缺点：长序列任务会占用上下文窗口、需要大量调参

4. LoRA (Low-Rank Adaptation)
- 原理：
用低秩分解表示权重更新：ΔW = BA (A∈R^{r×k}, B∈R^{d×r}, r≪min(d,k))


W' = W + ΔW = W + BA
- 优点：不增加推理延迟、参数效率极高(通常r=8或16)、多个任务适配器可动态加载
- 缺点：需要选择应用的目标层、秩的选择影响性能

5. (IA)^3 (Infused Adapter by Inhibiting and Amplifying Inner Activations)
- 原理：
通过学习三个向量(l_k, l_v, l_ff)来缩放键、值和前馈网络的激活值。

attention_output = softmax(Q(l_k·K)^T/√d)(l_v·V)
ffn_output = l_ff·FFN(x)
- 优点：比LoRA更少的参数、在T0模型上表现优异
- 缺点：适用范围较窄、研究还不够充分

6. 差分学习率(Differential Learning Rate)
- 原理：
不同层使用不同学习率，底层使用较小学习率，顶层使用较大学习率。


7. 渐进解冻(Progressive Unfreezing)
- 原理：从顶层开始逐步解冻和训练更多层。


8. 基于强化学习的微调(RL Fine-tuning)
- 原理：使用强化学习(如PPO)优化不可微分的指标(如BLEU, ROUGE)。
[人类反馈] → [奖励模型] → [RL优化]