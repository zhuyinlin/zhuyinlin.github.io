vim
====

:download:`Practice Vim <../_doc/tools/Practical Vim.pdf>`
:download:`Modern Vim <../_doc/tools/modern_vim.pdf>`

vim 使用技巧
==============

+ vim 粘贴系统剪切板中的内容：Shift ＋ Insert
+ 查看插件变量值： :echo <var_name>
+ 不同版本vim 对语言的支持不同， 切换可使用 :code:`sudo update-alternatives --config vim`



代码整理
--------------

.. code:: 

        ＝＝   # 格式化当前行
        gg=G   # 格式化整个文档
        <n>=   # 格式当前行及接下来的n行代码

normal
--------------

.. code::

    gJ       # 将下一行连接到本行末尾   
    f + <char>    # 找到<char>, 光标在该字符上
    t + <char>    # 找到<char>, 光标在该字符前一个字符上
    <C-a> # 数字加1
    <C-x> # 数字减1

选取
--------------

.. code::
     
        vi <char>      # 选中<char> 之间的文本/段落（不包括<char>）
        vis(entence)   # 选中一个句子
        vib(lock)      # 选中一个block (()之间)
        viw(ord)       # 选中一个单词
        vip(aragraph)  # 选中一个段落

        va <char>     # 选中<char> 之间的文本（包括<char>） 

插入
--------------

.. code::

        i/a    #  光标前/后位置插入
        I/A    #  行首/末插入
        :r! <command> # 命令行的运行结果插入到光标之后

跳转
--------------

