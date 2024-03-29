title: PHP 基础
author: Laiyong Wang
date: 2022-06-23 10:41:50
tags:
---
1. == 与 === 的区别
== 只判断值是否相等，不判断类型
=== 不仅判断值是否相等，还判断类型

2. isset 与 empty 的区别
isset 判断变量是否存在
empty 判断变量的值是否为空或者false或者0

3. 魔术方法
```
__get($property); //当调用一个未定义的属性时，此方法会被触发，传递的参数是被访问的属性名 
__sett($property, $value);//给一个未定义的属性赋值时，此方法会被触发，传递的参数是被设置的属性名和值;这里的没有声明包括当使用对象调用时，访问控制为proteced,private的属性（即没有权限访问的属性）
__isset($property);//当在一个未定义的属性上调用isset()函数时调用此方法 (判断类中私有属性或方法是否存在时自动调用)
__unset($property); 当在一个未定义的属性上调用unset()函数时调用此方法 (清除类中私有变量时自动调用)
与__get方法和__set方法相同，这里的没有声明包括当使用对象调用时，访问控制为proteced,private的属性（即没有权限访问的属性） 
__construct;//构造方法，当一个对象创建时调用此方法
__destruct;//析构方法，PHP将在对象被销毁前（即从内存中清除前）调用这个方法
__call($method, $arg_array);//当调用一个未定义的方法是调用此方法 (当调用类实例中不存在的函数时自动执行)
__callStatic;//类似于 __call() 魔术方法，__callStatic() 是为了处理静态方法调用，PHP5.3.0以上版本有效 
__sleep,serialize() 检查类中是否有魔术名称 __sleep 的函数。如果这样，该函数将在任何序列化之前运行。它可以清除对象并应该返回一个包含有该对象中应被序列化的所有变量名的数组。 
使用 __sleep 的目的是关闭对象可能具有的任何数据库连接，提交等待中的数据或进行类似的清除任务。此外，如果有非常大的对象而并不需要完全储存下来时此函数也很有用
__wakeup;//unserialize() 检查具有魔术名称 __wakeup 的函数的存在。如果存在，此函数可以重建对象可能具有的任何资源。 
使用 __wakeup 的目的是重建在序列化中可能丢失的任何数据库连接以及处理其它重新初始化的任务
__toString;//在将一个对象转化成字符串时自动调用，比如使用echo打印对象时
__set_state;//调用var_export()导出类时，此静态方法会被调用。本方法的唯一参数是一个数组，其中包含按 array('property' => value, ...) 格式排列的类属性
__clone;//对象的复制(引用赋值)$per1=$per2; 而这在内存中只有一块地址,而$per1=clone $per2 这时有两块内存地址
__autoload;//自动加载使用的类文件  该函数是在引用的页面添加
__invoke;//当尝试以调用函数的方式调用一个对象时，__invoke 方法会被自动调用
```
4. static $this self 的区别
$this 则表示该类实例化对象的引用
static 指向调用该 static 方法或者属性的类
self 永远指向代码所在的类

5. private protected public final 区别
private： 私有的，其定义的方法和属性仅本身可用（self this）
protected： 受保护的，其定义的方法和属性仅本身（self、this）及子类（parent）可用
public： 公共的，子类可自调用也可parent：调用父类，也可以实例化随便调用
final：  final 修饰的类，不能被继承，final 修饰的成员方法，在子类中不能覆盖（重写）该方法。

6. 什么是抽象类
只能被继承，且其里面的所有方法在子类中实现后才能使用

7. 什么是接口
接口interface是一个规定,定义了实现某种服务的一般规范,声明了所需的函数和常量,但是不能定义成员属性,类可以实现多个接口,并且接口也可以继承接口
类中必须实现接口中定义的所有方法，否则会报一个致命错误。类可以实现多个接口，用逗号来分隔多个接口的名称。

