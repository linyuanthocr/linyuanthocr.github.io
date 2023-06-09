# LORA: LOW-RANK ADAPTATION OF LARGE LANGUAGE MODELS

## Background
Many applications in natural language processing rely on adapting one large-scale, pre-trained language model to multiple downstream applications. Such adaptation is usually done via fine-tuning, which updates all the parameters of the pre-trained model. The major downside of fine-tuning is that the new model contains as many parameters as in the original model. 


## Approach

#### Problem statement

![83231da55295e96361c22fa4a93e18be.png](evernotecid://0AE6B2C5-79A7-417B-BA48-0887E4E60256/appyinxiangcom/7849907/ENResource/p3053)

#### Existing methods
Two major methods to tackle this problem:
1. Use Adaptor Layer: introduce latency. (large neural networks rely on hardware parallelism to keep the latency low, and adapter layers have to be processed sequentially. )
2. Directly Optimizing the Prompt is Hard. Use Prefix tuning. (Prefix tuning is difficult to optimize and that its performance changes non-monotonically in trainable parameters, confirming similar observations in the original paper. More)

#### Method
1. LOW-RANK-PARAMETRIZED UPDATE MATRICES

![cef92944db83b51045051195cf02775d.png](evernotecid://0AE6B2C5-79A7-417B-BA48-0887E4E60256/appyinxiangcom/7849907/ENResource/p3052)


For a pre-trained weight matrix $W_0 ∈ R_d×k$, we constrain its update by representing the latter with a low-rank decompostion:
```math
W_0 +\Delta W = W_0 + BA
```

where 
```math 
B ∈ R_d×r, A ∈ R_r×k. 
```
And the rank $r \ll min(d, k)$.  During training, $W_0$ is frozen and does not receive gradient updates, while $A$ and $B$ contain trainable parameters. 

**A Generalization of Full Fine-tuning.**

**No Additional Inference Latency.**

2. APPLYING LORA TO TRANSFORMER

Apply LoRA to any subset of weight matrices in a neural network to reduce the number of trainable parameters. 
In the Transformer architecture, there are four weight matrices in the self-attention module ($W_q,W_k,W_v,W_o$) and two in the MLP module. We treat $W_q$ (or $W_k, W_v$) into attention heads. We limit our study to **only adapting the attention weights** for downstream as a single matrix of dimension $d_model ×d_model$, even though the output dimension is usually sliced tasks and freeze the MLP modules.

**Practical Benefits and Limitations.**
The most significant benefit comes from the reduction in memory and storage usage.

It is not straightforward to batch inputs to different tasks with different A and B in a single forward pass, if one chooses to absorb A and B intoW to eliminate additional inference latency. Though

#### 优点：
1. 方便灵活，大模型冻住且能在不同任务里分享，切换任务时只对插入的低阶参数矩阵优化。方便部署。
2.  训练省时省力，不需要对绝大部分参数计算梯度和维护优化器参数，比起adaptive optimizer省3倍空间。
3. 相对于full fine tune模型，没有inference latency
4. LORA与其他算法可并行使用。

#### links:
https://arxiv.org/abs/2106.09685
