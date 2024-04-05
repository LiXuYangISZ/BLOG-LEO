---
title: 零、带你走进Redis的世界 ⭐必看必看⭐
date: 2024-04-05 14:46:00
tags: Redis
categorys: Redis从入门到入土
swiper_index: 3
description: 学习Redis的安装和使用的场景，能够解决哪些问题~
---

# 1. NoSQL数据库简介

## 1.1  技术发展

<font color=blue>技术的分类</font>

1、解决功能性的问题：Java、Jsp、RDBMS、Tomcat、HTML、Linux、JDBC、SVN

2、解决扩展性的问题：Struts、Spring、SpringMVC、Hibernate、Mybatis

3、解决性能的问题：NoSQL、Java线程、Hadoop、Nginx、MQ、ElasticSearch

### 1.1.1.  Web1.0时代

​		Web1.0的时代，数据访问量很有限，用一夫当关的高性能的单点服务器可以解决大部分问题。

![image-20220127230749355](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035121.png)

 

###  1.1.2  Web2.0时代

​		随着Web2.0的时代的到来，用户访问量大幅度提升，同时产生了大量的用户数据。加上后来的智能移动设备的普及，所有的互联网平台都面临了巨大的性能挑战。

​                                    ![image-20220127231010869](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035319.png)

### 1.1.3.  解决CPU及内存压力(采用分布式)

 	   但是Session存在哪里?因为每次访问的服务器可能不是同一台.上次用户的数据保存在服务器1,用户是登录状态,下一次可能访问的服务器2,没有该用户的session,用户显示的未登录状态.

![image-20220127231230724](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035577.png)

### 1.1.4.  解决IO压力

![image-20220127231339820](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035165.png)

## 1.2.  NoSQL数据库

### 1.2.1.  NoSQL数据库概述

**NoSQL**(NoSQL = Not Only SQL )，意即“不仅仅是SQL”，泛指==**非关系型的数据库**==。 

<font color=red>NoSQL 不依赖业务逻辑方式存储，而以简单的key-value模式存储。因此大大的增加了数据库的扩展能力。</font>

- 不遵循SQL标准。

- 不支持ACID。

- 远超于SQL的性能。

### 1.2.2  NoSQL适用场景 

- 对数据高并发的读写

- 海量数据的读写

- 对数据高可扩展性的



### 1.2.3  NoSQL不适用场景

- 需要事务支持

- 基于sql的结构化查询存储，处理复杂的关系,需要即席查询。

- <font color=red>（用不着 sql 的和用了 sql 也不行的情况，请考虑用 NoSql）</font>

### 1.2.4 Memcache 

![image-20220127232722449](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035703.png)

### 1.2.5 Redis 

![image-20220127232802506](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035489.png)

### 1.2.6. MongoDB 

![image-20220127232953050](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035726.png)

## 1.3. 行式存储数据库（大数据时代） 

### 1.3.1. 行式数据库

![image-20220127233719653](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035879.png)

### 1.3.2. 列式数据库 

![image-20220127233756939](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035423.png)

## 1.4. 图关系型数据库

![image-20220127233900089](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020043512.png)

主要应用：社会关系，公共交通网络，地图及网络拓谱(n*(n-1)/2)

![image-20220127233919283](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035305.png)

## 1.5. DB-Engines 数据库排名

http://db-engines.com/en/ranking

![image-20220127234052885](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035877.png)

# 2. Redis 概述安装

➢ Redis 是一个开源的 ==key-value 存储系统==。 

➢ 和 Memcached 类似，它支持存储的 value 类型相对更多，包括 ==string(字符串)、 list(链表)、set(集合)、zset(sorted set --有序集合)和 hash（哈希类型）==。

 ➢ 这些数据类型都支持 push/pop、add/remove 及取交集并集和差集及更丰富的操作， 而且这些操作都是原子性的。 

➢ 在此基础上，Redis 支持各种不同方式的排序。 

➢ 与 memcached 一样，为了保证效率，==数据都是缓存在内存中==。 

➢ 区别的是 Redis 会==周期性的把更新的数据写入磁盘==或者把修改操作==写入追加的记 录文件==。 

➢ 并且在此基础上实现了 ==master-slave(主从)同步==。



## 2.1. 应用场景 

### 2.1.1. 配合关系型数据库做高速缓存

➢ 高频次，热门访问的数据，降低数据库 IO 

➢ 分布式架构，做 session 共享

![image-20220130164924992](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020035920.png)

### 2.1.2. 多样的数据结构存储持久化数据

![image-20220130165019016](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036894.png)

## 2.2. Redis 安装

![image-20220130165138807](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036223.png)

![image-20220130165209747](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036750.png)

### 2.2.1. 安装版本  

➢ 6.2.1 for Linux（==redis-6.2.1.tar.gz==） 

➢ 不用考虑在 windows 环境下对 Redis 的支持



### 2.2.2. 安装步骤 

#### 2.2.2.1. 准备工作：下载安装最新版的 gcc 编译器  安装 C 语言的编译环境 

```shell
yum install centos-release-scl scl-utils-build 
yum install -y devtoolset-8-toolchain 
scl enable devtoolset-8 bash 
```

测试 gcc 版本

**gcc --version**

![image-20220130170103407](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036159.png)

#### 2.2.2.2. 下载 redis-6.2.1.tar.gz 放/opt 目录  

#### 2.2.2.3. 解压命令：tar -zxvf redis-6.2.1.tar.gz  

#### 2.2.2.4. 解压完成后进入目录：cd redis-6.2.1 

####  2.2.2.5. 在 redis-6.2.1 目录下再次执行 make 命令（只 是编译好） 

####  2.2.2.6. 如果没有准备好 C 语言编译环境，make 会报错 —Jemalloc/jemalloc.h：没有那个文件

 解决方案：先安装gcc,之后运行 **make distclean** 

#### 2.2.2.8. 在 redis-6.2.1 目录下再次执行 make 命令（只 是编译好） 

![image-20220130165912341](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036719.png)

#### 2.2.2.9. 跳过 make test 继续执行: make install

![image-20220130165934887](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020044200.png)

### 2.2.3. 安装目录：/usr/local/bin

查看默认安装目录： 

- redis-benchmark:性能测试工具，可以在自己本子运行，看看自己本子性能如何 

- redis-check-aof：修复有问题的 AOF 文件，rdb 和 aof 后面讲 

- redis-check-dump：修复有问题的 dump.rdb 文件 

- redis-sentinel：**Redis 集群使用** 

- redis-server：Redis 服务器启动命令

- redis-cli：**客户端，操作入口**



### 2.2.4. 前台启动（不推荐） 

前台启动，命令行窗口不能关闭，否则服务器停止

![image-20220130170604162](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036078.png)

### 2.2.5. 后台启动（推荐）

#### 2.2.5.1.备份 redis.conf  

拷贝一份 redis.conf 到其他目录,防止后期把文件修改坏无法恢复.

```shell
cp /opt/module/redis-6.2.6/redis.conf /etc/redis.conf
```

#### 2.2.5.2.后台启动设置 ==daemonize no 改成 yes==

修改 redis.conf(257 行)文件将里面的 daemonize no 改成 yes，让服务在后台启动

![image-20220130171051853](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036350.png)

#### 2.2.5.3.Redis 启动

```shell
redis-server /etc/redis.conf
```

