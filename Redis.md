[toc]

# *Redis* 简介

​	*Redis* 是一个使用 *C* 语言编写的，开源的（*BSD*许可）高性能非关系型（*NoSQL*）的键值对数据库。

​	*Redis* 可以存储键和五种不同类型的值之间的映射。键的类型只能为字符串，值支持五种数据类型：字符串、列表、集合、散列表、有序集合。

​	与传统数据库不同的是 *Redis* 的数据是存在内存中的，所以读写速度非常快，因此 *redis* 被广泛应用于缓存方向，每秒可以处理超过 10 万次读写操作，是已知性能最快的 *Key*-*Value* *DB*。另外，*Redis* 也经常用来做分布式锁。除此之外，*Redis* 支持事务 、持久化、*LUA*脚本、*LRU*驱动事件、多种集群方案。

# *Redis* 数据结构

​	*Redis*支持的键值类型共有五种，字符串类型(*String*)、散列类型(*hash*)、列表类型(*list*)、集合类型(*set*)和有序集合类型(*zet*)。

​	*Redis*支持的键值数据类型每种都有自己底层的内部编码实现，而且是多种实现，*Redis* 会在合适的场景选择合适的内部编码。

​	这样设计有两个好处：

1. 可以改进内部编码，而对外的数据结构和命令没有影响。
2. 多种内部编码实现可以在不同场景下发挥各自的优势。

## 字符串(String)

​	字符串内部编码分为*int*、*embstr*和*raw*。

>  + *int*：8个字节的长整型。
>  + *embstr*：*embedded string* 嵌入式字符串，小于等于44个字节的字符串。
>  + *raw*：大于44个字节的字符串

## *Redis* 的队列如何异步使用

​	*Redis* 的 *list* 结构可以作为队列使用，*rpush* 生产消息，*lpop* 消费消息，*lpop* 没有取到消息时，可以让线程休眠一会再获取消息

​	*blpop* 指令，在队列没有消息时，会阻塞线程直到消息被生产，然后再获取消息

## *Bitmap* 应用场景

> 1. 用户签到
>
>    *UID* 作为 *key*，当前日期作为偏移量
>
> 2. 统计活跃用户
>
>    当前日期作为 *key*,用户 *id* 作为偏移量，把多天的 *key* 做逻辑与操作
>
> 3. 用户在线状态
>
>    *key* 不限，用户 *id* 作为偏移量，上线就设置1
>
> 4. 布隆过滤器
>
>    做为布隆过滤器的二进制向量

# 为什么要用 *Redis* 而不用 *map*/*guava* 做缓存

​	缓存分为本地缓存和分布式缓存。以  *Java* 为例，使用自带的 *map* 或者 *guava* 实现的是本地缓存，最主要的特点是轻量以及快速，生命周期随着 *jvm* 的销毁而结束，并且在多实例的情况下，每个实例都需要各自保存一份缓存，缓存不具有一致性。

​	使用 *redis* 或 *memcached* 之类的称为分布式缓存，在多实例的情况下，各实例共用一份缓存数据，缓存具有一致性。缺点是需要保持 *redis* 或 *memcached* 服务的高可用，整个程序架构上较为复杂。

# *Redis* 和 *Memcached* 的区别

1. *Memcached* 是多线程，非阻塞 *IO* 复用的网络模型，*Redis* 使用单线程的 *IO* 复用模型、在高并发场景的压力下，多线程非阻塞式*IO*的 *Memcached* 表现会更加优异。

2. *redis* 支持持久化，*memcached* 不支持。

3. *redis* 不仅支持 *key/value*，还支持 *string*、*set*、*zset*、*hash* 等类型，*memcached* 仅支持简单的 *key/value*。

# *Redis* 有哪些优缺点？

优点：

