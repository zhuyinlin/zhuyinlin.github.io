=======
NLP
=======

`cs224n <http://web.stanford.edu/class/cs224n/>`_

.. contents::
   :local:
   :backlinks: top


********************
Word Representation
********************

fMRI-imaging of the brain suggests that word embeddings are related to how the human brain encodes meaning [#mitchell2008predicting]_

`原理1 <http://www.offconvex.org/2015/12/12/word-embeddings-1/>`_

`原理2 <http://www.offconvex.org/2016/02/14/word-embeddings-2/>`_

word2vec [#mikolov2013efficient]_
==================================

无监督学习


Motivation
-----------

- 直接的文字表达信息稀疏，不足以提供文字单元间的关系，因此会造成需要更大量的数据来学习
- one-hot representation 存在的问题：维数灾难和稀疏（sparse）会导致计算上的问题; 忽略了词之间的关系
- 低维特征在套用DL 更合适

基本思想
--------

• we have a large corpus of text
• Every word in a fixed vocabulary is represented by a vector
• Go through each position t in the text, which has a center word c and context (“outside”) words o
• Use the similarity of the word vectors for c and o to calculate the probability of o given c (or vice versa)
• Keep adjusting the word vectors to maximize this probability

模型
--------

CBOW(Continuous Bag-of-Words)
""""""""""""""""""""""""""""""
通过上下文来预测中心词

CBOW smoothes over a lot of the distributional information (by treating an entire context as one observation). For the most part, **this turns out to be a useful thing for smaller datasets** . 

Skip-Gram
""""""""""
用目标词（中心词）预测上下文中的词

Skip-gram treats each context-target pair as a new observation, and **this tends to do better when we have larger datasets**.

推荐阅读 :download:`skip-gram <../_doc/NLP/Word2Vec_Skip-Gram.pdf>` (`中文版 <https://zhuanlan.zhihu.com/p/27234078>`_ 原文 `patr1 <http://mccormickml.com/2016/04/19/word2vec-tutorial-the-skip-gram-model/>`_ `part2 <http://mccormickml.com/2017/01/11/word2vec-tutorial-part-2-negative-sampling/>`_)

训练
-------
这个训练过程的参数规模非常巨大，一般来说，有Hierarchical Softmax、Negative Sampling等方式来解决。

- Word pairs and "phases"
- 对高频词抽样
- negative sampling:随机（跟概率有关）选择k个negative 单词, 仅更新positive 及 k 个 negative 词的权重；works better for frequent words and lower dimensional vectors
- hierarchical softmax(Huffman Tree): tends to be better for infrequent words




实现
------
`gensim <https://radimrehurek.com/gensim/>`_


优缺点
------

没有充分利用所有的语料

GloVe
=======
TODO: Some ideas from Glove paper have been shown to improve skip-gram (SG) model also (eg. sum both vectors)

GloVe(Global Vectors for Word Representation)是一个基于全局词频统计（count-based & overall statistics）的词表征工具,非神经网络方法，其得到的词向量捕捉到了单词之间一些语义特性，比如相似性（similarity）、类比性（analogy）

基本思想
--------

- 根据语料库（corpus）构建一个共现矩阵（Co-ocurrence Matrix）:math:`X`
- 构建词向量（Word Vector）和共现矩阵（Co-ocurrence Matrix）之间的近似关系
- 训练

实现
------

`glove-python <https://github.com/maciejkula/glove-python>`_

优缺点
---------

更容易并行化

Word2Vec 和 Glove 的对比
=========================

word2vec仍然是state-of-the-art的，相比之下GloVe略逊一筹(performance上差别不大?)

两个模型在并行化上有一些不同，即GloVe更容易并行化，所以对于较大的训练数据，GloVe更快。


Sentence Embedding
==================


评价
=====

1. Intrinsic Evaluation（内部评测）:

   - similarity task
   - analogy task

WordSim-353, SimLex-999, Word analogy task, Embedding visualization等。不仅要评测pair-wise的相似度，还考虑词向量用于推理的实际效果(Analogical Reasoning)

2. Extrinsic Evaluation（外部评测）：

评估训练出的词向量在其它任务上的效果，即其通用性。(Task-specific）

3. 其他直接方式 

   - Evaluation of Word Vector Representations by Subspace Alignment (Tsvetkov et al.) 
   - Evaluation methods for unsupervised word embeddings (Schnabel et al.) 

*******************
keyword extraction
*******************

自动文摘（Automatic Summarization）
====================================
TextRank [#mihalcea2004textrank]_
---------------------------------

Learning Phrases
----------------



******
NLG
******

turotial:

- `char-rnn <http://karpathy.github.io/2015/05/21/rnn-effectiveness/>`_

.. figure:: /_static/algorithm/NLP/NLP_survey.png
   :align: center
   :name: fig_NLG_survey

Seq2Seq
=========

cho2014learning [#cho2014learning]_ 提出 Encoder-Decoder 结构, 以及 :abbr:`GRU (Gated Recurrent Unit)` 这个 RNN 结构。

Cho etl [#cho2014learning]_ 的模型结构中，语义向量c（整个句子的）需要作用到每个时刻t, 而在sutskever2014sequence [#sutskever2014sequence]_ 中Encoder 最后输出的中间语义只作用于 Decoder 的第一个时刻，这样子模型理解起来更容易一些。 另外，作者使用了一个trick: 将源句子顺序颠倒后再输入 Encoder 中，使得性能得到提升。 Google 的机器对话 [#vinyals2015neural]_ 用的就是这个 seq2seq 模型。 `文中参数量计算过程 <https://blog.csdn.net/Jerr__y/article/details/53749693>`_

.. figure:: /_static/algorithm/NLP/seq2seq.jpg
   :align: center
   :scale: 50 %
   :name: fig_seq2seq

   基础seq2seq模型

Cho etl [#cho2014learning]_ 的 decoder 中，每次预测下一个词都会用到中间语义c，而这个c主要就是最后一个时刻的隐藏状态。bahdanau2014neural [#bahdanau2014neural]_ 提出了attention模型(详情查看 :ref:`attention-mechanism`)，在Decoder进行预测的时候，Encoder 中每个时刻的隐藏状态都被利用上了。这样子，Encoder 就能利用多个语义信息（隐藏状态）来表达整个句子的信息了。此外，Encoder用的是双端的 :abbr:GRU (Gated Recurrent Unit)`  `深度学习中的注意力机制(2017版) <https://blog.csdn.net/malefactor/article/details/78767781>`_

.. figure:: /_static/algorithm/NLP/seq2seq_attention.jpg
   :align: center
   :scale: 50 %
   :name: fig_seq2seq_attention

   attention 机制的 seq2seq模型

jean2014using [#jean2014using]_ 提出sampled_softmax 用于解决词表太大的问题

`self attention <http://www.cnblogs.com/robert-dlut/p/8638283.html>`_

`attention is all you need <https://kexue.fm/archives/4765>`_


Code Tutorial
---------------

 `tensorflow nmt <https://github.com/tensorflow/nmt>`_

Training tricks
----------------

- teacher forcing
     At some probability, we use the current target word as the decoder’s next input rather than using the decoder’s current guess. This technique acts as training wheels for the decoder, aiding in more efficient training. However, teacher forcing can **lead to model instability during inference** , as the decoder may not have a sufficient chance to truly craft its own output sequences during training. Thus, we must be mindful of how we are setting the teacher_forcing_ratio, and not be fooled by fast convergence.

- gradient clipping
     This is a commonly used technique for countering the “exploding gradient” problem. In essence, by clipping or thresholding gradients to a maximum value, we prevent the gradients from growing exponentially and either overflow (NaN), or overshoot steep cliffs in the cost function.

- loss 函数average 到 batch就可，不用到timestep， `which plays down the errors made on short sentences <https://github.com/tensorflow/nmt#loss>`_ .

Decoding
----------

- ancestral/random sampling
- greedy decoding/search (1-best)
- beam-search decoding (n-best)
- 门特卡罗搜索?

缺点
---------

- 只能计算前缀部分的概率（改进可用recursive neural network）
- 使用最大似然估计模型参数

第一个缺点使seq2seq **不容易理解文本** ，因为AI-requires being able to understand bigger things from knowing about small parts.

第二个缺点使seq2seq的 **对话不像真实的对话** ，只考虑当前对话最大似然忽略了对话对未来的影响:: 

   - 容易出现“I don’t know”（因为其概率最大，其他方向的相互抵消）；
   - 对话重复（不考虑上下文的关系）等问题。

针对第二个缺点，我们了解到概率最高的输出不一定等于好的输出，好的对话需要考虑长久的信息。可以引入强化学习，人为设计相关的reward让机器更好地学习。

*************
Image Caption
*************

`综述 <https://blog.csdn.net/m0_37731749/article/details/80520144>`_

`综述2 <https://blog.csdn.net/xiaxuesong666/article/details/79176572>`_

**************
Attention 机制
**************

详情查看 :ref:`attention-mechanism`


*****
术语
*****
- Copus: 语料库
- stemming: 词干化
- Word Embedding: 词嵌入
- Distributed Representation
- Distributional Representation
- Information Retrieval (IR)
- Natural Language Processing (NLP)
- Natural Language Inference(NLI)
- Out of Vacabulary(OOV)

.. [#mitchell2008predicting] Mitchell T M, Shinkareva S V, Carlson A, et al. `Predicting human brain activity associated with the meanings of nouns[J] <http://www.cs.cmu.edu/~tom/pubs/science2008.pdf>`_ . science, 2008, 320(5880): 1191-1195.  
.. [#mikolov2013efficient] Mikolov T, Chen K, Corrado G, et al. `Efficient estimation of word representations in vector space[J] <http://papers.nips.cc/paper/5346-sequence-to-sequence-learning-with-neural-networks.pdf>`_ . arXiv preprint arXiv:1301.3781, 2013. 
.. [#mihalcea2004textrank] Mihalcea R, Tarau P. Textrank: `Bringing order into text[C] <http://www.aclweb.org/anthology/W04-3252>`_ //Proceedings of the 2004 conference on empirical methods in natural language processing. 2004.
.. [#cho2014learning] Cho K, Van Merriënboer B, Gulcehre C, et al. `Learning phrase representations using RNN encoder-decoder for statistical machine translation[J] <https://arxiv.org/abs/1406.1078>`_ . arXiv preprint arXiv:1406.1078, 2014. 
.. [#sutskever2014sequence] Sutskever I, Vinyals O, Le Q V. `Sequence to sequence learning with neural networks[J] <http://papers.nips.cc/paper/5346-sequence-to-sequence-learning-with-neural-networks.pdf>`_ . Advances in neural information processing systems, 2014: 3104-3112. 
.. [#vinyals2015neural] Vinyals O, Le Q. `A neural conversational model[J] <https://arxiv.org/pdf/1506.05869.pdf>`_ . arXiv preprint arXiv:1506.05869, 2015.
.. [#bahdanau2014neural] Bahdanau D, Cho K, Bengio Y. `Neural machine translation by jointly learning to align and translate[J] <https://arxiv.org/pdf/1409.0473.pdf>`_ . arXiv preprint arXiv:1409.0473, 2014.
.. [#jean2014using] Jean S, Cho K, Memisevic R, et al. `On using very large target vocabulary for neural machine translation[J] <https://arxiv.org/pdf/1412.2007.pdf>`_ . arXiv preprint arXiv:1412.2007, 2014.
