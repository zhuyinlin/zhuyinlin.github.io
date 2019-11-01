Shell
=====

常用命令
--------

.. code-block:: bash
           
       getconf LONG_BIT     # 查看系统是几位的
       cat /proc/cpiinfo    # 查看CPU信息 
       cat /etc/issue       # 查看ubuntu版本
       ifconfig  -a        # 查看本机ip
       lspci |grep VGA      # 查看GPU信息
       lspci -v -s XX:XX.X  # 根据前面查到信息的编号查询具体GPU信息

       sudo -s              # 以 root 身份运行
       chmod +x <filename>    # 修改执行权限 
       chown <user_name> <file/dir name> # 修改文件所有者

       pwd                  # 获取当前文件路径
       mkdir                # 文创建文件夹
       rm -rf !(<file1>|<file2>)  # 删除除 file1 和 file2 以外的文件
           
       mdfind   # 查找文件 
       echo $0  # 查看当前使用的shell
       echo $ZSH_VERSION  #查看zsh 版本

       grep -i -r "XXX" filename # 查找包含字符串的文件

       grep -A/-B/-C  n          # A, B, C 分别表示可以显示匹配行的后、前、后前多少行内容

       head -l   # 一行一行地读数据
       read -r   # 读数据

       set -e  # error checking
       set -x/+x  # 开启/关闭 打印到屏幕
       set -o  # 查看

变量
----

.. code-block:: bash

        readonly <var_name>  # 只读变量
        unset <var_name>     # 删除变量
        
        # 特殊变量
        $0    # 当前脚本的文件名
        $n    # 传递给脚本或函数的第 n 个参数
        $#    # 传递给脚本或函数的参数个数
        $*    # 传递给脚本或函数的所有参数。
              # 被双引号(" ")包含时，将所有的参数作为一个整体("$1 $2 … $n")输出
        $@    # 传递给脚本或函数的所有参数。
              # 被双引号(" ")包含时，将各个参数分开("$1" "$2" … "$n")输出
        $?    # 上个命令的退出状态，或函数的返回值
        $$    # 当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID

.. Tip::
        在使用变量时，推荐给所有变量加上花括号，这是个好的编程习惯。

替换
----
.. code-block:: bash

       echo -e <string>   # -e 表示对转义字符进行替换, 不加会原样输出(单引号)

       # 命令替换
       `command`          # 即Shell先执行命令，将输出结果暂时保存，在适当的地方输出。

       # 变量替换
       ${var}          # 变量本来的值
       ${var:-word}    # 若 var 为空或已被删除，返回 word，但不改变 var 的值
       ${var:=word}    # 若 var 为空或已被删除, 返回 word，并将 var 的值设置为 word
       ${var:?message} # 若 var 为空或已被删除，那么将消息 message 送到标准错误输出
                       # 可以用来检测变量 var 是否可以被正常赋值
                       # 若此替换出现在Shell脚本中，那么脚本将停止运行
       ${var:+word}    # 若 var 被定义，那么返回 word，但不改变 var 的值

运算符
------
原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。

