## LLaMa

7B dict(n_layer=32, n_head=32, n_embd=4096)

|--- transformer (nn.ModuleDict)
|      |--- wte (nn.Embedding)                       
|      |--- h (nn.ModuleList of Block, n_layer)
|      |      |--- rms_1 (RMSNorm)
|      |      |--- attn (CausalSelfAttention)
|      |      |      |--- c_attn (nn.Linear)
|      |      |      |--- c_proj (nn.Linear)
|      |      |--- rms_2 (RMSNorm)
|      |      |--- mlp (MLP)
|      |      |      |--- c_fc1 (nn.Linear)
|      |      |      |--- c_fc2 (nn.Linear)
|      |      |      |--- c_proj (nn.Linear)
|      |--- ln_f (RMSNorm)
|--- lm_head (nn.Linear)      

1. Embedding层：vocab_size * n_embd
2. Transformer层：每个Transformer层包括一个自注意力层和一个前馈神经网络层
   * 自注意力层：3 * n_embd * (n_embd // n_head)
   * 前馈神经网络层：n_embd * 4 * n_embd + 4 * n_embd * n_embd
3. 层数：n_layer * Transformer
4. 输出层：n_embd * vocab_size
5. 位置编码、层归一化