#### 2.2.5.4.用客户端访问：redis-cli  

#### 2.2.5.5.多个端口可以：redis-cli -p6379  

#### 2.2.5.6.测试验证： ping 

![image-20220130171216335](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036830.png)

#### 2.2.5.7.Redis 关闭 

<font color=red>shutdown+quit</font>

![image-20220130171304170](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036847.png)

<font color=red>exit+kill</font>

![image-20220130171340864](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036910.png)

### 2.2.6. Redis 介绍相关知识

![image-20220130171416048](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036919.png)

Redis 是**<font color=red>单线程+多路 IO 复用技术 </font>**

多路复用是指使用一个线程来检查多个文件描述符（Socket）的就绪状态，比如调用 select 和 poll 函数，传入多个文件描述符，如果有一个文件描述符就绪，则返回，否则 阻塞直到超时。得到就绪状态后进行真正的操作可以在同一个线程里执行，也可以启 动线程执行（比如使用线程池）

==串行 vs 多线程+锁（memcached） vs 单线程+多路 IO 复用(Redis)==

![image-20220130171454534](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036606.png)



# 3. 常用五大数据类型

哪里去获得 redis 常见数据类型操作命令  http://www.redis.cn/commands.html

## 3.1. Redis 键(key)

- **keys *** 查看当前库所有 key (匹配：keys *1)

- **exists key** 判断某个 key 是否存在 

- **type key** 查看你的 key 是什么类型 

- **del key** 删除指定的 key 数据

![image-20220130172127695](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036455.png)

- ==unlink key 根据 value 选择非阻塞删除==

仅将 keys 从 keyspace 元数据中删除，真正的删除会在后续异步操作。

- **expire key 10** 10 秒钟：为给定的 key 设置过期时间

- **ttl key** 查看还有多少秒过期，-1 表示永不过期，-2 表示已过期

- **select** 命令切换数据库

- **dbsize** 查看当前数据库的 key 的数量

- **flushdb** 清空当前库 

- **flushall** 通杀全部库

![image-20220130172423322](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036813.png)

## 3.2. Redis 字符串(String)

### 3.2.1. 简介

- ==String 是 Redis 最基本的类型==，你可以理解成与 Memcached 一模一样的类型，一个 key 对应一个 value。
- ==String 类型是二进制安全的==。意味着 Redis 的 string 可以包含任何数据。比如 jpg 图片 或者序列化的对象。
- String 类型是 Redis 最基本的数据类型，==一个 Redis 中字符串 value 最多可以是 512M==

### 3.2.2. 常用命令

```shell
set <key> <value> #添加键值对
```

注意: 当数据库中 key 不存在时，可以将 key-value 添加数据,如果存在时，新value将覆盖旧value.

- **get**  <key> 查询对应键值

- **append** <key> <value> 将给定的<value> 追加到原值的末尾

- **strlen** <key>获得值的长度

- **setnx <key> <value>** 只有在 key 不存在时  设置 key 的值

- **incr** <key>

  将 key 中储存的数字值增1

  只能对数字值操作，如果为空，新增值为1

- **decr** <key>

  将 key 中储存的数字值减1

  只能对数字值操作，如果为空，新增值为-1

- **incrby / decrby** <key><步长> 将 key 中储存的数字值增减。自定义步长。

![image-20220130173641841](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036638.png)

- **mset** <key1><value1><key2><value2> ..... 

  同时设置一个或多个 key-value对 

- **mget** <key1><key2><key3> .....

  同时获取一个或多个 value 

- **msetnx** <key1><value1><key2><value2> ..... 

  同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。

<font color=red>**原子性，有一个失败则都失败**</font>

- **getrange** <key><起始位置><结束位置>

  获得值的范围，类似java中的substring，**前包，后包**

- **setrange** <key><起始位置><value>

  用 <value> 覆写<key>所储存的字符串值，从<起始位置>开始(**索引从0****开始**)。

- **setex <key><****过期时间****><value>**

  设置键值的同时，设置过期时间，单位秒。

- **getset** <key><value>

  以新换旧，设置了新值同时获得旧值。

![image-20220130174027591](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036591.png)

### 3.2.3. 数据结构

String的数据结构为==简单动态字符串==(Simple Dynamic String,缩写SDS)。是可以修改的字符串，内部结构实现上类似于Java的ArrayList，采用==预分配冗余空间==的方式来减少内存的频繁分配.

![image-20220130174207172](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020044463.png)

如图中所示，内部为当前字符串实际分配的空间capacity一般要高于实际字符串长度len。当字符串长度小于1M时，扩容都是加倍现有的空间，如果超过1M，扩容时一次只会多扩1M的空间。需要注意的是字符串最大长度为512M。

## 3.3. Redis 列表(List)

### 3.3.1. 简介

**<font color=red>单键多值</font>**

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。

它的底层实际是个==双向链表==，对两端的操作性能很高，通过索引下标的操作中间的节点性能会较差。

![image-20220130174353132](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036957.png)

### 3.3.2. 常用命令

- ==**lpush/rpush**== <key><value1><value2><value3> .... 从左边/右边插入一个或多个值。

- ==**lpop/rpop**== <key>从左边/右边吐出一个值。值在键在，值光键亡。

- **rpop/lpush** <key1><key2>从<key1>列表右边吐出一个值，插到<key2>列表左边。

- **lrange** <key><start><stop> 按照索引下标获得元素(从左到右)

- ==**lrange mylist 0 -1**==  0左边第一个，<font color=red>-1右边第一个，（0-1表示获取所有）</font>

- **lindex** <key><index>按照索引下标获得元素(从左到右)

- **llen** <key> 获得列表长度 

- **linsert <key> before** <value><newvalue>在<value>的后面插入<newvalue>插入值

- **lrem <key><n><value>** 从左边删除n个value(从左到右)

- **lset**<key><index><value>将列表key下标为index的值替换成value

![image-20220130175139456](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036311.png)

![image-20220130201405991](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020046384.png)

### 3.2.3 数据结构

List 的数据结构为==快速链表 quickList==。

首先在列表元素较少的情况下会使用一块连续的内存存储，这个结构是 ziplist，也即是 压缩列表。 

它将所有的元素紧挨着一起存储，分配的是一块连续的内存。 当数据量比较多的时候才会改成 quicklist。 因为普通的链表需要的附加指针空间太大，会比较浪费空间。比如这个列表里存的只 是 int 类型的数据，结构上还需要两个额外的指针 prev 和 next。

![image-20220210214301430](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020036175.png)

Redis 将链表和 ziplist 结合起来组成了 quicklist。也就是将多个 ziplist 使用双向指 针串起来使用。这样既满足了快速的插入删除性能，又不会出现太大的空间冗余。

### 3.2.4 补充：原子性

<font color=red>所谓原子操作是指不会被线程调度机制打断的操作</font> ；
这种操作一旦开始，就一直运行到结束，中间不会有任何 context switch （切换到另
一个线程）。
（1）在单线程中， 能够在单条指令中完成的操作都可以认为是"原子操作"，因为中
断只能发生于指令之间。
（2）在多线程中，不能被其它进程（线程）打断的操作就叫原子操作。
Redis 单命令的原子性主要得益于 Redis的单线程。

案例：
java 中的 i++是否是原子操作？不是
i=0;两个线程分别对 i进行++100次,值是多少？ 

![image-20220210223238563](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037909.png)