.. warning:: 
        \`expr 2 + 2`\  和 \`expr 2+2\` 二者是不一样的，前者输出计算结果，后者是表达式

        乘法符号用 ``\*``

        条件表达式要放在方括号之间，并且要有空格 (条件表达式可用test 函数替换)

.. code-block:: bash

       {m..n} 
       seq m n   # 生成数字 m .. n
       {a,b,c}{1,2,3}   # 字符串排列组合，生成 a1 a2 a3 b1 b2 b3 ...

.. image:: /_static/programing/Shell/relaOp.png

.. image:: /_static/programing/Shell/boolOp.png

.. image:: /_static/programing/Shell/strOp.png

.. image:: /_static/programing/Shell/fileOp.png

字符串
------
字符串可以用单引号，也可以用双引号，也可以不用引号。

* 单引号

  - 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
  - 单引号字串中不能出现单引号（对单引号使用转义符后也不行）。
   
* 双引号

  - 双引号里可以有变量
  - 双引号里可以出现转义字符
    
.. code-block:: bash

       {a..z}         # 生成字母a-z (改成大写生成A-Z) 

       ${#str_var}    # 获取字符串长度
       ${str_var:start:len} # 提取子字符串, len 没有给出时，默认到字符串尾
       `expr index "$str_var" word`   # 查找子字符串

       IFS=<char> read -ra parts <<< str_var  # 用指定字符分割字符串, 结果放在parts 里。-r 表示读取row 数据

       while IFS= read -rn1 c; do    
           echo $c
       done <<< "$str_var"   # 遍历字符串 

       ${var/find/replace}   # 字符串替换(找到的第一个匹配项)
       ${var//find/replace}  # 字符串替换(全部匹配项)

       declare -u str_var   
       ${str_var^^}          # 字符串转大写，前一种表达式要在var定义前
       declare -l str_var
       echo ${str_var,,}     # 字符串转小写


数组
----
bash支持一维数组(不支持多维数组), 在Shell中，用括号来表示数组，数组元素用“空格”符号分割开。

.. code-block:: bash

       ${array_name[index]}   # 数组第 index 个元素
       ${array_name[*]}
       ${array_name[@]}       # 数组的所有元素
       ${#array_name[@]}
       ${#array_name[*]}      # 数组长度
       ${#array_name[index]}  # 数组某个元素长度

       

函数
----
Shell 函数必须先定义后使用。

.. code-block:: bash
   
       function_name () {    
           list of commands    
           [ return value ]
       }
       # 如果你愿意，也可以在函数名前加上关键字 function
       function function_name () {    
           list of commands    
           [ return value ]
       }
       # 函数返回值，可以显式增加return语句；如果不加，会将最后一条命令运行结果作为返回值。
       unset .f function_name  # 删除函数
       
.. Note::
        Shell 函数返回值只能是整数，一般用来表示函数执行成功与否，0表示成功，其他值表示失败。如果 return 其他数据，比如一个字符串，往往会得到错误提示：“numeric argument required”。

        调用函数只需要给出函数名，不需要加括号

        传递函数直接在调用时在函数后输入即可，在函数体内部，通过 ``$n`` 的形式来获取参数的值

        函数返回值在调用该函数后通过 ``$?`` 来获得。

        如果你希望直接从终端调用函数，可以将函数定义在主目录下的 .profile 文件，这样每次登录后，在命令提示符后面输入函数名字就可以立即调用。


重定向
------
一般情况下，每个 Unix/Linux 命令运行时都会打开三个文件：

* 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
* 标准输出文件(stdout)：stdout 的文件描述符为1，Unix程序默认向stdout输出数据。
* 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。

.. code-block:: bash

        cmd > file       # 将命令的标准 **输出** 重定向到 file              
        cmd < file       # 将命令的标准 **输入** 重定向到 file              
        cmd n > file     # 将文件描述符为 n 的文件重定向到 file             
        cmd &> file      # 重定向命令的stdout和stderr到文件                 
        cmd > file n>&m  # 将文件描述符为 n 和 m 的文件重定向到 file        
        cmd < file n<&m  # 将文件描述符为 n 和 m 的文件重定向到 file        
        cmd > /dev/null  # 忽略命令的标准输出

        cmd << <delimiter>
            <document>
        <delimiter>        # 将两个 delimiter 之间的内容(document) 作为输入 

        cmd <<< <string> # 将命令的标准 **输入** 重定向到一行输入   

        cmd1 >(cmd2)                                
        cmd1 > >(cmd2)                                                     
        cmd1 | cmd2      # cmd1的输出是cmd2的输入

        tee -a filename  # 重定向的同时也能在命令行输出        

注：将 ``>`` 替换为 ``>>`` , 表示以 **追加** 的方式重定向。此时会先向写入的文件加换行符，不想要 `\n` 的话，可以在 cmd 后加 `-n`


文件
----

.. code-block:: bash
  
       # Shell 中包含脚本
       . filename
       source filename

正则表达式
------------

判断file是不是zip文件

.. code-block:: bash 

        if [[ $file = *.zip ]]; then    
            # do somethingfi
        ]]                              

匹配一个数字，一个点，一个数字。

.. code-block:: bash

        if [[ $str =~ [0-9]+.[0-9]+ ]]; then    
            # do somethingfi
        ]]