+ 性能极高，能到 100000 次/*s* 读写速度

+ 支持数据的持久化，对数据的更新采用 *Copy*-*on*-*write* 技术，可以异步地保存到磁盘上

+ 丰富的数据类型，*String*(字符串)、*List*(列表)、*Hash*(字典)、*Set*(集合)、*Sorted* *Set*(有序集合)

+ 原子性：*Redis* 的所有操作都是原子性的，多个操作通过 *MULTI* 和 *EXEC* 指令支持事务

+ 丰富的特性：*key* 过期、*publish*/*subscribe*、*notify*

+ 支持数据的备份，快速的主从复制

+ 节点集群，很容易将数据分布到多个*Redis*实例中

缺点：

+ 数据库容量受到物理内存的限制，不能用作海量数据的高性能读写

+ 适合的场景主要局限在较小数据量的高性能操作和运算上

## *Redis*使用单线程模型为什么性能依然很好？

+ 避免了线程切换的资源消耗

+ 单线程不存在资源共享与竞争，不用考虑锁的问题

+ 基于内存的，内存的读写速度非常快

+ 使用非阻塞的 *IO* 多路复用机制

+ 数据存储进行了压缩优化

+ 使用了高性能数据结构，如 *Hash*、跳表等

# *Redis* 单线程模型

​	*redis* 会将每个客户端都关联一个指令队列。客户端的指令通过队列来按顺序处理，先到先服务。

​	在一个客户端的指令队列中的指令是顺序执行的，但是多个指令队列中的指令是无法保证顺序的，例如执行完 *client-0* 的队列中的 *command-0* 后，接下去是执行哪个队列中的第一个指令是无法确定的，但是肯定不会同时执行两个指令。

​	*redis* 同样也会为每个客户端关联一个响应队列，通过响应队列来顺序地将指令的返回结果回复给客户端。

​	同样，一个响应队列中的消息可以顺序的回复给客户端，多个响应队列之间是无法保证顺序的。所有的客户端的队列中的指令或者响应，*redis* 每次都只能处理一个，同一时间绝对不会处理超过一个指令或者响应。

# *Redis* 缓存问题

## 缓存穿透

> 描述：
>
> ​	缓存穿透是指缓存和数据库中都没有的数据，而用户不断发起请求，如发起为*id*为“-1”的数据或*id*为特别大不存在的数据。这时的用户很可能是攻击者，攻击会导致数据库压力过大。
>
> 解决方案：
>
> 1. 接口层增加校验，如用户鉴权校验，*id*做基础校验，*id*<=0的直接拦截；
> 2. 从缓存取不到的数据，在数据库中也没有取到，这时也可以将*key*-*value*对写为*key*-*null*，缓存有效时间可以设置短点，如30秒（设置太长会导致正常情况也没法使用）。这样可以防止攻击用户反复用同一个*id*暴力攻击

## 缓存击穿

> 描述：
>
> ​	缓存击穿是指缓存中没有但数据库中有的数据（一般是缓存时间到期），这时由于并发用户特别多，同时读缓存没读到数据，又同时去数据库去取数据，引起数据库压力瞬间增大，造成过大压力
>
> 解决方案：
>
> 1. 设置热点数据永远不过期。
> 2. 加互斥锁

## 缓存雪崩

>  描述：
>
>  ​	缓存雪崩是指缓存中数据大批量到过期时间，而查询数据量巨大，引起数据库压力过大甚至 *down* 机。和缓存击穿不同的是，缓存击穿指并发查同一条数据，缓存雪崩是不同数据都过期了，很多数据都查不到从而查数据库。
>
> 解决方案：
>
> 1. 缓存数据的过期时间设置随机，防止同一时间大量数据过期现象发生。
> 2. 如果缓存数据库是分布式部署，将热点数据均匀分布在不同的缓存数据库中。
>
> 3. 设置热点数据永远不过期。

## 缓存一致性

​	缓存一致性要求数据更新的同时缓存数据也能够实时更新。

​	解决方案：

