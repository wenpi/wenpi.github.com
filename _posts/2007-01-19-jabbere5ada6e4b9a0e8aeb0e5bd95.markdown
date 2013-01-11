---
comments: true
date: 2007-01-19 11:18:00
layout: post
slug: jabber%e5%ad%a6%e4%b9%a0%e8%ae%b0%e5%bd%95
title: jabber学习记录
wordpress_id: 1116255
categories:
- 未分类
---

客户端  
是什么  
Jabber是一个开源的即时通讯平台，它使用一组基于XML的协议来实现即时通讯功能。它有点类似于Email。处于不同的Email提供商下的email使用者，可以互相进行通信，而Jabber就是让即时通讯也能实现互联互通的一个平台。




如何工作  
现在我们假设有甲和乙两个人，其中甲使用的是称为A的Jabber平台，而乙使用B平台。这时候甲会有一个Jabber帐户:甲@A，而乙则是乙@B.  
甲发一条信息到乙@B，这条信息首先会到A，然后A发送给B，接着B再发送到乙。  
也就是说，信息发送并非是以点对点形式发送的。而是Client->Server->Server->Client这样的形式。




如何使用  
首先我们必须选择一个Jabber客户端软件。  
Jabber客户端软件分为两种，一种是开放性的，一种是唯一性的。前者可以自己设定一个Jabber服务，例如gaim，而后者是针对某个Jabber服务设计的软件，不能自己设定Jabber服务，例如Google Talk，就是针对Google的Jabber服务所设计的。  
然后我们还要选择一个Jabber服务。  
选择并注册一个Jabber服务后，你将会得到帐户信息以及帐户所在的服务器的信息。  
例如Google Talk，如果你注册一个ID为wolfchen,那么你的Jabber帐户就是[wolfchen@gmail.com](mailto:wolfchen@gmail.com)，Jabber服务器就是talk.google.com:5222  
这时候我们可以打开Jabber客户端软件，把帐户和服务器信息都设好。这个有点类似于我们选择一个邮件客户端软件，然后把POP3的帐户信息和服务器信息设好。  
和非Jabber的IM服务通讯  
拿MSN Messenger(以下简称MSN)来说吧。如果我是一个Jabber用户，但是又想和MSN的用户通讯，该怎么办呢？  
由于MSN不是基于Jabber的，所以Jabber服务器不能和MSN的服务器直接通讯。因此，我们必须找到一个中转站以及一个进入MSN服务器的口令。  
很多Jabber服务，提供着针对MSN的中转站。我们可以直接使用那些中转站，而不一定使用他们的Jabber服务。  
然后，还需要一个进入MSN的服务器的口令。由于我们不是盖茨先生，所以要进入MSN的服务器，只能注册一个MSN帐户了。  
整个过程是这样的：  
Jabber用户->Jabber服务器1->Jabber服务器2->Jabber服务器2上的MSN中转站->通过Jabber用户提供的MSN帐户和密码以普通用户登陆，并发送信息给MSN用户。  
中转站就相当于一个MSN客户端，而我们不直接使用这个客户端，中转站这个客户端获得信息后，返回到我们的Jabber客户端或发送到其他的MSN用户。  
需要注意的是，中转站会保留你的MSN登陆信息在他们的服务器里，所以如果你并不是很信任一个中转站的时候，请不要使用它  
发送Jabber消息 




要发送一个Jabber即时消息，你必须初始化Muse Jabber API。通过创建一个JabberContext 类的实例来完成，接下来使用内容作为一个参数到Jabber session factory类的createSession()方法：   
1 //初始化Jabber context   
2 JabberContext jabberContext = new JabberContext("user", "pass", "localhost");   
4 //创建一个Jabber session factory的实例   
5 Jabber jabber = new Jabber();   
6 //创建新会话   
7 JabberSession jabberSession = jabber.createSession(jabberContext);   
上面的例子在第2行显示了一个新context的创建。JabberContext存储了指定的用户相关信息（用户名、密码、用户地址）以及稍后要使用context建立session时包含的一个唯一的会话标识。为了阐述意图，我直接使用了username、password和server。 




