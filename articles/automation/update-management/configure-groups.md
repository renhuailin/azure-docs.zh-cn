---
title: 将动态组与 Azure 自动化更新管理配合使用
description: 本文介绍如何将动态组与 Azure 自动化更新管理配合使用。
services: automation
ms.subservice: update-management
ms.date: 06/22/2021
ms.topic: conceptual
ms.openlocfilehash: b09def53b32d9616ddab541b3448f863b6ec781a
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112520525"
---
# <a name="use-dynamic-groups-with-update-management"></a>将动态组与更新管理配合使用

使用更新管理可定位 Azure VM 或非 Azure VM 的动态组以进行更新部署。 使用动态组可以在不编辑部署的情况下更新计算机。

> [!NOTE]
> 动态组不适用于经典 VM。

你可以通过 Azure 门户中的更新管理为 Azure 或非 Azure 计算机定义动态组。 请参阅[管理 VM 的更新](manage-updates-for-vm.md)。

动态组由 Azure 自动化在部署时计算的查询定义。 即使动态组查询检索到大量计算机，Azure 自动化一次最多只能处理 1000 台计算机。 请参阅 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management)。

> [!NOTE]
> 如果希望更新 1000 台以上计算机，我们建议将更新拆分为多个更新计划。 

## <a name="define-dynamic-groups-for-azure-machines"></a>为 Azure 计算机定义动态组

为 Azure 计算机定义动态组查询时，可以使用以下项填充动态组：

* 订阅
* 资源组
* 位置
* Tags

![选择组](./media/configure-groups/select-groups.png)

若要预览动态组查询的结果，请单击“预览”。 预览将显示当前的组成员身份。 在本例中，我们将为组 BackendServer 搜索具有标记 `Role` 的计算机。 如果多台计算机已添加此标记，系统会针对该组将它们添加到任何未来部署中。

![预览组](./media/configure-groups/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>为非 Azure 计算机定义动态组

非 Azure 计算机的动态组使用保存的搜索，也称为计算机组。 若要了解如何创建保存的搜索，请参阅[创建计算机组](../../azure-monitor/logs/computer-groups.md#creating-a-computer-group)。 创建保存的搜索后，可以从 Azure 门户“更新管理”中的保存的搜索列表中选择它。 单击“预览”，可预览保存的搜索中的计算机。

![屏幕截图显示了非 Azure（预览版）的“选择组”页和右侧的“预览”窗格。](./media/configure-groups/select-groups-2.png)

> [!NOTE]
> 不支持[查询跨多个 Log Analytics 工作区进行存储的数据](../../azure-monitor/logs/cross-workspace-query.md)的已保存搜索。

## <a name="next-steps"></a>后续步骤

可[查询 Azure Monitor 日志](query-logs.md)来分析更新评估、部署和其他相关管理任务。 它包含预定义的查询，可帮助你入门。
