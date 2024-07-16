title: 网络通信
author: Laiyong Wang
date: 2022-08-04 23:57:49
tags:
---
1. TCP UDP
TCP 一种面向连接(连接导向)的、可靠的、基于IP的传输层协议，能够对自己提供的连接实施控制。适用于要求可靠传输的应用，例如文件传输。面向字节流，传输慢
UDP 是一种面向无连接的传输层协议，不会对自己提供的连接实施控制。适用于实时应用，例如：IP电话、视频会议、直播等。以报文的方式传输，效率高
2. TCP 如何保证可靠传输
校验和，序列号，确认应答，超时重传，连接管理，流量控制，拥塞控制等
3. TCP 有哪些字段和常用字段
seq - 序号
ack - 确认序号，确认序号应该是上次已成功收到数据字节序号+1
fin - 结束
- 状态
LISTEN - 侦听来自远方TCP端口的连接请求； 
SYN-SENT -在发送连接请求后等待匹配的连接请求； 
SYN-RECEIVED - 在收到和发送一个连接请求后等待对连接请求的确认； 
ESTABLISHED- 代表一个打开的连接，数据可以传送给用户； 
FIN-WAIT-1 - 等待远程TCP的连接中断请求，或先前的连接中断请求的确认；
FIN-WAIT-2 - 从远程TCP等待连接中断请求；
CLOSE-WAIT - 等待从本地用户发来的连接中断请求； 
CLOSING -等待远程TCP对连接中断的确认； 
LAST-ACK - 等待原来发向远程TCP的连接中断请求的确认； 
TIME-WAIT -等待足够的时间以确保远程TCP接收到连接中断请求的确认； 
CLOSED - 没有任何连接状态；


4. TCP的三次握手
TCP是一种面向连接的单播协议，在发送数据前，通信双方必须在彼此间建立一条连接，所以采用三次握手建立一个连接

第一次握手：客户端发送网络包，服务端收到了。这样服务端就能得出结论：客户端的发送能力、服务端的接收能力是正常的。
第二次握手：服务端发包，客户端收到了。这样客户端就能得出结论：服务端的接收、发送能力，客户端的接收、发送能力是正常的。 从客户端的视角来看，我接到了服务端发送过来的响应数据包，说明服务端接收到了我在第一次握手时发送的网络包，并且成功发送了响应数据包，这就说明，服务端的接收、发送能力正常。而另一方面，我收到了服务端的响应数据包，说明我第一次发送的网络包成功到达服务端，这样，我自己的发送和接收能力也是正常的。
第三次握手：客户端发包，服务端收到了。这样服务端就能得出结论：客户端的接收、发送能力，服务端的发送、接收能力是正常的。 第一、二次握手后，服务端并不知道客户端的接收能力以及自己的发送能力是否正常。而在第三次握手时，服务端收到了客户端对第二次握手作的回应。从服务端的角度，我在第二次握手时的响应数据发送出去了，客户端接收到了。所以，我的发送能力是正常的。而客户端的接收能力也是正常的。

经历了上面的三次握手过程，客户端和服务端都确认了自己的接收、发送能力是正常的。之后就可以正常通信了

5. TCP的四次挥手
      1、第一次挥手：客户端发送一个 FIN 报文，报文中会指定一个序列号。此时客户端处于FIN_WAIT1状态。
      2、第二次握手：服务端收到 FIN 之后，会发送 ACK 报文，且把客户端的序列号值 + 1 作为 ACK 报文的序列号值，表明已经收到客户端的报文了，此时服务端处于 CLOSE_WAIT状态。
      3、第三次挥手：如果服务端也想断开连接了，和客户端的第一次挥手一样，发给 FIN 报文，且指定一个序列号。此时服务端处于 LAST_ACK 的状态。
      4、第四次挥手：客户端收到 FIN 之后，一样发送一个 ACK 报文作为应答，且把服务端的序列号值 + 1 作为自己 ACK 报文的序列号值，此时客户端处于 TIME_WAIT 状态。需要过一阵子以确保服务端收到自己的 ACK 报文之后才会进入 CLOSED 状态
      5、服务端收到 ACK 报文之后，就处于关闭连接了，处于 CLOSED 状态
      PS：第四步中，客户端第四次挥手时，原则上来说是最后一步，为啥不关闭还要等待，因为需要确认服务器收到客户端发送的数据，服务器若是收不到回重新第三次挥手，所以 TIME_WAIT 需要设置时间
