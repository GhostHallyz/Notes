nginx 模块说明

| 模块                          | 描述                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| ngx_http_core_module          | nginx核心基础模块                                            |
| ngx_http_access_module        | 该模块提供了一个简单的基于主机的访问控制。允许/拒绝基于ip地址 |
| ngx_http_addition_module      | 作为一个输出过滤器，支持不完全缓冲，分部分响应请求           |
| ngx_http_api_module           |                                                              |
| ngx_http_auth_basic_module    | 该模块提供了一个简单的基于主机的访问控制。允许/拒绝基于ip地址 |
| ngx_http_auth_jwt_module      |                                                              |
| ngx_http_auth_request_module  |                                                              |
| ngx_http_autoindex_module     | 该模块用于自动生成目录列表，只在ngx_http_index_module模块未找到索引文件时发出请求 |
| ngx_http_browser_module       | 该模块用来创建依赖于请求报头的值。如果浏览器为modern ，则$modern_browser等于modern_browser_value指令分配的值；如 果浏览器为old，则$ancient_browser等于 ancient_browser_value指令分配的值；如果浏览器为 MSIE中的任意版本，则 $msie等于1 |
| ngx_http_charset_module       | 该模块提供了一个简单的基于主机的访问控制。允许/拒绝基于ip地址 |
| ngx_http_dav_module           | 增加PUT,DELETE,MKCOL：创建集合,COPY和MOVE方法）默认情况下为关闭，需编译开启 |
| ngx_http_empty_gif_module     | 该模块在内存中常驻了一个1*1的透明GIF图像，可以被非常快速的调用 |
| ngx_http_f4f_module           |                                                              |
| ngx_http_fastcgi_module       | 该模块允许Nginx 与FastCGI 进程交互，并通过传递参数来控制FastCGI 进程工作。 FastCGI一个常驻型的公共网关接口 |
| ngx_http_flv_module           | 提供寻求内存使用基于时间的偏移量文件                         |
| ngx_http_geo_module           | 创建一些变量，其值依赖于客户端的IP地址                       |
| ngx_http_geoip_module         | 该模块创建基于与MaxMind GeoIP二进制文件相配的客户端IP地址的ngx_http_geoip_module变量 |
| ngx_http_grpc_module          |                                                              |
| ngx_http_gunzip_module        |                                                              |
| ngx_http_gzip_module          | 该模块同ngx_http_gzip_static_module功能一样                  |
| ngx_http_gzip_static_module   | 在线实时压缩输出数据流                                       |
| ngx_http_headers_module       |                                                              |
| ngx_http_hls_module           |                                                              |
| ngx_http_image_filter_module  | 传输JPEG/GIF/PNG 图片的一个过滤器）（默认为不启用。gd库要用到） |
| ngx_http_index_module         |                                                              |
| ngx_http_js_module            |                                                              |
| ngx_http_keyval_module        |                                                              |
| ngx_http_limit_conn_module    | 该模块可以针对条件，进行会话的并发连接数控制                 |
| ngx_http_limit_req_module     | 该模块允许你对于一个地址进行请求数量的限制用一个给定的session或一个特定的事件 |
| ngx_http_log_module           |                                                              |
| ngx_http_map_module           | 使用任意的键/值对设置配置变量                                |
| ngx_http_memcached_module     | 该模块用来提供简单的缓存，以提高系统效率                     |
| ngx_http_mirror_module        |                                                              |
| ngx_http_mp4_module           |                                                              |
| ngx_http_perl_module          | 该模块使nginx可以直接使用perl或通过ssi调用perl               |
| ngx_http_proxy_module         | 有关代理服务器                                               |
| ngx_http_random_index_module  | 从目录中随机挑选一个目录索引                                 |
| ngx_http_realip_module        | 这个模块允许从请求标头更改客户端的IP地址值，默认为关         |
| ngx_http_referer_module       | 该模块用来过滤请求，拒绝报头中Referer值不正确的请求          |
| ngx_http_rewrite_module       | 该模块允许使用正则表达式改变URI，并且根据变量来转向以及选择配置。如果在server级别设置该选项，那么他们将在 location之前生效。如果在location还有更进一步的重写规则，location部分的规则依然会被执行。如果这个URI重写是因为location部分的规则造成的，那么 location部分会再次被执行作为新的URI。 这个循环会执行10次，然后Nginx会返回一个500错误 |
| ngx_http_scgi_module          | 该模块用来启用SCGI协议支持，SCGI协议是CGI协议的替代。它是一种应用程序与HTTP服务接口标准。它有些像FastCGI但他的设计 更容易实现 |
| ngx_http_secure_link_module   | 计算和检查要求所需的安全链接网址                             |
| ngx_http_session_log_module   |                                                              |
| ngx_http_slice_module         |                                                              |
| ngx_http_spdy_module          |                                                              |
| ngx_http_split_clients_module | 该模块用来基于某些条件划分用户。条件如：ip地址、报头、cookies等等 |
| ngx_http_ssi_module           | 该模块提供了一个在输入端处理处理服务器包含文件（SSI）的过滤器，目前支持SSI命令的列表是不完整的 |
| ngx_http_ssl_module           | 使支持https请求，需已安装openssl                             |
| ngx_http_status_module        |                                                              |
| ngx_http_stub_status_module   | 获取nginx自上次启动以来的工作状态                            |
| ngx_http_sub_module           | 允许用一些其他文本替换nginx响应中的一些文本                  |
| ngx_http_upstream_module      | 该模块用于简单的负载均衡                                     |
| ngx_http_upstream_conf_module |                                                              |
| ngx_http_upstream_hc_module   |                                                              |
| ngx_http_userid_module        | 该模块用来处理用来确定客户端后续请求的cookies                |
| ngx_http_uwsgi_module         | 模块用来医用uwsgi协议，uWSGI服务器相关                       |
| ngx_http_v2_module            |                                                              |
| ngx_http_xslt_module          | 过滤转换XML请求                                              |

