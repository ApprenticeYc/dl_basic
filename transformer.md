transformer八股文

参考资料：https://zhuanlan.zhihu.com/p/682585974


=>
第一，讲是专门用来处理序列的模型
第二，encoder、decoder架构。包含了scaling dot product, mha，ffn，pe来捕获位置信息。
第三，res conn, ln 提升模型稳定性和训练效率
(梯度消失：在深度神经网络的训练过程中，由于链式法则的连乘效应，当网络层数过深时，梯度在反向传播过程中会逐渐减小到接近于0；)



1225 概念性的已经梳理完了，todo 把常见的几个地方代码实现出来
<=


11. transformer结构描述
Transformer是一种基于自注意力机制的深度学习模型，广泛应用于自然语言处理、计算机视觉等领域。其结构主要包括编码器（Encoder）和解码器（Decoder），每个部分都由多个相同的层堆叠而成。每层包括两个子层：多头自注意力机制（Multi-Head Self-Attention）和前馈神经网络（Feed-Forward Neural Network）。此外，Transformer还使用了位置编码（Positional Encoding）来捕捉序列中的位置信息，并在每个子层后添加了残差连接（Residual Connection）和层归一化（Layer Normalization）来提高模型的稳定性和训练效率。

7. cross-attention和self-attention区别
Cross-attention和Self-attention是注意力机制中的两种常见类型，它们在处理序列数据时具有不同的作用。
* Self-attention：关注的是输入序列自身内部元素之间的关系。在Self-attention中，查询（Query）、键（Key）和值（Value）都来自同一个输入序列。它通过对序列内部元素之间的交互进行建模，来捕捉序列内部的依赖关系。
* Cross-attention：关注的是两个不同输入序列之间元素的关系。在Cross-attention中，Query来自一个序列（通常是解码器输出），而Key和Value来自另一个序列（通常是编码器输出）。它通过对两个序列之间元素的交互进行建模，来捕捉序列之间的依赖关系。
简单来说，Self-attention更侧重于单个序列内部的关系挖掘，而Cross-attention则更侧重于不同序列之间的信息交互。


8. cross-attention在图文匹配中q和kv分别指什么，在机器翻译中分别指什么
在Cross-attention中，Query（Q）、Key（K）和Value（V）是注意力机制的核心组件，它们在处理不同任务时具有不同的含义。
* 在图文匹配中：
    * Query（Q）：通常来自文本编码器，表示文本序列中某个元素（如单词或短语）的嵌入向量。在图文匹配任务中，Query用于查询与图像相关的文本信息。
    * Key（K）和Value（V）：通常来自图像编码器，表示图像中某个区域（如网格单元或对象）的特征向量。Key用于与Query计算相似度，以确定图像中与文本相关的区域；而Value则用于根据相似度加权求和，以生成与文本匹配的图像特征表示。
* 在机器翻译中：
    * Query（Q）：通常来自解码器的前一个时间步的输出或隐藏状态，表示当前要翻译的目标语言单词的嵌入向量。在机器翻译任务中，Query用于查询源语言序列中与当前目标语言单词相关的信息。
    * Key（K）和Value（V）：通常来自编码器的输出或隐藏状态，表示源语言序列中每个单词的特征向量。Key用于与Query计算相似度，以确定源语言序列中与当前目标语言单词最相关的单词；而Value则用于根据相似度加权求和，以生成与当前目标语言单词对应的源语言上下文表示。


12. 注意力机制描述
注意力机制是一种用于增强模型对输入数据中重要部分关注度的技术。在深度学习模型中，注意力机制通过计算输入数据各部分与当前任务的相关性，为不同部分分配不同的权重，从而实现对重要信息的聚焦和对无关信息的忽略。注意力机制可以应用于各种序列处理任务，如机器翻译、文本摘要、语音识别等，显著提高模型的性能和解释性。


13. 为什么用多头注意力机制
多头注意力机制（Multi-Head Attention）是Transformer模型中的关键组件之一。它通过将输入数据分成多个头（Head），并在每个头上独立应用自注意力机制，从而捕捉到输入数据在不同子空间中的特征表示。多头注意力机制的好处在于：
* 增强模型的表达能力：通过多个头并行处理输入数据，模型能够捕捉到更丰富的特征信息。
* 提高模型的鲁棒性：不同头可能关注输入数据的不同方面，从而减小了模型对单一特征的依赖，提高了模型的鲁棒性。
* 并行计算：多头注意力机制可以并行计算，从而提高了模型的训练速度和效率。

14. attention的复杂度及为什么用位置编码
注意力机制的复杂度：
自注意力机制的复杂度主要取决于输入序列的长度N和嵌入向量的维度D。对于标准的自注意力机制，其时间复杂度和空间复杂度均为O(N^2 * D)，因为需要计算每个元素与其他所有元素的相似度。