.. code:: 

        # 前向跳转(大写相反)
        f <char>   # 跳转到字母
        t <char>   # 跳转到字母

        %  # 括号匹配

        # 表示位置
        m <char> # 把这个地方标示成 mark
        ' <char> # 跳到被标为 char 的那一行
        ''  # 回到刚才的位置
          
        # 文字段落(反方向用配对符号)
        {  # 跳到上一段的开头
        (  # 移到这个句子的开头
        [[ # 跳往上一个函式


多窗口操作
--------------
  
.. code::
          
        # 打开多个文件：
        vim file1 file2 ...  # 未启动时
        :e file / :open file  # - 已启动
  
        # 文件间切换：
        Ctrl + 6  # next
        :bn       # next
        :bp       # previous
        :b1~n     # go to n-th file

        # 打开多个窗口：
        :sp/split/new  # 水平切分窗口
        :vsp/vsplit    # 垂直切分窗口
 
        # 窗口间切换：
        Ctrl + w + 方向键
        Ctrl + w + h/j/k/l
        Ctrl + ww      # 依次向后切换到下一个窗格

        # 改变窗口大小
        Ctrl + <number> +/-

        # To change two vertically split windows to horizonally split
        Ctrl-w t Ctrl-w K
        # Horizontally to vertically:
        Ctrl-w t Ctrl-w H
        # Ctrl-w t makes the first (topleft) window current 
        # Ctrl-w K moves the current window to full-width at the very top 
        # Ctrl-w H moves the current window to full-height at far left

        # 文件浏览：
        :Ex       # 打开目录浏览器 
        :Sex      # 水平分割且显示目录浏览器
        :ls       # list 显示当前buffer情况

        # 关闭和保存：
        :qa   # 关闭所有窗口
        :wa   # 保存所有窗口
        :wqa  # 保存并退出所有窗口  

搜索
--------------

.. code::

   # 选中文字后，按y复制, 然后
   /
   <Ctrl> + r
   "

   # 多文件搜索使用vimgrep
   # vimgrep /匹配模式/[g][j] 要搜索的文件/范围
   # g：表示是否把每一行的多个匹配结果都加入
   # j：表示是否搜索完后定位到第一个匹配位置
   :vim <pattern> %              在当前打开文件中查找
   :vim <pattern> *              在当前目录下查找所有
   :vim <pattern> **             在当前目录及子目录下查找所有
   :vim <pattern> **/*.c         在所有目录下的所有*.c文件中查找

   :cn                           查找下一个 
   :cp                           查找上一个 
   :copen                        打开quickfix 
   :cw                           打开quickfix 
   :cclose                       关闭qucikfix

   # 使用args
   :args *.*                     将当前目录下的所有文件添加到搜索范围
   :argdo /<pattern> 

删除将文件末的 ^M
---------------------------

.. code::

    :%s/^M//g  # ^ 用<Ctrl V>, M 用<Ctrl M>


配置文件语法
==============

变量
--------------
1.  标量变量
    
    可以是数字或字符串，基本与perl相同。
    命名方式为：作用域:变量名
   
    .. code-block:: vim

            b:name  " 只对当前buffer有效的变量
            w:name  " 只对当前窗口有效的变量
            g:name  " 全局变量
            v:name  " vim预定义变量
            a:name  " 函数的参变量
    
    .. Tip::
            引用标量变量的时候请包含作用域和冒号

2. 一类有特殊含义的变量

   命名方式：Fun Character(这个词请参看Programming Perl)加上变量名

   .. code::
   
           $NAME  " 环境变量（一般变量名都是大写）
           &name  " 选项（vim处理某些事情的时候的默认设置）
           @r  " register（寄存器，不是汇编的EAX, EBX，看第2部分vim tips）
           
           常见环境变量例子：$VIMRUNTIME  " vim运行路径
           常见选项例子：&ic  " ignorecase
   
   .. Tip::
           使用set命令可以改变选项设置，例如: set ignorecase
           
           使用一个set命令可以看到当前所有的选项及其设置。

3. 变量赋值
  
   .. code::

       :let 变量名=值  # 最前面的冒号不仅是为了表示这是一个冒号命令，而且是必须的。
       :unlet! 变量名  # 释放变量

4. 运算符(和perl基本一样)
   
   :: 

           数学运算：+ - * / % .
           逻辑运算：== != > >= < <= ?:
           正则匹配运算符：=~ !~

控制结构
--------------

.. code-block:: vim
        
        if 条件
           语句块
        elseif 条件
           语句块
        else
           语句块
        endif
        
        while 条件
           语句块
        [break/continue]
        endwhile

函数
--------------

.. code-block:: vim

        " 定义
        function 函数名(参数)
            函数体
        endfunc     

        " 调用
        call 函数名(参数)  " 在脚本语句中使用 
        :call 函数名(参数) " 在vim命令中使用 
        
.. Note::
        在函数体中使用参数需要在参数变量名称前加上a:

执行命令,键盘绑定,命令行命令和自动命令
----------------------------------------

1. 执行命令
   
   .. code-block:: vim
           
           exec "命令"  " 用于在vim脚本中执行一系列vim命令
           :!外部命令  " 这是一个vim命令行命令，功能是调用外部程序

2. 键盘绑定 
   
   一般格式：映射命令 按键组合 命令组合

   .. code-block:: vim
           
           :help map-overview
           :help keycodes  "查看<CR> <up> <lt>等等分别表示什么意思
           map :全模式映射
           nmap :normal模式映射
           vmap :visual模式映射
           imap :insert模式映射

3. 命令行命令
   
   vim支持在启动的时候使用-c开关执行命令字符串

4. 自动命令
  
  .. code-block:: vim

          一般格式：autocmd 事件 文件类型 命令
          例子：au BufNewFile,BufRead *.pl setf perl
          解释：当事件 BufNewFile 和BufRead 发生在 *.pl 文件上的时候，执行命令：setf perl


插件
==============

我的快捷键一览(<leader> = ,)
-----------------------------

::

    # N mode
    [[       # 跳转到函数头
    :Td                    # 显示所有 todo 位置
    :Fx                    # 显示所有 fixme 位置

    ## nerdcommenter
    <leader> + c + space   # 注释toggle

    ## easymotion
    <leader><leader>s     # 然后输入要跳转到的字母，按显示的字母就可以跳转到该位置

    ## CtrlP
    ctrl + p        # 查找

    <leader> + ls   # NerdTree
    <leader> + tb   # TagBar

    ## riv
    z + a           # fold toggle
    z + M           # fold all
    z + R           # unfold all

    ## vim-surround 
    <leader> + cs + <old_s> + <new_s>  # surround 替换

    ## YCM
    <leader> + jd   # 跳转到定义或声明

    ## ultisnips 搭配 vim-snippets 使用(代码库shortcut在vim-snippets的UltiSnip目录下)
    <leader><tab>  # 补全, 在展开补全后下面的命令分别
    <C-b>          # 跳转到下一个替换部分(JumpForwardTrigger) 
    <C-z>          # 跳转到上一个替换部分(JumpBackwardTrigger)

    ## pymode
    <leader>b      # insert a breakpoint
    K              # 查看函数的帮助文档

    ## split-manpage(View any man page in a split vim window)
    <Leader>kk # opens the man page on a split window above the current window.
    <Leader>kj # opens the man page on a split window below the current window.
    <Leader>kh # opens the man page on a vertical split window to the left of the current window.
    <Leader>kl # opens the man page on a vertical split window to the right of the current window.

    ## 横竖屏切换
    <C-w> <Shift h>  # 将当前缓冲区置于最左侧
    <C-w> <Shift k>  # 将当前缓冲区置于最上方

    # V mode
    ## multi-select
    ctrl + n        # next
    ctrl + p        # go back to previous
    ctrl + x        # skip current and select next

    ## vim-surround
    S<surround>           # add surround

    ## vim-autoformat
    :Autoformat   # python 要装相应的插件


.. warning::

   ale 插件的ale_fix_on_save=1 会导致代码保存时自动调整格式

   gtags 插件会导致 nerdcommenter 的 <,cc> 命令不可用


自动补全类
--------------

`neocomplete <https://github.com/Shougo/neocomplete.vim>`_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

deprecated:
    现在使用 `deoplete <https://github.com/Shougo/deoplete.nvim#requirements>`_

设置python的omnifunc时，要根据vim的 +python 和 +python3 属性设置::
    
    pythoncomplete#complete  "for python
    pythonc3omplete#complete  "for python3

否则报错 unknown function pythoncomplete#complete 


`YouCompleteMe <https://github.com/ycm-core/YouCompleteMe>`_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
在 ``.vim/bundle/YouCompleteMe/third_party/ycmd/cpp/ycm/.ycm_extra_conf.py`` 配置信息如下
(新版位于 ``.vim/bundle/YouCompleteMe/third_party/ycmd/.ycm_extra_conf.py``)
  
.. code-block:: python

   '-isystem',
   '/usr/include',
   '-isystem',
   '/usr/include/c++/4.8.4',  # 根据实际的/usr/include/c++/中的文件夹名称(即C++版本号)修改
   '-isystem',
   '/usr/include/c++/4.9.2',
   '-isystem',
   '/usr/include',
   '/usr/include/x86_64-linux-gnu/c++',

实际上以上是vim自动补全时搜索路径，如果自动补全的内容位于 ``/usr/local/include`` 里面，则添加以下信息

.. code-block:: python

   '-isystem',
   '/usr/local/include',

详情参考 `Vim自动补全神器YouCompleteMe的配置 <http://www.cnblogs.com/starrytales/p/6031671.html>`_

.. Tip::

   推荐将 ``.ycm_extra_conf`` 文件拷贝到工程目录做修改使用，YouCompleteMe 会自动识别

`ultisnips <https://github.com/SirVer/ultisnips>`_ 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

自动添加代码块，UltiSnips 更像是一个调用引擎，它本身并不提供任何 snips, 可以结合vim-snippets 使用

要使用 UltiSnips，vim 需要开启对 python 的支持。

打开 vim 的时候， UltiSnips 会搜寻 $VIM 路径下的所有名字为 UltiSnips 的文件夹，然后根据文档类型来寻找对应的 snips。 可以参考vim-snippets的UltiSnip目录下定义的文件写自己的配置文件

.. code::

    snippet 关键词 “说明” 设定
    内容
    endsnippet

设定包括：

- b 代表只有关键词出现在行首的时候，才可以被展开
- A 代表自动展开
- w 代表可以展开这个 “词”，具体 “词” 的定义可以查看 :help iskeyword。直观感觉就是，这个关键词是单独的，和其他文字分开的。比如前后都是空格。
- i 代表可以忽略前后字节，直接展开关键词。（这个设定比 w 要更松）

详细信息查看 :help ultisnip

在snip展开后，对可替换部分进行修改，可替换部分的跳转快捷键为

.. code::

    <c-b> # 跳至下一个部分
    <c-x> # 跳至前一个部分

`参考链接 <http://vimzijun.net/2016/10/30/ultisnip/>`_


符号索引
----------

cscope
^^^^^^^^^^^^^^

deprecated:
    能查引用，但只支持C语言，且常年不更新

+ 建立符号列表
  
  进入源代码目录，建立符号表： ``cscope -R`` ， Ctrl-D 退出，当前目录会多一个 ``cscope.out`` 文件;
  
+ 使用指南 

  .. code::
          
          vim -t XXX``   # 进入函数
          Ctrl + ]       # 跳转到函数定义的地方 
          Ctrl + \ +s    # 出现所有调用、定义该函数的地方，输入索引号，回车即可进入
          Ctrl + <Space> + s  # 出现所有调用、定义该函数的地方，输入索引号，回车后会以水平方式在另外一个窗口显示。
          Ctrl + t   # 回到原来跳转前的地方，连续按两下''可以再回去。 

ctags
^^^^^^^^^^^^^^

deprecated:
    只能查定义，不能查引用

* 安装

  .. code-block:: bash
     
      brew install ctags  # MacOS 
      sudo apt-get install exuberant-ctags  # ubuntu 

* 命令

  .. code-block:: bash
      
      ctags -R  # 生成索引文件
       
  .. code-block:: vim
      
      Ctrl＋］  # 跳到当前光标下单词的标签
      Ctrl＋O   # 返回上一个标签
      Ctrl＋T   # 返回上一个标签
      :tag TagName  # 跳到TagName标签

      #  以上命令是在当前窗口显示标签，当前窗口的文件替代为包标签的文件，当前窗口光标跳到标签位置。如果不希望在当前窗口显示标签，可以使用以下命令
      :stag TagName  # 新窗口显示TagName标签，光标跳到标签处
      Ctrl＋W + ］  # 新窗口显示当前光标下单词的标签，光标跳到标签处
      # 当一个标签有多个匹配项时（函数 (或类中的方法) 被多次定义），":tags" 命令会跳转到第一处。如果在当前文件中存在匹配，那它将会被首先使用。可以用这些命令在各匹配的标签间移动：
      :tfirst    # 到第一个匹配
      :[count]tprevious  # 向前 [count] 个匹配
      :[count]tnext   # 向后 [count] 个匹配
      :tlast    # 到最后一个匹配
      # 或者使用以下命令选择要跳转到哪一个
      :tselect TagName # 输入以上命令后，vim会为你展示一个选择列表。然后你可以输入要跳转到的匹配代号 (在第一列)。其它列的信息可以让你知道标签在何处被定义过。

      # 以下命令将在预览窗口显示标签
      :ptag TagName   # 预览窗口显示TagName标签，光标跳到标签处
      Ctrl＋W + }   # 预览窗口显示当前光标下单词的标签，光标跳到标签处
      :pclose   # 关闭预览窗口
      :pedit file.h  # 在预览窗口编辑文件file.h（在编辑头文件时很有用）
      :psearch atoi  # 查找当前文件和任何包含文件中的单词并在预览窗口中显示匹配，在使用没有标签文件的库函数时十分有用。

.. Tip:
   在 ``.vimrc`` 中设置 ``set tag=tag;`` , 否则在进入自文件夹时出现 tag not found 的错误

gtags(GNU GLOBAL)
^^^^^^^^^^^^^^^^^^^

* 优点：

  1. 不但能查定义，还能查引用
  2. 原生支持 6 种语言（C，C++，Java，PHP4，Yacc，汇编）
  3. 扩展支持 50+ 种语言（包括 go/rust/scala 等，基本覆盖所有主流语言）
  4. 使用性能更好的本地数据库存储符号，而不是 ctags 那种普通文本文件
  5. 支持增量更新，每次只索引改变过的文件
  6. 多种输出格式，能更好的同编辑器相集成

* 安装

  .. code-block:: bash

      # 安装依赖库
      sudo apt install global libncurses5-dev libncursesw5-dev

      # 如果要支持其它语言，还需安装
      pip install pygments

      # 下载global并编译安装
      wget http://tamacom.com/global/global-6.6.3.tar.gz
      tar xvf global-6.6.6.tar.gz
      cd global-6.6.3
      ./configure
      make
      sudo make install 

  然后在 vimrc 中加入以下两个插件搭配使用::

     'ludovicchabant/vim-gutentags'
     'skywind3000/gutentags_plus'

  前者提供 gtags 数据库的无缝更新，后者提供数据库无缝切换
  相关配置参考 `Vim 8 中 C/C++ 符号索引：GTags 篇 <https://zhuanlan.zhihu.com/p/36279445>`_

  .. warning::
      gutentags_plus 的快捷键 ``<leader> cc`` 与nerdcommenter 的冲突，要设置一下

taglist
^^^^^^^^^^^^^^
* 安装: 使用vim-plug

* 命令

  .. code-block:: vim

      # 切换函数列表的开、关
      :TlistToggle   # 在打开和关闭间切换
      :TlistOpen  # 打开taglist窗口
      :TlistClose  # 关闭taglist窗口

      # 在taglist窗口中，可以使用下面的快捷键
      <CR>   # 跳到光标下tag所定义的位置，用鼠标双击此tag功能也一样
      o   # 在一个新打开的窗口中显示光标下tag 
      <Space>   # 显示光标下的tag的原型定义
      u   # 更新taglist窗口中的tag 
      s   # 更改排序方式，在按名字排列和按出现顺序排序间切换 
      x   # taglist窗口放大和缩小，方便查看较长的tag 
      *   # 打开一个折叠，同zo 
      *   # 将tag折叠起来，同zc 
      *   # 打开所有的折叠，同zR 
      =   # 将所有tag折叠起来，同zM 
      [[  # 跳到前一个文件 
      ]]  # 跳到后一个文件 
      q   # 关闭taglist窗口 
      <F1>   #显示帮助
        
tagbar
^^^^^^^^^^^^^^
+ 安装：使用vim-plug

language
---------------
riv
^^^^^^^^^^^^^^

To make it easier to visualize and easier to edit

Riv has a lot of shortcuts and built-in features that help editing RST. It has even some features that help managing todos or projects.

In case of visualizing results riv has shortcuts that are able to generate document and start it in browser to preview result.

.. code::
    
    :RivInstruction  # 查看相关命令 (好像没有？可以直接打开安装目录的doc查看)
    :RivQuickStart   #
    :RivPrimer       # 打开restructured 教程
    :RivSpecification # a detailed look at reStructuredText's specifications
    :RivCheatSheet   # a quick review.
   

InstantRst
^^^^^^^^^^^^^^
Preview rst document instantly. You can share the address through LAN too.

.. code::
    
    # Inside a rst buffer:
    :InstantRst[!]  # Preview current buffer. Add ! to preview ALL rst buffer.
    :StopInstantRst[!]  # Stop Preview current buffer Add ! to stop preview ALL rst buffer.

vim-multiple-cursors
--------------------

多位置选择编辑

.. code::

    # 选择一个变量修改： 先选中(v)第一个变量名，然后
    <C-n>   # 选择下一个, 然后进行相应操作

    # 多行前插入：先选中多行/block, 然后
    <C-n>   # 在所有行首插入

vim-fugitive
-------------

在vim 中使用git 命令

.. code::

    :Git <git command> # 从 VIM 命令行中运行任何的 git 命令

    :Gcommit # 打开一个水平分隔的窗口填写提交信息并使用 :wq 命令完成
    :Gblame  # 打开一个垂直分隔的窗口，其中包含有对每一次commit的信息
    :Gstatus # 打开一个窗口显示当前 git 仓库的状态,
    :Gvdiff  # 以垂直分隔窗口比较当前文件(r)和当前文件的 index 版本(l)



vim-autoformat
---------------

代码自动格式化

安装好后设置::
    
    au BufWrite * :Autoformat "自动格式化代码， 针对所有文件

需安装相应的format 工具才会生效

- clang-format

  .. code-block:: bash

      brew install clang-format //mac
      sudo apt-get install clang-format //ubuntu

- atype


neovim 配置
==============
1. 安装 neovim，配置文件放在 `~/.config/nvim/init.vim`
2. 安装 vim-plugin, 注意设置autoload 路径在 `~/.config/nvim/autoload/` 

其它
==============
查看插件列表—— ``:scriptnames``

“无法写入，已设定选项 buftype”—— ``:setlocal buftype=``  

修改python脚本的缩进量（例如由2转4），在vim编辑器中输入

.. code-block:: vim

    " changes every 2 spaces to a TAB character
    :set ts=2 sts=2 noet
    :retab!
    " changes every TAB to 4 spaces
    :set ts=4 sts=4 et
    :retab
    " ts := tabstop, sts := softtabstop and [no]et := [no]expandtab.

保存文件时想获得sudo权限

.. code-block::

   :w !sudo tee %  # tee 是一个将stdin写入文件的命令，%是vim的一个寄存器

查看冲突快捷键

.. code-block::

   :verbose map <keymap>
