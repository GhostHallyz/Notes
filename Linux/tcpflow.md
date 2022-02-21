sudo tcpflow -cp -i any port 18001



```
-c：将数据流打印到控制台，不要存储到文件中。

-p：非杂乱模式

-i：指定网络接口

port：指定监听的端口号

host：指定监听的域名或ip
```

https://github.com/simsong/tcpflow





```
usage: tcpflow [-aBcCDhJpsvVZ] [-b max_bytes] [-d debug_level]
     [-[eE] scanner] [-f max_fds] [-F[ctTXMkmg]] [-i iface] [-L semlock]
     [-m min_bytes] [-o outdir] [-r file] [-R file]
     [-S name=value] [-T template] [-w file] [-x scanner] [-X xmlfile]
      [expression]

   -a: do ALL post-processing（后处理）.
   -b max_bytes: max number of bytes per flow to save
   -d debug_level: debug level; default is 1
   -f: maximum number of file descriptors（fd，文件描述符） to use
   -h: print this help message (-hh for more help)
   -H: print detailed information about each scanner
   -i: network interface on which to listen
   -I: generate temporal packet-> byte index files for each flow (.findex)
       为每个流生成临时包->字节索引文件（.findex）
   -g: output each flow in alternating（交替） colors (note change!)
   -l: treat non-flag arguments as input files rather than a pcap expression
       将非标志参数视为输入文件而不是 pcap 表达式
   -L  semlock - specifies（指定） that writes are locked using a named semaphore（命名信号量）
   -p: don't use promiscuous（混杂） mode
   -q: quiet mode - do not print warnings
   -r file: read packets from tcpdump pcap file (may be repeated)
   -R file: read packets from tcpdump pcap file TO FINISH CONNECTIONS
   -v: verbose（冗长的） operation equivalent（相等的） to -d 10
   -V: print version number and exit
   -w file: write packets not processed（处理的） to file
   -o  outdir   : specify output directory (default '.')
   -X  filename : DFXML output to filename
   -m  bytes    : specifies skip that starts a new stream (default 16777216).
   -F{p} : filename prefix（前缀）/suffix（后缀） (-hh for options)
   -T{t} : filename template (-hh for options; default %A.%a-%B.%b%V%v%C%c)
   -Z: do not decompress gzip-compressed HTTP transactions（业务；处理）

Control of Scanners:
   -E scanner   - turn off all scanners except scanner
   -S name=value  Set a configuration parameter (-hh for info)

Settable Options (and their defaults):
   -S enable_report=YES    Enable report.xml ()
   -S http_cmd=    Command to execute on each HTTP attachment（附件） (http)
   -S http_alert_fd=-1    File descriptor to send information about completed HTTP attachments (http)
   -S netviz_histogram_dump=0    Dumps the histogram（直方图） (netviz)
   -S netviz_histogram_size=1000    Maximum histogram size (netviz)
   -S tcp_timeout=0    Timeout for TCP connections (tcpdemux)
   -S check_fcs=YES    Require valid Frame Check Sum (FCS) (wifiviz)

   -e http - enable scanner http
   -e md5 - enable scanner md5
   -e netviz - enable scanner netviz
   -e wifiviz - enable scanner wifiviz

   -x tcpdemux - disable scanner tcpdemux
Console output options:
   -B: binary output, even with -c or -C (normally -c or -C turn it off)
   -c: console print only (don't create files)
   -C: console print only, but without the display of source/dest header
   -0: don't print newlines after packets when printing to console   -s: strip non-printable characters (change to '.')
   -D: output in hex (useful to combine with -c or -C)

expression: tcpdump-like filtering expression

See the man page for additional information.
```

