---
title: Azure 经典部署模型
description: 经典部署模型现在被资源管理器模型取代，对 VM 和虚拟机规模集强制实施全局 vCPU 配额限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: b5b5e8c11bfe164aaa3539742dac8c4d267c69e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745598"
---
# <a name="classic-deployment-model"></a>经典部署模型

经典部署模型是上一代 Azure 部署模型。 这种部署模型对虚拟机和虚拟机规模集强制实施全局 vCPU 配额限制。 不建议再使用经典部署模型，现在已经被资源管理器模型取代。

要详细了解这两种部署模型以及使用资源管理器的优点，请参阅[资源管理器和经典部署](../../azure-resource-manager/management/deployment-models.md)。

创建新订阅时，将为其分配某个默认 vCPU 配额。 无论何时使用经典部署模型部署新的虚拟机，所有区域中新的和现有 vCPU 使用的总和不得超过针对经典部署模型批准的 vCPU 配额。

要详细了解这些配额，请参阅 [Azure 订阅和服务的限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

可以为经典部署模型请求增加 vCPU 配额限制。 在 Azure 门户中使用“帮助 + 支持”或“使用情况 + 配额”。 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>使用“帮助 + 支持”在订阅级别增加每个 VM 序列 vCPU 配额

按照以下说明在 Azure 门户中使用“帮助 + 支持”创建支持请求。

1. 在 [Azure 门户](https://portal.azure.com)菜单中，选择“帮助和支持”。

   ![选择 Azure 门户中的“帮助 + 支持”](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 选择“新建支持请求”。

   ![在 Azure 门户中创建新的支持请求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 在“问题类型”中，选择“服务和订阅限制（配额）”。 

   ![选择配额作为问题类型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 选择要增加其配额的订阅。

   ![选择要增加配额的订阅](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 对于“配额类型”，选择“计算 -VM (cores-vCPUs) 订阅限制增加”。 

   ![选择要增加的配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 选择“下一步: 解决方案”以打开“问题详细信息”。  选择“提供详细信息”以提供其他信息。

   ![提供详细信息以帮助处理你的请求](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在“配额详细信息”中，选择“经典”，然后选择“位置”。  

   ![添加详细信息，包括部署模型和位置](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 对于“SKU 系列”，选择要增加的一个或多个 SKU 系列。

   ![指定要增加的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 输入想对订阅设定的新限制。 要删除行，请从“SKU 系列”中取消选择 SKU 或选择废弃图标“X”。 为每个 SKU 系列输入配额后，请在“配额详细信息”中选择“保存并继续”以继续支持请求。 

   ![请求新的限制](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>使用“使用情况 + 配额”在订阅级别增加每个 VM 序列 vCPU 配额

按照以下说明在 Azure 门户中使用“使用情况 + 配额”创建支持请求。

1. 在 Azure 门户中，搜索并选择 [订阅](https://portal.azure.com)。

   ![在 Azure 门户中，转到“订阅”](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 选择要增加其配额的订阅。

   ![选择要修改的订阅](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 选择“使用情况 + 配额”。

   ![选择订阅的使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角选择“请求增加”  。

   ![选择以增加配额](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 选择“计算 - VM (cores-vCPUs) 订阅限制增加”作为“配额类型”。 

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 选择“下一步: 解决方案”以打开“问题详细信息”。  选择“提供详细信息”以提供其他信息。

   ![为请求提供详细信息](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在“配额详细信息”中，选择“经典”，然后选择“位置”。  

   ![选择配额详细信息，包括部署模型和位置](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 选择要增加的一个或多个 SKU 系列。

   ![选择要增加的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 输入想对订阅设定的新限制。 要删除行，请从“SKU 系列”中取消选择 SKU 或选择废弃图标“X”。 为每个 SKU 系列输入配额后，请在“配额详细信息”中选择“保存并继续”以继续支持请求。 

   ![输入新配额](./media/resource-manager-core-quotas-request/new-limits-classic.png)

