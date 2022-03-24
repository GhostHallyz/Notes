openssl config配置选项

```
全局选项
第一类是全局性选项：

--openssldir=OPENSSLDIR
安装目录，默认是 /usr/local/ssl 。
--prefix=PREFIX
设置 lib include bin 目录的前缀，默认为 OPENSSLDIR 目录。
--install_prefix=DESTDIR
设置安装时以此目录作为"根"目录，通常用于打包，默认为空。
zlib
zlib-dynamic
no-zlib
使用静态的zlib压缩库、使用动态的zlib压缩库、不使用zlib压缩功能。
threads
no-threads
是否编译支持多线程的库。默认支持。
shared
no-shared
是否生成动态连接库。
asm
no-asm
是否在编译过程中使用汇编代码加快编译过程。
enable-sse2
no-sse2
启用/禁用SSE2指令集加速。如果你的CPU支持SSE2指令集，就可以打开，否则就要关闭。
gmp
no-gmp
启用/禁用GMP库
rfc3779
no-rfc3779
启用/禁用实现X509v3证书的IP地址扩展
krb5
no-krb5
启用/禁用 Kerberos 5 支持
ssl
no-ssl
ssl2
ssl3
no-ssl2
no-ssl3
tls
no-tls
启用/禁用 SSL(包含了SSL2/SSL3) TLS 协议支持。
dso
no-dso
启用/禁用调用其它动态链接库的功能。[提示]no-dso仅在no-shared的前提下可用。

禁用选项
no-md2,no-md4,no-mdc2,no-ripemd
摘要算法
no-des,no-rc2,no-rc4,no-rc5,no-idea,no-bf,no-cast,no-camellia
对称加密算法
no-ec,no-dsa,no-ecdsa,no-dh,no-ecdh
不对称加密算法
no-comp
数据压缩算法
no-store
对象存储功能
```

1.下载 openssl 源码，

2.安装静态库
在下载后的目录中执行如下操作，安装静态库版本。

```bash
./config --prefix=/usr/local --openssldir=/usr/local/ssl
make && sudo make install 
```

3.安装动态库
安装目录为/usr/local下。安装动态库时增加-d选项，调试时使用动态库，达到跟踪代码的需求。

```bash
./config -d shared --prefix=/usr/local/ --openssldir=/usr/local/ssl enable-tfo enable-tls1_3
make clean
make && sudo make install
```

4.加入动态链接库的路径
注：执行此操作需要root身份

```bash
su - root 
echo "/usr/local/lib" >> /etc/ld.so.conf
echo "/usr/local/lib64" >> /etc/ld.so.conf 
ldconfig -v
```



```
ln -s /usr/local/openssl/lib/libssl.so.3 /usr/lib64/libssl.so.3
ln -s /usr/local/openssl/lib/libcrypto.so.3 /usr/lib64/libcrypto.so.3

ln -s /usr/local/openssl/lib/libssl.so.1.1 /usr/lib64/libssl.so.1.1
ln -s /usr/local/openssl/lib/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1
```





```
openssl s_client -connect 127.0.0.1:443 -debug -msg -tfo -tls1_3

openssl s_client -connect 121.40.156.24:443 -debug -msg -tfo -tls1_3


openssl s_server -accept 443 -key /root/openssl/apps/server.pem -cert /root/openssl/apps/server.pem -debug -msg -tfo


BIO_ADDRINFO_protocol(ai) == IPPROTO_TCP
```



```
./config -d shared --prefix=/usr/local/ssl3 --openssldir=/usr/local/ssl3 enable-tfo enable-tls1_3 enable-ssl3 enable-ssl3-method

./config -d --prefix=/usr/local/ssl3 --openssldir=/usr/local/ssl3 enable-tfo enable-tls1_3 enable-ssl3 enable-ssl3-method
```

