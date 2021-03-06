---
title: http-TCP
date: 2020-05-14 12:47:30
tags:
---

#### osi

###### 开放式系统互联通信参考模型（open system intercnnection reference model）;

##### 应用层 （Application Layer）

###### 提供为应用软件而设的接口，以设置与另一应用软件之间的通信。例如：http,https,ftp,telnet,ssh,smtp,pop3等

##### 表示层 (Presentation Layer)

###### 把数据转换为能与接收者的系统格式兼容并适合传输的格式

##### 会话层 （Session Layer）

###### 负责在数据传输中设置和维护计算机网络中两台计算机之间的通信连接

##### 传输层 （Transport Layer）

###### 把传输表头（th）加至数据以形成数据包。传输表头包含了所使用的协议等发送信息。例如TCP、UDP等。

##### 网络层 （Network Layer）

###### 数据链路层（Data Link Layer） 负责网络寻址，错误侦测和改错。当表头和表尾被加至数据包时，会形成帧。数据链表头（DLH）是包含了物理地址和错误帧测以及改错的方法。数据链表尾（DLT）是一串指示数据包末端的字符串。例如：以太网，无线局域网（wifi）和通用分组无线服务GPRS 等；

##### 物理层 （Physical Layer）

###### 在局部局域网上传送数据帧 （data frame）,它负责管理计算机通信设备和网络媒体之间的互通。包括了针脚、电压、线缆规范、集线器、中继器、网卡、主机适配器等；

#### TCP/IP四层模型

##### 应用层

应用层是大多数普通与网络相关的程序为了通过网络与其他程序通信所使用的层。这个层的处理过程是应用特有的；数据从网络相关的程序以这种应用内部使用的格式进行传送，然后被编码成标准协议的格式。每一个应用层协议一般都会使用到传输层协议TCP和UDP协议之一：

运行在TCP协议上的协议：

下面这些协议相当于不同的管家（各自负责各自的职责，可以这样理解）

1. HTTP（80端口），主要用于普通浏览。
2. HTTPS（443端口）,HTTP协议的安全版本。
3. FTP（20和21端口），由名知义，用于文件传输。
4. POP3（110端口），收邮件用。
5. SMTP（25端口），用来发送电子邮件。
6. SSH（22端口），用于加密安全登陆用。

运行在UDP协议上的协议：

1. DHCP（67端口，动态主机配置协议），动态配置IP地址。

其他：

1. DNS（Domain Name Service，域名服务），用于完成地址查找，邮件转发等工作（运行在TCP和UDP协议上）。
2. SNMP（Simple Network Management Protocol，简单网络管理协议），用于网络信息的收集和网络管理。
3. ARP（Address Resolution Protocol，地址解析协议），用于动态解析以太网硬件的地址。

##### 传输层

解决诸如端到端可靠性（数据是否已经到达目的地？）和保证数据按照正确的顺序到达这样的问题。TCP、UDP都是传输层协议。

##### 网络层

解决在一个单一网络上传输数据包的问题。IP协议是网络层协议。

##### 数据链路层

它是数据包从一个设备的网络层传输到另外一个设备的网络层的方法。这个过程能够在网卡的软件驱动程序中控制或者专用芯片中控制。这将完成如添加报头准备发送、通过实体介质实际发送这样一些数据链路功能。另一端，链路层将完成数据帧接收、去除报头并且将接收到的包传到网络层。

#### 通俗解释

**应用层协议 http协议定义了包的结构，定义了报文的格式（写信的格式），     tcp 传输层协议（管家与管家通信说我要寄信了） 进行握手，连接通信三步，断开通信 四步    网络层协议（邮差），确定ip地址，方便传输 数据链路层（邮筒）底层协议 驱动程序**

<!--more-->

## 关于TCP协议

TCP（Transmission Control Protocol， 传输控制协议）是一种面向连接的、**可靠的**、基于字节流的**传输层**通信协议。与之对应的是UDP（User Datagram Protocol ，用户数据报协议），是**不可靠**的**传输层**协议。

## 三步握手

