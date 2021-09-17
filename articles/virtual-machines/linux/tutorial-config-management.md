---
title: 教程 - 在 Azure 中管理 Linux 虚拟机配置
description: 在本教程中，你将学习如何在 Linux 虚拟机上识别更改和管理包更新版
author: mgoedtel
manager: gwallace
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2019
ms.author: magoedte
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: fae7a6065f04b34ea952dd474919d43dad46d0df
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690940"
---
# <a name="tutorial-monitor-changes-and-update-a-linux-virtual-machine-in-azure"></a>教程：监视更改并更新 Azure 中的 Linux 虚拟机

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

Azure [更改跟踪](../../automation/change-tracking/overview.md)允许你轻松识别更改，[更新管理](../../automation/update-management/overview.md)允许你管理 Azure Linux VM 的操作系统更新。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 管理 Linux 更新
> * 监视器更改和清单

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本教程需要 Azure CLI 2.0.30 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-vm"></a>创建 VM

若要查看诊断和指标的状态，需要创建一个 VM。 首先，使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 以下示例在 *eastus* 位置创建名为 *myResourceGroupMonitor* 的资源组。

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

现使用 [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 以下示例将创建名为 myVM 的 VM，并生成 SSH 密钥（如果它们尚不存在于 *~/.ssh/* 中）：

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="manage-software-updates"></a>管理软件更新

使用更新管理可以管理 Azure Linux VM 的更新和修补程序。
可以直接在 VM 中快速评估可用更新的状态、计划所需更新的安装以及查看部署结果，验证更新是否已成功应用到 VM。

有关定价信息，请参阅[更新管理的自动化定价](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>启用更新管理

为 VM 启用更新管理：

1. 在屏幕的左侧，选择“虚拟机”。
2. 从列表中选择一个虚拟机。
3. 在 VM 屏幕上的“操作”部分中，选择“更新管理”。 “启用更新管理”屏幕随即打开。

执行验证以确定是否为该 VM 启用了更新管理。
验证包括检查 Log Analytics 工作区和链接的自动化帐户，以及解决方案是否在工作区中。

[Log Analytics](../../azure-monitor/logs/log-query-overview.md) 工作区用于收集由功能和服务（如更新管理）生成的数据。
工作区提供了一个位置来查看和分析来自多个数据源的数据。
若要在需要更新的 VM 上执行其他操作，可使用 Azure 自动化运行针对 VM 的 Runbook，例如下载和应用更新。

验证过程还会检查 VM 是否预配了 Log Analytics 代理和自动化混合 Runbook 辅助角色。 此代理用于与虚拟机通信并获取关于更新状态的信息。

选择 Log Analytics 工作区和自动化帐户，然后选择“启用”以启用此解决方案。 启用此解决方案最长需要 15 分钟的时间。

如果在载入过程中发现缺少下列任何先决条件，则会自动添加这些条件：

* [Log Analytics](../../azure-monitor/logs/log-query-overview.md) 工作区
* [自动化帐户](../../automation/index.yml)
* VM 上已启用[混合 runbook 辅助角色](../../automation/automation-hybrid-runbook-worker.md)

“更新管理”屏幕随即打开。 配置要使用的位置、Log Analytics 工作区和自动化帐户，然后选择“启用”。 如果这些字段灰显，则意味着已为 VM 启用其他自动化解决方案，因此必须使用同一工作区和自动化帐户。

![启用“更新管理解决方案”](./media/tutorial-monitoring/manage-updates-update-enable.png)

启用解决方案最多可能需要 15 分钟。 在此期间，不应关闭浏览器窗口。 启用该解决方案后，VM 中缺少的更新信息会流向 Azure Monitor 日志。 这些数据需花费 30 分钟到 6 小时的时间才能用于分析。

### <a name="view-update-assessment"></a>查看更新评估

启用“更新管理”后，“更新管理”屏幕随即显示。 评估更新完成后，可在“缺失更新”选项卡上查看缺失更新的列表。

 ![查看更新状态](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>计划更新部署

若要安装更新，请计划一个遵循你的发布时间和服务窗口的部署。 可选择在部署中包括哪种更新类型。 例如，可包括关键或安全更新，排除更新汇总。

单击“更新管理”屏幕顶部的“计划更新部署”，计划用于虚拟机的新的更新部署。 在“新建更新部署”屏幕中，指定以下信息：

若要创建新的更新部署，请选择“计划更新部署”。 此时会打开“新建更新部署”页。 为下表中介绍的属性输入值，然后单击“创建”：

| properties | 描述 |
| --- | --- |
| 名称 |用于标识更新部署的唯一名称。 |
|操作系统| Linux 或 Windows|
| 要更新的组 |对于 Azure 计算机，请定义基于一组订阅、资源组、位置和标记的查询，生成要在部署中包含的 Azure VM 动态组。 </br></br>对于非 Azure 计算机，请选择现有的已保存搜索，以选择要包含在部署中的非 Azure 计算机组。 </br></br>有关详细信息，请参阅[动态组](../../automation/update-management/configure-groups.md)|
| 要更新的计算机 |选择已保存的搜索、已导入的组或者从下拉列表中选择“计算机”并选择单个计算机。 如果选择“计算机”，则计算机的就绪状态将在“更新代理商准备情况”列中显示 。</br> 要了解在 Azure Monitor 日志中创建计算机组的不同方法，请参阅 [Azure Monitor 日志中的计算机组](../../azure-monitor/logs/computer-groups.md) |
|更新分类|选择所需的所有更新分类|
|包括/排除更新|这将打开“包括/排除”页。 要包含或排除的更新位于单独的选项卡上。 有关如何处理包含的详细信息，请参阅[计划更新部署](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment) |
|计划设置|选择启动时间，然后选择任“一次”或“重复”|
| 前脚本 + 后脚本|选择要在部署前和部署后运行的脚本|
| 维护时段 |为更新设置的分钟数。 该值不能小于 30 分钟，且不能大于 6 小时 |
| 重新启动控制| 确定应如何处理重新启动。 可用选项包括：</br>需要时重新启动(默认)</br>永远重启</br>永不重启</br>仅重启 - 不安装更新|

此外，能够以编程方式创建更新部署。 若要了解如何使用 REST API 创建更新部署，请参阅[软件更新配置 - 创建](/rest/api/automation/softwareupdateconfigurations/create)。 此外，还有一个示例 Runbook，可用于创建每周更新部署。 若要了解有关此 Runbook 的详细信息，请参阅[为资源组中的一个或多个 VM 创建每周更新部署](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)。

完成配置计划后，单击“创建”按钮，然后返回到状态仪表板。
请注意，“已计划”表显示你创建的部署计划。

### <a name="view-results-of-an-update-deployment"></a>查看更新部署结果

在计划性部署开始后，可以在“更新管理”屏幕的“更新部署”选项卡上查看该部署的状态。
如果部署当前正在运行，则状态显示为“正在运行”。 如果部署已成功完成，则状态会更改为“成功”。
如果部署中有一个或多个更新失败，则状态为“部分失败”。
选择已完成的更新部署，查看该更新部署的仪表板。

![特定部署的更新部署状态仪表板](./media/tutorial-monitoring/manage-updates-view-results.png)

在“更新结果”中，磁贴总结了 VM 上更新和部署结果的总数。
右侧的表格详细列出了每个更新的细目以及安装结果，结果可能是以下值之一：

* 未尝试 - 由于定义的维护时段时长不足，因而未安装更新。
* 成功- 更新成功
* 失败- 更新失败

若要查看部署创建的所有日志条目，请选择“所有日志”。

选择“输出”磁贴，查看负责管理目标 VM 更新部署的 runbook 的作业流。

若要查看有关部署中错误的详细信息，请选择“错误”。

## <a name="monitor-changes-and-inventory"></a>监视器更改和清单

可以收集和查看清单，了解计算机上的软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项。 跟踪计算机的配置有助于查明环境中的操作问题，更好地了解计算机的状态。

### <a name="enable-change-and-inventory-management"></a>启用更改和清单管理

为 VM 启用更改和清单管理：

1. 在屏幕的左侧，选择“虚拟机”。
2. 从列表中选择一个虚拟机。
3. 在 VM 屏幕上的“操作”部分中，选择“清单”或“更改跟踪”。 此时会打开“启用更改跟踪和清单”屏幕。

配置要使用的位置、Log Analytics 工作区和自动化帐户，然后选择“启用”。 如果这些字段灰显，则意味着已为 VM 启用其他自动化解决方案，因此必须使用同一工作区和自动化帐户。 即使这些解决方案在菜单上是分开的，它们也是同一解决方案。 启用一个解决方案就会为 VM 启用两个解决方案。

![启用更改和清单跟踪](./media/tutorial-monitoring/manage-inventory-enable.png)

启用解决方案后，可能需要一些时间在 VM 上收集清单，然后才显示数据。

### <a name="track-changes"></a>跟踪更改

在 VM 中的“操作”下选择“更改跟踪”。 选择“编辑设置”，此时会显示“更改跟踪”页。 选择要跟踪的设置类型，然后选择“+ 添加”以配置设置。 Linux 上的可用选项为“Linux 文件”。

有关更改跟踪的详细信息，请参阅[排查 VM 上的更改问题](../../automation/troubleshoot/change-tracking.md)

### <a name="view-inventory"></a>查看清单

在 VM 中的“操作”下选择“清单”。 在“软件”选项卡上有一个表，列出了已发现的软件。 可在表中查看每个软件记录的高级详细信息。 这些详细信息包括软件名称、版本、发布者和上次刷新时间。

![查看清单](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>监视活动日志和更改

在 VM 的“更改跟踪”页中，选择“管理活动日志连接”。 此任务打开“Azure 活动日志”页。 选择“连接”，将更改跟踪连接到 VM 的 Azure 活动日志。

启用此设置后，导航到 VM 的“概览”页，然后选择“停止”以停止 VM。 出现提示时，选择“是”即可停止 VM。 将 VM 解除分配以后，请选择“启动”以重启 VM。

停止和启动 VM 时，会在活动日志中记录一个事件。 导航回到“更改跟踪”页。 选择页面底部的“事件”选项卡。 一段时间后，事件会显示在图表和表中。 可以选择每个事件来查看其详细信息。

![在活动日志中查看更改](./media/tutorial-monitoring/manage-activitylog-view-results.png)

此图表显示了一段时间内发生的更改。 添加活动日志连接以后，顶部的线形图会显示 Azure 活动日志事件。 条形图的每一行代表不同类型的可跟踪更改。 这些类型是 Linux 守护程序、文件、软件。 “更改”选项卡显示在可视化效果中显示的更改的详细信息，按更改发生时间以降序方式排列（最近发生的排在最前面）。

## <a name="next-steps"></a>后续步骤

在本教程中，你配置并查看了 VM 的更改跟踪和更新管理。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建资源组和 VM
> * 管理 Linux 更新
> * 监视器更改和清单

请转到下一教程，了解如何监视 VM。

> [!div class="nextstepaction"]
> [监视虚拟机](/previous-versions/azure/virtual-machines/linux/tutorial-monitor)