---
title: https
date: 2020-05-16 14:09:44
tags:
---

### http通信存在的问题 **HyperText Transfer Protocol，超文本传输协议**

- 容易被监听 
  - http通信都是明文，数据在客户端与服务器通信过程中，任何一点都可能被劫持。比如，发送了银行卡号和密码，hacker劫取到数据，就能看到卡号和密码，这是很危险的
- 被伪装 
  - http通信时，无法保证通行双方是合法的，通信方可能是伪装的。比如你请求www.taobao.com,你怎么知道返回的数据就是来自淘宝，中间人可能返回数据伪装成淘宝。
- 被篡改 
  - hacker中间篡改数据后，接收方并不知道数据已经被更改

#### http 明文通信的理解

明文传输是指在传输时没有加密，直接将用户数据发送到网络上。至于你传输的是文字、图片还是你自己加密的消息，http协议都不做加密处理。相对的是https，无论你交给它什么数据，即使是你的加密消息，https还是会再做一次加密。

对于实施中间人攻击的攻击者，使用http协议，攻击者可以分辨传输的是图片、文字还是被加密的信息；而https的中间人攻击只能拿到一串加密的数据，很难分辨传输的是什么内容。

所谓明文传输是说通讯本身不提供加密的功能。
你自己加密过的数据在这里也是当明文来看的，否则任何传输都不是明文传输了。就算不能传二进制数据我们也可以把二进制数据转化为字母数字来传输的。

### 共享密钥加密和公开密钥加密

后续内容的需要，这里插播一段共享密钥加密和公开密钥加密

- 共享密钥加密 
  - 共享密钥的加密密钥和解密密钥是相同的，所以又称为对称密钥
- 公开密钥加密 
  - 加密算法是公开的，密钥是保密的。公开密钥分为私有密钥和公有密钥，公有密钥是公开的，任何人(客户端)都可以获取，客户端使用公有密钥加密数据，服务端用私有密钥解密数据。
- 异同 
  - 共享密钥加密与公开密钥加密相比，加解密处理速度快，但公开密钥更适应互联网下使用

### https解决的问题

https很好的解决了http的三个缺点（被监听、被篡改、被伪装），https不是一种新的协议，它是http+SSL(TLS)的结合体，SSL是一种独立协议，所以其它协议比如smtp等也可以跟ssl结合。https改变了通信方式，它由以前的http—–>tcp，改为http——>SSL—–>tcp；https采用了共享密钥加密+公开密钥加密的方式

- 防监听 
  - 数据是加密的，所以监听得到的数据是密文，hacker看不懂。
- 防伪装 
  - 伪装分为客户端伪装和服务器伪装，通信双方携带证书，证书相当于身份证，有证书就认为合法，没有证书就认为非法，证书由第三方颁布，很难伪造
- 防篡改 
  - https对数据做了摘要，篡改数据会被感知到。hacker即使从中改了数据也白搭。

### https连接过程

**HTTPS（全称：HyperText Transfer Protocol over Secure Socket Layer），可以理解为HTTP+SSL/TLS， 即 HTTP 下加入 SSL 层，HTTPS 的安全基础是 SSL，因此加密的详细内容就需要 SSL，用于安全的 HTTP 数据传输。**

- 客户端发送请求到服务器端
- 服务器端返回证书和公开密钥，公开密钥作为证书的一部分而存在(此时公钥也在里面，其实这个就是为了安全的得到对方发公钥，CA使用 ca 自己的私钥进行加密的，所以客户端如果收到这个证书会用CA的公钥取解密，验证这个证书的真实性，证书的其他内容都可以被篡改，但是，证书的签名是ca用自己的私钥加散列处理的，没有人能够伪造得了，所以数据的可靠性提高了)
- 客户端验证证书和公开密钥的有效性，如果有效，则生成共享密钥并使用公开密钥加密发送到服务器端
- 服务器端使用私有密钥解密数据，并使用收到的共享密钥加密数据，发送到客户端
- 客户端使用共享密钥解密数据
- SSL加密建立………

