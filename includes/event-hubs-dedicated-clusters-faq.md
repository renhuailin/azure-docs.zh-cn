---
title: include 文件
description: include 文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 10/23/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 0335481566ae3f28ac0f1e6bddce7050a65e7dc2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516982"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>可以使用群集来做什么？

对于事件中心群集，可以引入和流式传输多少取决于不同的因素，例如，你的发生器、消费者、你引入和处理的速度等。 

下表显示了我们在测试过程中获得的基准测试结果：

| 有效负载形状 | 接收方 | 入口带宽| 入口消息 | 出口带宽 | 出口消息 | TU 总数 | 每个 CU 的 TU 数 |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB 批 | 2 | 400 MB/秒 | 400k 消息数/秒 | 800 MB/秒 | 800k 消息数/秒 | 400 TU | 100 TU | 
| 10x10KB 批 | 2 | 666 MB/秒 | 66.6k 消息数/秒 | 1.33 GB/秒 | 133k 消息数/秒 | 666 TU | 166 TU |
| 6x32KB 批 | 1 | 1.05 GB/秒 | 34k 消息数/秒 | 1.05 GB/秒 | 34k 消息数/秒 | 1000 TU | 250 TU |

测试中使用了以下条件：

- 一个专用层事件中心群集使用四个容量单位 (CU)。 
- 用于引入的事件中心包含 200 个分区。 
- 引入的数据由从所有分区接收数据的两个接收方应用程序接收。

### <a name="can-i-scale-updown-my-cluster"></a>是否可以纵向扩展/纵向缩减群集？

创建后，群集将按最少 4 个小时的使用量计费。 在自助服务体验的预览版本中，你可以向事件中心团队提交[支持请求](https://ms.portal.azure.com/#create/Microsoft.Support)，以增加或缩减**Technical**  >  **Quota**  >  **专用群集**，使群集扩大或缩小。 完成纵向缩减群集的请求最多可能需要 7 天。 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>异地灾难恢复如何与群集配合工作？

可以将专用层群集下的命名空间与专用层群集下的另一个命名空间进行异地配对。 我们不鼓励在标准产品中将专用命名空间与命名空间配对，因为吞吐量限制不兼容并导致错误。 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>是否可以迁移标准命名空间，以使其属于专用层群集？
目前，我们不支持将事件中心数据从标准命名空间迁移到专用命名空间的自动迁移过程。 
