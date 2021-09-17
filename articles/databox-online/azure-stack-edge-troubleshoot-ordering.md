---
title: 通过 Azure 门户排查 Azure Stack Edge 排序问题
description: 介绍如何排查 Azure Stack Edge 排序问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/21/2021
ms.author: alkohli
ms.openlocfilehash: 46a7d0ed41b8f10874c19c8d70ddc5d8c5d7f197
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608283"
---
# <a name="troubleshoot-your-azure-stack-edge-ordering-issues"></a>排查 Azure Stack Edge 排序问题

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何排查 Azure Stack Edge 排序问题。

## <a name="unsupported-subscription-or-region"></a>不支持该订阅或区域

**错误说明：** 在 Azure 门户中，如果收到错误：

*不支持选定的订阅或区域。请选择其他订阅或区域。*

![不支持的订阅或区域](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**建议的解决方案：** 确保使用了受支持的订阅，例如 [Microsoft 企业协议 (EA)](https://azure.microsoft.com/overview/sales-number/)、[云解决方案提供商 (CSP)](/partner-center/azure-plan-lp) 或 [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/)。 不支持即用即付订阅。 有关详细信息，请参阅 [Azure Stack Edge 资源先决条件](azure-stack-edge-deploy-prep.md#prerequisites)。

Microsoft 有可能会根据具体情况，允许按事例升级订阅类型。 请与 [Microsoft 支持部门](https://azure.microsoft.com/support/options/)联系，以便他们能够了解你的需求并相应调整这些限制。

## <a name="selected-subscription-type-not-supported"></a>不支持所选的订阅类型

**错误：** 你有 EA、CSP 或赞助的订阅并收到以下错误：

*不支持所选订阅类型。 请确保使用支持的订阅。 [了解详细信息](azure-stack-edge-deploy-prep.md#prerequisites)。 如果使用的是受支持的订阅类型，请确保：
 
- 在通过经典门户下达订单时，已注册了 `Microsoft.DataBoxEdge` 提供程序。
- 在通过 Azure Edge Hardware Center（预览版）下达订单时，已注册了 `Microsoft.EdgeOrder` 提供程序。
 
有关如何注册的信息，请参阅[注册资源提供程序](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*。

**建议的解决方案：** 请按照以下步骤注册 Azure Stack Edge 资源提供程序：

1. 在 Azure 门户中，转到“主页” > “订阅” 。

2. 选择要用于订购设备的订阅。

3. 选择“资源提供程序”，然后搜索“Microsoft.DataBoxEdge” 。

    ![注册资源提供程序](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

如果注册资源提供程序时没有“所有者”或“参与者”访问权限来，会看到以下错误：*订阅“&lt;订阅名称&gt;”没有注册资源提供程序 Microsoft.DataBoxEdge 的权限。*

有关详细信息，请参阅[注册资源提供程序](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)。

## <a name="resource-provider-not-registered-for-subscription"></a>尚未为订阅注册资源提供程序

错误：在 Azure 门户中，选择要用于 Azure Stack Edge 或 Data Box Gateway 的订阅，然后收到以下错误之一：

*资源提供程序: 没有为订阅“&lt;订阅名称&gt;”注册 Microsoft.DataBoxEdge，并且你没有为订阅“&lt;订阅名称&gt;”注册资源提供程序的权限*。

没有为订阅 &lt;订阅名称&gt; 注册资源提供程序 Microsoft.EdgeOrder，并且你缺少权限，无法为订阅 &lt;订阅名称&gt; 注册资源提供程序。

**建议的解决方案：** 提升订阅访问权限或寻找具有注册资源提供程序时所需的所有者或参与者访问权限的他人。

## <a name="resource-disallowed-by-policy"></a>策略不允许的资源

**错误：** 在 Azure 门户中尝试注册资源提供程序时收到以下错误：

*策略不允许使用资源“&lt;资源名称&gt;”。（代码：RequestDisallowedByPolicy）。计划: 拒绝通常不需要的资源类型。策略: 不允许的资源类型。*

**建议的解决方案：** 发生此错误的原因是现有的一个 Azure Policy 分配阻止资源创建。 Azure Policy 定义和分配由组织的系统管理员设置，目的是在使用或创建 Azure 资源时确保相关符合性。 如果任何此类策略分配会阻止创建 Azure Stack Edge 资源，请与系统管理员联系，要求其编辑 Azure Policy 定义。

## <a name="next-steps"></a>后续步骤

* 深入了解如何[排查 Azure Stack Edge 问题](azure-stack-edge-gpu-troubleshoot.md)。
