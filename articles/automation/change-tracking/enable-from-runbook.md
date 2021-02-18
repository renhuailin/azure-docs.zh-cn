---
title: 从 Runbook 启用 Azure 自动化更改跟踪和库存
description: 本文介绍如何从 Runbook 启用更改跟踪和库存。
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e5b42d6102737b778ea5d19cd7da3c2f64881b1b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585922"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>从 runbook“启用更改跟踪和清单”

本文介绍如何使用 runbook 为你的环境中的 Vm 启用 [更改跟踪和清单](overview.md) 。 若要大规模启用 Azure VM，必须使用更改跟踪和库存启用现有 VM。

> [!NOTE]
> 在启用更改跟踪和库存时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](../how-to/region-mappings.md)。

此方法使用两个 runbook：

* **Enable-multiplesolution** -主要 runbook，用于提示输入配置信息，查询指定的 VM 并执行其他验证检查，然后调用 **AutomationSolution** runbook 为指定资源组中的每个 VM 配置更改跟踪和清单。
* **AutomationSolution** -为目标资源组中指定的一个或多个 vm 启用更改跟踪和清单。 它将验证是否满足先决条件，验证是否已安装并安装 Log Analytics VM 扩展（如果找不到），然后将 Vm 添加到链接到自动化帐户的指定 Log Analytics 工作区中的范围配置。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](../automation-security-overview.md)。
* [Log Analytics 工作区](../../azure-monitor/logs/design-logs-deployment.md)
* [虚拟机](../../virtual-machines/windows/quick-create-portal.md)。
* **AutomationSolution** runbook 使用的两个自动化资产。 如果自动化帐户中尚不存在此 runbook，则此 runbook 会在其首次运行期间自动导入 **enable-multiplesolution** runbook。
    * *LASolutionSubscriptionId*： Log Analytics 工作区所在位置的订阅 ID。
    * *LASolutionWorkspaceId*：链接到自动化帐户的 Log Analytics 工作区的工作区 ID。

    这些变量用于配置载入 VM 的工作区。 如果未指定这些项，脚本将首先搜索要在其订阅中更改跟踪和清单的任何 VM 载入，然后搜索该自动化帐户所在的订阅，然后再搜索你的用户帐户有权访问的所有其他订阅。 如果未正确配置，则可能会导致计算机载入到某些随机 Log Analytics 工作区。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和库存

1. 在 Azure 门户中，导航到 " **Automation 帐户**"。 在 " **自动化帐户** " 页上，从列表中选择你的帐户。

1. 在自动化帐户中，选择 "**清单**" 或 "**配置管理**" 下的 **更改跟踪**。

1. 选择 Log Analytics 工作区，然后单击“启用”。 启用库存或更改跟踪时，会显示一个横幅。

    ![启用更改跟踪和库存](media/enable-from-automation-account/enable-feature.png)

## <a name="install-and-update-modules"></a>安装和更新模块

需要将其更新到最新的 Azure 模块，并导入 [microsoft.operationalinsights](/powershell/module/az.operationalinsights) 模块，才能成功地通过 Runbook 为 vm 启用更新管理。

1. 在你的自动化帐户中的“共享资源”下选择“模块” 。

2. 选择“更新 Azure 模块”以更新到最新版本的 Azure 模块。

3. 单击“是”，将所有现有 Azure 模块更新到最新版本。

    ![更新模块](media/enable-from-runbook/update-modules.png)

4. 返回“共享资源”下的“模块” 。

5. 选择“浏览库”以打开模块库。

6. 搜索 `Az.OperationalInsights` 并将此模块导入到自动化帐户中。

    ![导入 OperationalInsights 模块](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>选择要管理的 Azure VM

启用更改跟踪和库存后，可添加 Azure VM，便于按功能进行管理。

1. 在自动化帐户中，选择“配置管理”下的“更改跟踪”或“库存”  。

2. 单击“添加 Azure VM”，添加你的 VM。

3. 从列表中选择你的 VM，然后单击“启用”。 此操作为 VM 启用更改跟踪和库存。

   ![为 VM 启用更改跟踪和库存](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > 如果在完成“更改跟踪和库存”设置之前尝试启用其他功能，会收到以下消息：`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>导入 Runbook 以启用更改跟踪和库存

1. 在你的自动化帐户中的“流程自动化”下选择“Runbook”。

2. 选择“浏览库”。

3. 搜索 **更新和更改跟踪**。

4. 选择 runbook，然后单击 "**查看源**" 页上的 "**导入**"。

5. 单击“确定”，将 Runbook 导入到自动化帐户中。

   ![导入 Runbook 进行设置](media/enable-from-runbook/import-from-gallery.png)

6. 在 " **Runbook** " 页上，选择 " **enable-multiplesolution** " runbook，然后单击 " **编辑**"。 在文本编辑器中，选择 "  **发布**"。

7. 出现确认提示时，单击 **"是"** 以发布 runbook。

## <a name="start-the-runbook"></a>启动 Runbook

必须已为 Azure VM 启用了更改跟踪和库存，才能启动此 Runbook。 它需要一个已启用该功能的现有 VM 和资源组，以便在目标资源组中配置一个或多个 Vm。

1. 打开“Enable-MultipleSolution”Runbook。

   ![多个解决方案 Runbook](media/enable-from-runbook/runbook-overview.png)

2. 单击“启动”按钮，在以下字段中输入参数值：

   * **VMNAME** - 要添加到更改跟踪和库存中的现有 VM 的名称。 将此字段留空可添加资源组中的所有 VM。
   * **VMRESOURCEGROUP** - 要启用的 VM 的资源组的名称。
   * **SUBSCRIPTIONID** - 要启用的新 VM 的订阅 ID。 将此字段留空可使用工作区的订阅。 使用不同的订阅 ID 时，请添加自动化帐户的运行方式帐户作为订阅的参与者。
   * ALREADYONBOARDEDVM - 已为更新手动启用的 VM 的名称。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM 所属的资源组的名称。
   * **SOLUTIONTYPE** - 输入 ChangeTracking。

   ![Enable-MultipleSolution Runbook 参数](media/enable-from-runbook/runbook-parameters.png)

3. 选择“确定”启动 Runbook 作业。

4. 从 " **作业** " 页中监视 runbook 作业和任何错误的进度。

## <a name="next-steps"></a>后续步骤

* 若要计划 Runbook，请参阅[在 Azure 自动化中管理计划](../shared-resources/schedules.md)。

* 有关使用此功能的详细信息，请参阅 [管理更改跟踪](manage-change-tracking.md) 和 [管理清单](manage-inventory-vms.md)。

* 若要排查该功能的常见问题，请参阅[排查更改跟踪和清单问题](../troubleshoot/change-tracking.md)。


