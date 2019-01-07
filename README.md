# openmessaging-http

The HTTP binding for OpenMessaging consists of two parts: producing and consuming.
We use HTTP2 in the implemention. 
## Produce

The main idea of producing part of http binding is simple, we just map all the essential info into the http protocal.
We use `POST` method to send messages.
All the meta info of message is in the HTTP header, and message body is put in the HTTP body.


### http headers 

Header | Constraints | Demo | Description
- | -
x-oms-version | Required| 1.0.0 | version of OMS
x-oms-h-destination| Required | orderQueue | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-messageId| Required | 7F00000100002873000000000004F49C | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-bornTimestamp| Required | 1533780827824 | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-bornHost| Required | 172.24.0.101:10035 | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-storeTimestamp| Required | 1533780827825 | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-storeHost| Required | 172.24.0.102:52511 | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-expireTime| OPTIONAL | 1533780830000 | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-priority| OPTIONAL | 1 |See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-compression| OPTIONAL | gzip | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-traceId| OPTIONAL | 1E0578887D3F18B4AAC22B64D2B00A5E | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-transactionId| OPTIONAL | 1E0578887D3F18B4AAC22B64D2B40A62 | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-messageKey| OPTIONAL | orderId-103368921567 | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-delayTime| OPTIONAL | 30000 |See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-durability| OPTIONAL | 1 | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-h-correlationId| OPTIONAL | 7F00000100002873000000000004F2B4 | See [message model](https://github.com/openmessaging/specification/blob/master/specification-schema.md#2-message-model) for details.
x-oms-p-service | OPTIONAL | helloService | All properties start with `x-oms-p-`

### response protocal


#### http status code 
status code | Description 
- | -
200 | Success 
4xx or 5xx | Failed. Detailed error code or failure reason is decided by the underlying vendor

#### http response header
vendors can add some specail meta info in the response header, e.g. message offset for this offset.

#### response body
Response body is not required. 


#### 

## Consume

### Pull Mode

In pull mode, the consumer get message in the request and response mode. 
At most one message would be returned for one pull request.
Useful for testing and low qps consuming scenario.

####Pull Request
http header  |Constraints| Description | 
- | - | -
x-oms-version | Required | version of OMS
x-oms-consumerId | Required|   user defined client Id. (e.g. consumer group in RocketMQ/Kafka)
x-oms-source| Required | consume message from this queue or topic. See x-oms-h-destination in producer.
x-oms-ack | OPTIONAL | If set true, ack is required for the `at least once` delivery semantics. False is default value for `at most once` delivery semantics
x-oms-waitMills | OPTIONAL | Time in milliseconds for waiting message. Used in long polling. 

####Pull Response
status code | Description 
- | -
200 | Success, with message 
204 | Success, no new message 
4xx or 5xx | Failed. Detailed error code or failure reason is decided by the underlying vendor

Message formated in HTTP protocal, same as produce format.

### Push Mode
The main difference between pull and push mode is that, in push mode, you can get multi messages with one request.

#### Push Reqeust

http header  |Constraints| Description | 
- | - | -
x-oms-version | Required | version of OMS
x-oms-consumerId | Required|   user defined client Id. (e.g. consumer group in RocketMQ/Kafka)
x-oms-source| Required | consume message from this queue or topic. See x-oms-h-destination in producer.
x-oms-ack | OPTIONAL | If set true, ack is required for the `at least once` delivery semantics. False is default value for `at most once` delivery semantics
x-oms-waitMills | OPTIONAL | Time in milliseconds for waiting message. Used in long polling. 
x-oms-callbackURL | OPTIONAL | Enable `callback push mode`, server will post messages to this URL. If empty url is set, previous callback procedure will be stopped. 

#### Push Response
status code | Description 
- | -
200 | Success. 
4xx or 5xx | Failed. Detailed error code or failure reason is decided by the underlying vendor

