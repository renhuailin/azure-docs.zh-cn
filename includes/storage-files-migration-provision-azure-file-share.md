---
title: include 文件
description: include 文件
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: c1fda4eb853e97b7a5b85beebe162ab3869e4a68
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "113798716"
---
Azure 文件共享存储在云中的 Azure 存储帐户中。
此处还需遵守另外一个性能注意事项适。

如果共享的活跃度很高（共享由多个用户和/或应用程序使用），则两个 Azure 文件共享就可能达到存储帐户的性能限制。

最佳做法是在部署存储帐户时，让每个存储帐户都有一个文件共享。
如果你有存档共享或预计文件共享的日常活跃度较低，可将多个 Azure 文件共享加入同一存储帐户。

相对于 Azure 文件同步，这些注意事项更适用于通过 Azure VM 进行的直接云访问。如果计划只在这些共享上使用 Azure 文件同步，则可以将多个文件共享分组到一个 Azure 存储帐户中。

如果已创建共享列表，应将每个共享映射到它所在的存储帐户。

在上一阶段中，你确定了适当数量的共享。 在此步骤中，你具有存储帐户到文件共享的映射。 现在，可部署适当数量的 Azure 存储帐户，其中包含相应数量的 Azure 文件共享。

请确保每个存储帐户的区域相同，并且与已部署的存储同步服务资源的区域相匹配。

> [!CAUTION]
> 如果创建的 Azure 文件共享具有 100 TiB 的限制，则该共享只能使用本地冗余存储或区域冗余存储冗余选项。 使用 100 TiB 的文件共享之前，应考虑自己的存储冗余需求。

默认情况下，将创建具有 5 TiB 限制的 Azure 文件共享。 按照[创建 Azure 文件共享](../articles/storage/files/storage-how-to-create-file-share.md)中的步骤创建大型文件共享。

部署存储帐户时，还要考虑一个问题，那就是 Azure 存储的冗余。 请参阅 [Azure 存储冗余选项](../articles/storage/common/storage-redundancy.md)。

资源的名称也很重要。 例如，如果将 HR 部门的多个共享归入一个 Azure 存储帐户中，则应适当地命名存储帐户。 同样，命名 Azure 文件共享时，应使用与本地对应项所用名称类似的名称。