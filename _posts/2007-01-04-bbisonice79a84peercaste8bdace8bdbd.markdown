---
comments: true
date: 2007-01-04 17:08:00
layout: post
slug: bbisonic%e7%9a%84peercast%e8%bd%ac%e8%bd%bd
title: bbisonic的Peercast转载
wordpress_id: 1108139
categories:
- 未分类
---

原文[http://blog.csdn.net/bbisonic/archive/2006/04/18/667974.aspx](http://blog.csdn.net/bbisonic/archive/2006/04/18/667974.aspx)




现在的P2P流媒体主要有两种架构：




1.基于树的架构。这是由流媒体的多播演化而来的，也就是播放同一频道的节点组成一棵树，提供广播的源节点为这棵树的根。每个节点可以为下层几个节点提供数据。但这种架构仍然会对上层的结点造成太大的负担，而且在节点动态加入和退出的情况下树不易维护。另外还存在传输延迟问题，所以树的高度不能太大。




2.基于图(MESH)的架构。通过邻居发现寻找相关的节点。这种架构可以实现完全非中心化。




Peercast采用的是基于图的架构。所有Peercast节点都在同一网络中，而且一个Peercast可以同时转播多个频道。由于Peercast集合了客户端和服务器功能，所以一个Peercast可以同时是广播者、转播者和收听者。




Peercast网络架构可分为三层。




第一层是YP。  
YP(yp.peercast.org)从广播者中收集频道信息，是整个网络的根。




第二层是广播者。  
广播者向YP发送频道信息，这样YP就能有一个完整的广播者的列表。




第三层是转播者  
转播者收听频道，每个广播者维护一份转播者的列表。




P2P流媒体的运行模式  
视频／音频输入＋编码器＋Peercast＋播放器




视／音频输入：这是频道的来源，可以是实时事件（电视），也可以是文件（MP3/WMV）  
编码器：用于将文件编码成更易于传输的流格式，可以是SHOUTcast DSP和windows media encoder等  
播放器：播放编码后传输的文件




Peercast：根据其完成的功能可分为下列几个模块：  
1.获取媒体数据：作为广播者读取编码器发送过来的流数据  
2.用户界面：以GUI和网页方式提供控制  
3.数据传送：在节点间传递控制信息和频道流具体数据  
4.节点选择：选择要进行传输的最佳节点  
5.缓冲管理：管理流缓冲以实现流媒体下载和播放  
6.HTTP服务器。将流数据用HTTP方式送往播放器




节点的加入与退出：




当一个Peercast节点第一次加入Peercast网络时（点击yp.peercast.org中特定频道的PLAY按钮），例如访问地址是peercast://pls/EF49346D72FD05F234D3DA2C33FF3A9C?ip=61.213.94.129:2010 。它会先与广播这个电台的IP（61.213.94.129:2010）建立连接。由于这个广播者是不变的，所以至少会有一个特定的连接。如果广播者是满负荷的，那么这个节点可以通过这个广播者同其他转播同一电台的节点建立连接。  
通常会建立8个连接。




  
在这点上是与Gnutella网络不同的，由于必定存在一个广播者，所以不必实现Gnutella中关于节点第一次加入网络的机制（GWebCache），而此时广播者相当于BT软件的一个原始种子。




与本节点转播同一频道的节点（也就是邻居）的信息保存在ChanHitList中，其中ChanHitList是一份ChanHit的链表，每个ChanHit保存一个相关结点的信息。Hit的意思是你想收听的频道的广播者或转播者。




拥塞控制：




当output队列超过50%时进入拥塞控制模式。系统丢弃一些incoming包，并根据包类型和跳树来给outgoing包区分优先值。当output队列降到25%时，系统会关闭拥塞控制模式。




转播时间更久的节点比刚开始转播的节点拥有更高的优先权，这体现在TTL上。刚开始转播的节点广播的包的TTL值为1，而这个TTL值每5分钟会递增1。这样转播时间超过35分钟的结点会有着最高的TTL值7。




节点间的通信：  
节点间的通信通过发送和接收控制信息包来实现。Peercast专用协议PCP规定了控制信息包的类型和格式。  
ServMgr负责分配、删除和使用servent对象，每个servent对象负责一个具体的连接，而其中包的发送、接收和解析工作由PCP Stream来实现。




传输者的选择：




在建立初始连接之后，节点需要选择一个最佳的节点来传输频道数据。  
选择的顺序依次如下：  
1.本地转播者  
2.邻居转播者  
3.本地广播者  
4.邻居转播者




比如说如果找到邻居转播者就不用继续往下找，然后从其中选取出最佳的节点作为传输对象，其他作为备用传输者。  
这个最佳可从以下三个方面来衡量：




  
如果这个传输者退出网络，那么必须重新按上次方法选择下一个传输者。  
由于备用传输者较多，所以当节点动态退出网络时不会造成太大的影响。




  
缓冲机制：




流（stream）：流在Peercast中是一个非常重要的概念。所谓流，就是字符串的集合。所以无论是包、视/音频数据都可以看做是一个流。




Peercast的缓冲机制是通过ChanPacketBuffer实现的，里面包含有多个ChanPacket，每个ChanPacket封装了实际的数据。  





///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////  
二、Peercast收听电台的源代码流程分析






原文：[http://blog.csdn.net/bbisonic/archive/2006/04/12/660876.aspx](http://blog.csdn.net/bbisonic/archive/2006/04/12/660876.aspx)




以收听JOKV-FM(TEST)为例，在YP上点击Play，则其URL地址为  
peercast://pls/25838B9F1EAE27079B793C9FBA0E4156?tip=222.148.187.176:7144




  
case WM_COPYDATA:  
{  
COPYDATASTRUCT *pc = (COPYDATASTRUCT *)lParam;  
LOG_DEBUG("URL request: %s",pc->lpData);  
if (pc->dwData == WM_PLAYCHANNEL)  
{  
ChanInfo info;  
servMgr->procConnectArgs((char *)pc->lpData,info);  
chanMgr->findAndPlayChannel(info,false);  
}  
//sys->callLocalURL((const char *)pc->lpData,servMgr->serverHost.port);  
}  
break;




// 解析连接参数，str表示相应的频道URL，例65051E037A7A2A3433090065051E037A?tip=211.132.83.9:7144  
// 从URL中解析频道的相关信息以初始化info




void ServMgr::procConnectArgs(char *str,ChanInfo &info)  
{  
char arg[512];  
char curr[256];




//使args等于？后面的字符串，即tip=211.132.83.9:7144  
char *args = strstr(str,"?");  
if (args)  
*args++=0;




info.initNameID(str);




if (args)  
{  
//nextCGIarg分解字符串，把"tip"保存到curr中，"211.132.83.9"保存到arg中  
while (args=nextCGIarg(args,curr,arg))  
{  
LOG_DEBUG("cmd: %s, arg: %s",curr,arg);




if (strcmp(curr,"sip")==0)  
// sip - add network connection to client with channel  
{  
Host h;  
h.fromStrName(arg,DEFAULT_PORT);  
if (addOutgoing(h,servMgr->networkID,true))  
LOG_NETWORK("Added connection: %s",arg);




}else if (strcmp(curr,"pip")==0)  
// pip - add private network connection to client with channel  
{  
Host h;  
h.fromStrName(arg,DEFAULT_PORT);  
if (addOutgoing(h,info.id,true))  
LOG_NETWORK("Added private connection: %s",arg);  
}else if (strcmp(curr,"ip")==0)  
// ip - add hit  
{  
Host h;  
h.fromStrName(arg,DEFAULT_PORT);  
ChanHit hit;  
hit.init();  
hit.host = h;   
hit.rhost[0] = h;  
hit.rhost[1].init();  
hit.chanID = info.id;  
hit.recv = true;




chanMgr->addHit(hit);  
}else if (strcmp(curr,"tip")==0)  
// tip - add tracker hit  
{  
Host h;  
h.fromStrName(arg,DEFAULT_PORT);  
chanMgr->addHit(h,info.id,true);  
}




  
}  
}  
}




根据info中的信息寻找和播放频道




  
void ChanMgr::findAndPlayChannel(ChanInfo &info, bool keep)  
{  
ChanFindInfo *cfi = new ChanFindInfo;  
cfi->info = info;  
cfi->keep = keep;  
cfi->func = findAndPlayChannelProc;





  
sys->startThread(cfi);  
}




启动线程




THREAD_PROC findAndPlayChannelProc(ThreadInfo *th)  
{  
ChanFindInfo *cfi = (ChanFindInfo *)th;




ChanInfo info;  
info = cfi->info;




  
Channel *ch = chanMgr->findChannelByNameID(info);




chanMgr->currFindAndPlayChannel = info.id;




if (!ch)  
ch = chanMgr->findAndRelay(info);




if (ch)  
{  
// check that a different channel hasn`t be selected already.  
if (chanMgr->currFindAndPlayChannel.isSame(ch->info.id))  
chanMgr->playChannel(ch->info);




if (cfi->keep)  
ch->stayConnected = cfi->keep;  
}




delete cfi;  
return 0;  
}




创建频道




// 寻找和转播相应频道  
Channel *ChanMgr::findAndRelay(ChanInfo &info)  
{  
char idStr[64];  
info.id.toStr(idStr);  
LOG_CHANNEL("Searching for: %s (%s)",idStr,info.name.cstr());  
peercastApp->notifyMessage(ServMgr::NT_PEERCAST,"Finding channel...");




  
Channel *c = NULL;




c = findChannelByNameID(info);




//如果当前没有转播该频道，则新创建一个频道  
if (!c)  
{  
c = chanMgr->createChannel(info,NULL);  
if (c)  
{  
c->setStatus(Channel::S_SEARCHING);   
c->startGet();  
}  
}




for(int i=0; i<600; i++) // search for 1 minute.  
{




c = findChannelByNameID(info);




if (!c)  
{  
peercastApp->notifyMessage(ServMgr::NT_PEERCAST,"Channel not found");  
return NULL;  
}




  
if (c->isPlaying() && (c->info.contentType!=ChanInfo::T_UNKNOWN))  
break;




sys->sleep(100);  
}




return c;  
}




创建频道  
  
Channel *ChanMgr::createChannel(ChanInfo &info, const char *mount)  
{  
lock.on();




Channel *nc=NULL;




nc = new Channel();




//将新建的频道加入频道列表  
nc->next = channel;  
channel = nc;




  
nc->info = info;  
nc->info.lastPlayStart = 0;  
nc->info.lastPlayEnd = 0;  
nc->info.status = ChanInfo::S_UNKNOWN;  
if (mount)  
nc->mount.set(mount);  
nc->setStatus(Channel::S_WAIT);  
nc->type = Channel::T_ALLOCATED;  
nc->info.createdTime = sys->getTime();




LOG_CHANNEL("New channel created");




lock.off();  
return nc;  





开始获取数据，即新创建一个Source并调用startStream进行实际传输  
void Channel::startGet()  
{  
srcType = SRC_PEERCAST;  
type = T_RELAY;  
info.srcProtocol = ChanInfo::SP_PCP;





  
sourceData = new PeercastSource();




startStream();  
}




启动传输线程  
void Channel::startStream()  
{  
thread.data = this;  
thread.func = stream;  
if (!sys->startThread(&thread))  
reset();  
}




进行实际的流传输，调用ChannelSource::stream函数  
THREAD_PROC Channel::stream(ThreadInfo *thread)  
{  
// thread->lock();




Channel *ch = (Channel *)thread->data;




while (thread->active && !peercastInst->isQuitting)  
{  
LOG_CHANNEL("Channel started");




  
ChanHitList *chl = chanMgr->findHitList(ch->info);  
if (!chl)  
chanMgr->addHitList(ch->info);




ch->sourceData->stream(ch);




LOG_CHANNEL("Channel stopped");




if (!ch->stayConnected)  
{  
break;  
}else  
{  
if (!ch->info.lastPlayEnd)  
ch->info.lastPlayEnd = sys->getTime();




unsigned int diff = (sys->getTime()-ch->info.lastPlayEnd) + 5;




LOG_DEBUG("Channel sleeping for %d seconds",diff);  
for(unsigned int i=0; i<diff; i++)  
{  
if (!thread->active || peercastInst->isQuitting)  
break;  
sys->sleep(1000);   
}  
}  
}




ch->endThread();




return 0;




}


}


///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////




三、用Peercast广播视频文件(WMV格式)






原文：[http://blog.csdn.net/bbisonic/archive/2006/04/12/660678.aspx](http://blog.csdn.net/bbisonic/archive/2006/04/12/660678.aspx)




1.安装Windows media encoder




2.新建会话中选择自定义会话




3.源来自选择文件，在文件名中点击浏览选择要广播的视频(WMV)




4.在输出中选择自编码器拉传递,端口号填8080




5.点击应用




6.在Peercast的Broadcast页面中URL填入[http://localhost:8080](http://localhost:8080/),其他项根据你的选择设置,然后点击Create Relay




7.在WIndows media encoder中菜单中选择控制->开始编码




8.若广播建立成功,在Relays页面中可以看见刚刚建立的频道,点击Play即可播放




///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////




四、Peercast各个版本的更新记录






原文：[http://blog.csdn.net/bbisonic/archive/2006/04/12/660660.aspx](http://blog.csdn.net/bbisonic/archive/2006/04/12/660660.aspx)




///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////




五、Peercast的日志实现






原文：[http://blog.csdn.net/bbisonic/archive/2006/04/06/653221.aspx](http://blog.csdn.net/bbisonic/archive/2006/04/06/653221.aspx)




这里以输出DEBUG信息为例子。




输出日志函数为LOG_DEBUG。具体使用方法为LOG_DEBUG("Play request: %s",pc->lpData);




void LOG_DEBUG(const char *fmt,...)  
{  
if (servMgr)  
{  
if ((servMgr->showLog & (1<<LogBuffer::T_DEBUG)) && (!servMgr->pauseLog))  
{  
va_list ap;  
va_start(ap, fmt);  
ADDLOG(fmt,ap,LogBuffer::T_DEBUG);  
va_end(ap);   
}  
}  
}




ADDLOG函数




void ADDLOG(const char *fmt,va_list ap,LogBuffer::TYPE type)  
{  
if(sys)  
{  
const int MAX_LINELEN = 1024;




char str[MAX_LINELEN+1];  
vsnprintf(str,MAX_LINELEN-1,fmt,ap);  
str[MAX_LINELEN-1]=0;




if (type != LogBuffer::T_NONE)  
sys->logBuf->write(str,type);




peercastApp->printLog(type,str);  
}  
}







void APICALL MyPeercastApp ::printLog(LogBuffer::TYPE t, const char *str)  
{  
ADDLOG(str,logID,true,NULL,t);  
if (logFile.isOpen())  
{  
logFile.writeLine(str);  
logFile.flush();  
}  
}




///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////




六、Sys.h源代码分析 




原文：[http://blog.csdn.net/bbisonic/archive/2006/04/06/652931.aspx](http://blog.csdn.net/bbisonic/archive/2006/04/06/652931.aspx) 





String类：完成字符串的一些定义和操作




Random类：可调用next方法返回随机数




Sys类：提供一些系统功能，如线程操作、返回随机数、返回时




WEvent类：




WLock类：对临界区操作的封装，用于线程同步




ThreadInfo类：线程信息







String类：完成字符串的一些定义和操作




Random类：可调用next方法返回随机数




Sys类：提供一些系统功能，如线程操作、返回随机数、返回时




WEvent类：




WLock类：对临界区操作的封装，用于线程同步




ThreadInfo类：线程信息




///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////




七、Peercast的命令行使用方式






原文：[http://blog.csdn.net/bbisonic/archive/2006/04/06/652749.aspx](http://blog.csdn.net/bbisonic/archive/2006/04/06/652749.aspx)




Peercast也可以从命令行启动，有如下几种参数选择




Peercast：正常方式启动




Peercast -inifile：启动，并设置配置文件参数




Peercast -kill：启动后立即关闭




Peercast -url：按照频道的URL地址启动Peercast并播放相应电台




Peercast -multi：以非互斥方式启动




具体实现如下：




int APIENTRY WinMain(HINSTANCE hInstance,  
HINSTANCE hPrevInstance,  
LPSTR lpCmdLine,  
int nCmdShow)  
{  
char tmpURL[8192];  
tmpURL[0]=0;  
char *chanURL=NULL; //频道地址




  
iniFileName.set(".\\peercast.ini");




// off by default now  
showGUI = false; //初始化时不打开图形窗口




//根据命令行参数进行处理  
if (strlen(lpCmdLine) > 0)  
{  
char *p;  
if ((p = strstr(lpCmdLine,"-inifile"))!=NULL)   
iniFileName.setFromString(p+8);




if (strstr(lpCmdLine,"-zen"))   
showGUI = false;




if (strstr(lpCmdLine,"-multi"))   
allowMulti = true;




if (strstr(lpCmdLine,"-kill"))   
killMe = true;




if ((p = strstr(lpCmdLine,"-url"))!=NULL)  
{  
p+=4;  
while (*p)  
{  
if (*p=='"')  
{  
p++;  
break;  
}   
if (*p != ' ')  
break;  
p++;  
}  
if (*p)  
strncpy(tmpURL,p,sizeof(tmpURL)-1);  
}  
}




// get current path  
{  
exePath = iniFileName;  
char *s = exePath.cstr();  
char *end = NULL;  
while (*s)  
{  
if (*s++ == '\\')  
end = s;  
}  
if (end)  
*end = 0;  
}




  
if (strnicmp(tmpURL,"peercast://",11)==0)  
{  
if (strnicmp(tmpURL+11,"pls/",4)==0)  
chanURL = tmpURL+11+4;  
else  
chanURL = tmpURL+11;  
showGUI = false;  
}




///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////  
八、wsocket.h源文件分析

  
原文：[http://blog.csdn.net/bbisonic/archive/2006/04/06/652642.aspx](http://blog.csdn.net/bbisonic/archive/2006/04/06/652642.aspx)  





WSAClientSocket继承自ClientSocket，完成对基本WinSock函数的封装。




ClientSocket只是提供一个接口，具体实现由其继承类WSAClientSocket(WINDOWS)和UClientSocket (UNIX)实现




这里先介绍一下Host类：




unsigned int ip; //主机IP




unsigned short port; //主机端口号  
unsigned int value;




下面介绍一下WSAClientSocket的具体实现




//初始化,每个Winsock应用都必须加载合适的WinSock DLL版本.加载库是通过调用WSAStartup函数实现的




void WSAClientSocket::init()  
{  
WORD wVersionRequested;  
WSADATA wsaData;  
int err;  
  
wVersionRequested = MAKEWORD( 2, 0 );  
err = WSAStartup( wVersionRequested, &wsaData );  
if ( err != 0 )  
throw SockException("Unable to init sockets");




}




//建立套接字，通过调用socket函数来实现




void WSAClientSocket::open(Host &rh)  
{  
sockNum = socket (AF_INET, SOCK_STREAM, IPPROTO_TCP);




if (sockNum == INVALID_SOCKET)  
throw SockException("Can`t open socket");




setBlocking(false);  
#ifdef DISABLE_NAGLE  
setNagle(false);  
#endif




host = rh;




memset(&remoteAddr,0,sizeof(remoteAddr));




remoteAddr.sin_family = AF_INET;  
remoteAddr.sin_port = htons(host.port);  
remoteAddr.sin_addr.S_un.S_addr = htonl(host.ip);




}




//服务器绑定。一旦为某种协议创建了套接字，就必须将套接字绑定到一个已知地址上。使用bind函数




void WSAClientSocket::bind(Host &h)  
{  
struct sockaddr_in localAddr;




if ((sockNum = socket (PF_INET, SOCK_STREAM, IPPROTO_TCP)) == -1)  
throw SockException("Can`t open socket");




setBlocking(false);  
setReuse(true);




memset(&localAddr,0,sizeof(localAddr));  
localAddr.sin_family = AF_INET;  
localAddr.sin_port = htons(h.port);  
localAddr.sin_addr.s_addr = INADDR_ANY;




if( ::bind (sockNum, (sockaddr *)&localAddr, sizeof(localAddr)) == -1)  
throw SockException("Can`t bind socket");




//接下来要做的，是将套接字置入监听模式。bind函数的作用只是将套接字和指定的地址关联在一起。指示套接字等待连接传入的API是listen




if (::listen(sockNum,SOMAXCONN))  
throw SockException("Can`t listen",WSAGetLastError());




host = h;  
}




//现在我们已做好了接受客户机连接的准备，通过ACCEPT函数来完成




ClientSocket *WSAClientSocket::accept()  
{




int fromSize = sizeof(sockaddr_in);  
sockaddr_in from;




int conSock = ::accept(sockNum,(sockaddr *)&from,&fromSize);




  
if (conSock == INVALID_SOCKET)  
return NULL;




  
WSAClientSocket *cs = new WSAClientSocket();  
cs->sockNum = conSock;




cs->host.port = from.sin_port;  
cs->host.ip = from.sin_addr.S_un.S_un_b.s_b1<<24 |  
from.sin_addr.S_un.S_un_b.s_b2<<16 |  
from.sin_addr.S_un.S_un_b.s_b3<<8 |  
from.sin_addr.S_un.S_un_b.s_b4;




  
cs->setBlocking(false);  
#ifdef DISABLE_NAGLE  
cs->setNagle(false);  
#endif




return cs;  
}




//关闭套接字




void WSAClientSocket::close()  
{  
if (sockNum)  
{  
shutdown(sockNum,SD_SEND);




setReadTimeout(2000);  
try  
{  
//char c;  
//while (readUpto(&c,1)!=0);  
//readUpto(&c,1);  
}catch(StreamException &) {}




if (closesocket(sockNum))  
LOG_ERROR("closesocket() error");




  
sockNum=0;  
}  
}




//客户端连接




void WSAClientSocket::connect()  
{  
if (::connect(sockNum,(struct sockaddr *)&remoteAddr,sizeof(remoteAddr)) == SOCKET_ERROR)  
checkTimeout(false,true);




}




//发送数据




void WSAClientSocket::write(const void *p, int l)  
{  
while (l)  
{  
int r = send(sockNum, (char *)p, l, 0);  
if (r == SOCKET_ERROR)  
{  
checkTimeout(false,true);   
}  
else if (r == 0)  
{  
throw SockException("Closed on write");  
}  
else  
if (r > 0)  
{  
stats.add(Stats::BYTESOUT,r);  
if (host.localIP())  
stats.add(Stats::LOCALBYTESOUT,r);




updateTotals(0,r);  
l -= r;  
p = (char *)p+r;  
}  
}  
}




//接收数据




int WSAClientSocket::read(void *p, int l)  
{  
int bytesRead=0;  
while (l)  
{  
int r = recv(sockNum, (char *)p, l, 0);  
if (r == SOCKET_ERROR)  
{  
// non-blocking sockets always fall through to here  
checkTimeout(true,false);




}else if (r == 0)  
{  
throw EOFException("Closed on read");




}else  
{  
stats.add(Stats::BYTESIN,r);  
if (host.localIP())  
stats.add(Stats::LOCALBYTESIN,r);  
updateTotals(r,0);  
bytesRead += r;  
l -= r;  
p = (char *)p+r;  
}  
}  
return bytesRead;  
}







String类：完成字符串的一些定义和操作




Random类：可调用next方法返回随机数




Sys类：提供一些系统功能，如线程操作、返回随机数、返回时




WEvent类：




WLock类：对临界区操作的封装，用于线程同步




ThreadInfo类：线程信息
