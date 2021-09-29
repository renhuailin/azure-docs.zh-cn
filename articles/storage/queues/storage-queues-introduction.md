---
title: Azure 队列存储简介 - Azure 存储
description: 查看 Azure 队列存储简介，它是一项可存储大量消息的服务。 队列存储服务包括 URL 格式、存储帐户、队列和消息。
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 1da3781b3dac4b79d8c8dc49decc669bfa7efde6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551729"
---
# <a name="what-is-azure-queue-storage"></a>什么是 Azure 队列存储？

Azure 队列存储是一项可存储大量消息的服务。 可以使用 HTTP 或 HTTPS 通过经验证的调用从世界任何位置访问消息。 队列消息大小最大可为 64 KB。 一个队列可以包含数百万条消息，直至达到存储帐户的总容量限值。 队列通常用于创建要异步处理的积压工作 (backlog)。

## <a name="queue-storage-concepts"></a>队列存储概念

队列存储包含以下组件：

![显示存储帐户、队列和消息之间关系的示意图。](./media/storage-queues-introduction/queue1.png)

- **URL 格式：** 可使用以下 URL 格式对队列进行寻址：

  `https://<storage account>.queue.core.windows.net/<queue>`

  可使用以下 URL 访问示意图中的某个队列：

  `https://myaccount.queue.core.windows.net/images-to-download`

- **存储帐户：** 对 Azure 存储进行的所有访问都要通过存储帐户完成。 有关存储帐户容量的信息，请参阅[标准存储帐户的可伸缩性和性能目标](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。

- **队列：** 一个队列包含一组消息。 队列名称 **必须** 全部小写。 有关命名队列的信息，请参阅[命名队列和元数据](/rest/api/storageservices/naming-queues-and-metadata)。

- **消息：** 一条消息（无论哪种格式）的最大大小为 64 KB。 在 2017-07-29 以前的版本中，允许的最大生存时间为 7 天。 在 2017-07-29 或更高版本中，最大生存时间可以是任何正数，或者是 -1（表示消息不会过期）。 如果省略此参数，则默认的生存时间为 7 天。

## <a name="next-steps"></a>后续步骤

- [创建存储帐户](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [通过 .NET 开始使用队列存储](storage-dotnet-how-to-use-queues.md)
- [通过 Java 开始使用队列存储](storage-java-how-to-use-queue-storage.md)
- [通过 Python 开始使用队列存储](storage-python-how-to-use-queue-storage.md)
- [通过 Node.js 开始使用队列存储](storage-nodejs-how-to-use-queues.md)
