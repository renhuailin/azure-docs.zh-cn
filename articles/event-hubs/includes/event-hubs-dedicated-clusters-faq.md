---
title: include 文件
description: include 文件
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/28/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 024c6d3e428d78ef4be4c42578f407b09148e001
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210701"
---
### <a name="what-can-i-achieve-with-a-cluster"></a>可以使用群集来做什么？

对于事件中心群集，可以引入和流式传输的数据量取决于各种因素，例如生成者、使用者、引入和处理速率等。 

下表显示了我们在测试期间实现的基准结果：

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

创建后，群集将按最少 4 个小时的使用量计费。 在自助服务体验的预览版中，你可以在“技术” > “配额” > “请求纵向扩展或纵向缩减专用群集”下向事件中心团队提交[支持请求](https://ms.portal.azure.com/#create/Microsoft.Support)，以纵向扩展或缩减群集  。 完成纵向缩减群集的请求最多可能需要 7 天。 

### <a name="how-does-geo-dr-work-with-my-cluster"></a>异地灾难恢复如何与群集配合工作？

可以将专用层群集下的命名空间与专用层群集下的另一个命名空间进行异地配对。 不鼓励将专用层命名空间与标准产品/服务中的命名空间配对，因为吞吐量限制不兼容并会导致错误。 

### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>是否可以迁移标准命名空间，以使其属于专用层群集？
目前，我们不支持将事件中心数据从标准命名空间迁移到专用命名空间的自动迁移过程。 