为什么用位置编码：
* Transformer模型本身并不包含序列信息，即它是位置不变的（Position-Invariant）。为了向模型引入序列信息，需要在输入数据中添加位置编码（Positional Encoding）。
* 位置编码是一种将位置信息嵌入到输入向量中的方法，使得模型能够感知到输入序列中元素的顺序和相对位置。
* 常见的位置编码方法包括正弦和余弦函数的组合、可学习的位置向量等。通过添加位置编码，Transformer模型能够更有效地处理序列数据。


如何解决长距离依赖
自注意力机制，能让序列中任意两个位置的序列进行计算


14 scale dot-product attn如何实现的
￼
Attn = q.*k/ sqrt(dim_k)
Attn = softmax(attn)
Attn = attn .* v


Mha如何实现？
todo
￼

15. attention中为什么➗k的维度开根号
在Transformer的自注意力机制中，计算相似度时通常会将Query和Key的点积结果除以一个缩放因子√k（k为Key的维度）。这是为了防止点积结果过大，导致softmax函数进入梯度消失区域，从而影响模型的训练效果。通过除以√k，可以将点积结果的期望值控制在一定范围内，使得softmax函数的梯度更加稳定，有利于模型的训练。


16. 残差的作用
残差连接（Residual Connection）是深度学习中的一种技术，它通过将输入数据直接添加到输出数据中，实现了信息的直接传递。残差连接的主要作用有：
* 缓解梯度消失问题：在深层神经网络中，梯度消失是一个常见的问题。残差连接通过直接连接输入和输出，使得梯度可以直接传递回较早的层，从而缓解了梯度消失问题。
* 增强模型的表达能力：残差连接允许模型学习输入数据的恒等映射，从而增强了模型的表达能力。当模型需要学习复杂函数时，它可以通过残差连接将简单函数（如恒等映射）与复杂函数组合起来，实现更强大的功能。
* 提高模型的稳定性：残差连接可以使得模型在训练过程中更加稳定，减少了模型崩溃的风险。


17. BN和LN区别
批量归一化（Batch Normalization, BN）和层归一化（Layer Normalization, LN）是两种常见的归一化技术，它们的主要区别在于归一化的维度不同。
* BN：在批量维度上进行归一化，即对每个特征在所有样本上进行归一化。BN能够加速模型的收敛速度，提高模型的泛化能力，并且具有一定的正则化效果。但是，BN对批量大小比较敏感，当批量较小时，归一化效果可能会变差。
* LN：在特征维度上进行归一化，即对每个样本在所有特征上进行归一化。LN不受批量大小的限制，适用于各种批量大小的场景。此外，LN在RNN和Transformer等序列模型中表现较好，因为这些模型中的输入数据通常具有不同的长度和分布。

18. transformer中用BN可以吗
在Transformer模型中，通常使用层归一化（LN）而不是批量归一化（BN）。这是因为Transformer模型中的输入数据通常是序列数据，具有不同的长度和分布，而LN在特征维度上进行归一化，更适合处理这种情况。此外，LN不受批量大小的限制，可以应用于各种批量大小的场景。因此，在Transformer模型中使用LN通常比使用BN更有效。

19. BN和Dropout在训练和推理时的区别
批量归一化（BN）和Dropout是两种常用的正则化技术，它们在训练和推理时的行为有所不同。
* BN：
    * 训练时：BN会根据当前批量数据计算均值和方差，并对输入数据进行归一化处理。同时，BN还会学习两个可训练参数（缩放因子和偏移量），用于对归一化后的数据进行进一步调整。
    * 推理时：在推理阶段，BN会使用整个训练集的均值和方差来对输入数据进行归一化，而不是使用当前批量的均值和方差。这是为了确保推理时的数据分布与训练时保持一致。
* Dropout：
    * 训练时：Dropout会随机地将一部分神经元的输出置为零（或按照一定比例缩小），从而防止模型过拟合。在每次训练迭代中，Dropout都会以一定的概率（如0.5）随机丢弃神经元。
    * 推理时：在推理阶段，Dropout不会被应用。为了保持模型在推理时的输出与训练时一致，通常会对模型的权重进行缩放（即乘以1-Dropout概率），以补偿训练时丢弃神经元的影响。

mask机制
防止序列未来的信息泄露
对不同长度的序列进行padding

Mask如何实现
对于未来帧，在self attn构建上三角矩阵，设置成-inf，这样经过softmax，这些位置权重会趋近0
padding mask，在需要padding的位置，设置成-inf，…


20. ViT的结构描述
Vision Transformer（ViT）是一种将Transformer模型应用于计算机视觉任务的模型。其结构主要包括以下几个部分：
* 图像分块（Patch Embeddin
