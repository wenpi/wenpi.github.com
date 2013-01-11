---
comments: true
date: 2009-12-08 11:20:00
layout: post
slug: openims%e5%ae%89%e8%a3%85%e8%af%95%e9%aa%8c-ims%e5%bc%80%e6%ba%90%e8%af%95%e9%aa%8c
title: OpenIMS安装试验---IMS开源试验
wordpress_id: 1576025
categories:
- 未分类
---

# OpenIMSCore及客户端 step by step 傻瓜安装配置指南








    
        


            
以下都是是我在一台机子上跑的过程，所以中间有些步骤没有做，将来会研究如何在不同的机子上安装并且写一篇更详细的指南。主要参考了以下两篇资料：  
(1)OpenIMSCore installation Guide:[http://openimscore.org/installation_guide](http://openimscore.org/installation_guide)  
(2)OpenIMS安装日记：[http://dongwflj.blog.163.com/blo ... 712007101635251844/](http://dongwflj.blog.163.com/blog/static/163437712007101635251844/)  
  
  
  
  
开始进入正题：  
  
  
＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝前期准备＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝  
1。安装linux操作系统，内核最好在2.6以上；  
  
  
2。安装jdk，参见java安装日志。这一步非常重要，我装的是fedora 8，默认安装的是icedtea,在icedtea基础上编译的FHoSS，执行fhoss.sh时会一闪而过，用http://localhost: 8080也不能登录HSS。必须在"添加/删除软件"里把浏览－基本系统－JAVA全部删除，然后按照网上的方法安装jdk，我安装的是jdk-6u5。 之后编译HSS就一帆风顺了。  
  
  
3。安装mysql，libxml2,libmysql(这个没有装，似乎也可以编译通过）。装完启动mysql：  
  
fedora下：  
  
chkconfig --level 5 mysqld on  
  
service mysqld start  
  
  
4.安装bind，同样启动：  
  
fedora下：  
  
chkconfig --level 5 named on  
  
service named start  
  
  
＝＝＝＝＝＝＝＝＝＝＝＝＝＝下载源码＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝  
5.做完准备工作下载openims代码.  
这点我想详细说一下，按照网上的方法，在windows上用svn下载的代码要作的步骤过多，我没有去尝试，按照openimscore.org上的方 法在linux下用svn下载也是有点问题，编译会有错误。所以我是从官网的ftp上下的，地址是ftp: //ftp.berlios.de/pub/openimscore/snapshots/。下下来以后编译很顺利，所以不必用svn下下来自讨苦吃。如 果你坚持用svn来装，可以按照官网上的命令一步一步来，我对svn不太熟![](http://bbs.cnttr.com/images/smilies/default/smile.gif)。  
  
  
＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝开始编译＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝  
  
  
6. 进入ser_ims，输入make install-libs all 应该一切OK。  
  
  
7. 进入FHoss， 输入  
  
# java -version  
java version "1.5.0_07"  
Java(TM) 2 Runtime Environment, Standard Edition (build 1.5.0_07-b03)  
Java HotSpot(TM) Client VM (build 1.5.0_07-b03, mixed mode)  
确保java的版本大于1.5。jdk的重要性我在2里面已经说过了，如果jdk安装不对，HSS是怎么也起不来的。  
  
  
8. ant compile  
  
ant deploy  
  
cd ..  
  
编译完成开始配置：  
  
9。缺省的domain是：open-ims.test  
  
编辑ser_ims/cfg/configurator.sh 改变相应的设置。类似一个文本替换工具。  
  
  
＝＝＝＝＝＝＝＝＝＝＝＝＝＝配置环境＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝  
10.DNS 配置：可以按照ser_ims/cfg/open-ims.dnszone来配置DNS（其中默认的ip是127.0.0.1,可以改为自己的ip。）  
1）在/etc/named.conf 增加:  
  
zone "open-ims.test" IN {  
  
type master;  
  
file "open-ims.dnszone";  
  
}  
  
2）拷贝open-ims.dnszone 到/var/named/chroot/var/named下，然后可以把其中默认的ip127.0.0.1改为自己的ip。（如果是在一台机子上跑，可 以不用改）并可以做符号链接到/var/named（做符号链接可选，如果只是在机子上跑起来，可以不做符号链接）  
  
3）还应该配置反向解析（可选，不做也可以运行,反向解析是指根据IP地址可以方便的查询到对方所属的机构，现在已经很少用到这个功能了。）  
  
4）修改/etc/resolv.conf 是DNSserver指向自己。（最好备份一下这个文件，因为我在学校上网，每次上网都会重写这个文件）  
  
  
  
配置完成后如下例所示：（Fedora）  
  
(1) /etc/named.conf:  
  
...  
  
zone "open-ims.test" IN {  
  
type master;  
  
file "open-ims.dnszone";  
  
};  
  
（下面这一段是网上的教程里面些的，可以不做，这是10（3）的反向解析）  
zone "1.168.192.in-addr.arpa" {   
  
type master;  
  
file "named.1.168.192";  
  
};  
  
(2) 创建/var/named/chroot/var/named/ open-ims.dnszone和named.1.168.192（后半部分可选，同上，我没有做）  
（可以替换所有的127.0.0.1成为需要的IP地址，我没有试过，我只是之间把ser-ims里面的open-ims.dnszone拷过来了）  
附：以下是网上教程里面把127.0.0.1换成自己ip地址的做法，还要作反向解析  
open-ims.dnszone：  
  
named.1.168.192  
  
...  
  
@ IN SOA open-ims.test. root.localhost. (  
  
...)  
  
IN NS open-ims.test.  
  
185 IN PTR open-ims.test.  
  
185 IN PTR pcscf  
  
185 IN PTR icscf  
  
185 IN PTR scscf  
  
185 IN PTR hss  
  
(3) /etc/resolve.conf（可选，同上）  
nameserver 192.168.1.185 (IP 地址根据本机地址确定)  
search open-ims.test  
domain open-ims.test  
  
   
  
11。MySQL配置(用户名root/密码空)  
  
mysql -u root -p -h localhost < ser_ims/cfg/icscf.sql  
mysql -u root -p -h localhost < FHoSS/scripts/hss_db.sql   
mysql -u root -p -h localhost < FHoSS/scripts/userdata.sql  
  
 12。配置IMS Core  
  
cd /opt/OpenIMSCore  
  
cp ser_ims/cfg/*.cfg .  
cp ser_ims/cfg/*.xml .  
cp ser_ims/cfg/*.sh .  
  
可以编辑FHoss/deploy里面的文件。  
  
13。启动IMS,可能需要增加文件执行属性.  
选择在终端中执行一下文件  
/opt/OpenIMSCore/pcscf.sh  
  
/opt/OpenIMSCore/icscf.sh  
  
/opt/OpenIMSCore/scscf.sh  
  
/opt/OpenIMSCore/FHoSS/deploy/startup.sh  
  
使用http://localhost:8080 访问用户名是：  
  
* hssAdmin - password: hss  
* hss - password: hss  
  
用户可以使用：/conf/tomcat-user.xml 配置其他用户。  
  
其他注意事项：需要打开Fedora的防火墙在4060和8080端口。需要配置scscf使用MD5鉴权，否则客户端使用AKA注册。   
  
  
  
==============================================================================================  
OpenIMS客户端傻瓜安装指南  
ok,我在上面讲到了如何安装和配置OpenIMSCore，现在我们就用OpenIMSCore上的介绍的三个客户端([http://openimscore.org/faq](http://openimscore.org/faq))之一的FOKUS' OpenIC (Open IMS Client)（[http://www.fokus.fraunhofer.de/ims/components/open_ic.php?lang=en](http://www.fokus.fraunhofer.de/ims/components/open_ic.php?lang=en)）来进行测试，以下是安装和配置的过程。  
  
1.我们选择安装linux版的OpenIC，点击网页下载客户端([http://www.fokus.fraunhofer.de/t](http://www.fokus.fraunhofer.de/t) ... /OpenIC_Lite.tar.gz)，并且下载说明书([http://www.fokus.fraunhofer.de/t](http://www.fokus.fraunhofer.de/t) ... Lite_User_Guide.pdf)。  
  
2.首先解压下载下来的文件OenIC_Lite.tar.gz：  
  
3.修改OpenIC_Lite.sh的属性：在解压好的目录里右键点击OpenIC_Lite.sh，在"权限"标签页里把所有的"访问"都修改为"读写"，点选"允许以程序执行文件"。  
  
4.用编辑器打开OpenIC_Lite.sh文件，修改JAVA HOME为你安装的jdk的jre的路径，修改后的文件如下：  
#!/bin/bash  
  
# ---------------------  
# set JAVA_HOME to your own preferences  
# ---------------------  
  
JAVA_HOME=/usr/java/jdk1.6.0_05/jre  
  
# --------------------------------------------------------------  
# Include JAR Files  
# --------------------------------------------------------------  
  
。。。。。  
  
5.双击执行OpenIC_Lite.sh文件。  
  
6. 执行后首先要配置User Profile，Display name里面写上你自己喜欢的名字，.etc Brad Pitt 。由于OpenIMSCore里的HSS里面默认有两个用户 [bob@open-ims.test](mailto:bob@open-ims.test)和[alice@open-ims.test](mailto:alice@open-ims.test)，所以以bob为例，我们在Public Identity里面输入sip:bob@open-ims.test，在Private Identity里面输入[bob@open-ims.test](mailto:bob@open-ims.test), Secret Key，就是注册key，可以在HSS里面看到，默认的bob的密码就是bob，alice的密码就是alice :)  
  
7.然后配置Server Profile，在Proxy IP里面填入P－CSCF的IP（！）地址，我的是127.0.0.1，Port Nr就是P－CSCF的端口，我的是4060， Realm是IMS的域名，我的是open-ims.test。  
  
8.Application里面是你要订阅的业务，写得很明白。免费版，连个presence都没有..  
  
点击save，开始运行。  
  
9.如果要模拟两个用户打电话，就直接把OpenIC的目录拷贝一个到另一个地方，修改profile.cfg,内容如下：  
#OpenIMS Client config file  
#Wed Mar 19 17:06:15 CST 2008  
OP=00000000000000000000000000000000  
AMFSTAR=0000  
secretKey=bob  
showExitDialog=true  
AMF=0000  
autoPlaySound=true  
SQN=000000000000  
realm=open-ims.test  
privateIdentity=bob@open-ims.test  
proxyCSCF=127.0.0.1\:4060/UDP  
publicIdentities=sip\:bob@open-ims.test  
displayName=Yangkai  
useAK=true  
earlyIMS=false  
autoSignIn=true  
autoAnswer=false  
  
看到了吧，上面我们设置的东西都在这里，呵呵。所以我们可以修改bob为alice，就可以用alice来登录了。、  
  
10.alice也登录后，就可以直接打电话了，自己可以慢慢探索 : )  
  
觉得免费版的功能有点太弱了，也希望有能力的朋友可以自己写一个，拿出来分享一下～   
  
  
  
=========================进阶版==================================  
通过我和Qinjian的努力，终于可以在不同的机子上部署各个实体，也可以在不同的机子上通过客户端来进行通信了。  
  
ok，进入正题。  
  
在以前的部分我们讲过在配置OpenIMSCore的时候，ip地址都是配置为127.0.0.1，就是说各个实体都是在一台电脑上，这样的结果多少让人 有些难以信服。所以我和Qinjian都在研究如何在不同的机子上部署各个实体，最后客户端通过接入OpenIMSCore来进行通信，这样就可以认为是 简单的模拟IMS网络了。其实难度不大，主要是我们刚学习linux，许多基本的概念和命令都不知道，所以遇到了很多的困难。下面我们按步骤的来进行说 明，如何配置linux和OpenIMS（其实主要是配置Linux上的DNS解析服务)。  
  
一.配置OpenIMSCore  
  
1.进入／opt/OpenIMSCore/ser_ims/cfg,双击configurator.sh，选择"在终端中运行"，然后在中断中按提示输 入域名和 ip，我起的是open-ims.test(其他的无所谓，但是要在dns配置里面作相应的配置，下文会讲)，ip是我的公网ip: 59.64.xx.xx(这点很关键，必须填自己机子的ip)。hss不用配，因为从它的配置文件可以看到hss总是监听127.0.0.1地址的，在哪 台机子上运行都一样。  
  
2.拷贝／opt/OpenIMSCore/ser_ims/cfg下的所有文件到/opt/OpenIMSCore下。  
  
二.配置Linux  
这里花费了我们主要的时间，主要是因为我们不熟悉一些linux的基本概念的使用方法，关键词：DNS服务，防火墙，还是按步骤来写吧。  
  
1.关闭防火墙，或者关闭防火墙对DNS请求的屏蔽。（fedora 8默认是屏蔽的T_T,让我们很久都不明白问题出在那里，花费了2天时间……）。我选择把防火墙关闭了，输入 service iptables stop.  
  
2.修改每台机子上的resolv.conf文件  
／etc目录下的resolv.conf文件是用于配置本机的dns服务器地址，例如我自己的dns服务器是自己，那么resolv.conf文件文件可以是如下内容：  
; generated by /sbin/dhclient-script（这行是系统自带的，不用修改）  
nameserver 59.64.xx.xx(希望谁做dns服务器，这里就写上那台机子的ip)  
search open-ims.test（跟你定义的域名有关）  
domain open-ims.test  
  
那么为什么要配置这一步呢，这是因为我们默认的IMS域名是open-ims.test，如果不到自己指定的dns服务器去解析，你希望电信网通或者是自己单位的dns能知道这个域名是指向哪么……  
  
3.修改相应的DNS服务器上的name.conf文件（在/etc目录下），  
name.conf是配置这台机子在作DNS服务器的情况下，怎样解析相应的域名的（需要相应的DNS方面的知识，可以在我的blog前面的两篇文章里面看看）。我的name.conf文件如下，蓝色部分是相对应于原文件做过修改的部分：  
options {  
listen-on port 53 { any; };  
listen-on-v6 port 53 { any; };  
directory "/var/named";  
dump-file "/var/named/data/cache_dump.db";  
statistics-file "/var/named/data/named_stats.txt";  
memstatistics-file "/var/named/data/named_mem_stats.txt";  
allow-query { any; };  
recursion yes;  
# forward first;  
# forwarders {  
# 211.68.71.5;  
# };  
};  
  
logging {  
channel default_debug {  
file "data/named.run";  
severity dynamic;  
};  
};  
  
zone "." IN {  
type hint;  
file "named.ca";  
};  
zone "open-ims.test" IN {  
type master;  
file "open-ims.dnszone";  
notify no;  
};  
  
include "/etc/named.rfc1912.zones";  
  
其中，蓝色的三个"any"是因为fedora 7以后都是默认不接收别人的dns解析请求的，只接受自己的dns协议请求。所以不难理解在作为dns服务器的named.conf文件中，一定要修改为any来接受其他用户发来的dns解析请求。后面的蓝色部分是对应于相应的ims的域名的，我用默认的open-ims.test，为什么要这么些可以参考blog里的前面一篇文章。  
  
4.修改DNS服务器上的 open-ims.dnszone文件（/var/named/chroot/var/named目录下）  
open-ims.dnszone文件是配置DNS服务器如何解析xx.open-ims.test到相应的ip地址上的，我的配置如下：  
$ORIGIN open-ims.test.  
$TTL 1W  
@ 1D IN SOA localhost. root.localhost. (  
2006101001 ; serial  
3H ; refresh  
15M ; retry  
1W ; expiry  
1D ) ; minimum  
  
1D IN NS ns  
ns 1D IN A 59.64.153.202  
  
pcscf 1D IN A 59.64.153.202  
  
open-ims.test. 1D IN A 59.64.153.202  
icscf 1D IN A 59.64.153.202  
_sip 1D SRV 0 0 5060 icscf  
_sip._udp 1D SRV 0 0 5060 icscf  
_sip._tcp 1D SRV 0 0 5060 icscf  
  
open-ims.test. 1D IN NAPTR 10 50 "s" "SIP+D2U" "" _sip._udp.open-ims.test.  
open-ims.test. 1D IN NAPTR 20 50 "s" "SIP+D2T" "" _sip._tcp.open-ims.test.  
  
  
scscf 1D IN A 59.64.153.202  
  
hss 1D IN A 59.64.153.202  
  
ue 1D IN A 59.64.153.202  
  
presence 1D IN A 59.64.153.202  
  
看到没有，里面的ip就是各个实体所部署在的机子的ip地址，例如我的p－cscf是在59.64.153.187上，则我就可以把pcscf那一行的ip地址改为59.64.153.187。如果我想把各个实体部署在不同的地址上，那么就通过这个文件就可以在dns上定义各个实体的ip地址。  
  
  
  
ok，基本的内容就在上面，下面我通过一个例子来说明，例如我把pcscf部署在59.64.153.187（记为A）,s/icscf和hss都部署在 59.64.153.202上（记为B），dns服务为59.64.153.111（C）上，另外两台客户端(D和E)用客户端（在这里我们用以前提到的 OpenIC）来登录IMSCore来进行通信（网络可以更复杂，但是都是一个道理），配置如下：  
  
(1).A－E这五台机子的DNS服务器都设为是C，则我们按照二.2来修改。最好把防火墙都关掉（我比较懒）  
(2).C通过二.3和4来配置解析，其中pcscf的ip设为A的，其他实体的ip都设为B的。  
(3).客户端的pcscf地址设为pcscf.open-ims.test(也可以直接是A的地址)  
  
ok，可以按我在前面文章里面说过的步骤来测试了，不过这个免费的OpenIC-Lite版只有语音通话和消息功能，presence功能要收费……  


        
    


