RNN
========

tutorial:

- `RNN series <http://www.wildml.com/2015/09/recurrent-neural-networks-tutorial-part-1-introduction-to-rnns/>`_ (TODO)
- `LSTM <http://colah.github.io/posts/2015-08-Understanding-LSTMs/>`_  `翻译版本 <https://blog.csdn.net/jerr__y/article/details/58598296>`_
- `TF Tutorial <https://colah.github.io/posts/2015-08-Understanding-LSTMs/>`_

普通RNN的问题
--------------
在实际实现中，RNN 无法解决“long-term dependencies.”

原因 `Hochreiter (1991) [German] <http://people.idsia.ch/~juergen/SeppHochreiter1991ThesisAdvisorSchmidhuber.pdf>`_ and `Bengio, et al. (1994) <http://www-dsi.ing.unifi.it/~paolo/ps/tnn-94-gradient.pdf>`_ ,

Long Short Term Memory(LSTM)  [#hochreiter1997long]_
------------------------------------------------------

Remembering information for long periods of time is practically their default behavior, not something they struggle to learn!

LSTM的巧妙之处在于通过增加输入门限，遗忘门限和输出门限，使得自循环的权重是变化的，这样一来在模型参数固定的情况下，不同时刻的积分尺度可以动态改变，从而避免了梯度消失或者梯度膨胀的问题。

Motivation
^^^^^^^^^^^^

long-term dependency problem

应用
------
- speech recognition
- language modeling
- translation
- image captioning
- …


.. [#hochreiter1997long]  `Hochreiter S, Schmidhuber J. Long short-term memory[J] <http://www.bioinf.jku.at/publications/older/2604.pdf>`_ . Neural computation, 1997, 9(8): 1735-1780. 