在第5行，一个Jabber session factory被创建，我们在第7行使用它创建了一个新的JabberSession，Muse进入由Jabber服务器提供的服务的主要接口。服务器的主要服务是：   
•连接服务： 从Jabber服务器连接和断开   
•用户服务： 针对用户鉴定及注册   
•现场服务： 接收从其它用户/服务来的及你自己广播的现场信息   
•登记表服务： 密友列表或地址薄   
•聊天服务： 发送多种类型的消息--组聊、私聊、标题等等   
•服务器服务： 获得与由这个Jabber服务器提供的服务相关的信息   
•客户服务： 获得关于其他用户的信息，比如用户登录的最后时间 




现在我们已经有了一个已经初始化的Jabber session，我们能够用它在我们才创建的JabberSession对象中使用connect()方法去连接到Jabber 服务器： 




8 //连接到服务器   
9 jabberSession.connect("localhost", 5222);   
要连接到一个Jabber服务器，我们指定地址及在指定服务器上的机器端口号。标准来说，缺省Jabber端口是5222。   
现在JabberSession已经连接到服务器，我们能够在我们的服务中用login()方法登录：   
10 //登录到Jabber服务器   
11 jabberSession.getUserService().login();   
在第11行，我们使用JabberSession获得一个到UserService的引用，接下来在我们服务中调用login()方法。注意方法自身不指定任何用户信息。当在上面第7行JabberSession被创建时login()从与JabberSession相关的JabberContext中获得这些信息。   
现在我们已经成功登录到Jabber服务器，我们能够开始发送和接收消息。下面代码段显示了如何构造一个简单的标题风格的消息：   
12 //构造测试消息   
13 JabberChatMessage msg = new   
14 JabberChatMessage(JabberChatMessage.TYPE_HEADLINE);   
15 msg.setSubject("Hello world");   
16 msg.setBody("Hello world");   
17 msg.setTo("[user2@localhost](mailto:user2@localhost)");   
在第13行，我们创建了一个JabberChatMessage实例。单个参数指定了我们要求的消息类型：TYPE_HEADLINE。JabberChatMessage类的名字容易引起误解，实际上，它可以用来包含在Jabber协议中定义的消息的任何四种类型--普通、聊天、标题及错误。在第15行，setSubject()和setBody()分别指定主题和内容。最后，setTo()在第17行设置消息接收器的JID。 




在封装下，JabberChatMessage将所有这些信息转换成一个内在的DOM（文档对象模型）树，这样当我们已经准备好发送消息给Jabber服务器时能够轻松产生XML。 




最后步骤：用sendMessage()方法发送消息：   
18 //发送消息   
19 jabberSession.sendMessage(msg); 




在封装下 




依据上面例子所得的事实，Muse API有效地隐藏了与连接管理和XML语法分析有关的所有细节，因此允许你集中精力在手边的任务上：创建一个消息服务。然而，理解一些基础协议交换证明是有用的。让我们关注一下当我们连接到服务器时发生的XML交换，登录，并象上面代码描述的一样发送消息。在下面的XML交换中，被客户接收的消息（我们例子代码）带了RECV前缀，送到服务器的消息带有SEND前缀： 




SEND: <?xml version="1.0" encoding="UTF-8" ?>   
<stream:stream to="localhost"   
xmlns="jabber:client"   
xmlns:stream="[http://etherx.jabber.org/streams](http://etherx.jabber.org/streams)">   
RECV: <stream:stream from="localhost" id="3D160545">




所有的Jabber交换都发生在一个XML流的上下文。在我们客户与Jabber服务器之间连接的生命期间，两个完全的XML文档在一个时间传输一个片段。上面显示的最初交换允许客户开始发送XML流文档到服务器并且服务器开始发送XML流文档到客户。 




下面，一个要求鉴定的信息被发送给服务器：   
SEND: <iq xmlns="jabber:client" type='get' id='id_10028'>   
<query xmlns="jabber:iq:auth">   
<username>user</username>   
</query>   
</iq>   
RECV: <iq xmlns="jabber:client" type='result' id='id_10028'>   
<query xmlns="jabber:iq:auth">   
<username>user</username>   
<password />   
<digest/>   
<sequence>482</sequence>   
<token>3D15E63A</token>   
<resource />   
</query>   
</iq>   
在用户鉴定程序前述那段显示了第一次交换。开始，客户向服务器询问哪种鉴定方式对于给定用户是有效的。服务器段用下面的鉴定方法回答： 




