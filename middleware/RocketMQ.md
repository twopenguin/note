# RocketMQ概述

角色

Producer、COnsumer、Broker 和 NameServer

启动 RockerMQ 的顺序是先启动NameServer， 再启动Broker

# (1).环境准备

## NameServer

打开 `org.apache.rocketmq.namesrv.NameServerInstanceTest` 单元测试类，参考 `#startup()` 方法，我们编写 `#main(String[] args)` 静态方法，代码如下：

```java
// NameServerInstanceTest.java

public static void main(String[] args) throws Exception {
    // NamesrvConfig 配置
    final NamesrvConfig namesrvConfig = new NamesrvConfig();
    // NettyServerConfig 配置
    final NettyServerConfig nettyServerConfig = new NettyServerConfig();
    nettyServerConfig.setListenPort(9876); // 设置端口
    // 创建 NamesrvController 对象，并启动
    NamesrvController namesrvController = new NamesrvController(namesrvConfig, nettyServerConfig);
    namesrvController.initialize();
    namesrvController.start();
    // 睡觉，就不起来
    Thread.sleep(DateUtils.MILLIS_PER_DAY);
}
```

然后，右键运行，RocketMQ Namesrv 就启动完成。输出日志如下：

```java
17:54:03.354 [NettyEventExecutor] INFO  RocketmqRemoting - NettyEventExecutor service started
17:54:03.355 [FileWatchService] INFO  RocketmqCommon - FileWatchService service started
```

## Broker



## Producer



## Consumer



# (2).大体流程熟悉

Producer 启动后从NameSrv或者Broker信息

Producer 组装消息发送给Broker



Consumer 启动后从NameSrv获取Broker信息

Consumer 通过推或者拉的方式获取消息

Consumer 给Broker发确认消息

# (3).Producer 从 NameSrv 获取 Broker 信息

这儿设置了NameSrv的地址和端口，发现只是给`namesrvAddr` 属性赋值

```
producer.setNamesrvAddr("127.0.0.1:9876");
```

继续进入`producer` 的 `start` 方法

### 主动拉取更新部分信息

来看一段客户端主动从NameSrv 拉取`TopicRouteInfo`信息的代码：

```java
//MQClientAPIImpl#getTopicRouteInfoFromNameServer(java.lang.String, long, boolean)
//组装请求头信息
GetRouteInfoRequestHeader requestHeader = new GetRouteInfoRequestHeader();
requestHeader.setTopic(topic);
//组装Command
RemotingCommand request = RemotingCommand.createRequestCommand(RequestCode.GET_ROUTEINTO_BY_TOPIC, requestHeader);

//执行调用，获得Response
RemotingCommand response = this.remotingClient.invokeSync(null, request, timeoutMillis);
```





# (4)Producer 发送消息给Broker

##发送消息主要流程

**组装 `SendMessageRequestHeader` 请求头**

**组装 `RemotingCommand`信息**

**基于不同的`communicationMode` ，不同的发送方式，有`ONEWAY`,`ASYNC`,`SYNC` 三种方式**

**创建`ResponseFuture`对象，封装`opaque`、`invokeCallback` 等信息**

```java
final ResponseFuture responseFuture = new ResponseFuture(channel, opaque, timeoutMillis - costTime, invokeCallback, once);
this.responseTable.put(opaque, responseFuture);
```

**最终调用Netty的代码`channel.writeAndFlush(request).addListener` 发送 Request**

```java
channel.writeAndFlush(request).addListener(new ChannelFutureListener() {
	@Override
	public void operationComplete(ChannelFuture f) throws Exception {
		if (f.isSuccess()) {
			responseFuture.setSendRequestOK(true);
			return;
		}
		requestFail(opaque);
		log.warn("send a request command to channel <{}> failed.", RemotingHelper.parseChannelRemoteAddr(channel));
	}
});
```





直接来看核心方法`DefaultMQProducerImpl#sendDefaultImpl`：