![upload successful](/images/pasted-8.png)
6. 为嘛TCP的连接是三次，两次行不行
为了实现可靠数据传输， TCP 协议的通信双方， 都必须维护一个序列号， 以标识发送出去的数据包中， 哪些是已经被对方收到的。 三次握手的过程即是通信双方相互告知序列号起始值， 并确认对方已经收到了序列号起始值的必经步骤。如果只是两次握手， 至多只有连接发起方的起始序列号能被确认， 另一方选择的序列号则得不到确认

7. 为嘛TCP的连接是三次，关闭的时候确是四次
网上写的真他妈复杂
其实就是为了防止数据产生混乱，确保客户端和服务端都确认关闭，免得一方关闭，另一方还一直发数据，浪费资源

8. TIME_WAIT 和 CLOSE_WAIT 的区别在哪
TIME_WAIT：这个是面试的高频考点，就是要理解，为什么客户端发送 ACK 之后不直接关闭，而是要等一阵子才关闭。这其中的原因就是，要确保服务器是否已经收到了我们的 ACK 报文，如果没有收到的话，服务器会重新发 FIN 报文给客户端，客户端再次收到 ACK 报文之后，就知道之前的 ACK 报文丢失了，然后再次发送 ACK 报文
TIME_WAIT 持续的时间至少是一个报文的来回时间。一般会设置一个计时，如果过了这个计时没有再次收到 FIN 报文，则代表对方成功就是 ACK 报文，此时处于 CLOSED 状态。

9. 为什么客户端发出第四次挥手的确认报文要等2ms才能释放TCP连接
不一定是2ms，因为需要等待确认服务器收到了客户端发送的第三次挥手请求

10. 如果已经建立了连接，客户端出现了故障怎么办
保活计时器
服务器每收到一次客户端的请求后都会复位这个计时器，时间通常是设置为2小时，若两小时还没有收到客户端的任何数据，服务器就会发送一个探测报文段，以后每隔75秒发送一次。若一连发送10个探测报文仍然没反应，服务器就认为客户端出了故障，接着就关闭连接。
11. http 和 https 的区别
1、端口
https的端口是443，而http的端口是80，当然两者的连接方式也是不太一样的。
2、传输数据
http传输是明文的，而https是用ssl进行加密的。https具有安全性
3、申请证书
https传输一般是需要申请证书，申请证书可能会需要一定的费用。

https 即使用了对称加密也使用了非对称加密，如果单独只用对称加密，或者单独使用非对称加密都会出现问题
12. 什么是对称加密和非对称加密
- 对称加密
加密方和解密方使用同样的秘钥来进行加密和解密
- 非对称加密
甲方生成一对密钥并将其中的一把作为公用密钥向其它方公开。
得到该公用密钥的乙方使用该密钥对机密信息进行加密后再发送给甲方。
甲方再用自己保存的另一把专用密钥对加密后的信息进行解密。
甲方只能用其专用密钥解密由其公用密钥加密后的任何信息。

13. http2.0的特性
二进制分帧
多路复用
首部压缩
流量控制
请求优先级
服务器推送
14. 客户端禁止cookie，session 还能用么
能，sessionid 无论是http头还是url携带，只要能传输都行

15. 在浏览器中输入 url 地址到显示主页的过程
1、用户在浏览器中输入url地址
2、浏览器解析域名得到服务器ip地址
3、TCP三次握手建立客户端和服务器的连接
4、客户端发送HTTP请求获取服务器端的静态资源
5、服务器发送HTTP响应报文给客户端，客户端获取到页面静态资源
6、TCP四次挥手关闭客户端和服务器的连接
7、浏览器解析文档资源并渲染页面
16. OSI七层模型

![upload successful](/images/pasted-7.png)

17. http1 和 http2 的区别

18. csrf和xss的区别
csrf 跨站请求攻击。验证码、token、检测refer
xss 跨站脚本攻击，过滤用户输入

19. 哪些属性唯一确定一条TCP连接
源端口、目标端口、源ip、目标ip