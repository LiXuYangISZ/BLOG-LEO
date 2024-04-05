---
title: 五、分布式锁-Redission
date: 2024-04-05 14:46:05
tags: Redis
categorys: Redis从入门到入土
---

## 5、分布式锁-redission

### 5.1 分布式锁-redission功能介绍

基于setnx实现的分布式锁存在下面的问题：

**重入问题**：重入问题是指 获得锁的线程可以再次进入到相同的锁的代码块中，可重入锁的意义在于防止死锁，比如HashTable这样的代码中，他的方法都是使用synchronized修饰的，假如他在一个方法内，调用另一个方法，那么此时如果是不可重入的，不就死锁了吗？所以可重入锁他的主要意义是防止死锁，我们的synchronized和Lock锁都是可重入的。

**不可重试**：是指目前的分布式只能尝试一次，我们认为合理的情况是：当线程在获得锁失败后，他应该能再次尝试获得锁。

**超时释放：**我们在加锁时增加了过期时间，这样的我们可以防止死锁，但是如果卡顿的时间超长，虽然我们采用了lua表达式防止删锁的时候，误删别人的锁，但是毕竟没有锁住，有安全隐患

**主从一致性：** 如果Redis提供了主从集群，当我们向集群写数据时，主机需要异步的将数据同步给从机，而万一在同步过去之前，主机宕机了，就会出现死锁问题。

![1653546070602](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230913.png)

那么什么是Redission呢

Redisson是一个在Redis的基础上实现的Java驻内存数据网格（In-Memory Data Grid）。它不仅提供了一系列的分布式的Java常用对象，还提供了许多分布式服务，其中就包含了各种分布式锁的实现。

Redission提供了分布式锁的多种多样的功能

![image-20221226022903663](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230911.png)

### 5.2 分布式锁-Redission快速入门

引入依赖：

```java
<dependency>
	<groupId>org.redisson</groupId>
	<artifactId>redisson</artifactId>
	<version>3.13.6</version>
</dependency>
```

配置Redisson客户端：

```java
@Configuration
public class RedissonConfig {

    @Bean
    public RedissonClient redissonClient(){
        // 配置
        Config config = new Config();
        config.useSingleServer().setAddress("redis://192.168.174.128:6379");
        // 创建RedissonClient对象
        return Redisson.create(config);
    }
}
```

在 VoucherOrderServiceImpl使用Redisson带的分布式锁：

```java
@Resource
private RedissonClient redissonClient;

@Override
public Result seckillVoucher(Long voucherId) {
    // 1.获取优惠券信息
    SeckillVoucher voucher = seckillVoucherService.getById(voucherId);
    // 2.判断秒杀是否开始
    LocalDateTime beginTime = voucher.getBeginTime();
    LocalDateTime endTime = voucher.getEndTime();
    if(beginTime.isAfter(LocalDateTime.now()) || endTime.isBefore(LocalDateTime.now())){
        return Result.fail("不再秒杀时段内！");
    }
    // 3.判断库存是否充足
    if(voucher.getStock() < 1){
        //库存不足
        return Result.fail("库存不足！");
    }
    Long userId = UserHolder.getUser().getId();
    // 这个代码我们不用了，下面要用Redisson中的分布式锁
    // SimpleRedisLock lock = new SimpleRedisLock("order:" + userId, stringRedisTemplate);
    RLock lock = redissonClient.getLock("order:" + userId);
    boolean isLock = lock.tryLock();
    // 判断是否获取锁成功
    if(!isLock){
        // 获取锁失败，返回错误和重试
        return Result.fail("不允许重复下单~");
    }
    try {
        // 获取代理对象（只有通过代理对象调用方法，事务才会生效）
        IVoucherOrderService proxy = (IVoucherOrderService) AopContext.currentProxy();
        return proxy.createVoucherOrder(voucherId);
    } finally {
        lock.unlock();
    }
}
```

