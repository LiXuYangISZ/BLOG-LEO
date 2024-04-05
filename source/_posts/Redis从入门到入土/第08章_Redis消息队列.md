---
title: 七、Redis消息队列
date: 2024-04-05 14:46:07
tags: Redis
categorys: Redis从入门到入土
---

## 7、Redis消息队列

### 7.1 Redis消息队列-认识消息队列

什么是消息队列：字面意思就是存放消息的队列。最简单的消息队列模型包括3个角色：

* 消息队列：存储和管理消息，也被称为消息代理（Message Broker）
* 生产者：发送消息到消息队列
* 消费者：从消息队列获取消息并处理消息

![1653574849336](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007695.png)

使用队列的好处在于 **解耦：**所谓解耦，举一个生活中的例子就是：快递员(生产者)把快递放到快递柜里边(Message Queue)去，我们(消费者)从快递柜里边去拿东西，这就是一个异步，如果耦合，那么这个快递员相当于直接把快递交给你，这事固然好，但是万一你不在家，那么快递员就会一直等你，这就浪费了快递员的时间，所以这种思想在我们日常开发中，是非常有必要的。

这种场景在我们秒杀中就变成了：我们下单之后，利用redis去进行校验下单条件，再通过队列把消息发送出去，然后再启动一个线程去消费这个消息，完成解耦，同时也加快我们的响应速度。

这里我们可以使用一些现成的mq，比如kafka，rabbitmq等等，但是呢，如果没有安装mq，我们也可以直接使用redis提供的mq方案，降低我们的部署和学习成本。



### 7.2 Redis消息队列-基于List实现消息队列

**基于List结构模拟消息队列**

消息队列（Message Queue），字面意思就是存放消息的队列。而Redis的list数据结构是一个双向链表，很容易模拟出队列效果。

队列是入口和出口不在一边，因此我们可以利用：LPUSH 结合 RPOP、或者 RPUSH 结合 LPOP来实现。
不过要注意的是，当队列中没有消息时RPOP或LPOP操作会返回null，并不像JVM的阻塞队列那样会阻塞并等待消息。因此这里应该使用BRPOP或者BLPOP来实现阻塞效果。

![1653575176451](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007676.png)

**案例演示：**

```shell
######生产者######
127.0.0.1:6379> LPUSH l1 e1 e2; 
(integer) 2
######消费者######
127.0.0.1:6379> BRPOP l1 20
1) "l1"
2) "e1"
(11.81s)
127.0.0.1:6379> BRPOP l1 20
1) "l1"
2) "e2;"
127.0.0.1:6379> BRPOP l1 20 #队列没有元素会被阻塞，到超时时间还没有就返货null，结束获取
(nil)
(20.10s)
```

**基于List的消息队列有哪些优缺点？**
优点：

* 利用Redis存储，不受限于JVM内存上限
* 基于Redis的持久化机制，数据安全性有保证
* 可以满足消息有序性

缺点：

* 无法避免消息丢失【比如刚从消息队列取出一条消息，还没来得及处理，Redis就发生宕机，这个消息就会丢失】
* 只支持单消费者【一条消息只能被一个消费者消费，无法被多个消费者消费】

### 7.3 Redis消息队列-基于PubSub的消息队列

PubSub（发布订阅）是Redis2.0版本引入的消息传递模型。顾名思义，`消费者可以订阅一个或多个channel`，生产者向对应channel发送消息后，所有订阅者都能收到相关消息。

` SUBSCRIBE channel [channel] `：订阅一个或多个频道
 `PUBLISH channel msg `：向一个频道发送消息
 `PSUBSCRIBE pattern[pattern]` ：订阅与pattern格式匹配的所有频道

![1653575506373](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007701.png)

**案例演示**

![](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007699.png)



**基于PubSub的消息队列有哪些优缺点？**
优点：

* 采用发布订阅模型，支持多生产、多消费【一个消息可以发给多个/部分消费者，不同生产者往相同的频道发】

缺点：

* 不支持数据持久化
  * 为什么list作为消息队列可以持久化？是因为list本身是一个链表，用来做数据存储的。而我们把他当做消息队列来用了。而Redis中所有用来做数据存储的结构都支持持久化~ 而Pubsub就是用来做消息发送的，因此当我们发送一条消息，而这个消息没有被任何人订阅，频道没有被任何人订阅，那么这个消息就直接丢失了。
