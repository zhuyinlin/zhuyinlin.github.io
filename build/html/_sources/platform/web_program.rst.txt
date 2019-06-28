Web 编程
========

术语概念
--------

URI、URL和URN 的区别
test-driven development (TDD)

MVC(Model View Controller) pattern
-----------------------------------


REST(REpresentational State Transfer) architecture 
--------------------------------------------------

资源在网络中以某种表现形式进行状态转移::

    Resource：资源，即数据（网络的核心）；
    Representational：某种表现形式，比如用JSON，XML，JPEG等；
    State Transfer：状态变化。通过HTTP动词实现。

URL定位资源，用HTTP动词（GET,POST,DELETE,DETC）描述操作

1. REST描述的是在网络中client和server的一种交互形式；REST本身不实用，实用的是如何设计 RESTful API（REST风格的网络接口）;
2. Server提供的RESTful API中，URL中只使用名词来指定资源，原则上不使用动词。“资源”是REST架构或者说整个网络处理的核心。
3. 用HTTP协议里的动词来实现资源的添加，修改，删除等操作。即通过HTTP动词来实现资源的状态扭转： 
4. Server和Client之间传递某资源的一个表现形式，比如用JSON，XML传输文本，或者用JPG，WebP传输图片等。当然还可以压缩HTTP传输时的数据（on-wire data compression）
5. 用 HTTP Status Code传递Server的状态信息。

优点：

1. RESTful可以通过一套统一的接口为 Web，iOS和Android提供服务。
2. 另外对于广大平台来说，比如Facebook platform，微博开放平台，微信公共平台等，它们不需要有显式的前端，只需要一套提供服务的接口

`best practice(覃超) <https://www.zhihu.com/question/27785028>`_

UCRD
-------
crud是指在做计算处理时的增加(Create)、读取查询(Retrieve)、更新(Update)和删除(Delete)几个单词的首字母简写
