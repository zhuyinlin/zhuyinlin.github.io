文档工具
=========

.. contents::
    :local:
    :backlinks: top

restructuredText
----------------

Install
````````

.. code-block:: bash

   pip install sphinx // 安装sphinx
   //pip install restructuredtext-lint  //安装 restructuredtext-lint
   
   mkdir <targetDir>
   cd <targetDir>
   sphinx-quickstart

`configuration <http://www.sphinx-doc.org/en/master/usage/configuration.html#confval-language.>`_

`基础语法 <http://www.sphinx-doc.org/en/master/usage/restructuredtext/index.html?highlight=markup>`_

`主题修改等 <http://www.sphinx-doc.org/en/master/usage/configuration.html#confval-language.>`_

`style guide <https://developer.lsst.io/restructuredtext/style.html>`_

新建的项目包含以下文件::

    - Makefile: Developers who have compiled code should be familiar with this file. If not, think of it as a file containing instructions to build documentation output when using the make command.
    - build: This is the directory where generated files will be placed after a specific output is triggered.
    - source/_static: Any files that are not part of the source code (like images) are placed here, and later linked together in the build directory.
    - source/conf.py: This is a Python file holding configuration values for Sphinx, including the ones selected when sphinx-quickstart was executed in the terminal.
    - source/index.rst: The root of the documentation project. This will connect other files if the documentation is split into other files.

编辑好文档后，在 **项目根目录** 下运行以下命令生成html文件

.. code-block:: bash

    make html

Usage
``````

项目级目录结构
~~~~~~~~~~~~~~

在 ``index.rst`` 中输入目录结构::

    ============
    Project Docs
    ============

    This documents our project.

   .. toctree::
      :caption: Table of Contents
      :maxdepth: 2
      :numbered:

      pages/overview
      pages/content
      pages/glossary

It’s also possible to use ``globbing``, so you don’t have to add every new page manually to this list. It’s worth to spend a minute or two to understand the toctree.


单页目录结构可视化
~~~~~~~~~~~~~~~~~~~~
::

    This is an overview.

    .. contents::
       :local:
       :backlinks: top

    section 1
    =========

    sub-section 1
    -------------

    sub-section 2
    -------------

    section 2
    =========

- # with overline, for parts
- * with overline, for chapters
- =, for sections
- -, for subsections
- ^, for subsubsections
- ", for paragraphs

显示代码
~~~~~~~~~~~

代码显示有两种方法，第一种 code inline in the document itself::

   .. code-block:: python
      :linenos:
      :emphasize-lines: 0

      print("hello blog")

第二种 literal include of the file::

    .. literalinclude:: example.py
        :language: python
        :linenos:
        :emphasize-lines: 5




显示图片
~~~~~~~~~~~~

将图片放在 ``_static`` 目录下，然后::

    .. image:: _static/<image name>
      :target: ../../_static/development/docs/lsst_logo.jpg
      :height: 100px
      :width: 200 px
      :scale: 50 %
      :alt: alternate text  //无法显示图片时显示该文字
      :align: right //"top", "middle", "bottom", "left", "center", or "right"

      图注

.. Tip::

   路径也可以写为 /_static/development/docs/lsst_logo.jpg

或者::

    .. _fig_1:
    .. figure:: _static/<image name>

       图注

或者::

   .. figure:: /_static/development/docs/lsst_logo.jpg
      :name: fig-example-figure-label    // 用于引用
      :target: ../../_static/development/docs/lsst_logo.jpg
      :alt: LSST Logo

      图注

.. Note::
   `:name:` 后面的文字可作为超链接时的label，引用时
   
   - `:ref:`some text <label>``

   - `:numref: label` (此方法需要在conf.py 中设置numfig=True),
     这种方法会在图注上编号


math
~~~~~~~~~~~

:: 

   .. math:: \sigma_\mathrm{mean} = \frac{\sigma}{\sqrt{N}}
      :label: math-sample

   other place to ref :eq:`math-sample`

Highlight important parts
~~~~~~~~~~~~~~~~~~~~~~~~~

::

    .. Tip:: This is a tip

    .. note:: This is a note.

    .. warning:: This is a warning.

    .. important:: This is important.

    .. topic:: Topic Title

       Subsequent indented lines comprise
       the body of the topic, and are
       interpreted as body elements.


页面间的相互引用
~~~~~~~~~~~~~~~~~~~

在要跳转到的目标处添加label作为jump mark, 就如此处的 ``.. _the-glossary:`` ::

    .. _the-glossary:

    ========
    Glossary
    ========

    This is a glossary.

然后将这个label作为 ``:ref:`` 的参数，一起插入到引用的位置::

    =======
    Content
    =======

    This is explains everything.

    See the :ref:`the-glossary` for details.

引用文件
~~~~~~~~~

`官方文档 <http://www.sphinx-doc.org/en/stable/markup/inline.html#referencing-downloadable-files>`_

have a file ``mypdf.pdf`` in a directory ``doc`` . The directory ``doc`` and your rst file must be in the same directory::

    here is a pdf file :download:`pdf <doc/mypdf.pdf>`

Split page content over multiple files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

为了实现内容复用，或者多人合作写文档时能互相不干扰，可以将文档内容拆分插入::

    =======
    Content
    =======

    This is explains everything.

    .. include:: part1.inc

    .. include:: part2.inc

添加表格
~~~~~~~~~~

添加表格有两种方式，第一种 inline table ::

    .. table:: Truth table for "not"
        :widths: auto

        =====  =====
          A    not A
        =====  =====
        False  True
        True   False
        =====  =====

第二种先用文件保存表格数据，比如table.csv::
    
    "Treat", "Quantity", "Description"
    "Albatross", 2.99, "On a stick!"
    "Crunchy Frog", 1.49, "If we took the bones out, it wouldn't be crunchy, now would it?"
    "Gannet Ripple", 1.99, "On a stick!"

然后用 csv-table directive ::

    .. csv-table::
        :file: table.csv
        :header-rows: 1
        :widths: 15, 10, 30


Use constants for words or phrases
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

一些大量出现的文字可以用常量替换::

    .. |es| replace:: *Elasticsearch*

    Store your logs in |es|.

外部引用
~~~~~~~~~~~
footnotes::

    Reference to external sources with auto-numbered footnotes [#footnotes]_

    References
    ==========

    .. rubric:: Footnotes

    .. [#footnotes] http://www.sphinx-doc.org/en/stable/rest.html#footnotes

citation::

    Lorem ipsum [Ref]_ dolor sit amet.

    .. [Ref] Book or article reference, URL or whatever.

在引用参考文献时，可像footnote一样在前面加上 ``#`` 让其自动编号，标志符号可采用bib形式，并在文献后面加上链接地址 

注释
~~~~~

::

    ..
        这些是注释

markdown
----------
Set
``````
- vim: vim-instant-markdown 
- chrome: markdown previewer

Usage
```````

代码块

::

   ``` bash
   git clone
   ```

插入超链接

::

    This is [inline link](http links)


插入引文

::

    This is [example][@papername]

    [@papername]: http link "stypled citation"
