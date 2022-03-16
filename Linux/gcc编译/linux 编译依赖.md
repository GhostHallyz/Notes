

1.**Can't locate IPC/Cmd.pm in @INC** (@INC contains: /root/openssl/util/perl /usr/local/lib64/perl5 /usr/local/share/perl5 /usr/lib64/perl5/vendor_perl /usr/share/perl5/vendor_perl /usr/lib64/perl5 /usr/share/perl5 . /root/openssl/external/perl/Text-Template-1.56/lib) at /root/openssl/util/perl/OpenSSL/config.pm line 18.

```bash
[root@centos7 ~]# yum install -y perl perl-CPAN
# 全部选择默认配置
[root@centos7 ~]# perl -MCPAN -e shell
cpan[1]> install IPC/Cmd.pm
```

