---
title: Azure 资源管理器 vCPU 配额增加请求
description: Azure 资源管理器 vCPU 配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 2580da2a4ac7b943dee3e5e6ff8bdbd49664505b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745241"
---
# <a name="quota-increase-requests"></a>配额增加请求

对于每个区域中的每个订阅，虚拟机和虚拟机规模集的资源管理器 vCPU 配额在两个层级实施。

第一层是所有 VM 系列的“区域 vCPU 总数”限制。 第二层是每个 VM 系列个 vCPU 限制，如 D 系列的 vCPU。 每当要部署新的虚拟机时，该 VM 系列的现有和新 vCPU 使用的总和不得超过为该特定 VM 系列批准的 vCPU 配额。 此外，在所有 VM 系列中部署的新 vCPU 总数和现有 vCPU 总数不应超过为订阅批准的区域中 vCPU 总数配额。 如果超过了上述任一配额，将不允许部署 VM。
可以从 Azure 门户中为 VM 序列请求增大 vCPU 配额限制。 VM 序列配额增加会自动为“区域 vCPU 总数”限制增加相同的数量。

创建新订阅时，“区域 vCPU 总数”的默认值不能等于所有单个 VM 序列的默认 vCPU 配额之和。 这种情况可能会导致订阅的配额足以满足每个要部署的单个 VM 系列的要求。 对于所有部署，“区域 vCPU 总数”可能缺少足够配额。 在这种情况下，你将需要提交请求，以显式增加“区域 vCPU 总数”限制。 “区域 vCPU 总数”限制不能超过区域的所有 VM 系列上已批准配额的总和。

> [!NOTE]
> 如果想要提高限制或配额，使其超出默认限制，可以[免费建立联机客户支持请求](../../azure-resource-manager/templates/error-resource-quota.md#solution)。

有关配额的详细信息，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)以及 [Azure订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。