+ 在数据更新的同时立即去更新缓存；
+ 在读缓存之前先判断缓存是否是最新的，如果不是最新的先进行更新。

## 缓存无底洞

​	指的是为了满足业务要求添加了大量缓存节点，但是性能不但没有好转反而下降了的现象。

​	产生原因：缓存系统通常采用 *hash* 函数将 *key* 映射到对应的缓存节点，随着缓存节点数目的增加，键值分布到更多的节点上，导致客户端一次批量操作会涉及多次网络操作，这意味着批量操作的耗时会随着节点数目的增加而不断增大。此外，网络连接数变多，对节点的性能也有一定影响。

​	解决方案：

+ 优化批量数据操作命令；

+ 减少网络通信次数；

+ 降低接入成本，使用长连接 / 连接池，*NIO* 等。

# *Redis* 的同步机制

*Redis* 通过同步(*sync*)和指令传播(*command* *propagate*)两个操作完成同步。

同步(*sync*)：将从节点的数据库状态更新至与主节点的数据库状态一致。

> 从节点向主节点发送 *SYNC* 指令
>
> 收到 *SYNC* 指令，主节点执行 *BGSAVE* 指令，创建子进程生成 *RDB* 文件，并使用一个缓冲区记录从现在开始执行的所有写指令
>
> 主节点 *BGSAVE* 指令执行后，会将生成的 *RDB* 文件发送给从节点
>
> 从节点接收、载入 *RDB* 文件，将数据库状态更新至主节点执行 *BGSAVE* 指令时的数据库状态
>
> 从节点加载完 *RDB* 文件，通知主节点将记录在缓冲区里面的所有写指令发送给从节点，从节点执行这些写指令，将数据库状态更新至主节点当前数据库状态

指令传播(*command* *propagate*)：主节点数据被修改，会主动向从节点发送执行的写指令，从节点执行之后，两个节点数据状态又保持一致。

# *Redis*的持久化机制是什么？各自的优缺点？

​	*Redis* 提供两种持久化机制 *RDB*（默认）和 *AOF* 机制:

​	*RDB*：是 *Redis* *DataBase* 缩写快照，*Redis*默认的持久化方式。按照一定的时间将内存的数据以快照的形式保存到硬盘中，对应产生的数据文件为 *dump*.*rdb*。通过配置文件中的 *save* 参数来定义快照的周期。

​	*AOF*：持久化(*Append Only File*)，则是将 *Redis* 执行的每次写命令记录到单独的日志文件中，当重启 *Redis* 会重新将持久化的日志中文件恢复数据。

# *Redis*集群

## 主从模式

​	数据库分为两类：主数据库(*master*)和从数据库(*slave*)。

​	其中主从复制有如下特点：

> + 主数据库可以进行读写操作，当读写操作导致数据变化时会自动将数据同步给从数据库从数据库一般都是只读的，并且接收主数据库同步过来的数据。
>
> + 一个 *master* 可以拥有多个 *slave*，但是一个 *slave* 只能对应一个 *master*。*slave* 挂了不影响其他 *slave* 的读和 *master* 的读和写，重新启动后会将数据从 *master* 同步过来。*master* 挂了以后，不影响 *slave* 的读，但 *redis* 不再提供写服务，*master* 重启后 *redis* 将重新对外提供写服务。*master* 挂了以后，不会在 *slave* 节点中重新选一个 *master*

## *Sentinel*模式

​	主从模式的弊端就是不具备高可用性，当 *master* 挂掉以后，*Redis*将不能再对外提供写入操作，因此 *sentinel* 应运而生。

​	*sentinel* 中文含义为哨兵，顾名思义，它的作用就是监控*redis*集群的运行状况，特点如下：