•Plain 文本：<password />标签   
•零知识鉴定：使用<sequence>和<token>标签   
•<digest/>：象plain文本，但密码是SHA-1 (安全无序运算法则)-用用户的plain文本密码进行编码   
我们实际上使用<digest/>，象下面代码段描述的一样：   
SEND: <iq xmlns="jabber:client" type='set' id='id_10030'>   
<query xmlns="jabber:iq:auth">   
<username>user</username>   
<hash>425c73373237061edcc5f23ba239c6cc69556f5c</hash>   
<resource>Home</resource>   
</query>   
</iq>   
RECV: <iq xmlns="jabber:client" type='result' id='id_10030'></iq>   
在这一点，用户连接到服务器并能够开始发送和接收消息：   
SEND: <message xmlns="jabber:client" type='headline'   
id='id_10032' [to='user2@localhost'](mailto:to='user2@localhost')>   
<thread xmlns="jabber:client">id_10033</thread>   
<subject xmlns="jabber:client">Hello world</subject>   
<body xmlns="jabber:client">Hello world</body>   
</message>




上面片段显示了我们发送给接收者[user2@localhost](mailto:user2@localhost)的测试标题消息。 




你可能已经注意到在前述协议段有一个重复出现元素：ID标签。因为session能够由多个异步会话组成，ID标签匹配查询并响应。 




接收消息 




自从这篇文章的主要方向是向你显示如何开发一个报警系统，我就很少注意消息接收。然而，为了完整性的利益，我应该讨论在Muse API中处理接收消息的一些有用特性。另外，因为报警服务是被登录到Jabber服务器的用户执行的，你能够适当地期望一些消息接收者试图与发送者通信。 




Muse使用一个listener附在session上以接收进入消息的提示。下面代码显示了如何创建一个listener并将其附到session----listener实际上附加到连接上，但自从我们的接口是在session级别并且JabberSession类有一个便利的方法添加一个listener，我们就将其附加到session： 




1 jabberSession.addMessageListener(   
2 new JabberMessageListener() {   
3 public void messageReceived(JabberMessageEvent event) {   
4 if (event.getMessageType() == JabberCode.MSG_CHAT ) {   
5 JabberChatMessage msg =   
6 (JabberChatMessage)event.getMessage();   
7 JabberChatMessage reply = new   
8 JabberChatMessage(JabberChatMessage.TYPE_HEADLINE);   
9 reply.setTo(msg.getFrom());   
10 reply.setSubject("Re: "+msg.getSubject());   
11 reply.setBody(   
12 "I'm just a sender: please send messages to someone else");   
13   
14 //发送消息   
15 jabberSession.sendMessage(reply);   
16 }   
17 }   
18 }   
19 );   
客户能够添加几个消息listeners，每次一个消息被接收它们都要被调用。典型的，你为要处理的每一个类型的消息添加一个listener。另一个选择：为同一种消息类型但在那些消息中的每一种不同的行为添加多个listeners。然而另一种选择将添加一个要处理所有消息类型的listener。选择依赖于你想完成什么。 