## 3.4. Redis 集合(Set)

### 3.4.1. 简介

Redis set 对外提供的功能与 list 类似是一个列表的功能，特殊之处在于 set 是可以<font color=red>自动排重</font>的，当你需要存储一个列表数据，又不希望出现重复数据时，set 是一个很好的选 择，并且 set 提供了判断某个成员是否在一个 set 集合内的重要接口，这个也是 list 所不能提供的。

Redis 的 Set 是 ==string 类型的无序集合==。它底层其实是一个 value 为 null 的 ==hash 表==，所 以添加，删除，查找的复杂度都是 O(1)。

一个算法，随着数据的增加，执行时间的长短，如果是 O(1)，数据增加，查找数据的 时间不变

### 3.4.2. 常用命令

- **sadd  <key><value1><value2> .....** 将一个或多个 member 元素加入到集合 key 中，已经存在的 member 元素将被忽略
- **smembers** 取出该集合的所有值
- **sismember<key><value>** 判断集合是否为含有该值，有 1，没有 0
- **scard <key>** 返回该集合的元素个数。
- **srem  <key><value1><value2> ...** 删除集合中的某个元素。 
- **spop <key>**随机从该集合中吐出一个值。
- **srandmember <key><n>**随机从该集合中取出 n 个值。不会从集合中删除 。

- **move <source><destination>value** 把集合中一个值从一个集合移动到另一个集合
- **sinter <key1><key2>**返回两个集合的==交集==元素。
- **sunion <key1><key2>**返回两个集合的==并集==元素。
- **sdiff <key1><key2>**返回两个集合的==差集==元素(key1 中的，不包含 key2中的)

![image-20220210215939742](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037224.png)

![image-20220210220038515](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037551.png)

![image-20220210220053731](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037926.png)

### 3.4.3. 数据结构

Set 数据结构是 ==dict字典==，字典是用==哈希表==实现的。
Java 中 HashSet 的内部实现使用的是 HashMap，只不过所有的 value 都指向同一个对象。
Redis的 set结构也是一样，它的内部也使用 hash结构，所有的 value都指向同一个内部值。

## 3.5. Redis 哈希(Hash)

### 3.5.1. 简介

Redis hash 是一个键值对集合。
Redis hash 是一个 ==string 类型的 field 和 value 的映射表==，hash 特别适合用于存储对象。
类似 Java 里面的 Map<String,Object>
用户 ID 为查找的 key，存储的 value用户对象包含姓名，年龄，生日等信息，如果用
普通的 key/value结构来存储
**主要有以下 3 种存储方式：**

![image-20220210220339056](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037072.png)

![image-20220210220359629](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037639.png)

### 3.5.2. 常用命令

- **hset <key><field><value>:** 给<key>集合中的 <field>键赋值<value>
- **hget <key1><field>: **从<key1>集合<field>取出 value
- **hmset <key1><field1><value1><field2><value2>... : ** 批量设置 hash 的值
- **hexists<key1><field>: **查看哈希表 key 中，给定域 field 是否存在。
- **hkeys <key>: **列出该 hash 集合的所有 field
- **hvals <key>: **列出该 hash 集合的所有 value
- **hincrby <key><field><increment>: **为哈希表 key 中的域 field 的值加上增量 1 -1
- **hsetnx <key><field><value>: **将哈希表 key 中的域 field 的值设置为 value ，当且仅当域
  field 不存在 

![image-20220210220723448](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037710.png)

### 3.5.3. 数据结构

Hash 类型对应的数据结构是两种：==ziplist（压缩列表），hashtable（哈希表）==。当field-value 长度较短且个数较少时，使用 ziplist，否则使用 hashtable。

## 3.6. Redis 有序集合 Zset(sorted set)

### 3.6.1. 简介

Redis 有序集合 zset与普通集合 set 非常相似，是一个==没有重复元素的字符串集合==。
不同之处是有序集合的每个成员都关联了一个评分（score）,这个评分（score）被用来按照从最低分到最高分的方式排序集合中的成员。<font color=red>集合的成员是唯一的，但是评分可以是重复了</font> 。
因为元素是有序的, 所以你也可以很快的根据评分（score）或者次序（position）来获取一个范围的元素。
访问有序集合的中间元素也是非常快的,因此你能够使用有序集合作为一个没有重复成员的智能列表。

### 3.6.2. 常用命令

- **zadd <key><score1><value1><score2><value2>… :** 将一个或多个 member 元素及其 score 值加入到有序集 key 当中。
- **zrange <key><start><stop> [WITHSCORES]:** 返回有序集 key 中下标在<start><stop>之间的元素 . 带 WITHSCORES，可以让分数一起和值返回到结果集。
- **zrangebyscore key minmax [withscores] [limit offset count]:**返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。 ==有序集成员按 score 值递增(从小到大)次序排列==。
- **zrevrangebyscore key maxmin [withscores] [limit offset count]:** 同上，改为从大到小排列。
- **zincrby <key><increment><value>:** 为元素的 score加上增量
- **zrem <key><value>:** 删除该集合下，指定值的元素
- **zcount <key><min><max>:** 统计该集合，分数区间内的元素个数
- **zrank <key><value>:** 返回该值在集合中的排名，从 0 开始。

案例：<font color=red>如何利用 zset 实现一个文章访问量的排行榜？</font>

![image-20220210221727819](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037958.png)

### 3.6.3. 数据结构

SortedSet(zset)是 Redis 提供的一个非常特别的数据结构，一方面它<font color=red>等价于 Java的数据结构 Map<String, Double></font>，可以给每一个元素 value 赋予一个权重 score，另一方面它又<font color=red>类似于 TreeSet</font>，**内部的元素会按照权重 score 进行排序，可以得到每个元素的名次，还可以通过 score 的范围来获取元素的列表。**

**zset 底层使用了两个数据结构:**
（1）hash，hash 的作用就是关联元素 value 和权重 score，保障元素 ==value 的唯一性==，可以通过元素 value 找到相应的 score 值。
（2）跳跃表，跳跃表的目的在于给元素 value 排序，根据 score 的范围获取元素列表。

### 3.6.4. 跳跃表（跳表）

<font color=blue>1、简介</font>
有序集合在生活中比较常见，例如==根据成绩对学生排名，根据得分对玩家排名等==。对于有序集合的底层实现，可以用数组、平衡树、链表等。数组不便元素的插入、删除；平衡树或红黑树虽然效率高但结构复杂；链表查询需要遍历所有效率低。Redis采用的是跳跃表。跳跃表效率堪比红黑树，实现远比红黑树简单。

<font color=blue>2、实例</font>
对比有序链表和跳跃表，从链表中查询出 51

（1） 有序链表

![image-20220210222530470](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037399.png)

（2） 跳跃表

![image-20220210222735699](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037294.png)

从此可以看出==跳跃表比有序链表效率要高==

https://www.aliyundrive.com/s/ebUnLzhokVr

# 4. Redis Redis 配置文件介绍

自定义目录：`/etc/redis.conf`

## 4.1. ###Units 单位###

![image-20220214234408885](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037567.png)

## 4.2. ###INCLUDES 包含###

![image-20220214234519712](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037584.png)

类似 jsp 中的 include，多实例的情况可以把公用的配置文件提取出来

## 4.3. ### 网络相关配置 ###

### 4.3.1. bind

