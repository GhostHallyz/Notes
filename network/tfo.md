

```
#3的意思是开启TFO客户端和服务器端
#1表示开启客户端，2表示开启服务器端

echo 3 > /proc/sys/net/ipv4/tcp_fastopen
```



client.c

```c
#include <stdio.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <netdb.h>
#include <errno.h>
#include <string.h>


#define USE_IPV6 1
#if USE_IPV6

typedef struct in6_addr sq_in_addr;
typedef struct sockaddr_in6 sq_sockaddr_in;

#define SQ_AF_INET      AF_INET6
#define SQ_PF_INET      PF_INET6
#define sq_sin_family   sin6_family
#define sq_sin_port     sin6_port
#define sq_sin_addr     sin6_addr
#define sq_s_addr       s6_addr

#define CLIENT_IP_HEX_LEN 32
#define SQ_MAX_IP_STR_LENGTH 40

#else

typedef struct in_addr sq_in_addr;
typedef struct sockaddr_in sq_sockaddr_in;

#define SQ_AF_INET      AF_INET
#define SQ_PF_INET      PF_INET
#define sq_sin_family   sin_family
#define sq_sin_port     sin_port
#define sq_sin_addr     sin_addr
#define sq_s_addr       s_addr

#define CLIENT_IP_HEX_LEN 8
#define SQ_MAX_IP_STR_LENGTH 16


#endif /* USE_IPV6 */


int main(){
    sq_sockaddr_in serv_addr;

    char *data = "Hello, tcp fast open";
    int data_len = strlen(data);

    int sfd = socket(SQ_AF_INET, SOCK_STREAM, 0);

#if USE_IPV6
    inet_pton(SQ_AF_INET, "0:0:0:0:0:0:0:0", (struct sq_sockaddr_in *)&serv_addr.sq_sin_addr);
#else
    inet_pton(SQ_AF_INET, "0:0:0:0", (struct sq_sockaddr_in *)&serv_addr.sq_sin_addr);
#endif
    serv_addr.sq_sin_family = SQ_AF_INET;
    serv_addr.sq_sin_port = htons(5060);
    
    int len = sendto(sfd, data, data_len, MSG_FASTOPEN, (struct sockaddr *) &serv_addr, sizeof(serv_addr));
    printf("error: %d-%s\n", errno, strerror(errno));
    close(sfd);
}

```

server.c

```c
#include <stdio.h>
#include <string.h>
#include <sys/types.h> /* See NOTES */
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>

#define USE_IPV6 1
#if USE_IPV6

typedef struct in6_addr sq_in_addr;
typedef struct sockaddr_in6 sq_sockaddr_in;

#define SQ_AF_INET      AF_INET6
#define SQ_PF_INET      PF_INET6
#define sq_sin_family   sin6_family
#define sq_sin_port     sin6_port
#define sq_sin_addr     sin6_addr
#define sq_s_addr       s6_addr
#define SQ_INADDR_ANY   IN6ADDR_ANY_INIT

#define CLIENT_IP_HEX_LEN 32
#define SQ_MAX_IP_STR_LENGTH 40

#else

typedef struct in_addr sq_in_addr;
typedef struct sockaddr_in sq_sockaddr_in;

#define SQ_AF_INET      AF_INET
#define SQ_PF_INET      PF_INET
#define sq_sin_family   sin_family
#define sq_sin_port     sin_port
#define sq_sin_addr     sin_addr
#define sq_s_addr       s_addr
#define SQ_INADDR_ANY   INADDR_ANY

#define CLIENT_IP_HEX_LEN 8
#define SQ_MAX_IP_STR_LENGTH 16
#endif /* USE_IPV6 */


int main(){
    char buffer[256] = {0x0};
    sq_sockaddr_in serv_addr;
    int cfd = 0;
    int sfd = socket(SQ_AF_INET, SOCK_STREAM, 0);   // Create socket

    bzero((char *) &serv_addr, sizeof(serv_addr));
    serv_addr.sq_sin_family = SQ_AF_INET;
#if USE_IPV6
    inet_pton(SQ_AF_INET, "0:0:0:0:0:0:0:0", (struct sq_sockaddr_in *)&serv_addr.sq_sin_addr);
#else
    inet_pton(SQ_AF_INET, "0:0:0:0", (struct sq_sockaddr_in *)&serv_addr.sq_sin_addr);
#endif
    serv_addr.sq_sin_port = htons(5060);
    bind(sfd, (const struct sockaddr *)&serv_addr,sizeof(serv_addr));      // Bind to well known address

    int qlen = 5;                 // Value to be chosen by application
    int err = setsockopt(sfd, IPPROTO_TCP/*SOL_TCP*/, 23/*TCP_FASTOPEN*/, &qlen, sizeof(qlen));

    listen(sfd, 1);                // Mark socket to receive connections

    cfd = accept(sfd, NULL, 0);   // Accept connection on new socket
    while(1){
        int len = read(cfd, buffer, 256);
        if(len > 0)
             printf("tcp fast open: %s\\n", buffer);
        else
             break;
        // read and write data on connected socket cfd
    }
    close(cfd);
}
```