**进行测试：**

在集群环境下，一秒一千次请求~ 一个用户只能下一单。分布式锁测试成功~ 

![image-20221221131219831](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230922.png)

![image-20221221131345069](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230924.png)

### 5.3 分布式锁-redission可重入锁原理

在Lock锁中，他是借助于底层的一个voaltile的一个state变量来记录重入的状态的，比如当前没有人持有这把锁，那么state=0，假如有人持有这把锁，那么state=1，如果持有这把锁的人再次持有这把锁，那么state就会+1 ，如果是对于synchronized而言，他在c语言代码中会有一个count，原理和state类似，也是重入一次就加一，释放一次就-1 ，直到减少成0 时，表示当前这把锁没有被人持有。  

在redission中，我们的也支持支持可重入锁

在分布式锁中，他==采用hash结构==用来存储锁，其中<font color=red>大key表示表示这把锁是否存在，用小key表示当前这把锁被哪个线程持有</font>。流程图如下：

![image-20221221155020125](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230927.png)

> 为什么每次获取锁成功 或 释放锁 后都要重新设置锁的有效期呢?
>
> 这样是为了下面的业务有足够的时间去执行~

**<font color=orange>1、接下来我们一起分析一下当前的可重入锁实现的lua表达式</font>**

- 获取锁的Lua脚本：

```lua
local key = KEYS[1]; -- 锁的key
local threadId = ARGV[1]; -- 线程唯一标识
local releaseTime = ARGV[2]; -- 锁的自动释放时间
-- 判断是否存在
if(redis.call('exists', key) == 0) then
    -- 不存在, 获取锁
    redis.call('hset', key, threadId, '1'); 
    -- 设置有效期
    redis.call('expire', key, releaseTime); 
    return 1; -- 返回结果
end;
-- 锁已经存在，判断threadId是否是自己
if(redis.call('hexists', key, threadId) == 1) then
    -- 存在, 获取锁，重入次数+1
    redis.call('hincrby', key, threadId, '1'); 
    -- 设置有效期
    redis.call('expire', key, releaseTime); 
    return 1; -- 返回结果
end;
return 0; -- 代码走到这里,说明获取锁的不是自己，获取锁失败
```

- 释放锁的Lua脚本：

```lua
local key = KEYS[1]; -- 锁的key
local threadId = ARGV[1]; -- 线程唯一标识
local releaseTime = ARGV[2]; -- 锁的自动释放时间
-- 判断当前锁是否还是被自己持有
if (redis.call('HEXISTS', key, threadId) == 0) then
    return nil; -- 如果已经不是自己，则直接返回
end;
-- 是自己的锁，则重入次数-1
local count = redis.call('HINCRBY', key, threadId, -1);
-- 判断是否重入次数是否已经为0 
if (count > 0) then
    -- 大于0说明不能释放锁，重置有效期然后返回
    redis.call('EXPIRE', key, releaseTime);
    return nil;
else  -- 等于0说明可以释放锁，直接删除
    redis.call('DEL', key);
    return nil;
end;
```

**<font color=orange>2、测试Redission的分布式锁的可重入效果</font>**

```java
/**
 * @author lxy
 * @version 1.0
 * @Description 测试Redisson的分布式锁的可重入性质
 * @date 2022/12/21 16:01
 */
@Slf4j
@SpringBootTest
public class RedissonTest {

    @Resource
    private RedissonClient redissonClient;

    private RLock lock;

    @BeforeEach
    void setUp(){
        lock = redissonClient.getLock("order");
    }

    @Test
    void method1() throws InterruptedException {
        // 尝试获取锁
        boolean isLock = lock.tryLock(1L, TimeUnit.SECONDS);
        if (!isLock){
            log.error("获取锁失败....1");
            return;
        }
        try {
            log.info("获取锁成功....1");
            method2();
            log.info("开始执行业务....1");
        } finally {
            log.warn("开始释放锁....1");
            lock.unlock();
        }

    }

    void method2(){
        // 尝试获取锁
        boolean isLock = lock.tryLock();
        if(!isLock){
            log.error("获取锁失败....2");
            return;
        }
        try {
            log.info("获取锁成功....2");
            log.info("开始执行业务....2");
        } finally {
            log.warn("准备释放锁....2");
            lock.unlock();
        }
    }
}
```