默认情况 **bind=127.0.0.1** 只能接受本机的访问请求. 不写的情况下，无限制接受任何 ip 地址的访问
生产环境肯定要写你应用服务器的地址；**服务器是需要远程访问的，所以需要将其注释掉**

**<font color=red>如果开启了 protected-mode，那么在没有设定 bind ip且没有设密码的情况下，Redis
只允许接受本机的响应</font>**(后续将对其进行关闭)

![image-20220216215546907](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037102.png)

### 4.3.2. protected- -mode

![image-20220216220224864](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037550.png)

### 4.3.3. Port

端口号，默认 6379

![image-20220216220542814](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020037319.png)

### 4.3.4. tcp--backlog

设置 tcp 的 backlog，backlog 其实是一个连接队列，backlog队列总和=未完成三次握手队列 + 已经完成三次握手队列。
在高并发环境下你需要一个高 backlog 值来避免慢客户端连接问题。
注意 Linux内核会将这个值减小到/proc/sys/net/core/somaxconn 的值（128），所以需要确认增大/proc/sys/net/core/somaxconn 和/proc/sys/net/ipv4/tcp_max_syn_backlog（128）两个值来达到想要的效果

![image-20220216220745374](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038133.png)

### 4.3.5. timeout

一个空闲的客户端维持多少秒会关闭，0 表示关闭该功能。即永不关闭。

![image-20220216221234044](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038542.png)

### 4.3.6. tcp---keepalive

对访问客户端的一种心跳检测，每隔n 秒检测一次。
单位为秒，如果设置为 0，则不会进行 Keepalive检测，建议设置成 60

![image-20220216221701077](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038855.png)

## 4.4. ###GENERAL 通用###

### 4.4.1. daemonize

是否为后台进程，设置为 yes. 守护进程，后台启动

![image-20220216222015341](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038309.png)

### 4.4.2. pidfile

存放 pid 文件的位置，每个实例会产生一个不同的 pid文件

![image-20220216222549532](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038949.png)

### 4.4.3. loglevel

指定日志记录级别，Redis 总共支持四个级别：==debug、verbose、notice、warning==，默认为 notice
四个级别根据使用阶段来选择，生产环境选择 notice 或者 warning

![image-20220216222621998](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038461.png)

### 4.4.4. logfile

日志文件名称

![image-20220216222740748](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038574.png)

### 4.4.5. databases 16

设定库的数量 默认 16，默认数据库为 0，可以使用 SELECT <dbid>命令在连接上指定数据库 id

![image-20220216222829307](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038333.png)

## 4.5. ###SECURITY 安全###

### 4.5.1. 设置密码

![image-20220216224220227](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038311.png)

==访问密码的查看、设置和取消在命令中设置密码，只是临时的==。重启 redis服务器，密码就还原了。
永久设置，需要再配置文件中进行设置。

## 4.6. #### LIMITS 限制 ###

### 4.6.1. maxclients

➢ 设置 redis 同时可以与多少个客户端进行连接。
➢ 默认情况下为 <font color=red>10000 </font>个客户端。
➢ 如果达到了此限制，redis 则会拒绝新的连接请求，并且向这些连接请求方发出“max number of clients reached”以作回应。

![image-20220216224547099](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038647.png)

### 4.6.2. maxmemory

➢ 建议**<font color=red>必须设置</font>**，否则，将内存占满，造成服务器宕机
➢ 设置 redis 可以使用的内存量。一旦到达内存使用上限，redis将会试图移除内部数据，移除规则可以通过 **<font color=red>maxmemory-policy</font>** 来指定。
➢ 如果 redis 无法根据移除规则来移除内存中的数据，或者设置了“不允许移除”，那么 redis则会针对那些需要申请内存的指令返回错误信息，比如 SET、LPUSH等。 但是对于无内存申请的指令，仍然会正常响应，比如 GET 等。
➢如果你的 redis 是主redis（说明你的 redis 有从 redis），那么在设置内存使用上限时，需要在系统中留出一些内存空间给同步队列缓存，只有在你设置的是“不移除”的情况下，才不用考虑这个因素。

![image-20220216224927129](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038432.png)

### 4.6.3. maxmemory---policy

➢ volatile-lru：使用 LRU算法移除 key，只对设置了过期时间的键；（最近最少使用）
➢ allkeys-lru：在所有集合 key中，使用 LRU算法移除 key
➢ volatile-random：在过期集合中移除随机的 key，只对设置了过期时间的键
➢ allkeys-random：在所有集合 key中，移除随机的 key
➢ volatile-ttl：移除那些 TTL值最小的 key，即那些最近要过期的 key
➢ noeviction：不进行移除。针对写操作，只是返回错误信息

![image-20220216225459951](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038174.png)

### 4.6.4. maxmemory---samples

➢ 设置样本数量，LRU 算法和最小 TTL算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小，redis 默认会检查这么多个 key 并选择其中 LRU 的那个。
➢ 一般设置 3 到 7的数字，数值越小样本越不准确，但 性能消耗越小。

![image-20220216225631696](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038494.png)

# 5. Redis Redis 的发布和订阅

## 5.1. 什么是发布和订阅

Redis 发布订阅 (pub/sub) 是一种消息通信模式：==发送者 (pub) 发送消息，订阅者(sub) 接收消息==。
Redis 客户端可以订阅任意数量的频道。

## 5.2. Redis 的发布和订阅

1、客户端可以订阅频道如下图

![image-20220216225959304](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038615.png)

2、当给这个频道发布消息后，消息就会发送给订阅的客户端

![image-20220216230015561](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038446.png)

## 5.3. 发布订阅命令行实现

1、 打开一个客户端订阅 channel1--- `SUBSCRIBE channel1`

![image-20220216230107720](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038182.png)

2、打开另一个客户端，给 channel1 发布消息 hello ---`publish channel1 hello`

![image-20220216230140160](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038399.png)

**返回的 1 是订阅者数量**

3、打开第一个客户端可以看到发送的消息

![image-20220216230219641](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038478.png)

注：==发布的消息没有持久化，如果在订阅的客户端收不到 hello，只能收到订阅后发布的消息==



# 6.Redis常见命令

## 6.1 Redis数据结构介绍

Redis是一个key-value的数据库，key一般是String类型，不过value的类型多种多样：

![1652887393157](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038047.png)

**贴心小建议：命令不要死记，学会查询就好啦**

Redis为了方便我们学习，将操作不同数据类型的命令也做了分组，在官网（ https://redis.io/commands ）可以查看到不同的命令：

![1652887648826](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038726.png)

当然我们也可以通过Help命令来帮助我们去查看命令

![1652887748279](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020039047.png)

## 6.2 Redis 通用命令

通用指令是部分数据类型的，都可以使用的指令，常见的有：

- KEYS：查看符合模板的所有key
- DEL：删除一个指定的key
- EXISTS：判断key是否存在
- EXPIRE：给一个key设置有效期，有效期到期时该key会被自动删除
- TTL：查看一个KEY的剩余有效期

通过help [command] 可以查看一个命令的具体用法，例如：

![1652887865189](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020038367.png)

课堂代码如下

* KEYS

```sh
127.0.0.1:6379> keys *
1) "name"
2) "age"
127.0.0.1:6379>

# 查询以a开头的key
127.0.0.1:6379> keys a*
1) "age"
127.0.0.1:6379>
```

