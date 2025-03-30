---
title: Redis
date: 2022-01-12
category:
  - Language
tag:
  - java
  - api
star: false
sticky: false
---

# Redis

## install
```bash
docker search redis

docker pull redis
docker images

docker run -itd --name redis-dev -p 6379:6379 redis
docker ps

docker exec -ti redis-dev bash
root@d02c1ec11b1b:/data# redis-cli
127.0.0.1:6379> PING
PONG
```

## 数据类型
### string（字符串）
```bash
127.0.0.1:6379> SET test_str_key "this is a str value."
OK
127.0.0.1:6379> GET test_str_key
"this is a str value."
127.0.0.1:6379>
```

### hash（哈希）
```bash
127.0.0.1:6379> HMSET test_hash_key key1 "value_1" key2 "value_2"
OK
127.0.0.1:6379> HGET test_hash_key key1
"value_1"
127.0.0.1:6379> HGET test_hash_key key2
"value_2"
127.0.0.1:6379>
```

### list（列表）
```bash
127.0.0.1:6379> HGET test_hash_key key1
"value_1"
127.0.0.1:6379> HGET test_hash_key key2
"value_2"
127.0.0.1:6379> LPUSH test_list_key "value_0"
(integer) 1
127.0.0.1:6379> LPUSH test_list_key "value_1"
(integer) 2
127.0.0.1:6379> LPUSH test_list_key "value_2"
(integer) 3
127.0.0.1:6379> LRANGE test_list_key 0 0
1) "value_2"
127.0.0.1:6379> LRANGE test_list_key 0 1
1) "value_2"
2) "value_1"
127.0.0.1:6379> LRANGE test_list_key 0 2
1) "value_2"
2) "value_1"
3) "value_0"
127.0.0.1:6379>

```

### set（集合）
```bash
127.0.0.1:6379> SADD test_set_key "value_0"
(integer) 1
127.0.0.1:6379> SADD test_set_key "value_1"
(integer) 1
127.0.0.1:6379> SADD test_set_key "value_2"
(integer) 1
127.0.0.1:6379> SADD test_set_key "value_3"
(integer) 1
127.0.0.1:6379> SADD test_set_key "value_3"
(integer) 0
127.0.0.1:6379> SADD test_set_key "value_4"
(integer) 1
127.0.0.1:6379> SMEMBERS test_set_key
1) "value_1"
2) "value_2"
3) "value_3"
4) "value_4"
5) "value_0"
127.0.0.1:6379>
```
### zset(sorted set：有序集合)
```bash
127.0.0.1:6379> ZADD test_zset_key 0 value_0
(integer) 1
127.0.0.1:6379> ZADD test_zset_key 1 value_1
(integer) 1
127.0.0.1:6379> ZADD test_zset_key 1 value_11
(integer) 1
127.0.0.1:6379> ZADD test_zset_key 2 value_2
(integer) 1
127.0.0.1:6379> ZADD test_zset_key 3 value_3
(integer) 1
127.0.0.1:6379> ZADD test_zset_key 4 value_3
(integer) 0
127.0.0.1:6379> ZADD test_zset_key 5 value_5
(integer) 1
127.0.0.1:6379> ZRANGE test_zset_key 0 5
1) "value_0"
2) "value_1"
3) "value_11"
4) "value_2"
5) "value_3"
6) "value_5"
127.0.0.1:6379> ZRANGE test_zset_key 0 5 WITHSCORES
 1) "value_0"
 2) "0"
 3) "value_1"
 4) "1"
 5) "value_11"
 6) "1"
 7) "value_2"
 8) "2"
 9) "value_3"
10) "4"
11) "value_5"
12) "5"
127.0.0.1:6379>
```