```java
private SendResult sendDefaultImpl(Message msg,
    final CommunicationMode communicationMode,
    final SendCallback sendCallback,final long timeout
    ) throws MQClientException, RemotingException, MQBrokerException, InterruptedException {
        this.makeSureStateOK();
        Validators.checkMessage(msg, this.defaultMQProducer);

        final long invokeID = random.nextLong();
        //整个循环发送过程开始时间
        long beginTimestampFirst = System.currentTimeMillis();
        long beginTimestampPrev = beginTimestampFirst;
        long endTimestamp = beginTimestampFirst;
        TopicPublishInfo topicPublishInfo = this.tryToFindTopicPublishInfo(msg.getTopic());
        if (topicPublishInfo != null && topicPublishInfo.ok()) {
            boolean callTimeout = false;
            MessageQueue mq = null;
            Exception exception = null;
            SendResult sendResult = null;
            //总共需要查询几次
            int timesTotal = communicationMode == CommunicationMode.SYNC ? 1 + this.defaultMQProducer.getRetryTimesWhenSendFailed() : 1;
            int times = 0;
            String[] brokersSent = new String[timesTotal];
            for (; times < timesTotal; times++) {
                String lastBrokerName = null == mq ? null : mq.getBrokerName();
                MessageQueue mqSelected = this.selectOneMessageQueue(topicPublishInfo, lastBrokerName);
                if (mqSelected != null) {
                    mq = mqSelected;
                    brokersSent[times] = mq.getBrokerName();
                    try {
                        beginTimestampPrev = System.currentTimeMillis();
                        if (times > 0) {
                            //Reset topic with namespace during resend.
                            msg.setTopic(this.defaultMQProducer.withNamespace(msg.getTopic()));
                        }
                        long costTime = beginTimestampPrev - beginTimestampFirst;
                        if (timeout < costTime) {
                            callTimeout = true;
                            break;
                        }

                        sendResult = this.sendKernelImpl(msg, mq, communicationMode, sendCallback, topicPublishInfo, timeout - costTime);
                        endTimestamp = System.currentTimeMillis();
                        this.updateFaultItem(mq.getBrokerName(), endTimestamp - beginTimestampPrev, false);
                        switch (communicationMode) {
                            case ASYNC:
                                return null;
                            case ONEWAY:
                                return null;
                            case SYNC:
                                if (sendResult.getSendStatus() != SendStatus.SEND_OK) {
                                    if (this.defaultMQProducer.isRetryAnotherBrokerWhenNotStoreOK()) {
                                        continue;
                                    }
                                }

                                return sendResult;
                            default:
                                break;
                        }
                    } catch (RemotingException e) {
                        endTimestamp = System.currentTimeMillis();
                        this.updateFaultItem(mq.getBrokerName(), endTimestamp - beginTimestampPrev, true);
                        log.warn(String.format("sendKernelImpl exception, resend at once, InvokeID: %s, RT: %sms, Broker: %s", invokeID, endTimestamp - beginTimestampPrev, mq), e);
                        log.warn(msg.toString());
                        exception = e;
                        continue;
                    } catch (MQClientException e) {
                        endTimestamp = System.currentTimeMillis();
                        this.updateFaultItem(mq.getBrokerName(), endTimestamp - beginTimestampPrev, true);
                        log.warn(String.format("sendKernelImpl exception, resend at once, InvokeID: %s, RT: %sms, Broker: %s", invokeID, endTimestamp - beginTimestampPrev, mq), e);
                        log.warn(msg.toString());
                        exception = e;
                        continue;
                    } catch (MQBrokerException e) {
                        endTimestamp = System.currentTimeMillis();
                        this.updateFaultItem(mq.getBrokerName(), endTimestamp - beginTimestampPrev, true);
                        log.warn(String.format("sendKernelImpl exception, resend at once, InvokeID: %s, RT: %sms, Broker: %s", invokeID, endTimestamp - beginTimestampPrev, mq), e);
                        log.warn(msg.toString());
                        exception = e;
                        switch (e.getResponseCode()) {
                            case ResponseCode.TOPIC_NOT_EXIST:
                            case ResponseCode.SERVICE_NOT_AVAILABLE:
                            case ResponseCode.SYSTEM_ERROR:
                            case ResponseCode.NO_PERMISSION:
                            case ResponseCode.NO_BUYER_ID:
                            case ResponseCode.NOT_IN_CURRENT_UNIT:
                                continue;
                            default:
                                if (sendResult != null) {
                                    return sendResult;
                                }

                                throw e;
                        }
                    } catch (InterruptedException e) {
                        endTimestamp = System.currentTimeMillis();
                        this.updateFaultItem(mq.getBrokerName(), endTimestamp - beginTimestampPrev, false);
                        log.warn(String.format("sendKernelImpl exception, throw exception, InvokeID: %s, RT: %sms, Broker: %s", invokeID, endTimestamp - beginTimestampPrev, mq), e);
                        log.warn(msg.toString());

                        log.warn("sendKernelImpl exception", e);
                        log.warn(msg.toString());
                        throw e;
                    }
                } else {
                    break;
                }
            }

            if (sendResult != null) {
                return sendResult;
            }

            String info = String.format("Send [%d] times, still failed, cost [%d]ms, Topic: %s, BrokersSent: %s",
                times,
                System.currentTimeMillis() - beginTimestampFirst,
                msg.getTopic(),
                Arrays.toString(brokersSent));

            info += FAQUrl.suggestTodo(FAQUrl.SEND_MSG_FAILED);

            MQClientException mqClientException = new MQClientException(info, exception);
            if (callTimeout) {
                throw new RemotingTooMuchRequestException("sendDefaultImpl call timeout");
            }

            if (exception instanceof MQBrokerException) {
                mqClientException.setResponseCode(((MQBrokerException) exception).getResponseCode());
            } else if (exception instanceof RemotingConnectException) {
                mqClientException.setResponseCode(ClientErrorCode.CONNECT_BROKER_EXCEPTION);
            } else if (exception instanceof RemotingTimeoutException) {
                mqClientException.setResponseCode(ClientErrorCode.ACCESS_BROKER_TIMEOUT);
            } else if (exception instanceof MQClientException) {
                mqClientException.setResponseCode(ClientErrorCode.BROKER_NOT_EXIST_EXCEPTION);
            }

            throw mqClientException;
        }

        List<String> nsList = this.getmQClientFactory().getMQClientAPIImpl().getNameServerAddressList();
        if (null == nsList || nsList.isEmpty()) {
            throw new MQClientException(
                "No name server address, please set it." + FAQUrl.suggestTodo(FAQUrl.NAME_SERVER_ADDR_NOT_EXIST_URL), null).setResponseCode(ClientErrorCode.NO_NAME_SERVER_EXCEPTION);
        }

        throw new MQClientException("No route info of this topic, " + msg.getTopic() + FAQUrl.suggestTodo(FAQUrl.NO_TOPIC_ROUTE_INFO),
            null).setResponseCode(ClientErrorCode.NOT_FOUND_TOPIC_EXCEPTION);
    }

```