在上面的例子中，一个单独的listener被附加。Listener必须是实现JabberMessageListener接口的类的一个实例。这个接口要求一个要被执行的单个方法： void messageReceived(JabberMessageEvent)。当一个消息从服务器被接收时这个方法将被调用。在例子代码中，messageReceived()方法首先检查在第4行接收的消息类型。自从Jabber用户代理使用了MSG_CHAT类型发送消息，就只对这种类型消息才感兴趣。在第5行，Jabber消息从JabberMessageEvent 提取。在第9行通过设置收件人接收消息被用来构成一个给消息发送者的回复并且在第10行拷贝接收消息的主题。一个缺省错误文本被作为消息体在第11行被设置，并且，在第15行，消息使用前面例子同样的方法利用JabberSession对象的sendMessage()方法传输。  
XMPP, JEP/XEP协议研究  
XMPP(可扩展消息处理现场协议): 是基于可扩展标记语言（XML）的协议，它用于即时消息（IM）以及在线现场探测。它在促进服务器之间的准即时操作。这个协议可能最终允许因特网用户向因特网上的其他任何人发送即时消息，即使其操作系统和浏览器不同。XMPP的技术来自于Jabber，其实它是 Jabber的核心协定，所以XMPP有时被误称为Jabber协议。Jabber是一个基于XMPP协议的IM应用，除Jabber之外，XMPP还支持很多应用, 最终有望使用鉴定、访问控制、高级隐私、逐跳加密、端端加密以及与其它协议的相容等应用来支持IM  
XMPP中定义了三种实体，客户端，服务器，网关。通信能够在这三者的任意两个之间双向发生  
服务器：管理连接其他实体的会话;实体间路由; 存储客户端数据（如：基于XMPP即时消息与出席应用的用户的联系列表）  
客户端：基本的网络实体为客户端，多数客户端通过[TCP]连接直接连到服务器，客户端与服务器的推荐连接端口为5222。  
网关：网关承担着与异构即时通信系统的互联互通  
实体标识叫JID，一个有效JID包含一套有序元素：域标识符，结点标识符，资源标识符  
XMPP传输的是与即时通讯相关的指令。在以前这些命令要么用2进制的形式发送（比如QQ），要么用纯文本指令加空格加参数加换行苻的方式发送（比如MSN）。而XMPP传输的即时通讯指令的逻辑与以往相仿，只是协议的形式变成了XML格式的纯文本。这不但使得解析容易了，人也容易阅读了，方便了开发和查错。而XMPP的核心部分就是一个在网络上分片断发送XML的流协议。这个流协议是XMPP的即时通讯指令的传递基础，也是一个非常重要的可以被进一步利用的网络基础协议。所以可以说，XMPP用TCP传的是XML流。   
举个例子看看所谓的XML流是什么样子的？   
客户端：<?xml version='1.0'?>   
<stream:stream   
to='example.com'   
xmlns='jabber:client'   
xmlns:stream='http://etherx.jabber.org/streams'   
version='1.0'>   
服务器：<?xml version='1.0'?>   
<stream:stream   
from='example.com'   
id='someid'   
xmlns='jabber:client'   
xmlns:stream='http://etherx.jabber.org/streams'   
version='1.0'>   
...其他通信...   
客户端：<message [from='juliet@example.com'](mailto:from='juliet@example.com')   
[to='romeo@example.net'](mailto:to='romeo@example.net')   
xml:lang='en'>   
客户端： <body>Art thou not Romeo, and a Montague?</body>   
客户端：</message>   
服务器：<message [from='romeo@example.net'](mailto:from='romeo@example.net')   
[to='juliet@example.com'](mailto:to='juliet@example.com')   
xml:lang='en'>   
服务器：<body>Neither, fair saint, if either thee dislike.</body>   
服务器：</message>   
客户端：</stream:stream>   
服务器：</stream:stream>   
以文档的观点来看，客户端或服务器发送的所有XML文本连缀在一起，从<stream>到</stream>构成了一个完整的XML文档。其中的stream标签就是所谓的XML Stream。在<stream>与</stream>中间的那些<message>...</message>这样的XML元素就是所谓的XML Stanza（XML节）。XMPP核心协议通信的基本模式就是先建立一个stream，然后协商一堆安全之类的东西，中间通信过程就是客户端发送XML Stanza，一个接一个的。服务器根据客户端发送的信息以及程序的逻辑，发送XML Stanza给客户端。但是这个过程并不是一问一答的，任何时候都有可能从一个方发信给另外一方。通信的最后阶段是</stream>关闭流，关闭TCP/IP连接。  
XML节  
XML节通过XML流来发送，XMPP定义了三种顶级XML节  
<iq />  
<message />  
<presence />  
XMPP给这三种节定义了五种通用属性  
to  
from  
id  
type  
xml:lang  
to属性指定接收节的JID。  
from属性指定发送者的JID。  
id属性是可选的。并且，在接收应用（通常是一个服务器）中是唯一的。注意：流ID可能是严格安全的，并且因此必须是即不能预测也不能重复的  
type属性指定目的或消息上下文，出席或IQ节的详细信息。iq节的type属性有：Error，Get，Result，Set; presence节的type属性有：Available，Subscribe，Subscribed，Unsubscribe， Unsubscribed，Unavailable，Probe，Error，Invisible; message节的type属性有：Chat，Error，GroupChat，Headline，Normal  
xml:lang属性值指定任意可读XML字符数据的缺省语言  
  
<message />节定义了消息语义，<message />节可被看作"推"机制，一个实体推信息给其它实体，与EMAIL系统中发生的通信类似。所有消息节应该拥有'to'属性，指定有意的消息接收者；根据接收到那样的一个节，服务器应该路由或传送它到有意的接收者。  
  
<presence />节定义了出席语义，<presence />节可被看作基本广播或"出版-订阅"机制，多实体收到他们已订阅（在这种情况下，网络可利用信息）实体的信息。总的来说，出版实体应该发送一个不带'to'属性的出席节，在这种情况下，与此实体相连的服务器应该广播给所有订阅实体。然而，一个出版实体也可能发送一个带有'to'属性的出席节，此种情况下，服务器应该路由或传送节到有意的接收者。  
  
