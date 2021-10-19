---
title: 包含文件
description: 包含文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/04/2021
ms.author: tamram
ms.custom: include file, references_regions
ms.openlocfilehash: 8a50cc2f09d9094e8a7f327f0088988ca776a2c6
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713900"
---
下表介绍 Azure 常规用途 v2 (GPv2)、常规用途 v1 (GPv1) 和 Blob 存储帐户的默认限制。 “流入量”限制是指针对发送到存储帐户的所有数据的限制。 “流出量”限制是指针对从存储帐户接收的所有数据的限制。

Microsoft 建议对大多数场景使用 GPv2 存储帐户。 可以轻松将 GPv1 或 Blob 存储帐户升级为 GPv2 帐户，无需停机且无需复制数据。 有关详细信息，请参阅[升级到 GPv2 存储帐户](../articles/storage/common/storage-account-upgrade.md)。

> [!NOTE]
> 你可以请求更高的容量和流入量限制。 要请求提高限制，请联系 [Azure 支持](https://azure.microsoft.com/support/faq/)。

| 资源 | 限制 |
|--|--|
| 每个订阅每个区域的存储帐户数，包括标准存储帐户和高级存储帐户。 | 250 |
| 存储帐户的默认最大容量 | 5 PiB <sup>1</sup> |
| 每个存储帐户的 Blob 容器、Blob、文件共享、表、队列、实体或消息数目上限。 | 无限制 |
| 每个存储帐户的默认最大请求速率 | 每秒 20,000 个请求<sup>1</sup> |
| 以下区域 (LRS/GRS) 中每个常规用途 v2 和 Blob 存储帐户的默认最大流入量：<br /><ul><li>澳大利亚东部</li><li>美国中部</li><li>东亚</li><li>美国东部 2</li><li>法国中部</li><li>日本东部</li><li>韩国中部</li><li>北欧</li><li>美国中南部</li><li>东南亚</li><li>英国南部</li><li>西欧</li><li>美国西部</li></ul> | 60 Gbps<sup>1</sup> |
| 以下区域 (ZRS) 中每个常规用途 v2 和 Blob 存储帐户的默认最大流入量：<br /><ul><li>澳大利亚东部</li><li>美国中部</li><li>美国东部</li><li>美国东部 2</li><li>法国中部</li><li>日本东部</li><li>北欧</li><li>美国中南部</li><li>东南亚</li><li>英国南部</li><li>西欧</li><li>美国西部 2</li></ul> | 60 Gbps<sup>1</sup> |
| 前一行中未列出的区域中，每个常规用途 v2 和 Blob 存储帐户的默认最大流入量。 | 25 Gbps<sup>1</sup> |
| 常规用途 v1 存储帐户的默认最大流入量（所有区域） | 10 Gbps<sup>1</sup> |
| 以下区域 (LRS/GRS) 中每个常规用途 v2 和 Blob 存储帐户的默认最大流出量：<br /><ul><li>澳大利亚东部</li><li>美国中部</li><li>东亚</li><li>美国东部 2</li><li>法国中部</li><li>日本东部</li><li>韩国中部</li><li>北欧</li><li>美国中南部</li><li>东南亚</li><li>英国南部</li><li>西欧</li><li>美国西部</li></ul> | 120 Gbps<sup>1</sup> |
| 以下区域 (ZRS) 中每个常规用途 v2 和 Blob 存储帐户的默认最大流出量： <ul><li>澳大利亚东部</li><li>美国中部</li><li>美国东部</li><li>美国东部 2</li><li>法国中部</li><li>日本东部</li><li>北欧</li><li>美国中南部</li><li>东南亚</li><li>英国南部</li><li>西欧</li><li>美国西部 2</li></ul> | 120 Gbps<sup>1</sup> |
| 前一行中未列出的区域中，每个常规用途 v2 和 Blob 存储帐户的默认最大流出量。 | 50 Gbps<sup>1</sup> |
| 常规用途 v1 存储帐户的最大流出量（美国区域） | 如果已启用 RA-GRS/GRS，则为 20 Gbps；对于 LRS/ZRS，为 30 Gbps<sup>2</sup> |
| 常规用途 v1 存储帐户的最大流出量（非美国区域） | 如果已启用 RA-GRS/GRS，则为 10 Gbps；对于 LRS/ZRS，为 15 Gbps<sup>2</sup> |
| 每个存储帐户的最大 IP 地址规则数 | 200 |
| 每个存储帐户的虚拟网络规则数目上限 | 200 |
| 每个存储帐户的资源实例规则数目上限 | 200 |
| 每个存储帐户的专用终结点数目上限 | 200 |

<sup>1</sup> Azure 存储标准帐户根据请求支持更高的容量上限和更高的流入/流出量上限。 若要请求增加帐户限制，请与 [Azure 支持](https://azure.microsoft.com/support/faq/)联系。

<sup>2</sup> 如果存储帐户具有启用了读取访问权限的异地冗余存储 (RA-GRS) 或异地区域冗余存储 (RA-GZRS)，则辅助位置的流出量目标与主位置的流出量目标相同。 有关详细信息，请参阅 [Azure 存储复制](../articles/storage/common/storage-redundancy.md)。