进入核心方法`MQClientAPIImpl#sendMessage`, 有点类似主动拉取更新部分信息，

1. 先组装Header，然后获得Command，然后基于不同的模式发送



## SendCallback 原理

在我们发送消息的时候可以添加回调类即`SendCallback `，代码如下：

```java
void onSuccess(final SendResult sendResult);

void onException(final Throwable e);
```

其实回调类知识在异步发送消息的时候有用，接下来看看其原理,

直接使用idea 的 ctrl + alt + h 快捷键可以查到`onSuccess` 方法被哪儿调用，发现就只有一处`this.remotingClient.invokeAsync(xxx,xxx,InvokeCallback)`，如下是`InvokeCallback`的一个匿名内部类

```java
@Override
public void operationComplete(ResponseFuture responseFuture) {
	RemotingCommand response = responseFuture.getResponseCommand();
	if (null == sendCallback && response != null) {

		try {
			SendResult sendResult = MQClientAPIImpl.this.processSendResponse(brokerName, msg, response);
			if (context != null && sendResult != null) {
				context.setSendResult(sendResult);
				context.getProducer().executeSendMessageHookAfter(context);
			}
		} catch (Throwable e) {
		}

		producer.updateFaultItem(brokerName, System.currentTimeMillis() - responseFuture.getBeginTimestamp(), false);
		return;
	}

	if (response != null) {
		try {
			SendResult sendResult = MQClientAPIImpl.this.processSendResponse(brokerName, msg, response);
			assert sendResult != null;
			if (context != null) {
				context.setSendResult(sendResult);
				context.getProducer().executeSendMessageHookAfter(context);
			}

			try {
                //这儿就是调用的地方
				sendCallback.onSuccess(sendResult);
			} catch (Throwable e) {
			}

			producer.updateFaultItem(brokerName, System.currentTimeMillis() - responseFuture.getBeginTimestamp(), false);
		} catch (Exception e) {
			producer.updateFaultItem(brokerName, System.currentTimeMillis() - responseFuture.getBeginTimestamp(), true);
			onExceptionImpl(brokerName, msg, 0L, request, sendCallback, topicPublishInfo, instance,
				retryTimesWhenSendFailed, times, e, context, false, producer);
		}
	} else {
		producer.updateFaultItem(brokerName, System.currentTimeMillis() - responseFuture.getBeginTimestamp(), true);
		if (!responseFuture.isSendRequestOK()) {
			MQClientException ex = new MQClientException("send request failed", responseFuture.getCause());
			onExceptionImpl(brokerName, msg, 0L, request, sendCallback, topicPublishInfo, instance,
				retryTimesWhenSendFailed, times, ex, context, true, producer);
		} else if (responseFuture.isTimeout()) {
			MQClientException ex = new MQClientException("wait response timeout " + responseFuture.getTimeoutMillis() + "ms",
				responseFuture.getCause());
			onExceptionImpl(brokerName, msg, 0L, request, sendCallback, topicPublishInfo, instance,
				retryTimesWhenSendFailed, times, ex, context, true, producer);
		} else {
			MQClientException ex = new MQClientException("unknow reseaon", responseFuture.getCause());
			onExceptionImpl(brokerName, msg, 0L, request, sendCallback, topicPublishInfo, instance,
				retryTimesWhenSendFailed, times, ex, context, true, producer);
		}
	}
}

```



