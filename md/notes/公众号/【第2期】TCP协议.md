## TCP协议
TCP，全称Transmission Control Protocol，传输控制协议。**双工**协议。是主机对主机层的传输控制协议，提供可靠的连接服务。

对于前端而言，最常用的就是需要理解“三次握手”和“四次挥手”这两个重要的解决方案。

这两个方案是为了解决连接建立和连接关闭的问题的。

## 建立连接
> 例子：打电话/视频时，双方如何确定自己能够听到对方的声音，对方能够听到自己的声音呢？
> 具体场景：
> A->B：你可以听到吗？（A发起询问，以确定B能否听到自己的声音？）
> B->A：可以。你呢？（B听到A的询问，B能确定能够听到A的声音**B←A**！B发起询问，确定A能否听到自己的声音？当A听到B的回复时，A能确定能听到B的声音**A←B**，且B能听到自己的声音**A→B**）
> A->B：可以，我们开始说话吧。（当B收到A的回复时，B能确定A能听到自己的声音**B→A**）

图示如下：
![](https://mmbiz.qpic.cn/mmbiz_png/meG6Vo0Mevh5GSvJvRLPkCybNicq27Q8WkpUWQiate0JIUXYJ6iaSeyfxhfAia4Z464UyRibmZhrymRicicuFRS2ZSBiaQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

同理，“三次握手”也是如此：
![](https://mmbiz.qpic.cn/mmbiz_png/meG6Vo0Mevh5GSvJvRLPkCybNicq27Q8WRxZz6JzsB1uHsKJ1MFUWrLIFZUeJJZNbTXbxPYmNVdBRib8Q6krQ2OQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

