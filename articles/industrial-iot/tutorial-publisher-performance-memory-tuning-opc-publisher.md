---
title: Microsoft OPC 发布服务器性能和内存优化
description: 在本教程中，将了解如何优化 OPC 发布服务器的性能和内存。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787218"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>教程：优化 OPC 发布服务器性能和内存

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 调整性能
> * 将消息流调整到内存资源

在生产设置中运行 OPC 发布服务器时，必须考虑网络性能要求（吞吐量和延迟）和内存资源。 OPC 发布服务器公开以下命令行参数来帮助满足这些要求：

* 消息队列容量（版本 2.5 和更低版本的 `mq`，版本 2.6 中不可用，版本 2.7 的 `om`）
* IoT 中心发送间隔 (`si`)
* IoT 中心消息大小 (`ms`)

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>调整 IoT 中心发送间隔和 IoT 中心消息大小

`mq/om` 参数控制内部消息队列的容量上限。 在将消息发送到 IoT 中心之前，此队列会将所有消息缓冲。 OPC 发布服务器版本 2.5 及更低版本的默认队列大小为最高 2 MB，版本 2.7 的 IoT 中心消息为 4000（也就是说，如果 IoT 中心消息大小的设置为 256 KB，则队列大小将高达 1 GB）。 如果 OPC 发布服务器无法以足够快的速度向 IoT 中心发送消息，则此队列中的项目数将增加。 如果在测试运行期间出现这种情况，则可以执行以下一项或两项操作来缓解：

* 缩短 IoT 中心发送间隔 (`si`)

* 增大 IoT 中心消息大小（`ms` ，可将其最大值设置为 256 KB）

即使已调整 `si` 和 `ms` 参数，如果队列仍不断增长，最终将达到最大队列容量，消息将丢失。 这是因为 `si` 和 `ms` 参数都具有物理限制，因此，OPC 发布服务器与 IoT 中心之间的 Internet 连接的速度不够快，无法满足给定方案中必须发送的消息数。 在这种情况下，只有设置多个并行 OPC 发布服务器才会有所帮助。 `mq/om` 参数还对 OPC 发布服务器的内存消耗产生最大的影响。 

`si` 参数强制 OPC 发布服务器按指定间隔将消息发送到 IoT 中心。 当最大可用 IoT 中心消息大小为 256 KB（触发发送间隔以重置），或指定的间隔时间已过时，将发送一条消息。

`ms` 参数允许批处理发送到 IoT 中心的消息。 在大多数网络设置中，将单个消息发送到 IoT 中心的延迟时间比传输有效负载所用的时间要长。 这主要是由于服务质量 (QoS) 要求，因为消息仅在 IoT 中心处理后才会被确认。 因此，如果可接受到 IoT 中心的数据延迟，则应通过将 `ms` 参数设置为 0，将 OPC 发布服务器配置为使用最大消息大小 256 KB。 这也是使用 OPC 发布服务器的最具成本效益的方法。

使用默认配置时，每 10 秒 (`si=10`) 或当 256 KB IoT 中心消息数据可用时，会将数据发送到 IoT 中心(`ms=0`)。 这最多还会延迟 10 秒，但由于消息较大，丢失数据的可能性很低。 OPC 发布服务器版本 2.5 及更低版本的指标 `monitored item notifications enqueue failure` 和 OPC 发布服务器版本 2.7 的指标 `messages lost` 显示丢失的消息数。

当 `si` 和 `ms` 参数均设置为 0 时，只要数据可用，OPC 发布服务器就会向 IoT 中心发送一条消息。 这将导致 IoT 中心消息的平均大小仅超过 200 个字节。 但是，此配置的优点是，OPC 发布服务器从连接的资产中发送数据，而不会造成延迟。 对于必须发布大量数据的用例，丢失的消息数将会很高，因此不建议在这些情况下使用。

若要测量 OPC 发布服务器的性能，可使用 `di` 参数以指定的时间间隔（以秒为单位）将性能度量值打印到跟踪日志中。

## <a name="next-steps"></a>后续步骤
现在，您已了解如何优化 OPC 发布服务器的性能和内存，您可以查看 OPC 发布服务器 GitHub 存储库以获取更多资源：

> [!div class="nextstepaction"]
> [OPC 发布服务器 GitHub 存储库](https://github.com/Azure/Industrial-IoT)