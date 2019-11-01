.. include:: ../_refs/abbrs.ref

数据集
=======

.. contents::
    :local:
    :backlinks: top

数据存储
---------

思路: 分布式存储数据，训练时读写 HDFS 文件路径，

    - `Hadoop + HDF5 + HBase <https://support.hdfgroup.org/pubs/papers/Big_HDF_FAQs.pdf>`_
    - Hadoop + HBase , LMDB? 

问题：
 
    - 数据以什么格式存储？（ `hadoop小文件bad？ <https://stackoverflow.com/questions/49177531/storing-small-size-big-quantities-image-on-hdfs-for-later-processing>`_ ） 
    - 对于图片数据，仅用于存储图片路径？包含图片数据?
    - 写代码时怎么操作HBase(+HDF5)


分布式文件系统
^^^^^^^^^^^^^^

Hadoop


存储格式
^^^^^^^^^^

- HDF5: 通用数据格式（nasa），跨平台，I/O 弱于LMDB, 不支持croping操作? [#footnotes]_
- TFRrecords: 被诟病

数据库
^^^^^^^^^^

目前许多大型互联网项目都会选用MySQL（或任何关系型数据库） + NoSQL的组合方案。
 
+------+------------------+--------------------------------------+
|      | SQL              | NoSQL                                |
+======+==================+======================================+
| 实例 | MySQL, Postgres, | MongoDB, Redis, Hbase, LevelDB, LMDB |
+------+------------------+--------------------------------------+

关系型数据库适合存储结构化数据，如用户的帐号、地址：

1. 这些数据通常需要做结构化查询，比如join，这时候，关系型数据库就要胜出一筹
2. 这些数据的规模、增长的速度通常是可以预期的
3. 事务性、一致性
 
NoSQL适合存储非结构化数据，如文章、评论：

1. 这些数据通常用于模糊处理，如全文搜索、机器学习
2. 这些数据是海量的，而且增长的速度是难以预期的，
3. 根据数据的特点，NoSQL数据库通常具有无限（至少接近）伸缩性
4. 按key获取数据效率很高，但是对join或其他结构化查询的支持就比较差

When training a model on the ImageNet dataset, the image ﬁle option was more than ``17`` times slower than the **key-value** storage option.

For training deep neuralnetwork models, we suggest **key-value** based approaches to efﬁciently retrieve the **image data** . If the image data exceeds the capacity of local ﬁle systems, we might consider **distributed key-value storage** systems such as Redis, and HBase. However, we need to use the caution as the size of individual pixel arrays to be trained can be much larger than the desirable range of value sizes for key-value storages. For instance, HBase generally recommendsless than 100MB of value size [#footnotes]_

It is orders of magnitude faster to first turn the images into a database (e.g. LMDB or level DB) [#db2]_

- LevelDB
- LMDB: I/O 操作快，支持不同程序同时读取
- HBase [#db3]_


Pedestrain
-----------

+-----+------+------------------------------------------------------------------------------+--+
|     |      |                                                                              |  |
+-----+------+------------------------------------------------------------------------------+--+
| PRW | reID | - videos acquired through six near-synchronized cameras,                     |  |
|     |      | - contains 932 identities and 11,816 frames,                                 |  |
|     |      | - pedestrians are annotated with their bounding box positions and identities |  |
+-----+------+------------------------------------------------------------------------------+--+
|     |      |                                                                              |  |
+-----+------+------------------------------------------------------------------------------+--+

.. image:: /_static/research/reID_db.png 


手写汉字
--------

1. 规范的
   - 北京邮电大学发布的HCL2000脱机手写数据库 
   - 国家863中文手写评测数据
   - 中科院自动化研究所IAAS-4M手写汉字样本库: 3755个国标一级汉字、305个数码、符号、字母和若干个常用的繁体字，总计共4060个字符。每种字符有1000个样本，构成一个共有400万个字符样本的数据库。

2. 真实书写情况(局限于GB2312-80标准的6763类,并且数据总量仍然不够大)
   - 中国科学院发布的CASIA-OLHWDB1.0-1.2联机单字
   - CASIA-HWDB1.0-1.2文本行数据集
   - 华南理工大学发布的SCUT-COUCH

ICDAR 2013 offline HCCR competition dataset

NLP
-------

语料库
^^^^^^^

- NLTK中的Brown语料库

-  `Penn Tree Bank (PTB) <https://catalog.ldc.upenn.edu/ldc99t42>`_ dataset,

- WikiText-2

阅读理解
^^^^^^^^^
 |a_squad|
    超过 10 万个问答对，并通过突出显示段落中的几个单词来让模型回答一个问题

自然语言推理
^^^^^^^^^^^^^^
 |a_snli| Corpus
     包含 57 万个人类写的英语句子对 `links <https://nlp.stanford.edu/projects/snli/>`_

机器翻译
^^^^^^^^^^^^^^
WMT2014 的 4 千万个英语法语句子对

chatbot
^^^^^^^^

`Cornelll Movie-Dialog Corpus <https://www.cs.cornell.edu/~cristian/Cornell_Movie-Dialogs_Corpus.html>`_

ImageCaption
^^^^^^^^^^^^^^^

微软的COCO（CodaLab组织了一个排行榜；本地评测在这里）、Flickr8k、Flickr30k和SBU


EHR
-------

- Children’s Healthcare of Atlanta(CHOA)
- CMS dataset

CSS
^^^^^
::

    ├── Multi_Level_CCS_2015
    │   ├── ccs_multi_dx_tool_2015.csv    // icd-9 diagnose code to css code map
    │   ├── ccs_multi_pr_tool_2015.csv    // icd-9 procedure code to css code map
    │   ├── dxmlabel-13.csv
    │   ├── Multi_CCS_Load_Program.sas
    │   ├── Multi_CCS_Summary_Program.sas
    │   └── prmlabel-09.csv
    ├── Single_Level_CSS_2015
    │   ├── dxlabel 2015.csv
    │   ├── $dxref 2015.csv    // icd-9 diagnose code to ccs code map
    │   ├── prlabel 2014.csv
    │   └── $prref 2015.csv    // icd-9 procedure code to ccs code map

.. [#footnotes] https://www.researchgate.net/publication/306056875_An_analysis_of_image_storage_systems_for_scalable_training_of_deep_neural_networks#footnotes

.. [#db2] https://www.reddit.com/r/MachineLearning/comments/2nu5vf/how_to_store_millions_of_images_for_data_analysis/#db2

.. [#db3] https://stackoverflow.com/questions/16929832/difference-between-hbase-and-hadoop-hdfs#db3