* 无法避免消息丢失
  * 有人订阅消息就会被使用，没人订阅消息就会丢失~
* 消息堆积有上限，超出时数据丢失
  * 如果发送消息时，有消费者在监听，在消费者那里有一个缓存区域，把消息缓存下来，让消费者去处理。如果消息处理的很慢，并且还有源源不断的消息到来，因为消费者那里的空间是有上限的，超出就会消息丢失~

> 总结：这种模式的缺点较多，不适合做可靠性较高的消息模式~

### 7.4 Redis消息队列-基于Stream的消息队列

Stream 是 Redis 5.0 引入的一种新数据类型，可以实现一个功能非常完善的消息队列。

发送消息的命令：

![1653577301737](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007706.png)

例如：

![1653577349691](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007722.png)

读取消息的方式之一：XREAD

![1653577445413](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007385.png)

例如，使用XREAD读取第一个消息：

![1653577643629](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007441.png)

XREAD阻塞方式，读取最新的消息：

![1653577659166](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007485.png)

在业务开发中，我们可以循环的调用XREAD阻塞方式来查询最新消息，从而实现持续监听队列的效果，伪代码如下

![1653577689129](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007529.png)

注意：当我们指定起始ID为$时，代表`读取最新的消息`，如果我们处理一条消息的过程中，又有超过1条以上的消息到达队列，则下次获取时也只能获取到最新的一条，会出现`漏读消息`的问题

**案例演示：**

```shell
###生产者
127.0.0.1:6379> XADD s1 * k1 v1 # 向队列中发送消息
"1675947993952-0"
127.0.0.1:6379> XLEN s1 #查看队列中的消息个数
(integer) 1
###消费者1&&消费者2
127.0.0.1:6379> XREAD COUNT 1 STREAMS s1 0 #读取队列中的第一条消息 [可以说明消息可回溯]
1) 1) "s1"
   2) 1) 1) "1675947993952-0"
         2) 1) "k1"
            2) "v1"
            
###生产者
127.0.0.1:6379> XADD s1 * k2 v2 # 向队列中发送消息
"1675948153658-0"
                   
###消费者1
127.0.0.1:6379> XREAD COUNT 1 BLOCK 0 STREAMS s1 $ #阻塞读
1) 1) "s1"
   2) 1) 1) "1675948153658-0"
         2) 1) "k2"
            2) "v2"
```

**STREAM类型消息队列的XREAD命令特点：**

* 消息可回溯【消息读完不消失，永久的保存在消息队列中，啥时候还想看可以随时回来】
* 一个消息可以被多个消费者读取
* 可以阻塞读取
* 有消息漏读的风险【在消息处理的过程中，如果来了很多消息，我看不到，只能看到最新的消息】

### 7.5 Redis消息队列-基于Stream的消息队列-消费者组

消费者组（Consumer Group）：将多个消费者划分到一个组中，监听同一个队列。具备下列特点：

![image-20230210142845558](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007578.png)

创建消费者组：
![1653577984924](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007629.png)
key：队列名称
groupName：消费者组名称
ID：起始ID标示，$代表队列中最后一个消息，0则代表队列中第一个消息
MKSTREAM：队列不存在时自动创建队列
其它常见命令：

 **删除指定的消费者组**

```java
XGROUP DESTORY key groupName
```

 **给指定的消费者组添加消费者**

```java
XGROUP CREATECONSUMER key groupname consumername
```

 **删除消费者组中的指定消费者**

```java
XGROUP DELCONSUMER key groupname consumername
```

从消费者组读取消息：

```java
XREADGROUP GROUP group consumer [COUNT count] [BLOCK milliseconds] [NOACK] STREAMS key [key ...] ID [ID ...]
```

* group：消费组名称
* consumer：消费者名称，如果消费者不存在，会自动创建一个消费者
* count：本次查询的最大数量
* BLOCK milliseconds：当没有消息时最长等待时间
* NOACK：无需手动ACK，获取到消息后自动确认
* STREAMS key：指定队列名称
* ID：获取消息的起始ID：

  * ">"：从下一个未消费的消息开始 【正常情况下】
  * 其它：根据指定id从pending-list中获取已消费但未确认的消息，例如0，是从pending-list中的第一个消息开始 【异常情况下】

**案例演示：**

