---
title: 互联网大陆的守夜人-OpenSSL加密浅谈
date: '2016-11-23 10:01:18 -0800'
author: shidaxi
categories:
  - 技术谈
permalink: /openssl-nights-watch-of-the-internet
---

这是笔者发布在点融黑帮微信公众号\(id:DianrongMafia\)的一篇文章&lt;Game of Thrones 互联网大陆的守夜人&gt;，意外被36kr、每日头条等多家科技媒体的转载，为黑帮涨粉不少。



![](http://shixi-pub.oss-cn-shanghai.aliyuncs.com/site/openssl-got-nights-watch-snow.png)



> 「长夜将至，我从今开始守望，至死方休。我将不娶妻、不封地、不生子。我将不戴宝冠，不争荣宠。我将尽忠职守，生死於斯。我是黑暗中的利剑，长城上的守卫。我是抵御寒冷的烈焰，破晓时分的光线，唤醒眠者的号角，守护王国的坚盾。我将生命与荣耀献给守夜人，今夜如此，夜夜皆然。」——守夜人



互联网黄金时代的到来，让人们享受到了各种各样的便利，最大的功臣当然是计算机软硬件以及互联网通信技术的飞速发展，当然背后也有一些默默奉献的无名者，今天的文章的便是关于其中一位，我把它比喻为互联网大陆的守夜人\(The Night Watch\)——信息加密技术。它就像美剧Game of Thrones里面的守夜人一样，默默无闻地保卫着互联网这片大陆不受“异鬼”的侵袭。



##  数据加密浅谈

为了使文章不那么枯燥，先从几个故事讲起。

古代运用加密技术进行通信的著名案例是凯撒密码。凯撒是古代一位军事将领，他率先将加密技术应用于军事通信，他的基本思想是：通过把字母移动一定的位数来实现加密和解密。明文中的所有字母都在字母表上向后（或向前）按照一个固定数目进行偏移后被替换成密文。例如，当偏移量是3的时候，所有的字母A将被替换成D，B变成E，以此类推X将变成A，Y变成B，Z变成C。由此可见，位数就是凯撒密码加密和解密的密钥。

![](http://shixi-pub.oss-cn-shanghai.aliyuncs.com/site/openssl-got-caesar-code.png)

显然，这太简单了，拉丁字母只有26个，人脑即可轻松破译。另外，通过统计学知识，只要文章够长，每个字母在一篇文章中出现的概率是特定的，例如字母e在英文中出现的几率最大，那么只要对密文略加统计，即可得出，密文中出现最多的字母对应的就是e，其他以此类推。

后来人们经过一番改进，增加了密钥的长度或者复杂度，只要保证密钥够复杂，并且密钥只被通信双方知道，便没那么容易被破解。这种使用单个密钥的加密技术叫做对称加密，特点是双方使用预共享密钥\(Pre-Shared Key\)。

后来，这种加密技术广泛应用到后来的战争中，二战时有一个著名的关于密码的故事：考文垂大轰炸。由阿兰图灵领导的团队破解了德国的密码系统，得知德国将实行月光奏鸣曲计划对考文垂进行毁灭式轰炸。英国为了不让自己的破解系统暴露于德国之前，故而没有对城内居民做出警告，也没有采取非常规防御措施，最终被夷为平地。这个故事暴露了预共享密钥的缺陷：算法再复杂、密钥再长，也有被破译的可能；并且密钥必须是预共享的\(Pre-Shared\)，如何安全地传送密钥也是一个难题。

而太平洋战场的美国就比较聪明一些了。有一部 Nicolas Cage主演的讲述太平洋战争的电影叫做《风语者》\(Windtalkers\)。故事里面，美军征了大量纳瓦霍族人并训练成通讯兵，使用一种基于纳瓦霍语的密码传递军事情报，日军要是想破译密码，就必须抓到通讯兵。Cage的任务就是在战场上保护其中一个通讯兵不被日军抓到，万一不幸被抓，Cage必须亲手杀死通讯兵以保护密码。



![](http://shixi-pub.oss-cn-shanghai.aliyuncs.com/site/openssl-got-windtalkers.png)



这里通讯员就相当于一个"物理"形式的密钥，它的好处是，物理密钥被别人窃取才有可能进行破译，这就极大增加了安全性。现实生活中最常用的场景就是银行申请网银的时候都要配一个USB KEY，网上大额支付的时候需要在电脑上插入USB KEY，相对于纯密码更安全一些。  


后来人类发明了一样伟大的东西——计算机，并且经过不断改进，计算能力越来越强，人们开始使用计算机进行信息的加密解密，当然剑是双刃的，好处是我们可以使用更长更复杂的密钥或者算法来快速进行加密，坏处是，坏人也会用利用它更快地进行破解，将来可能还会出现更强大的计算机技术如量子计算机技术，也许当前理论上几百年才能破解的密文，未来只需几秒即可解密；而且预共享密钥的加密方法，密钥的安全传递始终是一个漏洞。于是一种更完美的加密技术应运而生——非对称加密技术。

非对称加密技术的基本原理是：生成一对密钥，一个叫公钥，是可以公开被所有人知道的；另外一个叫私钥，只有密钥的主人持有，其他任何人都不能得到；公钥加密的密文只有私钥才能解密，私钥加密的密文只有公钥才能解密。举个栗子：

![](http://shixi-pub.oss-cn-shanghai.aliyuncs.com/site/openssl-got-asymmetric-encryption.png)

如图所示，Alice要想给Bob发送私密数据，那么Bob先把公钥公开，Alice用Bob的公钥加密自己的数据，然后发送给Bob，Bob用自己的私钥进行解密；反过来，如果Bob要给Alice发送非私密但是必须真实的数据，Alice为了确定收到的数据确实是Bob发的而没有中间人篡改，她可以用Bob的公钥进行解密，如果能正常解密则证明消息确实是Bob发送，因为除了Bob以外其他人无法生成可以用该公钥解密的数据。

因此非对称加密技术广泛应用于通信加密、数字证书、数字签名、身份认证等等。

  
**  
OpenSSL**

去年，发生了一个震惊整个互联网的漏洞—心脏滴血\(Heartbleed\)，让OpenSSL这个默默无闻的却承担保护全世界网民信息安全的隐形“卫士”现身到大众面前。OpenSSL是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序和类库。全世界的网站大都在使用OpenSSL这个开源工具保护他们的信息安全，这么伟大的项目却穷的才几个程序员，而且分布在世界各地，通过网络彼此沟通协作，一直靠捐赠维持生计，捐赠者包括诺基亚、华为、甲骨文等知名企业。

![](http://shixi-pub.oss-cn-shanghai.aliyuncs.com/site/openssl-got-heart-bleeding.png)

那么问题来了，OpenSSL是如何保护我们的？



我们的互联网是通过千千万万通信设备\(路由设备、交换设备\)以及线缆\(电缆、光缆\)连接到一起的，当我们的数据在这些设备、线缆中传输的时候，很容易被人通过各种手段例如软件抓包、信号窃听等设备截取到，并根据传输协议进行解码获取敏感信息，如下图。

![](http://shixi-pub.oss-cn-shanghai.aliyuncs.com/site/openssl-got-wireshark.png)

好在我们有加密技术保护。拿WEB服务来说，我们可以通过部署SSL证书来启用HTTPS协议，这样在客户端浏览器与服务端webserver之间传输的任何信息都是密文，我们提交的表单中的账号和密码，以及服务端返回的包含个人信息的网页都被安全加密。下面简要解析一下其中原理。

由于篇幅有限，关于SSL如何加密解密原理这里不赘述，可以参考前面的非对称加密原理。当然实际过程会更复杂一些：非对称加密传递密钥，对称加密算法数据加密，消息摘要做完整性校验，并且每种加密方式还有多种算法供选择，具体使用哪种算法哪些参数是浏览器端跟服务端协商决定的。这里重点介绍一下证书的信任问题。



以我大点融网站为例：

点融网主站的证书是向特定的证书机构\(Certificate Authority，以下简称CA\)付费申请的，申请时需要提交自己的域名以及企业信息，证书机构审核通过给予颁发，证明我大点融是受信任的企业，所以浏览器访问点融网时，地址栏会显示一个绿色小锁标志，如下图所示：

![](http://shixi-pub.oss-cn-shanghai.aliyuncs.com/site/openssl-got-dianrong-website-small.png)

点融网使用了安全度更高的增强型的EV SSL证书\(Extended Validation SSL Certificate\)，不仅显示绿色小锁，还能显示包含完整公司名称的绿色条状标识：

![](http://shixi-pub.oss-cn-shanghai.aliyuncs.com/site/openssl-got-browser-ev-greenbar.png)

证书也可以自己制作，叫做自签名证书，不过首先也得自建一个CA。既然可以自己做，为何还要花钱求别人颁发？原因是，自建CA并不被其他用户的浏览器所信任，其他用户访问你部署自签名证书的网站时，浏览器会红色警告提示这不是一个合法CA颁发的证书。反面教材：12306.cn.

![](http://shixi-pub.oss-cn-shanghai.aliyuncs.com/site/openssl-got-12306-bad-cert.png)

为何买的证书浏览器就认为合法，而自签名的证书浏览器就认为不合法呢？原因是，浏览器内置了一部分合法CA的根证书，如果你的证书是这些CA之一签发的，那浏览器就认为是合法的，否则就不合法，除非把自建CA的根证书也安装到浏览器，让浏览器信任。  


**具体的证书信任验证过程如下：**

证书以证书链的形式组织，在颁发证书的时候首先要有根CA机构颁发的根证书，再由根CA机构颁发一个中级CA机构的证书，最后由中级CA机构颁发具体的SSL证书。在验证证书的时候，浏览器会对证书路径中的所有证书一级一级往上进行验证，只有路径中所有的证书都是受信任的，最后结果才是受信任的。如下图所示：  


![](http://shixi-pub.oss-cn-shanghai.aliyuncs.com/site/openssl-got-ssl-cert-detail.png)



## **SSL发展趋势**

早在2011年Google就已经开始启用全站SSL加密，百度也在2014年底开始测试全站加密，SSL的时代已经到来，妈妈再也不用担心我账号被盗、页面被运营商强插广告、中间人攻击等困扰了。  


在中国，SSL的普及之路还很遥远，个人认为主要障碍有如下几个：

1. 安全意识薄弱，很多网站负责人只关注内容、流量，不出安全事故，意识不到信息安全的重要性;
2. 加密带来大量额外计算资源开销，访问性能会降低，手持设备会更耗电，影响用户体验，因此很多网站默认不加密；
3. 增加成本，SSL证书通常需要向特定机构申请购买;
4. 额外技术成本，例如网站功能兼容性，浏览器兼容性，手机APP兼容性等等。 

随着金融互联网和电子商务的蓬勃发展，使得信息安全显得越来越重要，加密早已成了必备技术，相信终有一天，互联网会彻底抛弃HTTP，进入全面HTTPS时代。  


**那么，最后一个问题，作为群众的我如何科学地使用加密技术来保护个人信息？**  


在这里举几个我能想到的例子吧：

1. 本文主要讲SSL加密，当然是在访问任何网站尤其是P2P、支付、交易等相关网站的时候，如果默认没有使用https，可以尝试手动在http后面冒号前面加一个s来访问加密的服务；
2. 除了Web服务，还有例如邮件客户端配置加密的pop3、imap、smtp协议和端口\(具体方法参考邮件服务商的帮助页面\) ；
3. 网站众多，账号众多，记密码很头疼，但是千万不要用txt文件保存明文密码，推荐使用密码管理软件，如苹果系统的keychain，Win系统的Keepass等工具；
4. 千万千万千万\(重要事情说三遍\)尽量避免连接公共场合的开放的WiFi\(尤其是不加密的\);如果非连不可，请不要访问跟钱相关的网站; 如果非要访问，请记得使用加密方式访问。WiFi无线信号是空间内广播的，只要笔记本电脑装个软件即可监听所有连到该热点的设备之间的通信。