## 自定义消息发送规则

一个 Topic 会有多个 Message Queue ，如果使用 Producer 的 默认配置 ，这个 Producer 会轮流向各个 Message Queue 发送消息 。 Consumer 在消费消息的时候，会根据负载均衡策略，消费被分配到的 Message Queue ，如果不经过特定的设置，某条消息被发往 l哪个 Message Queue ，被哪个 Consumer 消费是未
知的 。
如果业务需要我们把消息发送到指定的 Message Queue 里，比如把同 一类型 的消息都发 往 相同的 Message Queue ， 该怎 么办呢？ 可以用 MessageQueueSelector  

发送消息的时候，把 MessageQueueSelector 的对象作为参数，使用

```java
 public SendResult send ( Message msg, MessageQueueSelector selector, Object arg ）
```



函
数发送消 息即可 。 在 MessageQueueSelector 的实现中，根据传人的 Object 参
数，或者根据 Message 消息内容确定把消息发往那个 Message Queue ，返回被
选中的 Message Queue 。 

# (5)Broker 接受消息并保存

1.`SendMessageProcessor`  用来处理`Producer` 发过来的消息，`processRequest` 方法把字节转化为`RemotingCommand` ，在上一截中提到，发送的消息最后会被转化为一个`RemotingCommand` 对象

2.调用`SendMessageProcessor#sendMessage` 来处理消息，具体逻辑如下：



3.messageCheck

```java
super.msgCheck(ctx, requestHeader, response);
```



4.获取消息体

```java
//获取消息体
final byte[] body = request.getBody();
```

5.如果未指定具体的队列号，就随机取一个

```java
int queueIdInt = requestHeader.getQueueId();
//小于0表示未设置
if (queueIdInt < 0) {    queueIdInt = Math.abs(this.random.nextInt() % 99999999) % topicConfig.getWriteQueueNums();}
```

6.对RETRY类型的消息处理。如果超过最大消费次数，则topic修改成”%DLQ%” + 分组名， 即加  死信队 (Dead Letter Queue)，

```java
if (!handleRetryAndDLQ(requestHeader, response, request, msgInner, topicConfig)) {
    return response;
}
```



7.把消息组装成`MessageExtBrokerInner` 对象

```java
MessageExtBrokerInner msgInner = new MessageExtBrokerInner();
msgInner.setTopic(requestHeader.getTopic());
msgInner.setQueueId(queueIdInt);
msgInner.setBody(body);
msgInner.setFlag(requestHeader.getFlag());
MessageAccessor.setProperties(msgInner, MessageDecoder.string2messageProperties(requestHeader.getProperties()));
msgInner.setPropertiesString(requestHeader.getProperties());
msgInner.setBornTimestamp(requestHeader.getBornTimestamp());
msgInner.setBornHost(ctx.channel().remoteAddress());
msgInner.setStoreHost(this.getStoreHost());
msgInner.setReconsumeTimes(requestHeader.getReconsumeTimes() == null ? 0 : requestHeader.getReconsumeTimes());
//等等
```