```shell
###生产者
127.0.0.1:6379> XADD s1 * k1 v1  #向s1中加入消息
"1676013442138-0"
127.0.0.1:6379> XADD s1 * k2 v2
"1676013446154-0"
127.0.0.1:6379> XADD s1 * k3 v3
"1676013453085-0"
127.0.0.1:6379> XADD s1 * k4 v4
"1676013459707-0"
127.0.0.1:6379> XADD s1 * k5 v5
"1676013469043-0"
127.0.0.1:6379> XADD s1 * k6 v6
"1676013473875-0"
127.0.0.1:6379> XADD s1 * k7 v7
"1676013478635-0"
127.0.0.1:6379> XLEN s1 # 查看队列长度
(integer) 7
127.0.0.1:6379> XGROUP CREATE s1 g1 0 # 创建消费者组
OK

### 消费者1
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >
1) 1) "s1"
   2) 1) 1) "1676013442138-0"
         2) 1) "k1"
            2) "v1"
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >
1) 1) "s1"
   2) 1) 1) "1676013446154-0"
         2) 1) "k2"
            2) "v2"
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >
1) 1) "s1"
   2) 1) 1) "1676013469043-0"
         2) 1) "k5"
            2) "v5"

### 消费者2
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 > 
1) 1) "s1"
   2) 1) 1) "1676013453085-0"
         2) 1) "k3"
            2) "v3"
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >
1) 1) "s1"
   2) 1) 1) "1676013459707-0"
         2) 1) "k4"
            2) "v4"
###根据消费者1&消费者2的消费，可以看出消费组中的消费者是竞争关系的，并且同一个消费组中不会出现重复消费~

### 消费者1
127.0.0.1:6379> XACK s1 g1 1676013442138-0 1676013446154-0 1676013453085-0 1676013459707-0 1676013469043-0
(integer) 5 #对前五条消息进行ACK确认
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 > #继续消费消息
1) 1) "s1"
   2) 1) 1) "1676013473875-0"
         2) 1) "k6"
            2) "v6"
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >
1) 1) "s1"
   2) 1) 1) "1676013478635-0"
         2) 1) "k7"
            2) "v7"
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 >
(nil)
(2.08s)
# 由于1676013473875-0 和 1676013478635-0 被消费者消费后并没有ACK确认，会进入pending_list
127.0.0.1:6379> XPENDING s1 g1 - + 10 #查看pending_list中的消息，可以看出共两条消费失败的消息
1) 1) "1676013473875-0"
   2) "c1"
   3) (integer) 37154
   4) (integer) 1
2) 1) "1676013478635-0"
   2) "c1"
   3) (integer) 35353
   4) (integer) 1
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 0 #从pending_list中获取消息
1) 1) "s1"
   2) 1) 1) "1676013473875-0"
         2) 1) "k6"
            2) "v6"
127.0.0.1:6379> XACK s1 g1 1676013473875-0 # 消费完进行ACK确认
(integer) 1
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 0 #从pending_list中获取消息
1) 1) "s1"
   2) 1) 1) "1676013478635-0"
         2) 1) "k7"
            2) "v7"
127.0.0.1:6379> XACK s1 g1 1676013478635-0 # 消费完进行ACK确认
(integer) 1
127.0.0.1:6379> XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS s1 0 #再次获取，pending_list已经为空
1) 1) "s1"
   2) (empty array)
```

消费者监听消息的基本思路：

![image-20230210153808554](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007036.png)

STREAM类型消息队列的XREADGROUP命令特点：

* 消息可回溯
* 可以多消费者争抢消息，加快消费速度
* 可以阻塞读取
* 没有消息漏读的风险[因为读取过的消息会有标记，下次直接从有标记的下一条消息读取即可]
* 有消息确认机制，保证消息至少被消费一次

最后我们来个小对比

![image-20230210145602447](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007085.png)

> Redis的Stream基本满足中小项目的需求，如果是大型项目，则可以使用专门的MQ：RocketMQ、RabbitMQ、Kfaka等

### 7.6 基于Redis的Stream结构作为消息队列，实现异步秒杀下单

需求：

* 创建一个Stream类型的消息队列，名为stream.orders

* 修改之前的秒杀下单Lua脚本，在认定有抢购资格后，直接向stream.orders中添加消息，内容包含voucherId、userId、orderId
* 项目启动时，开启一个线程任务，尝试获取stream.orders中的消息，完成下单

①创建消息队列

```shell
127.0.0.1:6379> XGROUP CREATE stream.orders g1 0 MKSTREAM
OK
```

