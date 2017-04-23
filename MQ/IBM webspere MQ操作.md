查看队列管理器身份认证：
dis chlauth(*)

添加IP认证：
set CHLAUTH(DC.SVRCONN)                     TYPE(ADDRESSMAP) ADDRESS(190.6.62.246)                    USERSRC(CHANNEL)

不能关闭队列管理器
ps -ef| grep QM_ESB
查看通道信息
dis chs(*)

start CHANNEL(channelname)

一、WebSphereMQ常用命令：QM（队列管理器名）QM_QUEUE（队列名）
1、创建队列管理器
crtmqm QM

2、删除队列管理器
dltmqm QM

3、查看队列管理器运行状态是否为运行（running）
dspmq -m QM

4、停止队列管理器
endmqm　-i QM

不能关闭队列管理器
ps -ef| grep QM_ESB
kill -9

5、启动队列管理器
strmqm QM

显示命令服务器
dspmqcsv QM

停止命令服务器
endmqcsv QM

启动命令服务器
strmqcsv QM

6、放入测试消息命令
amqsput QM_QUEUE QM(将消息逐行放入队列，每行代表一条消息，直到输入空行结束)

7、取出消息命令
amqsget BUSINESS_LOG_CONTENT_QUEUE QM(把所有消息取出)

8、浏览消息命令
amqsbcg QM_QUEUE QM(浏览消息而不取出)

9、用户交互管理界面程序runmqsc，用end退出交互模式
runmqsc QM

二、runmqsc命令：CHLNAME（通道名称）QUEUENAME（队列名称）LISTENERNAME（侦听器名称）
1、查看自定义的发送通道(不是以'SYSTEM.'命名开头)
dis chl(*)  CHLTYPE(SDR)

2、查看自定义的接收通道(不是以'SYSTEM.'命名开头)
dis chl(*)  CHLTYPE(RCVR)

3、查看通道状态
dis chstatus(CHLNAME) ---STATUS

4、停止通道
stop chl(CHLNAME) MODE(FORCE)

STOP CHANNEL(DC.SVRCONN) MODE(TERMINATE) STATUS(STOPPED)

5、启动通道
start chl(CHLNAME)

6、重置通道
reset chl(CHLNAME)

reset chl(CHLNAME) SEQNUM(1) 重置通道序列号为1

7、查看队列当前深度和最大深度
dis queue(REALTIME_RECV) CURDEPTH MAXDEPTH
dis queue(REALTIME_SEND) CURDEPTH MAXDEPTH
查看传输队列的当前深度和最大深度
dis queue(XMITQ) CURDEPTH MAXDEPTH
dis queue(ASYNC_REQUEST_QUEUE) CURDEPTH MAXDEPTH
dis queue(BUSINESS_LOG_CONTENT_QUEUE) CURDEPTH MAXDEPTH
dis queue(ASYNC_RESPONSE_QUEUE) CURDEPTH MAXDEPTH

8、查看自定义的侦听器（不是以'SYSTEM.'命名开头）
dis listener(*)

查看端口
dis LISTENER(LISTENER)

9、查看侦听器状态
dis lsstatus(LISTENERNAME) STATUS

10、停止侦听器
STOP LISTENER(LISTENERNAME)

11、启动侦听器
START LISTENER(LISTENERNAME)

定义监听器
def listener(LISTENER) TRPTYPE(TCP)  PORT(1414) BACKLOG(0) CONTROL(QMGR)


// 将队列管理器设置为中文字符集
ALTER QMGR CCSID (1381)
// 将队列管理器的死信队列设为Q1，缺省传输队列强行设置为Q2
ALTER QMGR DEADQ (Q1) DEFXMITQ (Q2) FORCE
// 显示队列管理器的全部属性
DISPLAY QMGR
// 显示队列管理的字符集
DISPLAY QMGR CCSID

// 创建本地队列Q
DEFINE QLOCAL (Q)
// 将本地队列Q 的最大深度属性设置为5
ALTER QLOCAL (Q) MAXDEPTH (5)
// 重新创建本地队列Q。如果Q 已经存在，则将其属性全部重置为缺省属性。
// 注意，这时队列中原有的消息仍然保留。
DEFINE QLOCAL (Q) REPLACE
// 删除本地队列Q
DELETE QLOCAL (Q)

// 创建接收端通道 C
DEFINE CHANNEL (C) CHLTYPE (RCVR)
// 创建发送方通道 C，连接对方的 IP 地址为 127.0.0.1，端口为 1414，通道连接传输队列XQ
DEFINE CHANNEL (C) CHLTYPE (SDR) CONNAME ('127.0.0.1 (1414)') XMITQ (XQ)
// 删除通道 C
DELETE CHANNEL (C)
// 修改通道 C 的批次消息数量为 50
ALTER CHANNEL (C) CHLTYPE (SDR) BATCHSZ (50)
// 显示通道 C 的心跳间隔
DISPLAY CHANNEL (C) HBINT

PING CHANNEL 只检查通道能否连通，而不检查目前是否连通
PING CHANNEL(channel_name) [DATALEN(16 | integer)]


查看运行中的通道状态
DIS CHS(channel_name) ALL

查看队列管理器的身份认证是否打开
dis qmgr chlauth

设置身份认证
alter qmgr chlauth(enabled) 打开身份认证
alter qmgr chlauth(disabled) 关闭身份认证



所以，在发送方出现不确定状态时，只需要比较一个发送方的 CURLUWID 和接收方的
TLUWID，就可以知道该批消息在接收端是否已经提交，从而在发送方做出相应的动作
可。具体步骤如下：
比较双方的 LUWID
l  对于不确定 (In-doubt)  状态的发送端：
DISPLAY CHSTATUS(name) SAVED CURLUWID
l  对于接收端：
DISPLAY CHSTATUS(name) SAVED LSTLUWID
2)  如果两者相同，说明该批消息在接收端已经完整地收到并提交。在发送端执行：
RESOLVE CHANNEL(name) ACTION(COMMIT)
3)  如果两者不同，说明该批消息在接收端未能完整地收到并提交。在发送端执行：
RESOLVE CHANNEL(name) ACTION(BACKOUT)


HP机安装MQ命令
swinstall -s /tmp/mqm/p600-100-051021.v11 -x allow_incompatible=true

验证查看MQ版本：dspmqver