<iq />节定义了请求语义，<iq />节可被看作一个请求－响应机制，与[HTTP]在某些方面相似。IQ语义让一个实体向其它实体请求或接收其它实体的响应成为可能。请求与响应的数据内容由IQ无素的直接子元素的命名空间声明定义，并且，交互由请求实体通过使用'id'属性来跟踪。因此，IQ交互遵从结构化数据交换的一个通用模式，此交换例如得到/结果或设置/结果（虽然如果合适的话，对一个请求的响应可能会以错误返回）。




JEP/XEP:在Jabber协议中叫Jabber Enhancement Proposals（JEP），后改成XMPP Extension Protocols（XEP）。顾名思义，这也就是Jabber或者XMPP的扩展具体实现通信协议，比如XMPP Jabber practice 即时通讯开发实践上介绍的JEP定义了一个桥牌游戏的协议；Jabbe 的"正在键入消息……"的实现方法；Jabber 的隐身功能等等详细具体实现协议




已有开源代码介绍：  
Wildfire（java）, ejabberd, jabberd(c++) djabberd等开源IM服务器  
1. Wildfire 3.0  
授权: GPL or 商用  
操作系统平台：所有(使用Java开发)  
XMPP Jabber 协议实现情况：98%  
Tim 评价：  
安装和使用非常简单，安装后进入Web界面进行2~3分钟的配置所有的东西都设好了。使用Java语言开发，在目前Java开发人员到处普及的情况下进行维护和扩展的成本非常低。在我的测试中加上 Connection Manager 的情况下单台服务器可支持 30 万并发用户。缺点是目前还不支持cluster。如果企业内部部署IM使用 Wildfire + Spark 是最佳的组合。  
见：[http://hi.baidu.com/jabber/blog/category/Wildfire](http://hi.baidu.com/jabber/blog/category/Wildfire)




2. ejabberd  
授权: GPL  
操作系统平台：Linux, Windows, MacOS X 等  
XMPP Jabber 协议实现情况：91%  
Tim 评价：  
Ejabberd目前是可扩展性最好的一种Jabber/XMPP服务器,支持分布多个服务器，并且具有容错处理，单台服务器失效不影响整个cluster运作。  
顾虑就是它采用一种大家都没听过的语言Erlang开发，所以很多人可能会象我一样因为这个原因放弃了它。




3. Jabberd 2.x  
授权: GPL  
操作系统平台：主要是 Linux, (Windows 也支持，但不知道性能怎样)  
XMPP Jabber 协议实现情况：76%  
Tim 评价：  
自从jabber.org改用ejabberd之后，Jabberd一直都在走下坡路。扩展性比不上ejabberd, 易用性比不上 Wildfire，唯一的优势是使用C开发，如果你坚持要用C开发，那么还是选择jabberd吧。




4. Jabberd 1.x  
授权: GPL  
操作系统平台：主要是 Linux, (Windows 也支持，但不知道性能怎样)  
XMPP Jabber 协议实现情况：45%  
Tim 评价：  
在几年前 jabberd 就是 Jabber 的代名词，至今很多 Jabber 文档仍然介绍的是 Jabber 1.4，  
在我以前写的《Jabber 服务器占有率比较》中仍然排名第一。但是它很多新的规范都不支持，相信大部分用户都将转向新的服务器。  
见：[http://hi.baidu.com/jabber/blog/item/7d25bb199f31a44542a9ad02.html](http://hi.baidu.com/jabber/blog/item/7d25bb199f31a44542a9ad02.html)




5. 后起之秀 DJabberd  
授权: open source  
操作系统平台：主要是 Linux, (Perl写的，其他平台应该也支持)  
XMPP Jabber 协议实现情况：N/A  
Tim 评价：  
djabberd 使用 epoll 技术，理论上单台服务器可以支持更多用户。Djabberd目前主要应用在LiveJournal上，大部分XMPP协议都支持，稳定性也不用置疑。但是因为推出时间尚短，很多细节功能可能需要时间慢慢完善。  
djabberd介绍见：[http://hi.baidu.com/jabber/blog/category/Djabberd](http://hi.baidu.com/jabber/blog/category/Djabberd)




开源客户端就更加多了，只要支持jabber协议即可  