8. 抽象类和接口类的相同点和不同点
- 抽象类与接口的相同点：
1、都是用于声明某一种事物，规范名称、参数，形成模块，未有详细的实现细节。
2、都是通过类来实现相关的细节工作
3、都可以用继承，接口可以继承接口形成新的接口，抽象类可以继承抽象类从而形成新的抽象类
- 抽象类与接口的不同点：
1、抽象类可以有属性、普通方法、抽象方法，但接口不能有属性、普通方法、可以有常量
2、抽象类内未必有抽象方法，但接口内一定会有“抽象”方法
3、语法上有不同
4、抽象类用abstract关键字在类前声明，且有class声明为类，接口是用interface来声明，但不能用class来声明，因为接口不是类。
5、抽象类的抽象方法一定要用abstract来声明，而接口则不需要
6、抽象类是用extends关键字让子类继承父类后，在子类实现详细的抽象方法。而接口则是用implements让普通类在类里实现接口的详细方法，且接口可以一次性实现多个方法，用逗号分开各个接口就可
7、语法上，接口类，不能有方法体，即｛｝符号
- 各自的特点：
抽象类内未必有抽象方法，但有抽象方法的类，则必是抽象类
抽象类内，即便全是具体方法，也不能够实例化，只要新建类来继承后，实例继承类才可以
接口可以让一个类一次性实现多个不同的方法
接口本身就是抽象的，但注意不是抽象类，因为接口不是类，只是其方法是抽象的。所以，其也是抽象的


9. echo、print、print_r、var_dump 的区别
echo  是语句  不是函数没有返回值 可以输出单个字符串或多个字符串 多个字符串之间用逗号分开。
print 是语句 ，可以使用括号，也可以不使用括号： print 或 print()。只允许输出一个字符串 返回值总为1， echo 输出的速度比 print 快， echo 没有返回值，print有返回值1。
print_r（）函数  不会输出数据类型  能打印出复杂类型变量的值。利用print_r()可以打印出整个数组内容及结构，按照一定格式显示键和元素，事实上，它不仅仅用于打印，而是用于打印关于变量的易于理解的信息
var_dump() 会输出数据类型 判断一个变量的类型与长度，并输出变量的数值，如果变量有值，输出的是变量的值，并返回数据类型。此函数显示关于一个或多个表达式的结构信息，包括表达式的类型和值。数组将递归展开值，通过缩进显示其结构。

10. 常见http状态码
200 : 成功，表示访问成功，正常状态。
301 : 永久移动，表示本网页已经永久性的移动到一个新的地址，在客户端自动将请求地址改为服务器返回的新地址。
302 : 临时重定向，表示网页暂时性的转移到一的新的地址，客户端在以后可以继续向本地址发起请求。
303 : 表示必须临时重定向，并且必须使用GET方式请求。
304 : 重定向至浏览器本身，当浏览器多次发起同一请求，且内容未更改时，使用浏览器缓存，这样可以减少网络开销。
401 : 表示协议格式出错，可能是此IP地址被禁止访问该资源，与403类似。
403 : 表示没有权限，服务器拒绝访问请求。
404 : 这是最常见的错误，表示找不到系统资源，但是只是暂时性地。
500 : 表示服务器程序错误，一个通用的错误信息。
503 : 表示服务器繁忙，或者服务器负载，通常这只是一个临时状态。

11. 反转类反转方法
```
$refClass = new ReflectionClass(Student::class);
var_dump($refClass->getMethod('setBase'));
var_dump($refClass->getMethods());
var_dump($refClass->getConstants());
var_dump($refClass->getDefaultProperties());
```

12. 垃圾回收机制
zval变量容器，除了包含变量的类型和值，还包括两个字节的额外信息。第一个是"is_ref"，是个bool值，用来标识这个变量是否是属于引用集合(reference set)。通过这个字节，php引擎才能把普通变量和引用变量区分开来，由于php允许用户通过使用&来使用自定义引用，zval变量容器中还有一个内部引用计数机制，来优化内存使用。