#### 客户端认证的通信的过程

- 客户端需要认证的过程跟服务器端需要认证的过程基本相同，并且少了最开始的两步。这种情况都是证书存储在客户端，并且应用场景比较少，一般金融才使用，比如支付宝、银行客户端都需要安装证书

### 后续的问题

- - 怎样保证公开密钥的有效性 
    - 你也许会想到，怎么保证客户端收到的公开密钥是合法的，不是伪造的，证书很好的完成了这个任务。证书由权威的第三方机构颁发，并且对公开密钥做了签名。
  - https的缺点 
    - https保证了通信的安全，但带来了加密解密消耗计算机cpu资源的问题 ，不过，有专门的https加解密硬件服务器
  - 各大互联网公司，百度、淘宝、支付宝、知乎都使用https协议，为什么？ 
    - 支付宝涉及到金融，所以出于安全考虑采用https这个，可以理解，为什么百度、知乎等也采用这种方式？为了防止运营商劫持！http通信时，运营商在数据中插入各种广告，用户看到后，怒火发到互联网公司，其实这些坏事都是运营商(移动、联通、电信)干的,用了https，运营商就没法插播广告篡改数据了。

4.比较完整的过程：

**1. 客户端发起HTTPS请求**

　　这个没什么好说的，就是用户在浏览器里输入一个https网址，然后连接到server的443端口。

　　**2. 服务端的配置**

　　采用HTTPS协议的服务器必须要有一套数字证书，可以自己制作，也可以向组织申请。区别就是自己颁发的证书需要客户端验证通过，才可以继续访问，而使用受信任的公司申请的证书则不会弹出提示页面(startssl就是个不错的选择，有1年的免费服务)。这套证书其实就是一对公钥和私钥。如果对公钥和私钥不太理解，可以想象成一把钥匙和一个锁头，只是全世界只有你一个人有这把钥匙，你可以把锁头给别人，别人可以用这个锁把重要的东西锁起来，然后发给你，因为只有你一个人有这把钥匙，所以只有你才能看到被这把锁锁起来的东西。

　　**3. 传送证书**

　　这个证书其实就是公钥，只是包含了很多信息，如证书的颁发机构，过期时间等等。

　　**4. 客户端解析证书**

　　这部分工作是有客户端的TLS来完成的，首先会验证公钥是否有效，比如颁发机构，过期时间等等，如果发现异常，则会弹出一个警告框，提示证书存在问题。如果证书没有问题，那么就生成一个随机值。然后用证书对该随机值进行加密。就好像上面说的，把随机值用锁头锁起来，这样除非有钥匙，不然看不到被锁住的内容。

　　**5. 传送加密信息**

　　这部分传送的是用证书加密后的随机值，目的就是让服务端得到这个随机值，以后客户端和服务端的通信就可以通过这个随机值来进行加密解密了。

　　**6. 服务段解密信息**

　　服务端用私钥解密后，得到了客户端传过来的随机值(私钥)，然后把内容通过该值进行对称加密。所谓对称加密就是，将信息和私钥通过某种算法混合在一起，这样除非知道私钥，不然无法获取内容，而正好客户端和服务端都知道这个私钥，所以只要加密算法够彪悍，私钥够复杂，数据就够安全。

　　**7. 传输加密后的信息**

　　这部分信息是服务段用私钥加密后的信息，可以在客户端被还原。

　　**8. 客户端解密信息**

　　客户端用之前生成的私钥解密服务段传过来的信息，于是获取了解密后的内容。整个过程第三方即使监听到了数据，也束手无策。



5.总结整个过程：

1.服务器向CA机构获取证书（假设这个证书伪造不了），当浏览器首次请求服务器的时候，服务器返回证书给浏览器。（证书包含：公钥+申请者与颁发者的相关信息+签名）

2.浏览器得到证书后，开始验证证书的相关信息，证书有效（没过期等）。（验证过程，比较复杂，详见上文）。

