队列管理器

创建队列管理器

命令：crtmqm <qmgrName>
示例：
crtmqm -ld /var/mqm/log -lc -lf 16384 -lp 25 -ls 5 QMCIS
参数说明：
-ld 日志文件目录
-lc 使用循环日志
-lf 日志文件页大小
-lp 主日志文件个数
-ls 辅助日志文件个数
QMCIS：队列管理器名
启动队列管理器

命令：strmqm <qmgrName>
停止队列管理器
命令：endmqm -c|-w|-i|-p <qmgrName>
参数说明：
-c      受控关闭（或停顿关闭）。这是缺省值。
队列管理器停止，但仅当所有应用程序已断开连接后才停止。当前正在处理的任何 MQI 调用已完成。
立即将控制权返回给您，并且不通知您队列管理器是何时停止的。
对通过服务器连接通道连接的任何客户机应用程序的影响等效于以 QUIESCE 方式发出的 STOP CHANNEL 命令。
-w   等待关闭。
此类型的关闭等效于受控关闭（除了仅当队列管理器已停止后才将控制权返回给您之外）。当执行关闭时，您将接收到消息：等待队列管理器 qmName 结束。
对通过服务器连接通道连接的任何客户机应用程序的影响等效于以 QUIESCE 方式发出的 STOP CHANNEL 命令。
-i     立即关闭。队列管理器在完成所有当前正在处理的 MQI 调用后停止。任何在该命令发出后发出的 MQI 请求都将失败。当队列管理器再次启动时，任何未完成的工作单元将回滚。
队列管理器结束后返回控制权。
对通过服务器连接通道连接的任何客户机应用程序的影响等效于以 FORCE 方式发出的 STOP CHANNEL 命令。
-p    抢先关闭。
仅在意外情况下使用此类型的关闭。例如，当队列管理器在常规 endmqm 命令下未停止。
队列管理器可以停止而不等待应用程序断开连接或 MQI 调用完成。这可能会产生 WebSphere MQ 应用程序的不可预测的结果。关闭方式设置为立即关闭。如果队列管理器稍后未停止，关闭方式将升级，且终止所有剩余的的队列管理器进程。
对通过服务器连接通道连接的任何客户机应用程序的影响等效于以 TERMINATE 方式发出的 STOP CHANNEL 命令。
删除队列管理器

命令：dltmqm
显示队列管理器启动状态

命令：dspmq
命令结果显示示例：
QMNAME(Test)                                              STATUS(正在运行)
QMNAME(Test2)                                             STATUS(已正常结束)
管理本地MQ对象

使用 MQSC 命令执行本地管理任务
使用MQSC命令执行定义或修改队列、通道等操作。MQSC命令可以有以下两种使用方式：
交互地使用 MQSC 命令：runmqsc [qmgrName]
从文本文件运行 MQSC 命令：runmqsc [qmgrName] < myprog.in  > myprog.out
命名 WebSphere MQ 对象的规则：
WebSphere MQ 认证信息、通道、客户机通道、侦听器、名称列表、进程、队列、服务和存储类对象存在于各自独立的对象名称空间中，因此，每个不同类型的对象都可以有相同的名称。但是，同一个名称空间中的对象不能与任何其它的对象同名。（例如，本地队列不能和模型队列有相同的名称。）WebSphere MQ 中的名称都区分大小写；但是，您应该记住不包含在引号中的小写字符将转换为大写。
显示或修改队列管理器属性
display qmgr  (display可简写为dis)
示例：
alter qmgr CCSID(819)         #修改字符集
def ql(DEADQ) defpsist(yes) maxdepth(20000) replace
alter qmgr deadq(DEADQ)    #设置死信队列为DEADQ
队列