第二个额外字节是"refcount"，用以表示指向这个zval变量容器的变量(也称符号即symbol)个数。所有的符号存在一个符号表中，其中每个符号都有作用域(scope)。
<https://cloud.tencent.com/developer/article/2045397?from=article.detail.1723827&areaSource=106000.1&traceId=qP4JYBfIWDzQdslhGZ4wx>

13. 数组的实现方式
哈希表+双向链表（哈希冲突是会写到同一个链表上），通过key值读取，使得可以在O(1)的时间复杂度下实现数组的增删, 并同时支持线性遍历和随机访问

14. 常见的设计模式
单例模式：保证在整个应用程序的生命周期中，单例类的实例只存在一个
工厂模式：定义一个创建对象的接口，让子类去实例化具体类
观察者模式 发布/订阅模式：当一个对象状态发生变化时，依赖它的对象全部会收到通知，并自动更新
适配器模式：将一个类的接口转换成客户希望的接口，使得原本不兼容的接口可以兼容
依赖注入模式：是ioc的一种实现方式。用来减少程序中的耦合
策略模式：同一功能的不同实现方式

15. HTTP中GET和POST的区别
GET在浏览器回退时是无害的，而POST会再次提交请求。
GET产生的URL地址可以被Bookmark，而POST不可以。
GET请求会被浏览器主动cache，而POST不会，除非手动设置。
GET请求只能进行url编码，而POST支持多种编码方式。
GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
GET请求在URL中传送的参数是有长度限制的，而POST没有。
对参数的数据类型，GET只接受ASCIl字符，而POST没有限制

16. 如何解决 PHP 内存溢出问题
增大 PHP 脚本的内存分配（ini_set('memory_limit',-1);）
变量引用之后及时销毁（垃圾）
将数据分批处理（递归）
17. php类的静态调用和实例化调用各自的利弊
静态方法可以直接调用，比实例化效率高，但不能自动销毁

18.  PHP 中传值与传引用的区别，什么时候传值什么时候传引用
按值传递：函数范围内对值的任何改变在函数外部都会被忽略
按引用传递：函数范围内对值的任何改变在函数外部也能反映出这些修改
优缺点：按值传递时，php必须复制值。特别是对于大型的字符串和对象来说，这将会是一个代价很大的操作。按引用传递则不需要复制值，对于性能提高很有好处。（优缺点会考到）
19. 单点登录（session共享）
php.ini 里配置 session 的存储方式及存储路径
使用redis/mem/mysql作为session存储可以实现多台服务器同一个session_id访问到相同的session内容

20. 自动加载的原理
自动加载的原理，就是在我们new一个class的时候，PHP系统如果找不到你这个类，就会去自动调用本文件中的__autoload($class_name)方法，我们new的这个class_name 就成为这个方法的参数。所以我们就可以在这个方法中根据我们需要new class_name的各种判断和划分就去require对应的路径类文件，从而实现自动加载

21. 谈谈对消息队列的理解
- 优点：
程序解耦：两个程序在交互时，不会因为一方中断而导致服务停止
异步处理：可以同时处理多个请求，缩短响应时长
流量削峰：可以适用在商品秒杀，在客户端请求量很大的时候，有效的进行流量削峰
- 缺点：
消息可用性降低
系统复杂度提高
数据可能不一致

22. PHP 如何实现多继承
接口多继承
trait
23. include和require的区别是什么？
include 在引入不存在文件时会产生一个警告但会继续运行
require 会出现一个致命性错误，并不会运行

24. 长连接、短连接的区别和使用
长连接：client与server建立连接，连接后不断开。且一直存在。
短连接：client与server在通讯时才建立连接。完成后久会断开。
长连接常用于socket通信，短链接常用于web http服务

25. swoole 为什么这么快 ？和go有什么区别？
常驻内存：避免重复加载带来的性能损耗，提升海量性能
协程异步：提高对 I/O 密集型场景并发处理能力（如：微信开发、支付、登录等）
区别：
swoole使用多线程eventloop处理IO事件，多进程执行用户层php代码
go使用单线程eventloop处理IO事件，多线程实现协程调度，执行用户层代码

