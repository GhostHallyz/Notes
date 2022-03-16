SSL(Secure Sockets Layer)是传输层的套接字安全协议，

1. 数据加密
2. 身份认证
3. 完整性保护

HTTPS（Secure Hypertext Transfer Protocol）安全超文本传输协议，是HTTP的升级版，主要针对HTTP的三大缺点：

1. 安全性是最大的短板，报文内容大部分都是明文，没有加密机制，如果被截获就完全没有秘密可言；
2. 缺少身份验证，你不知道和你会话的是google还是社会我谷哥, 会到中间人攻击或是钓鱼网站的欺骗;
3. 缺少完整性校验手段，无法证明报文在传输过程中是否被篡改，因为HTTP协议允许中间代理，这些代理可能就玩坏你的HTTP报文，而浏览器却很难自动去校验他们。



1. 

HTTPS 协议的主要功能基本都依赖于 TLS/SSL 协议，TLS/SSL 的功能实现主要依赖于三类基本算法：

1. 散列函数 ，基于散列函数验证信息的完整性。
2. 非对称加密，利用非对称加密实现身份认证和密钥协商。
3. 对称加密，利用对称加密算法和协商的密钥对应用数据进行加密传输。

![8d94d15c613b46ae85b923682ce33caa_tplv-k3u1fbpfcp-watermark](.\image\8d94d15c613b46ae85b923682ce33caa_tplv-k3u1fbpfcp-watermark.webp)



例如算法套件：TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256

TLS:协议名

ECDHE:密钥交换方法，Elliptic Curve Diffie-Hellman Ephemeral基于椭圆曲线的DH短时加密算法。

RSA:身份认证方式，用来做服务器的签名，RSA使用非对称加密，一套公钥和私钥，服务器用私钥加密的数据，客户端只能用服务器公钥才能解开，确认签名真伪。

AES_128_GCM:数据加密方式，握手后采用这种方法来生成对称加密的密钥，AES 对称算法，密钥长度 128 位，分组模式是 GCM；

SHA256: 使用哈希算法256bit保证数据完整性

