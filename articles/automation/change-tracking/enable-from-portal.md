---
title: 从 Azure 门户启用 Azure 自动化更改跟踪和库存
description: 本文介绍了如何从 Azure 门户启用更改跟踪和库存功能。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6ca5b1df05c04937598278378e001f41f08ef23c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183628"
---
# <a name="enable-change-tracking-and-inventory-from-azure-portal"></a>从 Azure 门户启用更改跟踪和库存

本文介绍如何为 Azure 门户中的一个或多个 Azure Vm 启用 [更改跟踪和清单](overview.md) 。 若要大规模启用 Azure VM，必须使用更改跟踪和库存启用现有 VM。

可用于管理 VM 的资源组数量受限于[资源管理器部署限制](../../azure-resource-manager/templates/deploy-to-resource-group.md)。 资源管理器部署限制为每个部署5个资源组。 其中有两个资源组是保留的，用于配置 Log Analytics 工作区、自动化帐户和相关资源。 因此，还剩下三个资源组可供选择用于通过“更改跟踪和库存”进行管理。 此限制仅应用于并发设置，不应用于可通过自动化功能管理的资源组数目。

> [!NOTE]
> 在启用更改跟踪和库存时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](../how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](../index.yml)。
* [虚拟机](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和库存

1. 在 Azure 门户中，导航到“虚拟机”。

2. 使用复选框选择要添加到更改跟踪和库存的 VM。 一次最多可以为三个不同的资源组添加计算机。 Azure VM 可以位于任何区域中，无论自动化帐户的位置如何。

    ![VM 列表](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > 使用筛选器控件从不同的订阅、位置和资源组中选择 VM。 可以单击顶部的复选框来选择列表中的所有虚拟机。

3. 在“配置管理”下，选择“更改跟踪”或“库存”。  

4. 虚拟机的列表已经过筛选，仅显示了位于相同订阅和位置的虚拟机。 如果你的虚拟机位于三个以上资源组中，则会选择前三个资源组。

5. 默认情况下会选择一个现有的 Log Analytics 工作区和自动化帐户。 若要使用不同的 Log Analytics 工作区和自动化帐户，请单击“自定义”以从“自定义配置”页选择它们。 选择 Log Analytics 工作区时，系统会执行一项检查来确定它是否与某个自动化帐户相链接。 如果找到了链接的自动化帐户，则会出现以下屏幕。 完成后单击“确定”。

    ![选择工作区和帐户](media/enable-from-portal/select-workspace-and-account.png)

6. 如果所选的工作区未链接到自动化帐户，则会出现以下屏幕。 选择一个自动化帐户，完成后单击“确定”。

    ![无工作区](media/enable-from-portal/no-workspace.png)

7. 取消选择不想启用的任何虚拟机旁边的复选框。 已取消选择无法启用的 VM。

8. 单击“启用”以启用所选功能。 完成安装最多需要 15 分钟时间。

## <a name="next-steps"></a>后续步骤

* 有关使用此功能的详细信息，请参阅 [管理更改跟踪](manage-change-tracking.md) 和 [管理清单](manage-inventory-vms.md)。
* 若要排查该功能的常见问题，请参阅[排查更改跟踪和清单问题](../troubleshoot/change-tracking.md)。