1. 客户端发送 SYN报文给服务器端，序列号是n，进入 SYN_SEND 状态。
2. 服务器端收到 SYN 报文，回应一个ACK（序列号是n+1）同时发一个 SYN （序列号是m），进入 SYN_RECV 状态。
3. 客户端收到服务器端的 SYN 报文，回应一个 ACK(序列号是m+1）报文，进入 Established 状态

![](../img/http/tcp-request.png)

首先由Client发出请求连接即 SYN=1 ACK=0  (请看头字段的介绍), TCP规定SYN=1时不能携带数据，但要消耗一个序号,因此声明自己的序号是 seq=x

然后 Server 进行回复确认，即 SYN=1 ACK=1 seq=y, ack=x+1,

再然后 Client 再进行一次确认，但不用SYN 了，这时即为 ACK=1, seq=x+1, ack=y+1.

然后连接建立，为什么要进行三次握手呢（两次确认）
————————————————
![](../img/http/request01.png)

# 为什么 TCP 需要握手这个操作

在解答为什么 TCP 需要三次握手， 而不是两次之前， 首先需要回答的问题是:

- 为什么需要握手这个操作， 能不能不握手？

对比一下 UDP 的通信流程和 TCP 的通信流程， 可以发现， 在 UDP 协议中， 是没有握手这个操作的

这里就引出了 TCP 与 UDP 的一个基本区别， TCP 是可靠通信协议， 而 UDP 是不可靠通信协议。

- TCP 的可靠性含义： 接收方收到的数据是完整， 有序， 无差错的。
- UDP 不可靠性含义： 接收方接收到的数据可能存在部分丢失， 顺序也不一定能保证。

TCP 协议为了实现可靠传输， 通信双方需要判断自己已经发送的数据包是否都被接收方收到， 如果没收到， 就需要重发。 为了实现这个需求， 很自然地就会引出序号（sequence number） 和 确认号（acknowledgement number） 的使用。

发送方在发送数据包（假设大小为 10 byte）时， 同时送上一个序号( 假设为 500)，那么接收方收到这个数据包以后， 就可以回复一个确认号（510 = 500 + 10） 告诉发送方 “我已经收到了你的数据包， 你可以发送下一个数据包， 序号从 510 开始” 。

这样发送方就可以知道哪些数据被接收到，哪些数据没被接收到， 需要重发。

为了实现可靠传输，发送方和接收方始终需要同步( SYNchronize )序号。 需要注意的是， 序号并不是从 0 开始的， 而是由发送方随机选择的初始序列号 ( Initial Sequence Number, ISN )开始 。 由于 TCP 是一个双向通信协议， 通信双方都有能力发送信息， 并接收响应。 因此， 通信双方都需要随机产生一个初始的序列号， 并且把这个起始值告诉对方。
![](../img/http/tcp.png)

## 四步挥手

1. 客户端发送一个 FIN ，告诉服务器想关闭连接。
2. 服务器收到这个 FIN ，发回一个 ACK。
3. 服务器通知应用程序关闭网络连接，应用程序关闭后通知服务器。服务器发送一个 FIN 给客户端 。
4. 客户端发回 ACK 报文确认

## 为什么挥手要四步

这是因为服务端的 LISTEN 状态下的 SOCKET 当收到客户端建立连接请求的SYN 报文后，它可以把 ACK 和 SYN （ ACK 起应答作用，而 SYN 起同步作用）放在一个报文里来发送。但关闭连接时，当服务器收到客户端的 FIN 报文通知时，服务器只能发一个回应报文ACK：“哦，我知道了”，然后通知应用程序。应用程序完成全部数据发送并确定可以终止了，服务器才能发送FIN告诉客户端可以真正断开连接了。所以这一步ACK报文和FIN报文需要分开发送，因此多了一个步骤。

## TCP报文格式

![](../img/http/report.png)

- ACK ： TCP协议规定，只有ACK=1时有效，也规定连接建立后所有发送的报文的ACK必须为1。
- SYN(SYNchronization) ： 在连接建立时用来同步序号。当SYN=1而ACK=0时，表明这是一个连接请求报文。对方若同意建立连接，则应在响应报文中使SYN=1和ACK=1. 因此, SYN置1就表示这是一个连接请求或连接接受报文。
- FIN （finis）即完，终结的意思， 用来释放一个连接。当 FIN = 1 时，表明此报文段的发送方的数据已经发送完毕，并要求释放连接。

当客户A 没有东西要发送时就要释放 A 这边的连接，A会发送一个报文（没有数据），其中 FIN 设置为1,  服务器B收到后会给应用程序一个信，这时A那边的连接已经关闭，即A不再发送信息（但仍可接收信息）。  A收到B的确认后进入等待状态，等待B请求释放连接， B数据发送完成后就向A请求连接释放，也是用FIN=1 表示， 并且用 ack = u+1(如图）， A收到后回复一个确认信息，并进入 TIME_WAIT 状态， 等待 2MSL 时间。

为什么要等待呢？

为了这种情况： B向A发送 FIN = 1 的释放连接请求，但这个报文丢失了， A没有接到不会发送确认信息， B 超时会重传，这时A在 WAIT_TIME 还能够接收到这个请求，这时再回复一个确认就行了。（A收到 FIN = 1 的请求后 WAIT_TIME会重新记时）

————————————————
另外服务器B存在一个保活状态，即如果A突然故障死机了，那B那边的连接资源什么时候能释放呢？ 就是保活时间到了后，B会发送探测信息， 以决定是否释放连接

**【注意】中断连接端可以是Client端，也可以是Server端。**

假设Client端发起中断连接请求，也就是发送FIN报文。Server端接到FIN报文后，意思是说"我Client端没有数据要发给你了"，但是如果你还有数据没有发送完成，则不必急着关闭Socket，可以继续发送数据。所以你先发送ACK，"告诉Client端，你的请求我收到了，但是我还没准备好，请继续你等我的消息"。这个时候Client端就进入FIN_WAIT状态，继续等待Server端的FIN报文。当Server端确定数据已发送完成，则向Client端发送FIN报文，"告诉Client端，好了，我这边数据发完了，准备好关闭连接了"。Client端收到FIN报文后，"就知道可以关闭连接了，但是他还是不相信网络，怕Server端不知道要关闭，所以发送ACK后进入TIME_WAIT状态，如果Server端没有收到ACK则可以重传。“，Server端收到ACK后，"就知道可以断开连接了"。Client端等待了2MSL后依然没有收到回复，则证明Server端已正常关闭，那好，我Client端也可以关闭连接了。Ok，TCP连接就这样关闭了！

#### 补充

https://www.cnblogs.com/qdhxhz/p/8468913.html  http参考

 1.HTTP协议，即**超文本传输协议**(Hypertext transfer protocol)。是一种详细规定了**浏览器和万维网(WWW = World Wide Web)服务器**之间互相通信的规则，通过因特网传送万维网文档的数据传送协议。

   2.HTTP协议作为TCP/IP模型中应用层的协议也不例外。HTTP协议通常承载于TCP协议之上，有时也承载于TLS或SSL协议层之上，这个时候，就成了我们常说的HTTPS。如下图：

![img](https://images2018.cnblogs.com/blog/1090617/201802/1090617-20180225205151424-227704907.png)

  3.HTTP是一个**应用层协议**，由请求和响应构成，是一个标准的客户端服务器模型。HTTP是一个**无状态**的协议。

  4.HTTP默认的端口号为**80**，HTTPS的端口号为**443**。

  5.浏览网页是HTTP的主要应用，但是这并不代表HTTP就只能应用于网页的浏览。HTTP是一种协议，只要通信的双方都遵守这个协议，HTTP就能有用武之地。比如咱们常用的QQ，迅雷这些软件，都会使用HTTP协议(还包括其他的协议)。

 

##  二.HTTP特点             

  1、**简单快速**：客户向服务器请求服务时，只需传送**请求方法**和**路径**。由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。

  2、**灵活**：HTTP允许**传输任意类型的数据对象**。正在传输的类型由Content-Type加以标记。

  3、**HTTP 0.9和1.0使用非持续连接**：限制每次连接只处理一个请求，服务器处理完客户的请求，并收到客户的应答后，即断开连接。**HTTP 1.1使用持续连接**：不必为每个web对象创建一个新的连接，一个连接可以传送多个对象，采用这种方式可以节省传输时间。

  4、**无状态**：HTTP协议是**无状态，无连接协议**。**无状态是指协议对于事务处理没有记忆能力**。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。

1. 协议对于事务处理没有记忆能力【事物处理】【记忆能力】
2. 对同一个url请求没有上下文关系【上下文关系】
3. 每次的请求都是独立的，它的执行情况和结果与前面的请求和之后的请求是无直接关系的，它不会受前面的请求应答情况直接影响，也不会直接影响后面的请求应答情况【无直接联系】【受直接影响】
4. 服务器中没有保存客户端的状态，客户端必须每次带上自己的状态去请求服务器【状态
5. 每一个访问都是无连接，服务器挨个处理访问队列里的访问，处理完一个就关闭连接，这事儿就完了，然后处理下一个新的
6. 无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接

 5、**支持B/S及C/S模式。**

 

## 三.HTTP工作流程          

   一次HTTP操作称为一个事务，其工作过程可分为四步：

  1.首先客户机与服务器需要建立连接。只要单击某个超级链接，HTTP的工作开始。
  2.建立连接后，客户机发送一个请求给服务器，请求方式的格式为：统一资源标识符（URL）、协议版本号，后边是MIME信息包括请求修饰符、客户机信息和可能的内容。
  3.服务器接到请求后，给予相应的响应信息，其格式为一个状态行，包括信息的协议版本号、一个成功或错误的代码，后边是MIME信息包括服务器信息、实体信息和可能的内容。
 4.客户端接收服务器所返回的信息通过浏览器显示在用户的显示屏上，然后客户机与服务器断开连接。
 如果在以上过程中的某一步出现错误，那么产生错误的信息将返回到客户端，有显示屏输出。对于用户来说，这些过程是由HTTP自己完成的，用户只要用鼠标点击，等待信息显示就可以了。

## 四.HTTP之请求消息Request 

  客户端发送一个HTTP请求到服务器的请求消息包括以下格式：

  **请求行**、**请求头部**、**空行**和**请求数据**四个部分组成。

**(1)Get请求例子**

![img](https://images2018.cnblogs.com/blog/1090617/201802/1090617-20180225211250427-305880476.png)

##### 第一部分：请求行，用来说明请求类型,要访问的资源以及所使用的HTTP版本.

GET说明请求类型为GET,[/562f25980001b1b106000338.jpg]为要访问的资源，该行的最后一部分说明使用的是HTTP1.1版本。

##### 第二部分：请求头部，紧接着请求行（即第一行）之后的部分，用来说明服务器要使用的附加信息

从第二行起为请求头部，HOST将指出请求的目的地.User-Agent,服务器端和客户端脚本都能访问它,它是浏览器类型检测逻辑的重要基础.该信息由你的浏览器来定义,并且在每个请求中自动发送等等

##### 第三部分：空行，请求头部后面的空行是必须的

即使第四部分的请求数据为空，也必须有空行。

##### 第四部分：请求数据也叫主体，可以添加任意的其他数据。

这个例子的请求数据为空。

**POST请求例子**

 ![img](https://images2018.cnblogs.com/blog/1090617/201802/1090617-20180225211616653-1269363528.png)

第一部分：请求行，第一行明了是post请求，以及http1.1版本。
第二部分：请求头部，第二行至第六行。
第三部分：空行，第七行的空行。
第四部分：请求数据，第八行。

 

##  五.HTTP之响应消息Response

一般情况下，服务器接收并处理客户端发过来的请求后会返回一个HTTP的响应消息。

HTTP响应也由四个部分组成，分别是：**状态行**、**消息报头**、**空行**和**响应正文**。

 ![img](https://images2018.cnblogs.com/blog/1090617/201802/1090617-20180225211855634-541362260.png)

##### 第一部分：状态行，由HTTP协议版本号， 状态码， 状态消息 三部分组成。

第一行为状态行，（HTTP/1.1）表明HTTP版本为1.1版本，状态码为200，状态消息为（ok）

##### 第二部分：消息报头，用来说明客户端要使用的一些附加信息

第二行和第三行和第四行为消息报头，
Date:生成响应的日期和时间；Content-Type:指定了MIME类型的HTML(text/html),编码类型是ISO-8859-1

##### 第三部分：空行，消息报头后面的空行是必须的

##### 第四部分：响应正文，服务器返回给客户端的文本信息。

空行后面的html部分为响应正文。

 

##  6.HTTP之状态码          

状态代码有**三位数字**组成，第一个数字定义了响应的类别，共分五种类别:

1xx：**指示信息--表示请求已接收，继续处理**

2xx：**成功--表示请求已被成功接收、理解、接受**

3xx：**重定向--要完成请求必须进行更进一步的操作**

4xx：**客户端错误--请求有语法错误或请求无法实现**

5xx：**服务器端错误--服务器未能实现合法的请求**

常见状态码：

![img](https://images2018.cnblogs.com/blog/1090617/201802/1090617-20180225213621277-297070017.png)

 

## 7.HTTP请求方法          

根据HTTP标准，HTTP请求可以使用多种请求方法。
**HTTP1.0**定义了三种请求方法： **GET, POST** 和 **HEAD**方法。
**HTTP1.1**新增了五种请求方法：**OPTIONS, PUT, DELETE, TRACE 和 CONNECT** 方法。

![img](https://images2018.cnblogs.com/blog/1090617/201802/1090617-20180225213836463-1534510001.png)

 

## 8.HTTP工作原理           

​    HTTP协议定义Web客户端如何从Web服务器请求Web页面，以及服务器如何把Web页面传送给客户端。HTTP协议采用了**请求/响应模型**。客户端向服务器发送一个请求报文，请求报文包含请求的方法、URL、协议版本、请求头部和请求数据。服务器以一个状态行作为响应，响应的内容包括协议的版本、成功或者错误代码、服务器信息、响应头部和响应数据。

以下是 HTTP 请求/响应的步骤：

**1、客户端连接到Web服务器**

一个HTTP客户端，通常是浏览器，与Web服务器的HTTP端口（默认为80）建立一个TCP套接字连接。例如，[http://www.oakcms.cn。](http://www.oakcms.cn./)

**2、发送HTTP请求**

通过TCP套接字，客户端向Web服务器发送一个文本的请求报文，一个请求报文由请求行、请求头部、空行和请求数据4部分组成。

**3、服务器接受请求并返回HTTP响应**

Web服务器解析请求，定位请求资源。服务器将资源复本写到TCP套接字，由客户端读取。一个响应由状态行、响应头部、空行和响应数据4部分组成。

**4、释放连接[TCP连接](http://www.jianshu.com/p/ef892323e68f)**

若connection 模式为close，则服务器主动关闭[TCP连接](http://www.jianshu.com/p/ef892323e68f)，客户端被动关闭连接，释放[TCP连接](http://www.jianshu.com/p/ef892323e68f);若connection 模式为keepalive，则该连接会保持一段时间，在该时间内可以继续接收请求;

**5、客户端浏览器解析HTML内容**

客户端浏览器首先解析状态行，查看表明请求是否成功的状态代码。然后解析每一个响应头，响应头告知以下为若干字节的HTML文档和文档的字符集。客户端浏览器读取响应数据HTML，根据HTML的语法对其进行格式化，并在浏览器窗口中显示。

 

##  9.GET和POST的区别    

   1、GET提交的数据会放在URL之后，以?分割URL和传输数据，参数之间以&相连，如EditPosts.aspx?name=test1&id=123456. POST方法是把提交的数据放在HTTP包的Body中.

   2、GET提交的数据大小有限制（因为浏览器对URL的长度有限制），而POST方法提交的数据没有限制.

   3、GET方式需要使用Request.QueryString来取得变量的值，而POST方式通过Request.Form来获取变量的值。

   4、GET方式提交数据，会带来安全问题，比如一个登录页面，通过GET方式提交数据时，用户名和密码将出现在URL上，如果页面可以被缓存或者其他人可以访问这台机器，就可以从历史记录获得该用户的账号和密码.