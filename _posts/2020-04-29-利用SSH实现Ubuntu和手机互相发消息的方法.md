---
layout:     post
title:      "利用SSH实现Ubuntu和手机互相发消息的方法"
date:       2020-4-29 15:03:00
author:     "Zhao Wang"
tags:
    - Linux
    - 即时通讯
    - SSH 
    - Ubuntu
---
Ubuntu上不能装各种各样的即时通讯软件，并且即使能够用wine，也有各种各样的bug，因此最好的方法就是用原生的方法实现通讯。这里我们采用SSH实现ubuntu与手机的通讯。

# 安装手机客户端
可以安装各个类型的ssh客户端到手机上。

# 安装SSH服务器客户端
一般ubuntu都自带SSH客户端，但是不带SSH服务器端，用以下命令安装：

    sudo apt-get install openssh-server
# 启动SSH server

    sudo service sshd start
# 给手机发消息
在手机上打开SSH软件，登录软件后，在电脑端给手机发消息：

    sudo wall '这是一条测试消息'
这样就在手机端接收到了消息。

用手机给ubuntu发消息的原理也是一样的。

# 及时结束SSH登录

用完后及时结束ssh server：

    sudo service sshd stop