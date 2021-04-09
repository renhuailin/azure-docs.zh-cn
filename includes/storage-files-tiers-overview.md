---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6d06a46d2eaaad362890f1e3e44dbc746fa10898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633371"
---
Azure 文件存储提供了四种不同的存储层（高级、事务优化、热和冷存储层），因此你能够根据方案的性能和价格要求定制共享：

- **Premium**：高级文件共享由固态磁盘 (SSD) 提供支持；对于 IO 密集型工作负载，它们提供稳定的高性能和低延迟（大多数 IO 操作的延迟低至几毫秒）。 高级文件共享适合各种各样的工作负载，例如数据库、网站托管和开发环境。 高级文件共享既可用于服务器消息块 (SMB) 协议，也可用于网络文件系统 (NFS) 协议。
- **事务优化**：事务优化文件共享可实现事务繁重的工作负载，这些工作负荷不需要高级文件共享提供的延迟。 可在由硬盘驱动器 (HDD) 提供支持的标准存储硬件上使用事务优化文件共享。 虽然事务优化一直被称为“标准”层，但这指的是存储介质类型而不是层本身（热和冷存储层也是“标准”层，因为它们位于标准存储硬件上）。
- **Hot**：热文件共享提供针对常规用途文件共享方案（如团队共享）优化的存储。 可在由 HDD 提供支持的标准存储硬件上使用热文件共享。
- **Cool**：冷文件共享提供针对在线存档存储方案优化的经济高效的存储。 可在由 HDD 提供支持的标准存储硬件上使用冷文件共享。

高级文件共享是在 FileStorage 存储帐户中部署的，仅可在预配账单模型中使用。 有关高级文件共享的预配计费模型的详细信息，请参阅[了解预配高级文件共享](../articles/storage/files/understanding-billing.md#provisioned-model)。 标准文件共享（包括事务优化、热和冷文件共享）部署在常规用途版本 2 (GPv2) 存储帐户类型中，以即用即付计费的形式提供。 热文件共享和冷文件共享在所有 Azure 公共区域和 Azure 政府区域中均可用。 事务优化文件共享在所有 Azure 区域中可用，包括 Azure 中国和 Azure 德国区域。

为工作负载选择存储层时，请考虑你的性能和使用要求。 如果工作负载要求延迟低至个位数，或者你正在使用本地 SSD 存储媒体，则可能最适合使用高级层。 如果低延迟不太重要，例如在使用从 Azure 本地装载或通过 Azure 文件同步本地缓存的团队共享时，则从成本的角度来看，标准存储可能更加适合。

在存储帐户中创建文件共享后，无法将该共享移动到专用于其他存储帐户类型的层级。 例如，若要将事务优化文件共享移到高级层，必须在 FileStorage 存储帐户中创建一个新的文件共享，然后再将原始共享中的数据复制到 FileStorage 帐户中的这个新的文件共享。 建议在 Azure 文件共享之间使用 AzCopy 来复制数据，但你也可使用工具，例如适合 Windows 的 `robocopy`，或者适合 macOS 和 Linux 的 `rsync`。 

无需创建新的存储帐户和迁移数据，即可在标准层（事务优化、热和冷）之间移动 GPv2 存储帐户中部署的文件共享，但在更改层级时将产生事务成本。 在将共享从更热的冷移动到更冷的层时，共享中每个文件将产生更冷层的写入事务费用。 如果将文件共享从更冷的层移动到更热的层，则共享中每个文件将更冷层的读取事务费用。

有关详细信息，请参阅[了解 Azure 文件存储计费](../articles/storage/files/understanding-billing.md)。