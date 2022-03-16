

1. 程序编译时，通过-Wl, -rpath参数指定的路径；
2. 环境变量LD_LIBRARY_PATH指定的路径
3. /etc/ld.so.conf或/etc/ld.so.conf.d/目录下任何一个文件中指定的路径
4. /lib路径
5. /usr/lib路径



加入动态链接库的路径

```
su - root 
echo "/usr/local/lib" >> /etc/ld.so.conf
echo "/usr/local/lib64" >> /etc/ld.so.conf 
ldconfig -v
```