**贴心小提示：在生产环境下，不推荐使用keys 命令，因为这个命令在key过多的情况下，效率不高**

* DEL

```sh
127.0.0.1:6379> help del

  DEL key [key ...]
  summary: Delete a key
  since: 1.0.0
  group: generic

127.0.0.1:6379> del name #删除单个
(integer) 1  #成功删除1个

127.0.0.1:6379> keys *
1) "age"

127.0.0.1:6379> MSET k1 v1 k2 v2 k3 v3 #批量添加数据
OK

127.0.0.1:6379> keys *
1) "k3"
2) "k2"
3) "k1"
4) "age"

127.0.0.1:6379> del k1 k2 k3 k4
(integer) 3   #此处返回的是成功删除的key，由于redis中只有k1,k2,k3 所以只成功删除3个，最终返回
127.0.0.1:6379>

127.0.0.1:6379> keys * #再查询全部的key
1) "age"	#只剩下一个了
127.0.0.1:6379>
```

**贴心小提示：同学们在拷贝代码的时候，只需要拷贝对应的命令哦~**

* EXISTS

```sh
127.0.0.1:6379> help EXISTS

  EXISTS key [key ...]
  summary: Determine if a key exists
  since: 1.0.0
  group: generic

127.0.0.1:6379> exists age
(integer) 1

127.0.0.1:6379> exists name
(integer) 0
```

* EXPIRE

**贴心小提示**：内存非常宝贵，对于一些数据，我们应当给他一些过期时间，当过期时间到了之后，他就会自动被删除~

```sh
127.0.0.1:6379> expire age 10
(integer) 1

127.0.0.1:6379> ttl age
(integer) 8

127.0.0.1:6379> ttl age
(integer) 6

127.0.0.1:6379> ttl age
(integer) -2

127.0.0.1:6379> ttl age
(integer) -2  #当这个key过期了，那么此时查询出来就是-2 

127.0.0.1:6379> keys *
(empty list or set)

127.0.0.1:6379> set age 10 #如果没有设置过期时间
OK

127.0.0.1:6379> ttl age
(integer) -1  # ttl的返回值就是-1
```



## 6.3 Redis命令-String命令

String类型，也就是字符串类型，是Redis中最简单的存储类型。

其value是字符串，不过根据字符串的格式不同，又可以分为3类：

* string：普通字符串
* int：整数类型，可以做自增.自减操作
* float：浮点类型，可以做自增.自减操作

![1652890121291](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020039967.png)

String的常见命令有：

* SET：添加或者修改已经存在的一个String类型的键值对
* GET：根据key获取String类型的value
* MSET：批量添加多个String类型的键值对
* MGET：根据多个key获取多个String类型的value
* INCR：让一个整型的key自增1
* INCRBY:让一个整型的key自增并指定步长，例如：incrby num 2 让num值自增2
* INCRBYFLOAT：让一个浮点类型的数字自增并指定步长
* SETNX：添加一个String类型的键值对，前提是这个key不存在，否则不执行
* SETEX：添加一个String类型的键值对，并且指定有效期

**贴心小提示**：以上命令除了INCRBYFLOAT 都是常用命令

* SET 和GET: 如果key不存在则是新增，如果存在则是修改

```java
127.0.0.1:6379> set name Rose  //原来不存在
OK

127.0.0.1:6379> get name 
"Rose"

127.0.0.1:6379> set name Jack //原来存在，就是修改
OK

127.0.0.1:6379> get name
"Jack"
```

* MSET和MGET

```java
127.0.0.1:6379> MSET k1 v1 k2 v2 k3 v3
OK

127.0.0.1:6379> MGET name age k1 k2 k3
1) "Jack" //之前存在的name
2) "10"   //之前存在的age
3) "v1"
4) "v2"
5) "v3"
```

* INCR和INCRBY和DECY

```java
127.0.0.1:6379> get age 
"10"

127.0.0.1:6379> incr age //增加1
(integer) 11
    
127.0.0.1:6379> get age //获得age
"11"

127.0.0.1:6379> incrby age 2 //一次增加2
(integer) 13 //返回目前的age的值
    
127.0.0.1:6379> incrby age 2
(integer) 15
    
127.0.0.1:6379> incrby age -1 //也可以增加负数，相当于减
(integer) 14
    
127.0.0.1:6379> incrby age -2 //一次减少2个
(integer) 12
    
127.0.0.1:6379> DECR age //相当于 incr 负数，减少正常用法
(integer) 11
    
127.0.0.1:6379> get age 
"11"

```

* SETNX

```java
127.0.0.1:6379> help setnx

  SETNX key value
  summary: Set the value of a key, only if the key does not exist
  since: 1.0.0
  group: string

127.0.0.1:6379> set name Jack  //设置名称
OK
127.0.0.1:6379> setnx name lisi //如果key不存在，则添加成功
(integer) 0
127.0.0.1:6379> get name //由于name已经存在，所以lisi的操作失败
"Jack"
127.0.0.1:6379> setnx name2 lisi //name2 不存在，所以操作成功
(integer) 1
127.0.0.1:6379> get name2 
"lisi"
```

* SETEX

```sh
127.0.0.1:6379> setex name 10 jack
OK

127.0.0.1:6379> ttl name
(integer) 8

127.0.0.1:6379> ttl name
(integer) 7

127.0.0.1:6379> ttl name
(integer) 5
```



## 6.4 Redis命令-Key的层级结构

Redis没有类似MySQL中的Table的概念，我们该如何区分不同类型的key呢？

例如，需要存储用户.商品信息到redis，有一个用户id是1，有一个商品id恰好也是1，此时如果使用id作为key，那就会冲突了，该怎么办？

我们可以通过给key添加前缀加以区分，不过这个前缀不是随便加的，有一定的规范：

Redis的key允许有多个单词形成层级结构，多个单词之间用':'隔开，格式如下：

![1652941631682](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020039600.png)

这个格式并非固定，也可以根据自己的需求来删除或添加词条。

例如我们的项目名称叫 heima，有user和product两种不同类型的数据，我们可以这样定义key：

- user相关的key：**heima:user:1**

- product相关的key：**heima:product:1**

如果Value是一个Java对象，例如一个User对象，则可以将对象序列化为JSON字符串后存储：

| **KEY**         | **VALUE**                                 |
| --------------- | ----------------------------------------- |
| heima:user:1    | {"id":1, "name": "Jack", "age": 21}       |
| heima:product:1 | {"id":1, "name": "小米11", "price": 4999} |

一旦我们向redis采用这样的方式存储，那么在可视化界面中，redis会以层级结构来进行存储，形成类似于这样的结构，更加方便Redis获取数据

![1652941883537](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020039261.png)



## 6.5 Redis命令-Hash命令

Hash类型，也叫散列，其value是一个无序字典，类似于Java中的HashMap结构。

String结构是将对象序列化为JSON字符串后存储，当需要修改对象某个字段时很不方便：

![1652941995945](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020039150.png)

Hash结构可以将对象中的每个字段独立存储，可以针对单个字段做CRUD：

![1652942027719](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020039782.png)

**Hash类型的常见命令**

- HSET key field value：添加或者修改hash类型key的field的值

- HGET key field：获取一个hash类型key的field的值

- HMSET：批量添加多个hash类型key的field的值

- HMGET：批量获取多个hash类型key的field的值

