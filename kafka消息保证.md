## kafka 消息保证

> Broker 为存储数据的服务器，broker 下可以有多个 topic，topic 为具体的一组数据，topic 分为多个 partition，不同的 partition 存储不同的数据，且无序。



#### 常见配置

```shell
producer.type = sync/async // 生产者同步/异步模式
request.required.acks = 1  // 0: 发送后直接返回成功，1:发送后等待 follower 确认后返回成功，all:发送后 follower 写入磁盘后返回成功

```

#### 如何保证数据有序

- 每个 topic 仅创建一个 partition

- 写入数据时，需要保证有序的数据写入一个 partition

#### 高可用

1. 创建多个 replication，作为 leader 的 follower
2. 客户端只与 leader 交互，leader 写入数据后， follower 会主动 pull leader，为了提高性能，follower 拉取到数据后会立即 ack，而不是等到写入磁盘 （默认行为，支持配置）
3. leader 收到所有 follower 的 ack 后，消息 commit，表示已成功 send，此时 kafka 只保证数据存在 follower 内存中，因此消息也不一定会被 consumer 消费，假使 broker 宕机，依然会丢失消息
4. 仅有被 commit 的消息，consumer 才可以消费



#### 客户端生产者

send 之后 get 返回值，通过判断返回值的方式确保消息发送成功。

1. 同步，send 之后串行调用 get
2. 异步，通过 callback 方式，判断发送是否成功，失败则重试

#### 客户端消费者

1. 自动提交
   1. 手动关闭提交 offset，每次真正消费完成，进行 commit
2. 手动提交