| 模块                      | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| ngx_mail_core_module      |                                                              |
| ngx_mail_auth_http_module |                                                              |
| ngx_mail_proxy_module     |                                                              |
| ngx_mail_realip_module    |                                                              |
| ngx_mail_ssl_module       |                                                              |
| ngx_mail_imap_module      | 一种邮件获取协议。它的主要作用是邮件客户端可以通过这种协议从邮件服务器上获取邮件的信息，下载邮件等。IMAP协议运行在TCP/IP协议之上， 使用的端口是143。它与POP3协议的主要区别是用户可以不用把所有的邮件全部下载，可以通过客户端直接对服务器上的邮件进行操作 |
| ngx_mail_pop3_module      | POP3即邮局协议的第3个版本,它是规定个人计算机如何连接到互联网上的邮件服务器进行收发邮件的协议。是因特网电子邮件的第一个离线协议标 准,POP3协议允许用户从服务器上把邮件存储到本地主机上,同时根据客户端的操作删除或保存在邮件服务器上的邮件。POP3协议是TCP/IP协议族中的一员，主要用于 支持使用客户端远程管理在服务器上的电子邮件 |
| ngx_mail_smtp_module      | SMTP即简单邮件传输协议,它是一组用于由源地址到目的地址传送邮件的规则，由它来控制信件的中转方式。SMTP协议属于TCP/IP协议族，它帮助每台计算机在发送或中转信件时找到下一个目的地。 |

| 模块                            | 描述                     |
| ------------------------------- | ------------------------ |
| ngx_stream_core_module          |                          |
| ngx_stream_access_module        |                          |
| ngx_stream_geo_module           |                          |
| ngx_stream_geoip_module         |                          |
| ngx_stream_js_module            |                          |
| ngx_stream_keyval_module        |                          |
| ngx_stream_limit_conn_module    |                          |
| ngx_stream_log_module           |                          |
| ngx_stream_map_module           |                          |
| ngx_stream_proxy_module         |                          |
| ngx_stream_realip_module        |                          |
| ngx_stream_return_module        |                          |
| ngx_stream_set_module           |                          |
| ngx_stream_split_clients_module |                          |
| ngx_stream_ssl_module           |                          |
| ngx_stream_ssl_preread_module   |                          |
| ngx_stream_upstream_module      |                          |
| ngx_stream_upstream_hc_module   |                          |
| ngx_stream_zone_sync_module     |                          |
|                                 |                          |
| ngx_google_perftools_module     | 调试用，剖析程序性能瓶颈 |