②修改lua表达式，新增3.5

![image-20230211163737153](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302140007125.png)

③VoucherOrderServiceImpl

```java
 /**
     * 优惠券订单处理器【基于消息队列】
     */
private class VoucherOrderHandler implements Runnable {

    private final static String queueName = "stream.orders";

    @Override
    public void run() {
        while (true) {
            try {
                // 1.获取消息队列中的订单信息  XREADGROUP GROUP g1 c1 COUNT 1 BLOCK 2000 STREAMS stream.orders >
                List <MapRecord <String, Object, Object>> list = stringRedisTemplate.opsForStream().read(
                    Consumer.from("g1", "c1"),
                    StreamReadOptions.empty().count(1).block(Duration.ofSeconds(2)),
                    StreamOffset.create(queueName, ReadOffset.lastConsumed())
                );
                // 2.判断消息是否获取成功
                if (list == null || list.isEmpty()) {
                    // 如果获取失败，说明没有消息，继续下一次循环
                    continue;
                }
                // 3.解析消息中的订单信息
                MapRecord <String, Object, Object> record = list.get(0);
                Map <Object, Object> values = record.getValue();
                VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(values, new VoucherOrder(),true);
                // 4.如果获取成功，可以下单
                handleVoucherOrder(voucherOrder);
                // 5.ACK确认 SACK strea.orders g1 id
                stringRedisTemplate.opsForStream().acknowledge(queueName,"g1",record.getId());
            } catch (Exception e) {
                log.error("处理订单异常：", e);
                handlePendingList();
            }
        }
    }

    /**
         * 处理PendingList中的订单
         */
    private void handlePendingList() {
        while (true) {
            try {
                // 1.获取pending-list中的订单信息  XREADGROUP GROUP g1 c1 COUNT 1  STREAMS stream.orders 0
                List <MapRecord <String, Object, Object>> list = stringRedisTemplate.opsForStream().read(
                    Consumer.from("g1", "c1"),
                    StreamReadOptions.empty().count(1),
                    StreamOffset.create(queueName, ReadOffset.from("0"))
                );
                // 2.判断消息是否获取成功
                if (list == null || list.isEmpty()) {
                    // 如果获取失败，说明pending-list没有异常消息，结束循环
                    break;
                }
                // 3.解析消息中的订单信息
                MapRecord <String, Object, Object> record = list.get(0);
                Map <Object, Object> values = record.getValue();
                VoucherOrder voucherOrder = BeanUtil.fillBeanWithMap(values, new VoucherOrder(),true);
                // 4.如果获取成功，可以下单
                handleVoucherOrder(voucherOrder);
                // 5.ACK确认 SACK stream.orders g1 id
                stringRedisTemplate.opsForStream().acknowledge(queueName,"g1",record.getId());
            } catch (Exception e) {
                log.error("处理pending-list订单异常：", e);
                try {
                    // 如果出现异常,休眠一会再尝试,避免一直尝试一直异常~
                    Thread.sleep(20);
                } catch (InterruptedException interruptedException) {
                    interruptedException.printStackTrace();
                }
            }
        }
    }
}
/**
     * 使用Lua脚本 + Stream消息队列实现秒杀下单
     *
     * @param voucherId
     * @return
     */
@Override
public Result seckillVoucher(Long voucherId) {
    // 获取用户id
    Long userId = UserHolder.getUser().getId();
    // 获取订单id
    long orderId = redisIdWorker.nextId("order");
    // 1.执行Lua脚本
    Long result = stringRedisTemplate.execute(
        SECKILL_SCRIPT,
        Collections.emptyList(),
        voucherId.toString(),
        userId.toString(),
        String.valueOf(orderId)
    );

    // 2.判断结果是否为0
    if (result != 0) {
        // 2.1 不为0，代表没有购买资格
        return Result.fail(result == 1 ? "库存不足" : "不能重复下单");
    }
    // 2.2 为0，有购买资格，把下单信息保存到消息队列【已经在LUA做过了】

    // 3.获取代理对象
    proxy = (IVoucherOrderService) AopContext.currentProxy();

    // 4. 返回订单id
    return Result.ok(orderId);
}
```

**秒杀压测**

![image-20230211163645535](https://blog-photos-lxy.oss-cn-hangzhou.aliyuncs.com/img/202302111636814.png)

> 可以看出咱们秒杀的接口性能非常好~