## Java使用
### 链接 Redis
```xml
<dependency>
    <groupId>io.lettuce</groupId>
    <artifactId>lettuce-core</artifactId>
    <version>6.1.8.RELEASE</version>
</dependency>
```
```java
public class RedisDev {
    public static void main(String[] args) {
        // <1> 创建单机连接的连接信息
        RedisURI redisURI = RedisURI.builder()
                .withHost("localhost")
                .withPort(6379)
                .withTimeout(Duration.of(1, ChronoUnit.SECONDS))
                .build();
        // <2> 创建客户端
        RedisClient redisClient = RedisClient.create(redisURI);
        // <3> 创建线程安全的连接
        StatefulRedisConnection<String, String> redisConnect = redisClient.connect();
        // <4> 创建同步命令
        RedisCommands<String, String> redisCommands = redisConnect.sync();

        String ping = redisCommands.ping();
        System.out.println("PONG".equals(ping)? "Success":"Fail");
        String result = redisCommands.set("dev:str_key", "value_0");
        System.out.println(result);
        result = redisCommands.get("dev:str_key");
        System.out.println(result);

        redisConnect.close();   // <5> 关闭连接
        redisClient.shutdown();  // <6> 关闭客户端
    }
}

Success
OK
value_0
```

### 发布与订阅
```java
    public static void pubSub() {
        RedisURI redisURI = RedisURI.builder()
                .withHost("localhost")
                .withPort(6379)
                .withTimeout(Duration.of(1, ChronoUnit.SECONDS))
                .build();
        RedisClient redisClient = RedisClient.create(redisURI);

        StatefulRedisPubSubConnection<String, String> redisPubSubConnection = redisClient.connectPubSub();
        redisPubSubConnection.addListener(new RedisPubSubListener<String, String>() {
            @Override
            public void message(String channel, String message) {
                System.out.println(String.format("Channel:%s,Message:%s", channel, message));
            }

            @Override
            public void message(String pattern, String channel, String message) {

            }

            @Override
            public void subscribed(String channel, long count) {

            }

            @Override
            public void psubscribed(String pattern, long count) {

            }

            @Override
            public void unsubscribed(String channel, long count) {

            }

            @Override
            public void punsubscribed(String pattern, long count) {

            }
        });
        //订阅
        redisPubSubConnection.sync().subscribe("dev:msg_channel");

        //发布
        StatefulRedisConnection<String, String> connect = redisClient.connect();
        for (int i = 0; i < 10; i++) {
            connect.sync().publish("dev:msg_channel", "message_" + i);
        }

        connect.close();
        redisPubSubConnection.close();
        redisClient.shutdown();
    }
```

### 生产与消费
```java
    public static void produce_consume() {
        Long queue_size = 10L;
        String queue_name = "message_queue";

        RedisURI redisURI = RedisURI.builder().withHost("localhost")
                .withPort(6379)
                .withTimeout(Duration.of(10, ChronoUnit.SECONDS))
                .build();
        RedisClient redisClient = RedisClient.create(redisURI);

        StatefulRedisConnection<String, String> redisConnect = redisClient.connect();
        RedisCommands<String, String> redisCommands = redisConnect.sync();
        System.out.println(redisCommands.ping());
        redisCommands.del(queue_name);

        Executors.newSingleThreadExecutor().submit(() -> {
            for (int i = 1; i <= 100; ) {
                if (queue_size >= redisCommands.llen(queue_name)) {
                    redisCommands.lpush(queue_name, "value_" + i);
                    System.out.println(">>> produce " + "  value_" + i);
                    i++;
                }
            }
        });
        boolean flag = true;
        while (flag) {
            if (redisCommands.llen(queue_name) > 0) {
                List<String> list = redisCommands.rpop(queue_name, 3);
                try {
                    // 消费耗时
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
                System.out.printf("⬇️ consume " + list);
                System.out.println();
            }
        }
        redisConnect.close();   // <5> 关闭连接
        redisClient.shutdown();  // <6> 关闭客户端
    }
```

[参考]
- https://www.runoob.com/redis/redis-tutorial.html