26. 协程适用的场景？
高并发服务，如秒杀系统、高性能API接口、RPC服务器，使用协程模式，服务的容错率会大大增加，某些接口出现故障时，不会导致整个服务崩溃。
爬虫，可实现非常巨大的并发能力，即使是非常慢速的网络环境，也可以高效地利用带宽。
即时通信服务，如IM聊天、游戏服务器、物联网、消息服务器等等，可以确保消息通信完全无阻塞，每个消息包均可即时地被处理。

27. 什么是心跳机制？
心跳就是业务层来提供一个连接判断是否存活
客户端定时发送一个心跳包，告诉服务器，服务器定时检测所有客户端。看最后一个心跳包的时间长短。如果过长则主动关闭这个连接。
服务器定时询问所有的客户端。如果没有反馈则关闭连接。
两种心跳方案有什么区别？
第一种，对服务和网络压力小，但需要客户但配合。
第二种对服务器和网络压力大。

28. 传输层主要有哪些协议？
主要有 TCP 和 UDP 协议。他们的区别是 TCP 是需要连接的 会经过三次握手，而且可以保证消息的可靠性。UDP 是不需要连接的，不保证消息的可靠性。

29. 假设现在有多个入口可以同时使用一个账户操作，这个账户只有十块钱，有哪些方法可以使得不超扣消费？（重点研究下锁的机制 以及 mvcc）
使用悲观锁
队列

30. OSI网络协议的七个层级
应用层：为用户提供常用的应用程序，每个网络应用对应着不同的协议。例如文件运输访问和管理，电子邮件等。HTTP SMTP
表示层：主要负责数据格式的转换，确保一个系统的应用层发送的消息可以被另一个系统的应用层读取；数据加密
会话层：负责网络中两节点的建立，在数据传输中维护计算机网络中两台计算机之间的通信连接，并决定何时终止通信（建立或解除与其他节点的联系）
传输层：实现两个用户进程间端到端的可靠通信，处理数据包的错误等传输问题 TCP UDP
网络层：逻辑地址寻址，实现不同网络之间的路径选择 IPv4 v6 ARP
数据链路层：建立逻辑连接、进行硬件地址寻址
物理层： 建立,维护,断开物理连接

31. http协议中，服务器如何区分不同用户？
cookie

32. token原理 
用户A第一次进入,通过验证机制(账号密码登陆)请求服务端token
服务端验证成功,给用户发送一个token(针对用户)
服务端根据token,在服务端存储对应的数据(文件,mysql,redis等)
用户A端获取到token,存储到用户端本地
用户A请求某接口,带上token
服务端通过token,验证用户有效性,返回数据

33.  http和https区别，常用方法都有哪些，加密过程
https 的加密是对称还是非对称(这题明显就是挖好坑等我跳的, 正确答案是既使用了对称(加密消息体)又使用了非对称(加密对称 key))
区别：
https协议需要到CA申请证书
http信息是明文传输，https有安全性的ssl/tls加密传输协议
连接方式不同，端口也不同。
http的连接时无状态的，https是由ssl/tls+http构建的可进行加密传输、身份认证的网络的协议
加密：
对称加密：靠一个密钥加密和解密数据。由客户端发送密钥给服务器，客户端将加密后的数据发送给服务器，服务器用相同的密钥解密数据。
非对称加密：服务器端把公钥传给客户端，客户端拿着公钥对数据进行加密，然后客户端发送加密过的数据到服务器，服务器将加密后的数据用私钥解密

34. 熔断 降级 限流
参考链接：https://zhuanlan.zhihu.com/p/61363959
- 关系
熔断是降级方式的一种，降级又是限流的一种方式，三者都是为了通过一定的方式去保护流量过大时，保护系统的手段。
- 限流算法
令牌痛：分配，分到了才能运行
漏斗：先存着，满了就拒绝
计数器：字面意思，单位时间内超过就拒绝

35. 具有相等成员的同一个类的两个实例与===运算符不匹配
```
$a = new stdClass();
$a->foo = "bar";
$b = clone $a;
var_dump($a === $b);//false
```

36. fastCGI 和 CGI 的区别？php-fpm是啥
fastCGI 是一种常驻内存的 CGI，只需要加载一次，解决了 CGI 程序每次都要重新进行 fork 一个新的进程。 最大的区别是 fastCGI 能独立运行，而 CGI 只能依托于 Apache 运行
php-fpm  是 fastCGI 的实现，并提供了进程管理的功能。
进程包含 master 进程和 worker 进程两种进程。
master 进程只有一个，负责监听端口，接收来自 Web Server 的请求，而 worker 进程则一般有多个(具体数量根据实际需要配置)，每个进程内部都嵌入了一个 PHP 解释器，是 PHP 代码真正执行的地方

37. PHP的数组和C语言的数组结构上有何区别？
 php的数组是一个hashtable 内存地址不连续  C数组是一个数组。内存地址连续
 hash基于数组。数组在内存空间上是连续的地址。hash则不连续
 
38. B+Tree是怎么进行搜索的
https://blog.csdn.net/weixin_44758458/article/details/88653810

39. PHP的的弱类型变量是怎么实现的？
通过zval结构。 zval 包含变量的信息;
```
zval{
    type
    value
}
```
type 记录变量的类型。然后根据不同的类型，找到不同的value

40. PHP中发起http请求有哪几种方式
curl、fscocket、socket

41. 海量数据处理面试题与十个方法大总结
https://blog.csdn.net/v_JULY_v/article/details/6279498

42. php进程模型，php怎么支持多个并发
PHP 默认并不支持多线程，要使用多线程需要安装 pthread 扩展，而要安装 pthread 扩展，必须使用 --enable-maintainer-zts 参数重新编译 PHP，这个参数是指定编译 PHP 时使用线程安全方式
43. nginx的进程模型，怎么支持多个并发
io多路复用
44. nginx+php运行原理；一个请求到达nginx的全部处理过程（nginx自身会调用哪些逻辑）、然后怎么与php通信，中间的流程是什么样的等等
与在浏览器中输入 url 到浏览器展示页面有相同的流程

![upload successful](/images/pasted-16.png)

1、nginx的worker进程直接管理每一个请求到nginx的网络请求。

2、对于php而言，由于在整个网络请求的过程中php是一个cgi程序的角色，所以采用名为php-fpm的进程管理程序来对这些被请求的php程序进行管理。php-fpm程序也如同nginx一样，需要监听端口，并且有master和worker进程。worker进程直接管理每一个php进程。

3、关于fastcgi：fastcgi是一种进程管理器，管理cgi进程。市面上有多种实现了fastcgi功能的进程管理器，php-fpm就是其中的一种。再提一点，php-fpm作为一种fast-cgi进程管理服务，会监听端口，一般默认监听9000端口，并且是监听本机，也就是只接收来自本机的

端口请求，所以我们通常输入命令 netstat -nlpt|grep php-fpm 会得到：

tcp        0      0 127.0.0.1:9000              0.0.0.0:*                   LISTEN      1057/php-fpm
　　

这里的127.0.0.1:9000 就是监听本机9000端口的意思。

4、关于fastcgi的配置文件，目前fastcgi的配置文件一般放在nginx.conf同级目录下，配置文件形式，一般有两种：fastcgi.conf  和 fastcgi_params。不同的nginx版本会有不同的配置文件，这两个配置文件有一个非常重要的区别：fastcgi_parames文件中缺少下列配置：

fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;

我们可以打开fastcgi_parames文件加上上述行，也可以在要使用配置的地方动态添加。使得该配置生效。

5、当需要处理php请求时，nginx的worker进程会将请求移交给php-fpm的worker进程进行处理，也就是最开头所说的nginx调用了php，其实严格得讲是nginx间接调用php