Debug测试：

![](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230938.png)

![](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230697.png)

**<font color=orange>3、接下来我们可以查看下Redisson中的分布式锁的实现：</font>**

![image-20221221163708051](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230707.png)

> 注意源码中的KEYS[1]指外边的大Key，AVG[1]：大Key的过期时间，AVG[2]：当前的线程ID

![image-20221221163825349](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230745.png)

> 源码中的KEYS[1]指外边的大Key，AVG[2]：大Key的过期时间，AVG[3]：当前的线程ID。KEYS[2]和ARGV[1]所代表的含义我们后面会讲解~

### 5.4 分布式锁-redission锁重试和WatchDog机制

关于锁可重试的原理见：https://www.processon.com/view/link/63a86e6534446c6f609d3a3f

关于锁超时续约 和 锁释放的原理见：https://www.processon.com/view/link/63a891cece3d3c6150d7c2ac

**Redission分布式锁原理**

![image-20221226021503722](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230750.png)

> 注意:只有leaseTime=-1,才会走WatchDog的逻辑

**总结：Redisson分布式锁原理**

- 可重入：利用hash结构记录线程id和重入次数
- 可重试：利用信号量和PubSub功能实现等待、唤醒，获取锁失败的重试机制
- 超时续约：利用watchDog，每隔一段时间（releaseTime / 3），重置超时时间

### 5.5 分布式锁-redission锁的MutiLock原理

为了提高redis的可用性，我们会搭建集群或者主从，现在以主从为例

此时我们去写命令，写在主机上， 主机会将数据同步给从机，但是假设在主机还没有来得及把数据写入到从机去的时候，此时主机宕机，哨兵会发现主机宕机，并且选举一个slave变成master，而此时新的master中实际上并没有锁信息，此时锁信息就已经丢掉了。

![image-20221226022637870](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230763.png)

为了解决这个问题，redission提出来了MutiLock锁，使用这把锁咱们就不使用主从了，每个节点的地位都是一样的， 这把锁加锁的逻辑需要写入到每一个主丛节点上，只有所有的服务器都写入成功，此时才是加锁成功，假设现在某个节点挂了，那么他去获得锁的时候，只要有一个节点拿不到，都不能算是加锁成功，就保证了加锁的可靠性。

![image-20221226022539880](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230772.png)

那么MutiLock 加锁原理是什么呢？笔者画了一幅图来说明

当我们去设置了多个锁时，redission会将多个锁添加到一个集合中，然后用while循环去不停去尝试拿锁，但是会有一个总共的加锁时间，这个时间是用需要加锁的个数 * 1500ms ，假设有3个锁，那么时间就是4500ms，假设在这4500ms内，所有的锁都加锁成功， 那么此时才算是加锁成功，如果在4500ms有线程加锁失败，则会再次去进行重试.



![1653553093967](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202212260230606.png)

### 5.6 总结

**1）不可重入Redis分布式锁：**
原理：利用setnx的互斥性；利用ex避免死锁；释放锁时判断线程标示
缺陷：不可重入、无法重试、锁超时失效
**2）可重入的Redis分布式锁：**
原理：利用hash结构，记录线程标示和重入次数；利用watchDog延续锁时间；利用信号量控制锁重试等待
缺陷：redis宕机引起锁失效问题
**3）Redisson的multiLock：**
原理：多个独立的Redis节点，必须在所有节点都获取重入锁，才算获取锁成功
缺陷：运维成本高、实现复杂