> + *sentinel* 模式是建立在主从模式的基础上，如果只有一个 *Redis* 节点，*sentinel* 就没有任何意义。
>
> + 当 *master* 挂了以后，*sentinel* 会在 *slave* 中选择一个做为 *master*，并修改它们的配置文件，其他 *slave* 的配置文件也会被修改，比如 *slaveof* 属性会指向新的 *master*。
>
> + 当 *master* 重新启动后，它将不再是 *master* 而是做为 *slave* 接收新的 *master* 的同步数据。
>
> + *sentinel* 因为也是一个进程有挂掉的可能，所以 *sentinel* 也会启动多个形成一个 *sentinel* 集群。
>
> + 多 *sentinel* 配置的时候，*sentinel* 之间也会自动监控当主从模式配置密码时，*sentinel* 也会同步将配置信息修改到配置文件中，不需要担心。
>
> + 一个 *sentinel* 或 *sentinel* 集群可以管理多个主从 *Redis*，多个 *sentinel* 也可以监控同一个 *redis*。
>
> + *sentinel* 最好不要和 *Redis* 部署在同一台机器，不然 *Redis* 的服务器挂了以后，*sentinel* 也挂了。

##  *Cluster* 模式介绍

​	*sentinel* 模式基本可以满足一般生产的需求，具备高可用性。但是当数据量过大到一台服务器存放不下的情况时，主从模式或 *sentinel* 模式就不能满足需求了，这个时候需要对存储的数据进行分片，将数据存储到多个 *Redis*实例中。*cluster* 模式的出现就是为了解决单机 *Redis* 容量有限的问题，将 *Redis* 的数据根据一定的规则分配到多台机器。

​	*cluster* 可以说是 *sentinel* 和主从模式的结合体，通过 *cluster* 可以实现主从和 *master* 重选功能，所以如果配置两个副本三个分片的话，就需要六个 *Redis* 实例。因为 *Redis* 的数据是根据一定规则分配到 *cluster* 的不同机器的，当数据量过大时，可以新增机器进行扩容。

​	使用集群，只需要将 *redis* 配置文件中的 *cluster*-*enable* 配置打开即可。每个集群中至少需要三个主数据库才能正常运行，新增节点非常方便。

​	*cluster* 集群特点：