8.调用`BrokerController` 的 `messageStory` 对象。来放消息，并返回`PutMessageResult` 对象

```java
PutMessageResult putMessageResult = null;
putMessageResult = this.brokerController.getMessageStore().putMessage(msgInner);
```

# (6).message存储

在上面一节中最后一步`putMessage` 最终是调用`CommitLog` 来进行消息的存储



# (7).定时消息

## Producer 发送定时消息

发送时，设置消息的**延迟级别**

```java
Message msg = new Message(...);
msg.setDelayTimeLevel(level);
```

这个数值对应`MessageStoreConfig` 的 `messageDelayLevel`字段

```java
private String messageDelayLevel = "1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h";
```

比如 setDelayTimeLevel(3 ） 表示延迟 10s 

## Broker 存储定时消息

- 🦅 存储消息时，延迟消息进入 `Topic` 为 `SCHEDULE_TOPIC_XXXX`。
- 🦅 延迟级别 与 消息队列编号 做**固定映射：QueueId = DelayLevel - 1**。



# 消息消费

消息消费有两种方式，对应的接口分别为：`DefaultMQPushConsumer`、`DefaultMQPullConsumer`



## DefaultMQPullConsumer

需要自己维护 Offset

# 源码

## ProcessQueue

ProcessQueue 对象里主要的内容是一个 TreeMap 和 一个读写锁 。 TreeMap 里以 Message Queue 的 Offset 作为 Key ，以消息内容的引用为 Value ，保存了所有从 MessageQueue 获取到，但是还未被处理的消息； 读写锁控制着多个线 程对 TreeMap 对象的并发访问 。

有 了 ProcessQueue 对象，流量控制就方便和灵活多了 ， 客户端在每次 Pull请求前会做下面三个判断来控制流量， 如代码清单 3-6 所示 。 

```java
//org.apache.rocketmq.client.impl.consumer.DefaultMQPushConsumerImpl#pullMessage
//消息个数
long cachedMessageCount = processQueue.getMsgCount().get();
//消息总大小
long cachedMessageSizeInMiB = processQueue.getMsgSize().get() / (1024 * 1024);

//判断获取但还未处理的消息个数
if (cachedMessageCount > this.defaultMQPushConsumer.getPullThresholdForQueue()) {
	this.executePullRequestLater(pullRequest, PULL_TIME_DELAY_MILLS_WHEN_FLOW_CONTROL);
	if ((queueFlowControlTimes++ % 1000) == 0) {
		log.warn(
			"the cached message count exceeds the threshold {}, so do flow control, minOffset={}, maxOffset={}, count={}, size={} MiB, pullRequest={}, flowControlTimes={}",
			this.defaultMQPushConsumer.getPullThresholdForQueue(), processQueue.getMsgTreeMap().firstKey(), processQueue.getMsgTreeMap().lastKey(), cachedMessageCount, cachedMessageSizeInMiB, pullRequest, queueFlowControlTimes);
	}
	return;
}

if (cachedMessageSizeInMiB > this.defaultMQPushConsumer.getPullThresholdSizeForQueue()) {
	this.executePullRequestLater(pullRequest, PULL_TIME_DELAY_MILLS_WHEN_FLOW_CONTROL);
	if ((queueFlowControlTimes++ % 1000) == 0) {
		log.warn(
			"the cached message size exceeds the threshold {} MiB, so do flow control, minOffset={}, maxOffset={}, count={}, size={} MiB, pullRequest={}, flowControlTimes={}",
			this.defaultMQPushConsumer.getPullThresholdSizeForQueue(), processQueue.getMsgTreeMap().firstKey(), processQueue.getMsgTreeMap().lastKey(), cachedMessageCount, cachedMessageSizeInMiB, pullRequest, queueFlowControlTimes);
	}
	return;
}
```

PushConsumer 会判断获取但还未处理的消息个数、消息总大小、 Offset 的跨度，任何一个值超过设定的大小就隔一段时间再拉取消息，从而达到流量控制的目的 。 此外 ProcessQueue 还可以辅助实现顺序消费的
逻辑， 。 





