---
title: RabbitMQ
category: 消息队列
---
- 重试机制
rabbitmq消费失败时,会触发重试机制,默认重试次数为3,间隔为1000ms(1s)
可配置:
```
spring:
        rabbitmq:
                listener:
                        simple:
                                retry:
                                        enabled: true
                                        max-attempts: 5
                                        initial-interval: 200
```
重试机制仅仅是消费者端的重试,消息不会重新进到消息队列中

- 哪些信息会是死信
1. ttl过期
2. 消息被拒绝(basic.reject, basic.nack)
3. 消息队列达到最大长度
4. 消费端达到最大重试次数

