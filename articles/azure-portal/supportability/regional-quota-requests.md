---
title: 请求提高 Azure 区域的 vCPU 配额限制
description: 如何在 Azure 门户中请求提高某个区域的 vCPU 配额限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: eadf740c6b5caccbf678a1238f993d4ec0b34095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745411"
---
# <a name="standard-quota-increase-limits-by-region"></a>标准配额：按区域提高限制

Azure 资源管理器针对虚拟机支持两种类型的 vCPU 配额：

* 即用即付 VM 和虚拟机预留实例受到标准 vCPU 配额的限制  。
* 现成 VM 受到现成 vCPU 配额的限制 。

对于每个区域中的每个订阅，将在两个层级强制实施即用即付和虚拟机预留实例的标准 vCPU 配额：

* 第一个层级是针对所有 VM 系列的区域 vCPU 总限制。
* 第二层级是按 VM 系列实施的 vCPU 限制，例如 D 系列的 vCPU。

每当部署新的现成 VM 时，该 VM 系列的新的和现有 vCPU 总用量不得超过该特定 VM 系列的已批准 vCPU 配额。 此外，在所有 VM 系列中部署的新的和现有 vCPU 总数不能超过订阅的已批准区域 vCPU 总配额。 如果超过其中的任一配额，则不允许进行 VM 部署。

可以使用 Azure 门户请求提高 VM 系列的 vCPU 配额限制。 提高 VM 系列配额会自动将区域 vCPU 总限制提高相同的数量。

创建新订阅时，默认的区域 vCPU 总数可能不等于所有各个 VM 系列的总默认 vCPU 配额。 这种差异可能导致订阅的配额足以满足每个要部署的 VM 系列的要求。 但是，可能没有足够的配额来适应所有部署的区域 vCPU 总数。 在这种情况下，必须提交请求以显式提高区域 vCPU 的总数限制。 区域 vCPU 总数限制不能超过该区域中所有 VM 系列的已批准配额总计。

若要详细了解标准 vCPU 配额，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)以及 [Azure 订阅和服务限制、配额与约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

若要详细了解如何提高现成 VM 的 vCPU 限制，请参阅[现成 VM 配额：提高所有 VM 系列的限制](low-priority-quota.md)。

可通过以下两种方式之一请求按区域提高 vCPU 标准配额限制。

## <a name="request-a-quota-increase-by-region-from-help--support"></a>从“帮助 + 支持”请求按区域提高配额

若要从“帮助 + 支持”请求按区域提高 vCPU 配额，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)菜单中，选择“帮助和支持”。

   ![“帮助 + 支持”链接](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在“帮助 + 支持”中，选择“新建支持请求” 。

    ![新建支持请求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 对于“问题类型”，选择“服务和订阅限制(配额)” 。

   ![选择问题类型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 对于“订阅”，请选择要提高其配额的订阅。

   ![选择一个订阅](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 对于“配额类型”，请选择“其他请求” 。

   ![选择配额类型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 选择“下一步: 解决方案”打开“问题详细信息” 。 在“说明”中提供以下信息：

    1. 对于“部署模型”，请指定“资源管理器” 。  
    1. 对于“区域”，请指定所需的区域，例如“美国东部 2” 。  
    1. 对于“新限制”，请指定区域的新 vCPU 限制。 此值不能超过此订阅的各个 SKU 系列的已批准配额总和。

    ![输入配额请求的详细信息](./media/resource-manager-core-quotas-request/regional-details.png)

1. 选择“查看 + 创建”继续创建支持请求。

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>从“订阅”请求按区域提高配额

若要从“订阅”请求按区域提高 vCPU 配额，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“订阅”。

   ![在 Azure 门户中转到“订阅”](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 选择要提高其配额的订阅。

   ![选择要修改的订阅](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左侧窗格中，选择“使用量 + 配额”。

   ![单击“使用量和配额”链接](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角选择“请求提高”。

   ![选择提高配额](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 在“配额类型”中，选择“其他请求” 。

   ![选择配额类型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 选择“下一步: 解决方案”打开“问题详细信息” 。 在“说明”框中提供以下附加信息：

    1. 对于“部署模型”，请指定“资源管理器” 。  
    1. 对于“区域”，请指定所需的区域，例如“美国东部 2” 。  
    1. 对于“新限制”，请指定区域的新 vCPU 限制。 此值不能超过此订阅的各个 SKU 系列的已批准配额总和。

    ![在“详细信息”中输入信息](./media/resource-manager-core-quotas-request/regional-details.png)

1. 选择“查看 + 创建”继续创建支持请求。