- HGETALL：获取一个hash类型的key中的所有的field和value
- HKEYS：获取一个hash类型的key中的所有的field
- HINCRBY:让一个hash类型key的字段值自增并指定步长
- HSETNX：添加一个hash类型的key的field值，前提是这个field不存在，否则不执行

**贴心小提示**：哈希结构也是我们以后实际开发中常用的命令哟

* HSET和HGET

```java
127.0.0.1:6379> HSET heima:user:3 name Lucy//大key是 heima:user:3 小key是name，小value是Lucy
(integer) 1
127.0.0.1:6379> HSET heima:user:3 age 21// 如果操作不存在的数据，则是新增
(integer) 1
127.0.0.1:6379> HSET heima:user:3 age 17 //如果操作存在的数据，则是修改
(integer) 0
127.0.0.1:6379> HGET heima:user:3 name 
"Lucy"
127.0.0.1:6379> HGET heima:user:3 age
"17"
```

* HMSET和HMGET

```java
127.0.0.1:6379> HMSET heima:user:4 name HanMeiMei
OK
127.0.0.1:6379> HMSET heima:user:4 name LiLei age 20 sex man
OK
127.0.0.1:6379> HMGET heima:user:4 name age sex
1) "LiLei"
2) "20"
3) "man"
```

* HGETALL

```java
127.0.0.1:6379> HGETALL heima:user:4
1) "name"
2) "LiLei"
3) "age"
4) "20"
5) "sex"
6) "man"
```

* HKEYS和HVALS

```java
127.0.0.1:6379> HKEYS heima:user:4
1) "name"
2) "age"
3) "sex"
127.0.0.1:6379> HVALS heima:user:4
1) "LiLei"
2) "20"
3) "man"
```

* HINCRBY

```java
127.0.0.1:6379> HINCRBY  heima:user:4 age 2
(integer) 22
127.0.0.1:6379> HVALS heima:user:4
1) "LiLei"
2) "22"
3) "man"
127.0.0.1:6379> HINCRBY  heima:user:4 age -2
(integer) 20
```

* HSETNX

```java
127.0.0.1:6379> HSETNX heima:user4 sex woman
(integer) 1
127.0.0.1:6379> HGETALL heima:user:3
1) "name"
2) "Lucy"
3) "age"
4) "17"
127.0.0.1:6379> HSETNX heima:user:3 sex woman
(integer) 1
127.0.0.1:6379> HGETALL heima:user:3
1) "name"
2) "Lucy"
3) "age"
4) "17"
5) "sex"
6) "woman"
```

## 6.6 Redis命令-List命令

**<font color=red>Redis中的List类型与Java中的LinkedList类似</font>**，可以看做是一个双向链表结构。既可以支持正向检索和也可以支持反向检索。

特征也与LinkedList类似：

* 有序
* 元素可以重复
* 插入和删除快
* 查询速度一般

常用来存储一个有序数据，例如：朋友圈点赞列表，评论列表等。

**List的常见命令有：**

- LPUSH key element ... ：向列表左侧插入一个或多个元素
- LPOP key：移除并返回列表左侧的第一个元素，没有则返回nil
- RPUSH key element ... ：向列表右侧插入一个或多个元素
- RPOP key：移除并返回列表右侧的第一个元素
- LRANGE key star end：返回一段角标范围内的所有元素
- BLPOP和BRPOP：与LPOP和RPOP类似，只不过在没有元素时等待指定时间，而不是直接返回nil

![1652943604992](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020039446.png)

* LPUSH和RPUSH

```java
127.0.0.1:6379> LPUSH users 1 2 3
(integer) 3
127.0.0.1:6379> RPUSH users 4 5 6
(integer) 6
```

* LPOP和RPOP

```java
127.0.0.1:6379> LPOP users
"3"
127.0.0.1:6379> RPOP users
"6"
```

* LRANGE

```java
127.0.0.1:6379> LRANGE users 1 2
1) "1"
2) "4"
```

## 6.7 Redis命令-Set命令

Redis的Set结构与Java中的HashSet类似，可以看做是一个value为null的HashMap。因为也是一个hash表，因此具备与HashSet类似的特征：

* 无序
* 元素不可重复
* 查找快
* 支持交集.并集.差集等功能

**Set类型的常见命令**

* SADD key member ... ：向set中添加一个或多个元素
* SREM key member ... : 移除set中的指定元素
* SCARD key： 返回set中元素的个数
* SISMEMBER key member：判断一个元素是否存在于set中
* SMEMBERS：获取set中的所有元素
* SINTER key1 key2 ... ：求key1与key2的交集
* SDIFF key1 key2 ... ：求key1与key2的差集
* SUNION key1 key2 ..：求key1和key2的并集





例如两个集合：s1和s2:

![](https://i.imgur.com/ha8x86R.png)

求交集：SINTER s1 s2

求s1与s2的不同：SDIFF s1 s2

![](https://i.imgur.com/L9vTv2X.png)





**具体命令**

```java
127.0.0.1:6379> sadd s1 a b c
(integer) 3
127.0.0.1:6379> smembers s1
1) "c"
2) "b"
3) "a"
127.0.0.1:6379> srem s1 a
(integer) 1
    
127.0.0.1:6379> SISMEMBER s1 a
(integer) 0
    
127.0.0.1:6379> SISMEMBER s1 b
(integer) 1
    
127.0.0.1:6379> SCARD s1
(integer) 2
```

**案例**

* 将下列数据用Redis的Set集合来存储：
* 张三的好友有：李四.王五.赵六
* 李四的好友有：王五.麻子.二狗
* 利用Set的命令实现下列功能：
* 计算张三的好友有几人
* 计算张三和李四有哪些共同好友
* 查询哪些人是张三的好友却不是李四的好友
* 查询张三和李四的好友总共有哪些人
* 判断李四是否是张三的好友
* 判断张三是否是李四的好友
* 将李四从张三的好友列表中移除

```java
127.0.0.1:6379> SADD zs lisi wangwu zhaoliu
(integer) 3
    
127.0.0.1:6379> SADD ls wangwu mazi ergou
(integer) 3
    
127.0.0.1:6379> SCARD zs
(integer) 3
    
127.0.0.1:6379> SINTER zs ls
1) "wangwu"
    
127.0.0.1:6379> SDIFF zs ls
1) "zhaoliu"
2) "lisi"
    
127.0.0.1:6379> SUNION zs ls
1) "wangwu"
2) "zhaoliu"
3) "lisi"
4) "mazi"
5) "ergou"
    
127.0.0.1:6379> SISMEMBER zs lisi
(integer) 1
    
127.0.0.1:6379> SISMEMBER ls zhangsan
(integer) 0
    
127.0.0.1:6379> SREM zs lisi
(integer) 1
    
127.0.0.1:6379> SMEMBERS zs
1) "zhaoliu"
2) "wangwu"
```

## 6.8 Redis命令-SortedSet类型

Redis的SortedSet是一个可排序的set集合，与Java中的TreeSet有些类似，但底层数据结构却差别很大。SortedSet中的每一个元素都带有一个score属性，可以基于score属性对元素排序，底层的实现是一个跳表（SkipList）加 hash表。

SortedSet具备下列特性：

- 可排序
- 元素不重复
- 查询速度快

因为SortedSet的可排序特性，经常被用来实现排行榜这样的功能。



SortedSet的常见命令有：

- ZADD key score member：添加一个或多个元素到sorted set ，如果已经存在则更新其score值
- ZREM key member：删除sorted set中的一个指定元素
- ZSCORE key member : 获取sorted set中的指定元素的score值
- ZRANK key member：获取sorted set 中的指定元素的排名
- ZCARD key：获取sorted set中的元素个数
- ZCOUNT key min max：统计score值在给定范围内的所有元素的个数
- ZINCRBY key increment member：让sorted set中的指定元素自增，步长为指定的increment值
- ZRANGE key min max：按照score排序后，获取指定排名范围内的元素
- ZRANGEBYSCORE key min max：按照score排序后，获取指定score范围内的元素
- ZDIFF.ZINTER.ZUNION：求差集.交集.并集

注意：所有的排名默认都是升序，如果要降序则在命令的Z后面添加REV即可，例如：

- **升序**获取sorted set 中的指定元素的排名：ZRANK key member
- **降序**获取sorted set 中的指定元素的排名：ZREVRANK key memeber



# 7.Redis的Java客户端-Jedis

在Redis官网中提供了各种语言的客户端，地址：https://redis.io/docs/clients/

![](https://i.imgur.com/9f68ivq.png)

其中Java客户端也包含很多：

![image-20220609102817435](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212012343760.png)

标记为❤的就是推荐使用的java客户端，包括：

- Jedis和Lettuce：这两个主要是提供了Redis命令对应的API，方便我们操作Redis，而SpringDataRedis又对这两种做了抽象和封装，因此我们后期会直接以SpringDataRedis来学习。
- Redisson：是在Redis基础上实现了分布式的可伸缩的java数据结构，例如Map.Queue等，而且支持跨进程的同步机制：Lock.Semaphore等待，比较适合用来实现特殊的功能需求。



## 7.1 Jedis快速入门

**入门案例详细步骤**

案例分析：

0）创建工程：

![1652959239813](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020039686.png)

1）引入依赖：

```xml
<!--jedis-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.7.0</version>
</dependency>
<!--单元测试-->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.7.0</version>
    <scope>test</scope>
</dependency>
```



2）建立连接

新建一个单元测试类，内容如下：

```java
private Jedis jedis;

@BeforeEach
void setUp() {
    // 1.建立连接
    // jedis = new Jedis("192.168.150.101", 6379);
    jedis = JedisConnectionFactory.getJedis();
    // 2.设置密码
    jedis.auth("123321");
    // 3.选择库
    jedis.select(0);
}
```



3）测试：

```java
@Test
void testString() {
    // 存入数据
    String result = jedis.set("name", "虎哥");
    System.out.println("result = " + result);
    // 获取数据
    String name = jedis.get("name");
    System.out.println("name = " + name);
}

@Test
void testHash() {
    // 插入hash数据
    jedis.hset("user:1", "name", "Jack");
    jedis.hset("user:1", "age", "21");

    // 获取
    Map<String, String> map = jedis.hgetAll("user:1");
    System.out.println(map);
}
```



4）释放资源

```java
@AfterEach
void tearDown() {
    if (jedis != null) {
        jedis.close();
    }
}
```





## 7.2 Jedis连接池

Jedis本身是线程不安全的，并且频繁的创建和销毁连接会有性能损耗，因此我们推荐大家使用Jedis连接池代替Jedis的直连方式

有关池化思想，并不仅仅是这里会使用，很多地方都有，比如说我们的数据库连接池，比如我们tomcat中的线程池，这些都是池化思想的体现。

### 7.2.1.创建Jedis的连接池

```java
/**
 * @author lxy
 * @version 1.0
 * @Description
 * @date 2022/8/27 21:58
 */
public class JedisConnectionFactory {
    private static final JedisPool jedisPool;

    static {
        // 配置连接池
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        //最大连接数 （最多允许创建的连接数）
        jedisPoolConfig.setMaxTotal(8);
        // 最大空闲连接 （最多预备的连接数）
        jedisPoolConfig.setMaxIdle(8);
        //最小空闲连接（在一段时间内，没有人使用，则可释放连接数到MinIdle）
        jedisPoolConfig.setMinIdle(0);
        //设置最长等待时间，ms (连接池中没有连接时，等待的时长。默认是 -1，无限制的等待)
        jedisPoolConfig.setMaxWaitMillis(200);
        // 创建连接池对象，参数：连接池配置、服务端ip、服务端端口、超时时间、（密码）
        jedisPool = new JedisPool(jedisPoolConfig, "192.168.174.128", 6379, 1000);
    }

    //获取Jedis对象
    public static Jedis getJedis(){
        return jedisPool.getResource();
    }

}
```

**代码说明：**

- 1） JedisConnectionFacotry：工厂设计模式是实际开发中非常常用的一种设计模式，我们可以使用工厂，去降低代的耦合，比如Spring中的Bean的创建，就用到了工厂设计模式

- 2）静态代码块：<font color=red>随着类的加载而加载，确保只能执行一次</font>，我们在加载当前工厂类的时候，就可以执行static的操作完成对 连接池的初始化

- 3）最后提供返回连接池中连接的方法.



### 7.2.2.改造原始代码

**代码说明:**

1.在我们完成了使用工厂设计模式来完成代码的编写之后，我们在获得连接时，就可以通过工厂来获得。而不用直接去new对象，降低耦合，并且使用的还是连接池对象。

2.当我们使用了连接池后，当我们关闭连接其实并不是关闭，而是将Jedis还回连接池的。

```java
    @BeforeEach
    void setUp(){
        //建立连接
        /*jedis = new Jedis("127.0.0.1",6379);*/
        jedis = JedisConnectionFacotry.getJedis();
         //选择库
        jedis.select(0);
    }

   @AfterEach
    void tearDown() {
        if (jedis != null) {
            jedis.close();
        }
    }
```

注意此时`jedis.close();`的底层调用的不再是关闭连接，而是将连接放回到连接池中

![image-20221015201419871](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020039599.png)

# 8.Redis的Java客户端-SpringDataRedis

SpringData是Spring中数据操作的模块，包含对各种数据库的集成，其中对Redis的集成模块就叫做SpringDataRedis，官网地址：https://spring.io/projects/spring-data-redis

* 提供了对不同Redis客户端的整合（Lettuce和Jedis）
* 提供了RedisTemplate统一API来操作Redis
* 支持Redis的发布订阅模型
* 支持Redis哨兵和Redis集群
* 支持基于Lettuce的响应式编程
* 支持基于JDK.JSON.字符串.Spring对象的数据序列化及反序列化
* 支持基于Redis的JDKCollection实现（基于Redis重新实现）

SpringDataRedis中提供了RedisTemplate工具类，其中封装了各种对Redis的操作。并且将不同数据类型的操作API封装到了不同的类型中：

![1652976773295](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020039975.png)



## 8.1.快速入门

SpringBoot已经提供了对SpringDataRedis的支持，使用非常简单：

### 8.1.1 导入pom坐标

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.7</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.heima</groupId>
    <artifactId>redis-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>redis-demo</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <!--redis依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <!--common-pool-->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
        </dependency>
        <!--Jackson依赖-->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

### 8.1.2 配置文件

```yaml
spring:
  redis:
    host: 192.168.174.128
    port: 6379
    password: 123321
    lettuce:
      pool:
        max-active: 8  #最大连接
        max-idle: 8   #最大空闲连接
        min-idle: 0   #最小空闲连接
        max-wait: 100ms #连接等待时间
```

### 8.1.3 测试代码

```java
@SpringBootTest
class RedisDemoApplicationTests {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    @Test
    void testString() {
        // 写入一条String数据
        redisTemplate.opsForValue().set("name", "虎哥");
        // 获取string数据
        Object name = redisTemplate.opsForValue().get("name");
        System.out.println("name = " + name);
    }
}
```

**贴心小提示：SpringDataJpa使用起来非常简单，记住如下几个步骤即可**

SpringDataRedis的使用步骤：

* 引入spring-boot-starter-data-redis依赖
* 在application.yml配置Redis信息
* 注入RedisTemplate

## 8.2 .数据序列化器

RedisTemplate可以接收任意Object作为值写入Redis：

![](https://i.imgur.com/OEMcbuu.png)

当我们执行完上面6.1 的测试方法后，从redis-cli下 获取name的值，结果如下：

![image-20221030174718267](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020040324.png)

为啥我们存入的值 和我们看到的不一样呢？

因为写入前后会把 Java对象 利用 JDK序列化器转成可处理的字节 来存储到Redis中，而序列化器底层是ObjectOutputStream。得到的结果就是上面那样

```java
public class RedisTemplate<K, V> extends RedisAccessor implements RedisOperations<K, V>, BeanClassLoaderAware {
	//四个序列化器
    private @Nullable RedisSerializer keySerializer = null;
	private @Nullable RedisSerializer valueSerializer = null;
	private @Nullable RedisSerializer hashKeySerializer = null;
	private @Nullable RedisSerializer hashValueSerializer = null;
    
    public void afterPropertiesSet() {

		super.afterPropertiesSet();

		boolean defaultUsed = false;

		if (defaultSerializer == null) {

			defaultSerializer = new JdkSerializationRedisSerializer(
					classLoader != null ? classLoader : this.getClass().getClassLoader());
		}
    }

}

//JdkSerializationRedisSerializer底层
ObjectOutputStream objectOutputStream = new ObjectOutputStream(outputStream);
```

缺点：

- 可读性差
- 内存占用较大

分析系统中的序列化器：

![image-20221112182438644](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020040102.png)

**<font color=red>我们可以自定义RedisTemplate的序列化方式</font>**，代码如下：

```java
/**
 * @author lxy
 * @version 1.0 
 * @date 2022/11/12 18:36
 * @Description 配置Redis的序列化器 
 * key：使用字符串序列化器，value的类型不确定，也就是Object，所以我们使用JSON序列化器
 */
@Configuration
public class RedisConfig {
    @Bean
    public RedisTemplate <String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        // 创建RedisTemplate对象
        RedisTemplate <String, Object> template = new RedisTemplate <>();
        // 创建连接工厂
        template.setConnectionFactory(connectionFactory);
        // 创建JSON序列化器
        //RedisSerializer.string(),RedisSerializer.json() 也可以得到对应的序列化器.
        GenericJackson2JsonRedisSerializer jsonRedisSerializer =
                new GenericJackson2JsonRedisSerializer();
        StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
        // 设置key的序列化
        template.setKeySerializer(stringRedisSerializer);
        template.setHashKeySerializer(stringRedisSerializer);
        // 设置Value的序列化
        template.setValueSerializer(jsonRedisSerializer);
        template.setHashValueSerializer(jsonRedisSerializer);
        // 返回
        return template;
    }
}
```

最终结果如图：

- 当存储字符串时

![image-20221120172922088](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020040981.png)

- 当存储对象时

![image-20221120174216014](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020040085.png)

整体可读性有了很大提升，并且能将Java对象自动的序列化为JSON字符串，并且查询时能自动把JSON反序列化为Java对象。不过，其中记录了序列化时对应的class名称，目的是为了查询时实现自动反序列化。这会带来额外的内存开销。

**<font color=blue>注意：如果运行后报下面的错误，可能是需要引入 `jackson-databind`</font>**

![image-20221120172000470](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020040391.png)

## 8.3 StringRedisTemplate

尽管JSON的序列化方式可以满足我们的需求，但依然存在一些问题，如图：

![image-20221120180904416](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020040566.png)

为了在反序列化时知道对象的类型，JSON序列化器会将类的class类型写入json结果中，存入Redis，会带来额外的内存开销。

==为了减少内存的消耗，我们可以采用手动序列化的方式==。换句话说，就是不借助默认的序列化器，而是我们自己来控制序列化的动作，同时，我们只采用String的序列化器，这样，在存储value时，我们就不需要在内存中就不用多存储数据，从而节约我们的内存空间

![1653054744832](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020040470.png)

这种用法比较普遍，因此SpringDataRedis就提供了RedisTemplate的子类：**<font color =red>StringRedisTemplate</font>**，它的key和value的序列化方式默认就是String方式。

![](https://i.imgur.com/zXH6Qn6.png)

省去了我们自定义RedisTemplate的序列化方式的步骤，而是直接使用：

```java
@SpringBootTest
class RedisTemplateTests {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;


    @Test
    void testString() {
        // 写入一条String数据
        stringRedisTemplate.opsForValue().set("name", "大李");
        // 获取String数据
        Object name = stringRedisTemplate.opsForValue().get("name");
        System.out.println("name"+name);
    }


    @Test
    public void testSaveUser() throws JsonProcessingException {
        ObjectMapper mapper = new ObjectMapper();
        //创建对象
        User user = new User("大李", 21);
        //手动序列化（这里也可以使用fastjson的相关方法）
        String json = mapper.writeValueAsString(user);
        //写入数据
        stringRedisTemplate.opsForValue().set("user:200",json);
        //获取数据
        String jsonUser =  stringRedisTemplate.opsForValue().get("user:200");
        User user1 = mapper.readValue(jsonUser, User.class);
        System.out.println("user1:"+user1);
    }
}
```

此时我们再来看一看存储的数据，小伙伴们就会发现那个class数据已经不在了，节约了我们的空间~

![image-20221120181737759](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020040613.png)

总结：RedisTemplate的两种序列化实践方案

* 方案一：
  * 自定义RedisTemplate
  * 修改RedisTemplate的序列化器为GenericJackson2JsonRedisSerializer

* 方案二：
  * 使用StringRedisTemplate
  * 写入Redis时，手动把对象序列化为JSON
  * 读取Redis时，手动把读取到的JSON反序列化为对象


## 8.4 Hash结构操作

在基础篇的最后，咱们对Hash结构操作一下，收一个小尾巴，这个代码咱们就不再解释啦

马上就开始新的篇章~~~进入到我们的Redis实战篇

```java
@SpringBootTest
class RedisStringTests {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;


    @Test
    void testHash(){
        stringRedisTemplate.opsForHash().put("user:400","name","大李");
        stringRedisTemplate.opsForHash().put("user:400","age","21");
		// 获取所有的键值对
        Map <Object, Object> entries = stringRedisTemplate.opsForHash().entries("user:400");
        System.out.println("entries = "+entries);
		// 获取所有的keys
        Set <Object> keys = stringRedisTemplate.opsForHash().keys("user:400");
        System.out.println("keys = "+keys);
        //获取所有的values
        List <Object> values = stringRedisTemplate.opsForHash().values("user:400");
        System.out.println("values = "+values);
    }
}
```

结果如下：

![image-20221120182810267](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212020040638.png)