3.验证完证书后，如果证书有效，客户端是生成一个随机数，然后用证书中的公钥进行加密，加密后，发送给服务器，服务器用私钥进行解密，得到随机数。之后双方便开始用该随机数作为钥匙，对要传递的数据进行加密、解密。

#### 小故事

故事的主人公是Bob，他有三个好朋友Pat、Doug和Susan。Bob经常跟他们写信，因为他的信是明文传输的，在传递过程可能被人截获偷窥，也可能被人截获然后又篡改了，更有可能别人伪装成Bob本人跟他的好朋友通信，总之是不安全的。他很苦恼，经过一番苦苦探索，诶，他发现计算机安全学里有一种叫非对称加密算法的东东，好像可以帮助他解决这个问题

**说明：非对称加密算法（RSA）是内容加密的一类算法，它有两个秘钥：公钥与私钥。公钥是公开的钥匙，所有人都可以知道，私钥是保密的，只有持有者知道。通过公钥加密的内容，只能通过私钥解开。非对称加密算法的安全性很高，但是因为计算量庞大，比较消耗性能。**

好了，来看看Bob是怎么应用非对称加密算法与他的好朋友通信的：

1、首先Bob弄到了两把钥匙：公钥和私钥。

![img](http://img.mp.itc.cn/upload/20170613/a5c6a09bf86244aaa7334162a972a9fa_th.jpg)

2、Bob自己保留下了私钥，把公钥复制成三份送给了他的三个好朋友Pat、Doug和Susan。

![img](http://img.mp.itc.cn/upload/20170613/22a5fa80d9744671a8791b34b7c9bcb6_th.jpg)

3、此时，Bob总算可以安心地和他的好朋友愉快地通信了。比如Susan要和他讨论关于去哪吃午饭的事情，Susan就可以先把自己的内容（明文）首先用Bob送给他的公钥做一次加密，然后把加密的内容传送给Bob。Bob收到信后，再用自己的私钥解开信的内容。

![img](http://img.mp.itc.cn/upload/20170613/9052aa6610c0474ab3c1d76ff2493846_th.jpg)

**说明**：这其实是计算机安全学里**加密**的概念，加密的目的是为了不让别人看到传送的内容，加密的手段是通过一定的加密算法及约定的密钥进行的（比如上述用了非对称加密算法以及Bob的公钥），而解密则需要相关的解密算法及约定的秘钥（如上述用了非对称加密算法和Bob自己的私钥），可以看出加密是可逆的（可解密的）。

4、Bob看完信后，决定给Susan回一封信。为了防止信的内容被篡改（或者别人伪装成他的身份跟Susan通信），他决定先对信的内容用hash算法做一次处理，得到一个字符串哈希值，Bob又用自己的私钥对哈希值做了一次加密得到一个签名，然后把签名和信（明文的）一起发送给Susan。

![img](http://img.mp.itc.cn/upload/20170613/d17cf74b7e984f0d84ff77d4be596838_th.jpg)

![img](http://img.mp.itc.cn/upload/20170613/3cdfce04d0c7429bbadade7c2e3c94ba.jpg)

![img](http://img.mp.itc.cn/upload/20170613/3032507804044955aaa73b453f5aa4b7_th.jpg)

**说明**：Bob的内容实质是明文传输的，所以这个过程是可以被人截获和窥探的，但是Bob不担心被人窥探，他担心的是内容被人篡改或者有人冒充自己跟Susan通信。这里其实涉及到了计算机安全学中的**认证**概念，Bob要向Susan证明通信的对方是Bob本人，另外也需要确保自己的内容是完整的。

5、Susan接收到了Bob的信，首先用Bob给的公钥对签名作了解密处理，得到了哈希值A，然后Susan用了同样的Hash算法对信的内容作了一次哈希处理，得到另外一个哈希值B，对比A和B，如果这两个值是相同的，那么可以确认信就是Bob本人写的，并且内容没有被篡改过。

![img](http://img.mp.itc.cn/upload/20170613/87fb6488c97d42a2b80a73c3377f0a74_th.jpg)

**说明**：4跟5其实构成了一次完整的通过**数字签名**进行认证的过程。数字签名的过程简述为：发送方通过不可逆算法对内容text1进行处理（哈希），得到的结果值hash1，然后用私钥加密hash1得到结果值encry1。对方接收text1和encry1，用公钥解密encry1得到hash1，然后用text1进行同等的不可逆处理得到hash2，对hash1和hash2进行对比即可认证发送方。

6、此时，另外一种比较复杂出现了，Bob是通过网络把公钥寄送给他的三个好朋友的，有一个不怀好意的家伙Jerry截获了Bob给Doug的公钥。Jerry开始伪装成Bob跟Doug通信，Doug感觉通信的对象不像是Bob，但是他又无法确认。

![img](http://img.mp.itc.cn/upload/20170613/0d4eea71c9df4ebeb214be8eb64fcad0_th.jpg)

7、Bob最终发现了自己的公钥被Jerry截获了，他感觉自己的公钥通过网络传输给自己的小伙伴似乎也是不安全的，不怀好意的家伙可以截获这个明文传输的公钥。为此他想到了去第三方权威机构“证书中心”（certificate authority，简称CA）做认证。证书中心用自己的私钥对Bob的公钥和其它信息做了一次加密。这样Bob通过网络将数字证书传递给他的小伙伴后，小伙伴们先用CA给的公钥解密证书，这样就可以安全获取Bob的公钥了。

![img](http://img.mp.itc.cn/upload/20170613/8c75b00d301f470a9f9645b5fe21ab77_th.jpg)

这时候有人会问 CA机构的公钥又是什么呢，既然是公钥，为什么小偷没有呢？

其实CA的公钥所有人都可以有，但是私钥你不会有啊，也就是说这个证书使用ca的私钥进行加密的，别人伪造不了的，https中如果你想与服务端通信，那么服务端会返回给你这个证书，你可以使用CA的公钥对这个证书的真实性进行验证，这个证书中包含服务器的公钥，以及机构的签名，过期时间等，你用ca公钥解密后，对比这个值和CA的签名是否一致即可判断这个证书的真假，真的你使用，假的，自然拒绝与它通信。



#### SSL/TLS 协议握手细节

**https是需要加密的，那么问题来了，在tcp/ip这么多层上（应用层，传输层，网络层）加在哪一层上好呢？**

**应用层协议 http协议定义了包的结构，定义了报文的格式（写信的格式），     tcp 传输层协议（管家与管家通信说我要寄信了） 进行握手，连接通信三步，断开通信 四步    网络层协议（邮差），确定ip地址，方便传输 数据链路层（邮筒）底层协议 驱动程序**

****

## SSL/TLS协议流程

1. 浏览器将自己支持的一套加密算法发给服务器，同时发一个浏览器随机数。
2. 服务器向浏览器发送选择的加密算法、服务器生成的随机数、服务器数字证书。
3. 浏览器收到证书后对证书的CA签名进行验证，如果验证通过，会从证书中拿到服务器的公钥。
4. 浏览器对浏览器随机数+服务器随机数进行处理，生成预备主密码。
5. 浏览器用服务器的公钥对预备主密码进行加密，发给服务器。
6. 服务器收到后使用自己的私钥解密出预备主密钥。
7. 浏览器和服务器分别使用预备主密钥和两个随机数，生成共享主密钥
8. 二者使用共享主密钥，使用对称加密算法加密数据。

每次https请求，都需要使用上面的流程握手协商吗？

- 答：第一次协商主密钥后，后面的请求就使用主密钥进行对称加密通信，不需要再协商。通过会话id来识别。

- https://zhuanlan.zhihu.com/p/31880655  https协议参考

  SSL/TLS协议是一个安全套件，加在应用层和传输层之间的。可以用在不同的协议上，进行数据加密，例如http. pop3,smtp等

  