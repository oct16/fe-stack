## 三次握手

>***为什么是三次而不是二次或四次？***
为了防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误

例如，client发出的报文并没有丢失但是在过程中延迟了，超过了连接释放的时间，接着server收到本应失效的请求，却认为这是一个新的请求

> TCP 的可靠连接是靠 seq（ sequence numbers 序列号）来达成的。TCP 设计中一个基本设定就是，通过TCP 连接发送的每一个包，都有一个sequence number。而因为每个包都是有序列号的，所以都能被确认收到这些包。但是seq没有绑定到整个网络的全局时钟以及 TCPs 可能有不同的机制来选择 ISN（初始序列号），所以，接收方一定需要跟发送方确认 SYN


```
TCP A                                                TCP B
​
  1.  CLOSED                                               LISTEN
​
  2.  SYN-SENT    --> <SEQ=100><CTL=SYN>               --> SYN-RECEIVED
​
  3.  ESTABLISHED <-- <SEQ=300><ACK=101><CTL=SYN,ACK>  <-- SYN-RECEIVED
​
  4.  ESTABLISHED --> <SEQ=101><ACK=301><CTL=ACK>       --> ESTABLISHED
​
  5.  ESTABLISHED --> <SEQ=101><ACK=301><CTL=ACK><DATA> --> ESTABLISHED
​
          Basic 3-Way Handshake for Connection Synchronization
​
                                Figure 7.
```

### 第一次握手
##### 客户端 -> 服务端
SYN=1, seq=k
客户端进入SYN_SEND

### 第二次握手
##### 服务端 ->客户端
SYN=1, ack=k+1, seq=j
服务端进入SYN_RECV

### 第三次握手
##### 客户端 -> 服务端
ack=j+1
客户端进入ESTABLISHED
服务端进入ESTABLISHED

### Referer
TCP 为什么是三次握手，而不是两次或四次？ - HioHio的回答 - 知乎
https://www.zhihu.com/question/24853633/answer/573627478