定义本地队列示例
def ql(FROMCCPC_1) maxdepth(10000) defpsist(yes) replace
参数说明：
ql(FROMCCPC_1)： 队列本地名称
maxdepth：队列上允许的最大消息数；
defpsist：队列中消息持久性默认值。
NO 该队列上的消息在队列管理器重新启动时丢失
YES 该队列上的消息在队列管理器重新启动时保存了下来。
关于消息在队列中的保存时间：消息在队列的保存时间与三个设置有关：队列defpsist属性、消息Persistence持久性属性和消息Expiry消息到期时间属性，其中队列defpsist属性是在创建队列时设置，消息Persistence和Expiry属性是应用程序往队列放入消息时指定。消息本身的Persistence值优先于队列defpsist值。Expiry指消息到期时间，即经过指定的时间后，消息如果还没被取走，此消息将过期（无效）。消息过期后，可能会自动从队列中删除（取决于不同操作系统的MQ实现）。对于非持久性消息，即使Expiry设为永不过期，重启队列管理器时，消息也将丢失。
定义远程队列示例
def qr(TOCCPC_1) rname(5819_1) rqmname(QMC5819) xmitq(QMGF) defpsist(yes) replace
参数说明：
qr(TOCCPC_1)：队列本地名称，名字可任意取；
rname(5819_1)：远程队列名，必须与远程队列管理器中的本地队列名相同；
rqmname(QMC5819)：远程队列管理器名，必须与远程队列管理器名相同；
xmitq(QMGF)：本地传输队列名称。
定义传输队列示例
def ql(QMGF) usage(XMITQ) defpsist(YES) maxdepth(50000) trigger trigtype(FIRST) trigdata(999581030604.5819) initq(SYSTEM.CHANNEL.INITQ) replace
参数说明：
ql (QMGF)：队列本地名称，名字可任意取；
usage(XMITQ)：指定此本地队列为传输队列，参数值固定填“XMITQ”；
trigger：启动触发（相应的，NOTRIGGER为禁用触发）
trigtype(FIRST) ：触发方式，建议值为“FIRST”，即第一条消息触发；
trigdata(999581030604.5819)：触发数据，值为传输队列将使用的发送通道名；
initq(SYSTEM.CHANNEL.INITQ)：触发队列，固定填“SYSTEM.CHANNEL.INITQ”。
触发器在此的作用是当传输队列接收到第一条消息时，将触发启动指定的发送通道。
查看队列
dis ql(qname) all     显示队列所有属性
dis ql(qname) curdepth 显示队列当前深度，即当前队列中存放的消息数
dis qs(qname) type(queue) all显示与队列相关的状态信息
dis qs(qname) type(handle) all显示与访问队列的句柄相关的状态信息
删除队列
delete ql(qname)
删除队列中的消息
clear ql(qname)
注：删除队列中的消息时出错时处理
尝试执行命令RESOLVE CHANNEL(channel_name) ACTION( COMMIT )后，再执行clear ql命令。
通道

定义接收通道
def chl(999581000107.5819) chltype(rcvr) trptype(tcp) replace
参数描述：
chl(999581000107.5819)：接收通道名称，必须与远程队列管理器中的发送对列名称相同。推荐的命名规则为 “远程队列管理器名.本地队列管理器名”；
chltype(rcvr)：指定通道为类型，值固定为“rcvr”；
trptype(tcp)：通讯协议，除非特别指明，一般采用“tcp”协议。
定义发送通道
def chl(5819.999581000107) chltype(SDR) discint(0) conname('10.2.106.106(1417)') xmitq(QMCIS) trptype(tcp) replace
定义服务器连接通道
def chl() chltype() replace
删除通道
Delete chl(channel name)
查看通道状态
dis chs(channel name)
通道的当前状态，它可以是正在启动、正在绑定、正在初始化、正在运行、正在停止、正在重试、已暂停、已停止、正在请求和通道状态未找到。
正常运行时，通道状态为“正在运行”；
关闭通道时，通道状态为“已停止”；
如果通道状态长时间处于“正在绑定”（binding）、“正在重试”（retrying），则表明通道不正常，需要人工介入。这时可查看队列管理器日志文件，日志文件中一般会提供异常原因。
关于“通道状态未找到”：此状态值仅表示当前没有建立到此通道的连接，并不能表示通道是否正常。
启动通道
Start chl(channel name)
关闭通道
Stop chl(channel name)
重置通道
reset chl(channel name)
监听器