> + 多个 *redis* 节点网络互联，数据共享
>
> + 所有的节点都是一主一从（也可以是一主多从），其中从不提供服务，仅作为备用
>
> + 不支持同时处理多个 *key*（如*MSET*/*MGET*），因为 *redis* 需要把 *key* 均匀分布在各个节点上，
>
> + 并发量很高的情况下同时创建 *key*-*value* 会降低性能并导致不可预测的行为
>
> + 支持在线增加、删除节点
>
> + 客户端可以连接任何一个主节点进行读写

# *Redis* **集群会有写操作丢失吗？**

以下情况可能导致写操作丢失：

> + 过期 *key* 被清理
>
> + 最大内存不足，导致 *Redis* 自动清理部分 *key* 以节省空间
>
> + 主库故障后自动重启，从库自动同步
>
> + 单独的主备方案，网络不稳定触发哨兵的自动切换主从节点，切换期间会有数据丢失

# *Redis*哈希槽

​	*Redis* 集群没有使用一致性 *hash*,而是引入了哈希槽的概念，*Redis* 集群有16384个哈希槽，每个 *key* 通过 *CRC*16 校验后对16384取模来决定放置哪个槽，集群的每个节点负责一部分哈希槽。

# 一致性哈希

https://www.zsythink.net/archives/1182

# 分布式锁

# *Redis* 的过期键的删除策略

+ 定时过期：每个设置过期时间的 *key* 都需要创建一个定时器，到过期时间就会立即清除。该策略可以立即清除过期的数据，对内存很友好；但是会占用大量的 *CPU* 资源去处理过期的数据，从而影响缓存的响应时间和吞吐量。

+ 惰性过期：只有当访问一个 *key* 时，才会判断该 *key* 是否已过期，过期则清除。该策略可以最大化地节省 *CPU* 资源，却对内存非常不友好。极端情况可能出现大量的过期 *key* 没有再次被访问，从而不会被清除，占用大量内存。

+ 定期过期：每隔一定的时间，会扫描一定数量的数据库的 *expires* 字典中一定数量的 *key*，并清除其中已过期的 *key*。该策略是前两者的一个折中方案。通过调整定时扫描的时间间隔和每次扫描的限定耗时，可以在不同情况下使得 *CPU* 和内存资源达到最优的平衡效果。

# *Redis*如何做内存优化

+ 缩减键值对象：满足业务要求下 *key* 越短越好；*value* 值进行适当压缩。

+ 共享对象池：即 *Redis* 内部维护[0-9999]的整数对象池，开发中在满足需求的前提下，尽量使用整数对象以节省内存。

+ 尽可能使用散列表(*hashes*)。

+ 编码优化，控制编码类型。

+ 控制 *key* 的数量 。

# *Redis*的内存用完了会发生什么

*Redis* 的默认回收策略是 *noenviction*，当内存用完之后，写数据会报错。

*Redis* 的其他内存回收策略含义：

> *volatile*-*lru*：从已设置过期时间的数据集中，淘汰最近最少使用的数据
>
> *volatile*-*ttl*：从已设置过期时间的数据集中，淘汰最早会过期的数据
>
> *volatile*-*random*：从已设置过期时间的数据集中，随机淘汰数据
>
> *allkeys*-*lru*：从数据集中，淘汰最近最少使用的数据
>
> *allkeys*-*random*：从数据集中，随机淘汰数据

# *Redis*有哪些适用场景

+ 会话缓存(*Session* *Cache*)，是 *Redis* 最常使用的一种情景，即 *Session* 共享

+ *list* 可以用来做消息队列

+ 全页缓存(*FPC*)

+ 用作网络版集合和队

+ 排行榜和计数器，*Redis* 在内存中对数字递增、递减的操作实现的非常好。*Set* 和 *Sorted* *Set* 使得我们在执行这些操作的时候非常简单

+ 发布和订阅

 # 分布式锁

## *Redis* 的 *SETNX* 指令

使用 *SETNX*（*set if not exist*）指令插入一个键值对，如果 *Key* 已经存在，那么会返回 *False*，否则插入成功并返回 *True*。

*SETNX* 指令和数据库的唯一索引类似，保证了只存在一个 *Key* 的键值对，那么可以用一个 *Key* 的键值对是否存在来判断是否存于锁定状态。

如果请求执行因为某些原因意外退出了，导致创建了锁但是没有删除锁，那么这个锁将一直存在，以至于以后缓存再也得不到更新。于是乎我们需要给锁加一个过期时间 *EXPIRE* 指令可以为一个键值对设置一个过期时间，从而避免了数据库唯一索引实现方式中释放锁失败的问题。

```bash
SETNX KEY VALUE
EXPIRE KEY TIME
```

## 误删锁的情形

如果执行时间超过超时时间呢，比如线程 *A* 还未执行完任务，就释放了锁，线程 *B* 接着执行任务，这时，线程 *A* 执行完了，把锁删掉（此时删除的时线程 *B* 获取的锁）。

方案一：为了避免这种情况，在删除锁之前可以做一个判断，验证 *key* 对应的 *value* 是不是自己线程的 *ID*。
方案二：我们可以让获得锁的线程开启一个守护线程，用来给快要过期的锁加长超时时间。当线程执行完任务，再显式关掉守护线程。

## *Redis* 的 *RedLock* 算法

使用了多个 *Redis* 实例来实现分布式锁，这是为了保证在发生单点故障时仍然可用。

- 尝试从 *N* 个互相独立 *Redis* 实例获取锁；
- 计算获取锁消耗的时间，只有时间小于锁的过期时间，并且从大多数（*N* / 2 + 1）实例上获取了锁，才认为获取锁成功；
- 如果获取锁失败，就到每个实例上释放锁。

