---
layout: post
title: 使用SendEmail发送邮件通知
tags: [SendEMail]
---

sendmail是最重要的邮件传输代理程序.但今天我们说的不是SendMail,而是SendEmail,请看清楚了,但是,还是介绍一下SendMail吧.

<!--more-->

### 介绍 ###

理解电子邮件的工作模式是非常重要的,一般情况下,我们把电子邮件程序分解成用户代理,</br>
传输代理和投递代理.用户代理用来接受用户的指令,将用户的信件传送至信件传输代理,</br>
如:outlook express、foxmail等.</br>
而投递代理则从信件传输代理取得信件传送至最终用户的邮箱.

当sendmail程序得到一封待发送的邮件的时候,</br>
它需要根据目标地址确定将信件投递给对应的服务器,这是通过DNS服务实现的.

例如一封邮件的目标地 址是admin@debug0.com,</br>
那么sendmail首先确定这个地址是用户名（ideal）+机器名 （linuxaid.com.cn）的格式,</br>
然后,通过查询DNS来确定需要把信件投递给某个服务器.</br>
其它的我就不多说了,关于更详细的邮件服务器及邮件发送原理,请搜索更多的相关资料.

### 使用 ###

在本例中,目的用于时时监控公司网络,获取公网IP,</br>
每天早上发邮件到139邮箱提醒,以便于手机端能及时接收到信息.

首先,先下载它:[SendEMail][]
[SendEMail]:http://caspian.dotconf.net/menu/Software/SendEmail/sendEmail-v1.56.tar.gz
</br>
Linux环境下,用Wget下载即可.

至于Tar.gz包的安装,</br>
方法如下(有些RHEL及Centos系统上面已经安装过rpm包的Sendmail,</br>
或者在安装光盘里面有RPM的安装包,请自请安装.效果一样):

<code>tar -zxvf sendEmail-v1.56.tar.gz</code>

<code>cp -a sendEmail-v1.56/sendEmail /usr/bin</code>

<code>chmod +x /usr/bin/sendEmail</code></p>

随后,要编写Shell脚本及任务计划,具体方法和参数如下:

常用参数:

-f 发送方

-t 接收方

-s SMTP服务器的域名或者ip

-u 邮件主题

-m 邮件内容

-a 添加附件

-cc 副本

-xu smtp验证用户

-xp 密码

-v 详细输出

-o message-file=/file.txt 正文从文件内读取

-o tls=yes

Shell脚本:

<pre><code>
#!/bin/bash
#获取公网IP地址发邮件通知
#执行者：XXX
#日期：2014/05/16
ip=curl http://members.3322.org/dyndns/getip
</pre></code>

内网邮件通知
<pre><code>
#本shell中internal.com指内部域,在生产环境中请以自己内网的邮件域来替代.
/usr/bin/sendEmail -s xxx.xxx.xxx.xxx:25 -xu test@internal.com -xp 123456 -f test@internal.com -t admin@internal.com -u "IP地址" -o message-content-type=html -o message-charset=utf-8 -m "您的IP是:[$ip]来自：广东省深圳市 电信"
</pre></code>

139邮箱通知
<pre><code>
/usr/bin/sendEmail -s mail.139.com -xu 189xxxxxxxx@139.com <br>-xp xxxxxx -f 189xxxxxxxx@139.com -t 189xxxxxxxx@139.com -u "IP地址" -o message-content-type=html -o message-charset=utf-8 -m "您的IP是:[$ip]来自：广东省深圳市 电信"
</pre></code>
</br>
<pre><code>
添加Shell的执行权限:
chmod +x /opt/shell/ip.sh
</br>
制定任务计划:
crontab -e
</pre></code>
</br>
每天早上06:30发139邮箱通知网络情况
</br>

<pre><code>
#执行者：XXX
30 06 * * * /opt/shell/ip.sh
</code></pre>
### 获取 ###

获取文件:[SendEMail][1]

[1]:http://caspian.dotconf.net/menu/Software/SendEmail/sendEmail-v1.56.tar.gz