定义监听器
def listener(QMC5819) TRPTYPE(TCP) PORT(1417) CONTROL(STARTONLY) BACKLOG(0) replace
参数描述：
listener(QMC5819)：监听器名，名称可任意取；
TRPTYPE(TCP)：通讯协议类型；
PORT（1417）：服务监听端口
CONTROL(string）指定如何启动和停止侦听器：
MANUAL 不会自动启动或自动停止侦听器。通过使用 START LISTENER 命令和 STOP LISTENER 命令来控制它。这是缺省值。

QMGR      当队列管理器启动和停止时，同时启动和停止被定义的侦听器。

STARTONLY 当队列管理器启动时，同时启动侦听器，但当队列管理器停止时，不请求停止侦听器。

启动监听器
start listener(QMC5819)
关闭监听器
stop listener(QMC5819)
查看监听器状态
dis lsstatus(LISTENER.TCP) all (QMC5819) all
WebSphere MQ 配置文件 mqs.ini

WebSphere MQ 配置文件 mqs.ini 包含和节点上所有队列管理器都相关的信息。它在安装期间自动创建。
WebSphere MQ for UNIX 系统的 mqs.ini 文件在 /var/mqm 目录中。它包含：
队列管理器的名称
缺省队列管理器的名称
和每个文件关联的文件位置
图 9 显示 WebSphere MQ 配置文件的示例：
图 9. UNIX(R) 系统的 WebSphere MQ 配置文件示例
#***********************************************************************#
#* Module Name: mqs.ini                                                *#
#* Type       : WebSphere MQ Machine-wide Configuration File           *#
#* Function   : Define WebSphere MQ resources for an entire machine    *#
#***********************************************************************#
#* Notes      :                                                        *#
#* 1) This is the installation time default configuration              *#
#*                                                                     *#
#***********************************************************************#
AllQueueManagers:
#***********************************************************************#
#* The path to the qmgrs directory, below which queue manager data     *#
#* is stored                                                           *#
#***********************************************************************#
DefaultPrefix=/var/mqm
LogDefaults:
   LogPrimaryFiles=3
   LogSecondaryFiles=2
   LogFilePages=1024
   LogType=CIRCULAR
   LogBufferPages=0
   LogDefaultPath=/var/mqm/log
QueueManager:
   Name=saturn.queue.manager
   Prefix=/var/mqm
   Directory=saturn!queue!manager
QueueManager:
   Name=pluto.queue.manager
   Prefix=/var/mqm
   Directory=pluto!queue!manager
DefaultQueueManager:
   Name=saturn.queue.manager
   ApiExitTemplate:
      Name=OurPayrollQueueAuditor
      Sequence=2
      Function=EntryPoint
   Module=/usr/ABC/auditor
      Data=123
   ApiExitCommon:
      Name=MQPoliceman
      Sequence=1
      Function=EntryPoint
   Module=/usr/MQPolice/tmqp
      Data=CheckEverything
队列管理器配置文件 qm.ini

队列管理器配置文件（qm.ini）包含特定队列管理器的相关信息。每个队列管理器都有一个队列管理器配置文件。创建和 qm.ini 文件关联的队列管理器时，将自动创建此文件。
qm.ini 文件保存在队列管理器占用的目录树的根中。例如，队列管理器 QMNAME 的配置文件的路径和名称是：
/var/mqm/qmgrs/QMNAME/qm.ini
队列管理器名称可长达 48 个字符的长度。但是，这并不保证此名称是有效的或唯一的。因此，目录名称是基于队列管理器名称生成的。这个过程也称为名称变换。有关描述，请参阅理解 WebSphere MQ 文件名。
图 10 显示在 WebSphere MQ for UNIX 系统中如何在队列管理器配置文件中排列属性的组。
图 10. WebSphere MQ for UNIX 系统的队列管理器配置文件示例
#* Module Name: qm.ini                                             *#
#* Type       : WebSphere MQ queue manager configuration file      *#
#  Function   : Define the configuration of a single queue manager *#
#*                                                                 *#
#*******************************************************************#
#* Notes      :                                                    *#
#* 1) This file defines the configuration of the queue manager     *#
#*                                                                 *#
#*******************************************************************#
ExitPath:
   ExitsDefaultPath=/var/mqm/exits
   ExitsDefaultPath64=/var/mqm/exits64
 Service:
   Name=AuthorizationService
   EntryPoints=13
  ServiceComponent:
   Service=AuthorizationService
   Name=MQSeries.UNIX.auth.service
   Module=/opt/mqm/bin/amqzfu 1
   ComponentDataSize=0
Log:
   LogPrimaryFiles=3
   LogSecondaryFiles=2
   LogFilePages=1024
   LogType=CIRCULAR
   LogBufferPages=0
   LogPath=/var/mqm/log/saturn!queue!manager/
XAResourceManager:
  Name=DB2 Resource Manager Bank
  SwitchFile=/usr/bin/db2swit
  XAOpenString=MQBankDB
   XACloseString=
  ThreadOfControl=THREAD
Channels: 2
   MaxChannels=20
   MaxActiveChannels=100
   MQIBindType=STANDARD
TCP:
   KeepAlive = Yes
QMErrorLog:
   ErrorLogSize=262144
   ExcludeMessage=7234
   SuppressMessage=9001,9002,9202
   SuppressInterval=30
   ApiExitLocal:
      Name=ClientApplicationAPIchecker
      Sequence=3
      Function=EntryPoint
   Module=/usr/Dev/ClientAppChecker
      Data=9.20.176.20   
MQ日志

队列管理器日志
从 WebSphere MQ 资源管理器使用日志队列管理器属性页，或 qm.ini 文件中的 Log 节来指定与此队列管理器上的日志记录相关的信息。
缺省情况下，这些设置从为队列管理器的缺省日志设置指定的设置继承（在WebSphere MQ 的日志缺省中描述）。如果您要以不同的方法配置此队列管理器，则仅更改这些设置。
有关计算日志大小的信息，请参阅计算日志的大小。
注：
以下参数列表中给出的限制由 WebSphere MQ 设置。操作系统限制可能减少最大可能的日志大小。
LogPrimaryFiles=3|2-254 (Windows)|2-510 (UNIX 系统)
创建队列管理器时分配的日志文件。
主日志文件的最小数目是 2，最大数目在 Windows 上是 254，在 UNIX 系统上是 510。缺省值是 3。
主日志文件和辅助日志文件的总数在 Windows 不能超过 255，在 UNIX 系统上不能超过 511，并且不能小于 3。
创建或启动队列管理器时检查此值。创建队列管理器后，您可更改它。但是，在重新启动队列管理器前值的更改不是有效的，不会立即产生作用。
LogSecondaryFiles=2|1-253 (Windows)|1-509 (UNIX 系统)
耗尽主文件时分配的日志文件。
辅助日志文件的最小数目是 1，在 Windows 上最大数目是 253，在 UNIX 系统上最大数目是 509。缺省数目是 2。
主日志文件和辅助日志文件的总数在 Windows 不能超过 255，在 UNIX 系统上不能超过 511，并且不能小于 3。
启动队列管理器时检查此值。您可更改此值，但是在重新启动队列管理器前更改不会有效，而且然后也不会立即发生作用。
LogFilePages=number
日志数据保存在称为日志文件的一系列文件中。日志文件大小以 4 KB 页为单位指定。
在 WebSphere MQ for UNIX 系统中，日志文件页数的缺省数是 1024，日志文件大小是 4 MB。日志文件的最小页数是 64，最大页数是 65 535。
在 WebSphere MQ for Windows 中，日志文件页数的缺省值是 256，日志文件大小是 1 MB。日志文件的最小页数是 32，最大页数是 65 535。
注：
创建队列管理器期间指定的日志文件大小不能因为队列管理器而更改。
LogType=CIRCULAR|LINEAR
队列管理器使用的日志记录类型。一旦创建了队列管理器，您就无法更改将使用的日志记录的类型。请参阅WebSphere MQ 的日志缺省中的 LogType 属性的描述，以获取关于创建具有您需要的日志记录类型的队列管理器的信息。
CIRCULAR
使用日志回滚系统停止时在处理中的事务来启动重新启动恢复。
请参阅循环日志记录 以获取更全面的循环日志记录的说明。
LINEAR
对于重新启动恢复和介质或正向恢复（通过重放日志内容来创建丢失或损坏的数据）。
请参阅线性日志记录以获取更全面的线性日志记录的说明。
LogBufferPages=0|0-4096
分配给缓冲区记录进行写操作的内存数量，以 4 KB 页为单位指定缓冲区大小。
缓冲区页的最小数量是 18，最大数量是 4096。缓冲区越大，其吞吐量越高，尤其是那些较大的消息。
如果您指定 0（缺省值），则队列管理器选择此大小。在 WebSphere MQ V6.0 中，这是 128（512 KB）。
如果您指定一个 1 到 17 之间的数，则队列管理器缺省是 18（72 KB）。如果您指定一个 18 和 4096 之间的数，则队列管理器使用指定的用于设置内存分配的数。
创建或启动队列管理器时检查此值，在这两个时候值可能会增加或减少。但是，对此值的更改在重新启动队列管理器之后才会有效。
LogPath=directory_name
队列管理器的日志文件所在的目录。该目录必须存在于队列管理器能写的本地设备上，更好的情况是该目录与消息队列在不同的驱动器上。指定不同的驱动器能在系统发生故障的情况下提供更好的保护。
缺省值是：
在 WebSphere MQ for Windows 中是 C:\Program Files\IBM\WebSphere MQ\log。
在 WebSphere MQ for UNIX 系统中是 /var/mqm/log。
您可以使用 -ld 标志在 crtmqm 命令中指定目录的名称。创建队列管理器时，在队列管理器目录下还会创建一个目录，此目录用于保存日志文件。这个目录的名称基于队列管理器名称。这确保日志文件路径是唯一的，并确保它符合目录名称长度的任何限制。
如果您没有在 crtmqm 命令中指定 -ld，则使用 LogDefaultPath 属性的值。
在 WebSphere MQ for UNIX 系统中，用户标识 mqm 和组 mqm 必须具有对日志文件的全部权限。如果您更改这些文件的位置，则必须自己提供这些权限。如果日志文件位于随此产品一起提供的缺省位置，则这不是必需的。
LogWriteIntegrity=SingleWrite|DoubleWrite|TripleWrite
记录器使用的用于可靠地写日志记录的方法。
SingleWrite
某些硬件保证当写操作写一个页并因任何原因失败时，对缓冲区中相同页的后续读操作会导致缓冲区中的每个字节出现下列两种情况：
是与写操作之前相同的字节，或
是在写操作中已写的字节
在此类硬件上（例如，启用 ssa 写高速缓存），因为该硬件可以保证完全的写完整性，所以记录器在单个写操作中写日志记录是很安全的。此方法提供最高级别的性能。
DoubleWrite
DoubleWrite 方法是在 WebSphere MQ V5.2 中使用的缺省方法，且只可用于向后兼容性的用途。
TripleWrite
这是缺省方法。在确定写完整性不可用的硬件中，使用 TripleWrite 方法写入日志记录，因为 TripleWrite 方法提供完整的写完整性。
计算日志的大小
确定队列管理器应该使用循环日志记录还是线性日志记录后，您需要估计该队列管理器需要的日志大小。日志大小是由以下日志配置参数确定的：
LogFilePages
每个主日志文件和辅助日志文件的大小（以 4K 页为单位）
LogPrimaryFiles
预分配的主日志文件数
LogSecondaryFiles
可创建的辅助日志文件数（主日志文件满时使用）
表 17显示队列管理器为各种操作记录的数据量。大多数队列管理器操作需要的日志空间量都很少。但是，当持久消息放入队列时，所有消息数据都必须写入日志，以便能恢复此消息。通常，日志大小取决于队列管理器需要处理的持久消息的数量和大小。
表 17. 日志开销大小（所有值都是近似的）
操作
大小
放入持久消息
750 字节 + 消息长度
如果是大型消息，则分隔成 15700 字节的段，每个段可以有 300 字节的开销。
取出消息
260 字节
同步点，落实
750 字节
同步点，回滚
1000 字节 + 12 字节（要回滚的每个取出或放入操作）
创建对象
1500 字节
删除对象
300 字节
改变属性
1024 字节
记录介质映像
800 字节 + 映像
映像分隔成 260 000 字节的段，每个段有 300 字节开销。
检查点
750 字节 + 200 字节（每个活动的工作单元）
可以为任何未落实的放入或取出（由于性能原因而进行了缓存）记录其它数据。
注：
每当队列管理器启动时，您都可以更改主日志文件和辅助日志文件的数目。
您无法更改日志文件大小；必须在创建队列管理器前确定它。
主日志文件的数量和日志文件大小确定创建队列管理器时预分配的日志空间的量。
主日志文件和辅助日志文件的总数在 UNIX 系统上不能超过 511，在 Windows 上不能超过 255，当存在长期运行事务时，这会限制队列管理器可用于重新启动恢复的最大日志空间量。队列管理器用于介质恢复所需的日志空间量不共享此极限。
使用循环日志记录时，队列管理器复用主日志空间。这意味着队列管理器的日志可以比您估计的队列管理器需要记录的数据量要小。日志文件满时，队列管理器将分配一个辅助日志文件（最多到极限值），并且序列中的下一个主日志文件不可用。
主日志文件可用于检查点期间的复用。由于日志空间量的减少，因此使用检查点前，队列管理器会考虑主日志和辅助日志空间。
如果您不定义比辅助日志文件更多的主日志文件，则队列管理器在采用检查点前可以分配辅助日志文件。这使得主日志文件可以复用。
错误日志
WebSphere MQ 使用了许多错误日志来捕获相关消息，这些消息与 WebSphere MQ 自身的操作、您启动的任何队列管理器以及来自正在使用的通道的错误数据有关。
错误日志的位置取决于队列管理器名称是否是已知的，以及错误是否与客户机有关。
如果队列管理器名称是已知的，则错误日志的位置如表 18 中所示。
表 18. 队列管理器的错误日志目录
平台
目录
UNIX 系统
/var/mqm/qmgrs/qmname/errors
Windows 系统
c:\Program Files\IBM\WebSphere MQ\qmgrs\qmname\errors
如果队列管理器名称是未知的，则错误日志的位置如表 19 中所示。
表 19. 系统错误日志目录
平台
目录
UNIX 系统
/var/mqm/errors
Windows 系统
c:\Program Files\IBM\WebSphere MQ\errors
如果是客户机应用程序发生了错误，则客户机上错误日志的位置如表 20 中所示。
表 20. 客户机错误日志目录
平台
目录
UNIX 系统
/var/mqm/errors
Windows 系统
c:\Program Files\IBM\WebSphere MQ Client\errors
在 WebSphere MQ for Windows 中，对错误的指示也同样添加到应用程序日志中，可以用与 Windows 系统一起提供的“事件查看器”应用程序来检查应用程序日志。
在安装期间，将在 UNIX 系统上的 /var/mqm 文件路径中，以及 Windows 系统上的 \IBM\WebSphere MQ\ 文件路径中创建 errors 子目录。errors 子目录最多可以包含 3 个错误日志文件，它们是：
AMQERR01.LOG
AMQERR02.LOG
AMQERR03.LOG
在创建了队列管理器后，队列管理器将在需要时创建 3 个队列管理器错误日志文件。这些文件的名称与它们在系统错误日志目录中的名称一样，即 AMQERR01、AMQERR02 和 AMQERR03，并且每个文件的缺省容量都是 256 KB。可从 WebSphere MQ 资源管理器中的扩展队列管理器属性页，或 qm.ini 文件中的 QMErrorLog 节来更改容量。在 UNIX 系统上，这些文件位于 /var/mqm/qmgrs/qmname 文件路径的 errors 子目录中，在 Windows 系统上，这些文件位于 \IBM\WebSphere MQ\qmgrs\qmname\errors 文件路径中。
错误消息生成后将被放在 AMQERR01 中。AMQERR01 大于 256 KB 时，将被复制到 AMQERR02 中。在此复制前，AMQERR02 被复制到 AMQERR03.LOG 中。而 AMQERR03 中以前的内容（如果有的话）将废弃。
这样，最新的错误消息总是放在 AMQERR01 中，而其它文件则用于保存错误消息的历史。
所有与通道相关的消息也将放在相应队列管理器的错误文件中，除非该队列管理器不可用或它的名称是未知的，此时，与通道相关的消息将放在系统错误日志目录中。
要检查任何错误日志文件的内容，请使用常用的系统编辑器。
早期的错误

还存在这些错误日志还没有建立就发生错误的特殊情况。WebSphere MQ 尝试在错误日志中记录任何这样的错误。日志的位置取决于建立了多少队列管理器。
如果由于损坏了配置文件，例如，不能确定位置信息，则将错误记录到根目录（/var/mqm 或 C:\Program Files\IBM\WebSphere MQ）中的在安装期间创建的 errors 目录中。
如果 WebSphere MQ 可以读取其配置信息，并且可以访问 Default Prefix 的值，则错误记录在由 Default Prefix 属性确定的目录的 errors 子目录中。例如，如果缺省前缀为 C:\Program Files\IBM\WebSphere MQ，则错误在 C:\Program Files\IBM\WebSphere MQ\errors 中记录。
要获取有关配置文件的更多信息，请参阅配置 WebSphere MQ。
注：
在 Windows 注册表中的错误由启动队列管理器时的消息通知的。
错误日志示例

图 23 显示了从 WebSphere MQ 错误日志抽取的内容：
图 23. WebSphere MQ 错误日志样本
17/11/2004  10:32:29 － 进程（2132.1） 用户（USER_1） 程序（runmqchi.exe）
AMQ9542: 队列管理器正在结束。
说明：
由于队列管理器已停顿，所以该程序将结束。
操作：
无。