ruby
======

安装
----

1. 安装 RVM 
   RVM 能在系统中安装和管理多个 Ruby 版本。同时还能管理不同的 gem 集。

2. 安装ruby

   .. code-block:: bash

       source ~/.rvm/scripts/rvm  //载入 RVM 环境（新开 Termal 就不用这么做了，会自动重新载入的）
       rvm -v  // 检查一下是否安装正确
       rvm list known  // 列出已知的 ruby 版本
       rvm install <x.x.x>  // 选择现有的 ruby 版本来进行安装
       rvm <x.x.x> --default  // 将指定版本的 Ruby 设置为系统默认版本

       ruby -v
       gem -v

       # 替换源
       gem source -r https://rubygems.org/
       gem source -a https://gems.ruby-china.org
       gem sources -l 

       gem install rails  # 安装rails

       bundle config mirror.https://rubygems.org https://gems.ruby-china.org  # 
      
   rvm 常用命令

   .. code-block:: bash 

      rvm list  //查询已经安装的 ruby 
      rvm remove <x.x.x>  // 卸载一个已安装ruby版本


语法规则
--------

class name: CamelCase
filename: snake case

Gemfile 语法
------------

.. code:: 

    >= <x.x.x>  # 安装最新版本
    ~> <x.x.x>  # 仅最后一位不同

rails
------

常用命令
``````````

.. code-block:: bash

    rails new
    rails g(enerate) [scaffold | controller | model]
    rails c(onsole)
    rails s(erver)
    rails t(est)
    rails destroy 
    
    rails db:setup
    rails db:migrate
    rails db:rollback [VERSION=0]

    bundle (install)

使用scaffold
``````````````

.. code-block:: bash
   
    bundle install --without production
    rails g scaffold <res_name> <res_item...>
    rails db:migrate  # update the database with our new data model

自己创建
``````````

.. code-block:: bash

   rails g controller <C_name> <action_name...>  # create contoller


