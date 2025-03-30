---
title: KafkaConf
date: 2022-01-12
category:
  - Middleware
tag:
  - tools
sticky: false
---

# KafkaConf


## 简介
![](https://docs.confluent.io/_images/kafka-intro.png)
![](https://upload.wikimedia.org/wikipedia/commons/thumb/6/64/Overview_of_Apache_Kafka.svg/1280px-Overview_of_Apache_Kafka.svg.png)

## config
```java
public class ReliableProducer {

   public static void main(String[] args) {

      // Kafka broker 地址
      String bootstrapServers = "localhost:9092";

      // 设置 Producer 的配置信息
      Properties props = new Properties();
      props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);

      // 设置消息可靠性配置参数
      // 0不会等待任何broker的响应并不能保证消息成功发送 但是这种吞吐量最高
      // 1 leader broker自己写入后就响应，不会等待ISR其他的副本写入，只要leader broker存活就不会丢失，即保证了不丢失，也保证了吞吐量。(默认值)
      // all或者-1：leader broker会等消息写入 并且ISR都写入后才会响应，这种只要ISR有副本存活就肯定不会丢失，但吞吐量最低。
      props.put(ProducerConfig.ACKS_CONFIG, "all");
      props.put(ProducerConfig.RETRIES_CONFIG, 3); // 自动重试 3 次
      props.put(ProducerConfig.MAX_IN_FLIGHT_REQUESTS_PER_CONNECTION, 1);//该参数指定了生产者在收到服务器晌应之前可以发送多少个消息。
      // 开启幂等性,由于消息是分batch(批次)发送的，每个batch(批次)都有一个序列号。
      //在Broker端，会追踪每个分区的最大序列号。如果出现序列号较小或相等的batch(批次)，broker将不会将该batch(批次)写入topic。
      //这样，除了保证了幂等性，还可以确保batch(批次)的顺序。
      props.put(ProducerConfig.ENABLE_IDEMPOTENCE_CONFIG, true);

      props.put(ProducerConfig.COMPRESSION_TYPE_CONFIG, "snappy");
      // 设置消息序列化：使用 StringSerializer 将键和值序列化为字节数组
      props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
      props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

      // 创建一个 KafkaProducer 实例，并指定 key 和 value 的类型为 String
      KafkaProducer<String, String> producer = new KafkaProducer<>(props);

      // 创建一个消息
      String topic = "test-topic";
      String key = "key";
      String value = "value";

      // 发送消息，并阻塞直到发送完成
      producer.send(new ProducerRecord<>(topic, key, value)).get();

      // 关闭 producer
      producer.close();
   }
}
```
## 关于消费策略：
- 最多一次（at most once）: 消息可能丢失也可能被处理，但最多只会被处理一次。可能丢失 不会重复
- 至少一次（at least once）:  消息不会丢失，但可能被处理多次。可能重复 不会丢失
- 确传递一次（exactly once）: 消息被处理且只会被处理一次。不丢失 不重复 就一次

1、ProducerConfig.ACKS_CONFIG 设置为0时，实现了at most once。
2、ProducerConfig.ACKS_CONFIG 设置为1时 实现了at least once （有一种情况就是消息成功写入，而这个时候由于网络问题producer没有收到写入成功的响应，producer就会开启重试的操作，直到网络恢复，消息就发送了多次）
3、kafka 0.11.0.0 版本引入了 idempotent producer 机制：enable.idempotent 设置为true。
Kafka 0.11.0.0 版本引入了幂等生产者和事务支持。幂等生产者确保同一消息多次发送时只写入一次。
在多分区场景下，事务确保所有消息要么全部成功写入，要么全部回滚，以保持数据完整性。
消费者端可能需要额外处理来确保每条消息只被精确地消费一次，如手动管理偏移量提交。

## 关键配置解读：
1. `enable.auto.commit`：是否开启自动提交Offset  默认 true (偏移量是在使用者的轮询方法执行期间提交的)
2. `auto.commit.interval.ms`：自动提交Offset的时间间隔  默认 5000ms(仅定义提交之间的最小延迟)
仅提交在以前的轮询调用中返回的记录的偏移量。由于处理发生在轮询调用之间，因此永远不会提交未处理记录的偏移量。这保证了`at-least-once`至少一次的交付语义。

## 处理速度慢的问题
轮询方法调用之间允许的最大延迟由 `max.poll.interval.ms` 配置定义，默认为 5 分钟。
如果使用者未能在该时间间隔内调用轮询方法，则将其视为死，并触发组重新平衡。
对于每个使用者的线程和每个记录需要很长时间才能处理的用例的默认配置，这种情况经常发生。

使用每个使用者线程模型时，可以通过调整以下配置值来解决此问题：
将 `max.poll.records` 设置为较小的值
将 `max.poll.interval.ms` 设置为更高的值

在kafka分区无法改动的情况下使用以下两种方案作为参考
1、使用[Confluent Parallel Consumer](https://www.confluent.io/blog/introducing-confluent-parallel-message-processing-client/?utm_source=twitter&utm_medium=organicsocial&utm_campaign=tm.devx_ch.introducing-confluent-parallel-message-processing-client_content.clients)
2、单线程消费，分配给多线程处理，处理完成后由主线程提交offset [多线程消费](https://www.confluent.io/blog/kafka-consumer-multi-threaded-messaging/)

- https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying
