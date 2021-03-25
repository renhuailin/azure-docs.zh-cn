---
title: 通过 Python azure-servicebus 包版本 7.0.0 使用 Azure 服务总线队列
description: 本文介绍如何使用 Python 向 Azure 服务总线队列发送消息，并从中接收消息。
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 0553062032a58ec9eb9cf3c474ee7c8f19fc544d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98631549"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>向 Azure 服务总线队列发送消息并从中接收消息 (Python)
本文介绍如何使用 Python 向 Azure 服务总线队列发送消息，并从中接收消息。 

## <a name="prerequisites"></a>先决条件
- Azure 订阅。 可以激活 [Visual Studio 或 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)。
- 如果没有可使用的队列，请遵循[使用 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)一文来创建队列。 记下服务总线命名空间的连接字符串以及创建的队列的名称 。
- 已安装 [Python Azure 服务总线](https://pypi.python.org/pypi/azure-servicebus)包的 Python 2.7 或更高版本。 有关详细信息，请参阅 [Python 安装指南](/azure/developer/python/azure-sdk-install)。 

## <a name="send-messages-to-a-queue"></a>向队列发送消息

1. 添加以下 import 语句。 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. 添加以下常量。 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - 将 `<NAMESPACE CONNECTION STRING>` 替换为服务总线命名空间的连接字符串。
    > - 将 `<QUEUE NAME>` 替换为该队列的名称。 
3. 添加一个方法以发送一条消息。

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    发送方是一个对象，充当你创建的队列的客户端。 稍后将创建它，并将其作为参数发送到此函数。 
4. 添加一个方法以发送一列消息。

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. 添加一个方法以发送一批消息。

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. 创建一个服务总线客户端，然后创建一个队列发送方对象来发送消息。

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-queue"></a>从队列接收消息
在 print 语句的后面添加以下代码。 此代码将持续接收新消息，直到在 5 (`max_wait_time`) 秒内未收到任何新消息。 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>完整代码

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>运行应用
运行应用程序时，应显示以下输出： 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

在 Azure 门户中，导航到你的服务总线命名空间。 在“概述”页上，验证传入和传出消息计数是否为 16  。 如果没有看到这些消息，请等待几分钟后再刷新页面。 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="传入和传出消息计数":::

在此“概述”页上选择队列，导航到“服务总线队列”页面 。 还可在此页上看到传入和传出消息计数 。 还可看到其他信息，如队列的当前大小和活动消息计数 。 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="队列详细信息":::


## <a name="next-steps"></a>后续步骤
请参阅以下文档和示例： 

- [适用于 Python 的 Azure 服务总线客户端库](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples)。 
    - sync_samples 文件夹包含一些示例，这些示例演示如何以同步方式与服务总线交互。 本快速入门就使用了此方法。 
    - async_samples 文件夹包含一些示例，这些示例演示如何以异步方式与服务总线交互。 
- [azure-servicebus 参考文档](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)