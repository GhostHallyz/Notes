



## TCP 段结构 (TCP segment structure)



![TCP_segment_header](.\image\TCP_segment_header.png)



### 源端口 Source port (16 bits)

发送方端口信息

### 目标端口 Destination port (16 bits)

接收方端口信息

### 序列号 Sequence number (32 bits)

具有双重作用：

- 如果 SYN 标志设置为 (1)，则这是初始序列号。实际第一个数据字节的序列号和相应 ACK 中的确认号就是这个序列号加 1。
- 如果 SYN 标志清零 (0)，则这是当前会话的该段的第一个数据字节的累积序列号。

### 确认号 Acknowledgment number (32 bits)

如果设置了 ACK 标志，则该字段的值是 ACK 的发送者期望的下一个序列号。这确认收到所有先前的字节（如果有的话）。每一端发送的第一个 ACK 确认对方的初始序列号本身，但没有数据。

### 数据偏移量 Data offset (4 bits)

以 32 位[字](https://en.wikipedia.org/wiki/Word_(computer_architecture))指定 TCP 标头的大小。标题的最小大小为 5 个字，最大为 15 个字，因此最小大小为 20 个字节，最大为 60 个字节，标题中最多允许 40 个字节的选项。该字段的名称源于它也是从 TCP 段开始到实际数据的偏移量。

### 保留 Reserved (3 bits)

保留位，以备将来使用，应设置为零。

### 标志 Flags (9 bits)

TCP  Flags

```
Reserved（3 位）:预留位
NS（1 位）：ECN-nonce - 隐藏保护
CWR（1 位）：发送主机设置拥塞窗口减少（CWR）标志，表明它收到了一个设置了 ECE 标志的 TCP 段，并已在拥塞控制机制中作出响应。
ECE（1 位）：ECN-Echo 具有双重作用，具体取决于 SYN 标志的值。它表明：
  如果 SYN 标志设置为 (1)，则 TCP 对等体具有ECN能力。
  如果 SYN 标志清零 (0)，则在正常传输期间接收到 IP 标头中设置了拥塞经历标志 (ECN=11) 的数据包。[b]这用作对 TCP 发送方的网络     拥塞（或即将发生的拥塞）的指示。
URG（1位）：表示紧急指针字段是重要的
ACK（1 位）：表示确认字段是重要的。客户端发送的初始 SYN 数据包之后的所有数据包都应设置此标志。
PSH（1 位）：推送功能。要求将缓冲的数据推送到接收应用程序。
RST（1位）：重置连接
SYN（1 位）：同步序列号。只有从每一端发送的第一个数据包应该设置这个标志。其他一些标志和字段基于此标志改变含义，有些仅在设置时有     效，而另一些则在清除时有效。
FIN（1 位）：来自发送方的最后一个数据包


                                    
                              +---------+ ---------\      active OPEN  
                              |  CLOSED |            \    -----------  
                              +---------+<---------\   \   create TCB  
                                |     ^              \   \  snd SYN    
                   passive OPEN |     |   CLOSE        \   \           
                   ------------ |     | ----------       \   \         
                    create TCB  |     | delete TCB         \   \       
                                V     |                      \   \     
                              +---------+            CLOSE    |    \   
                              |  LISTEN |          ---------- |     |  
                              +---------+          delete TCB |     |  
                   rcv SYN      |     |     SEND              |     |  
                  -----------   |     |    -------            |     V  
 +---------+      snd SYN,ACK  /       \   snd SYN          +---------+
 |         |<-----------------           ------------------>|         |
 |   SYN   |                    rcv SYN                     |   SYN   |
 |   RCVD  |<-----------------------------------------------|   SENT  |
 |         |                    snd ACK                     |         |
 |         |------------------           -------------------|         |
 +---------+   rcv ACK of SYN  \       /  rcv SYN,ACK       +---------+
   |           --------------   |     |   -----------                  
   |                  x         |     |     snd ACK                    
   |                            V     V                                
   |  CLOSE                   +---------+                              
   | -------                  |  ESTAB  |                              
   | snd FIN                  +---------+                              
   |                   CLOSE    |     |    rcv FIN                     
   V                  -------   |     |    -------                     
 +---------+          snd FIN  /       \   snd ACK          +---------+
 |  FIN    |<-----------------           ------------------>|  CLOSE  |
 | WAIT-1  |------------------                              |   WAIT  |
 +---------+          rcv FIN  \                            +---------+
   | rcv ACK of FIN   -------   |                            CLOSE  |  
   | --------------   snd ACK   |                           ------- |  
   V        x                   V                           snd FIN V  
 +---------+                  +---------+                   +---------+
 |FINWAIT-2|                  | CLOSING |                   | LAST-ACK|
 +---------+                  +---------+                   +---------+
   |                rcv ACK of FIN |                 rcv ACK of FIN |  
   |  rcv FIN       -------------- |    Timeout=2MSL -------------- |  
   |  -------              x       V    ------------        x       V  
    \ snd ACK                 +---------+delete TCB         +---------+
     ------------------------>|TIME WAIT|------------------>| CLOSED  |
                              +---------+                   +---------+

                      TCP Connection State Diagram
```



### 窗口大小 Window size (16 bits)

*接收窗口*的大小，它指定了该段的发送者当前愿意接收的窗口大小单位

### 校验和 Checksum (16 bits)

16 位[校验和](https://en.wikipedia.org/wiki/Checksum)字段用于对 TCP 报头、有效负载和 IP 伪报头进行错误检查。伪报头由[源 IP 地址](https://en.wikipedia.org/wiki/IPv4#Source_address)、[目标 IP 地址](https://en.wikipedia.org/wiki/IPv4#Destination_address)、TCP 协议的[协议号](https://en.wikipedia.org/wiki/List_of_IP_protocol_numbers)(6) 以及 TCP 报头和有效负载的长度（以字节为单位）组成。

### 紧急指针 Urgent pointer (16 bits)

如果设置了 URG 标志，那么这个 16 位字段是与指示最后一个紧急数据字节的序列号的偏移量。

### 选项 Options (变量 0–320 bits, 以 32 bits为单位)

该字段的长度由*数据偏移量决定*场地。选项最多有三个字段：Option-Kind（1 字节）、Option-Length（1 字节）、Option-Data（变量）。Option-Kind 字段指示选项的类型，并且是唯一不可选的字段。根据 Option-Kind 值，可以设置接下来的两个字段。Option-Length 表示选项的总长度，Option-Data 包含与选项相关的数据（如果适用）。

### 填充 Padding



