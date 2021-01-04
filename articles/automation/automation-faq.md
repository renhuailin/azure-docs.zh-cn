---
title: Azure 自动化常见问题解答 | Microsoft Docs
description: 本文提供了有关 Azure 自动化的常见问题解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 2b40cc3d4cea4476ffde8bee8cec694975eb5083
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724266"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自动化常见问题解答

本 Microsoft 常见问题解答列出了有关 Azure 自动化的常见问题解答。 如果你对其功能有任何其他问题，请前往讨论论坛并发布你的问题。 如果问题是常见问题，我们会将其添加到本文中，以便快速轻松地找到它。

## <a name="update-management"></a>更新管理

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>能否阻止意外的 OS 级别升级？

在某些 Linux 版本（例如 Red Hat Enterprise Linux）中，可以通过包执行 OS 级别的升级。 这可能会导致运行更新管理并更改 OS 版本号。 由于更新管理使用相同的方法来更新管理员将在 Linux 计算机本地使用的包，因此，此行为是有意实施的。

若要避免通过更新管理部署来更新 OS 版本，可以使用“排除”功能。

在 Red Hat Enterprise Linux 中，要排除的包名称为 `redhat-release-server.x86_64`。

### <a name="why-arent-criticalsecurity-updates-applied"></a>为什么没有应用关键更新/安全更新？

将更新部署到 Linux 计算机时，可以选择更新分类。 该选项会筛选满足指定条件的更新。 部署更新时，会在计算机本地应用此筛选器。

由于更新管理在云中执行更新扩充，因此可以将更新管理中的某些更新标记为存在安全影响，但是本地计算机上不会显示该信息。 如果向 Linux 计算机应用关键更新，可能有些更新不会被标记为对该计算机具有安全影响，因此不会应用这些更新。 但是，更新管理仍可能报告该计算机不合规，因为其中包含相关更新的其他信息。

在 CentOS 的 RTM 版本上无法按更新分类部署更新。 要为 CentOS 正确部署更新，请选择所有分类以确保应用更新。 对于 SUSE，仅选择 **其他更新** 作为分类时，如果它们与 zypper (程序包管理器相关，则可以安装其他一些安全更新) 或者首先需要其依赖项。 此行为是 zypper 的一项限制。 在某些情况下，可能需要重新运行更新部署，然后通过更新日志验证部署。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>能否在 Azure 租户之间部署更新？

如果你在向“更新管理”进行报告的另一个 Azure 租户中存在需要修补的计算机，则必须使用以下解决方法来计划它们。 可以将 [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) cmdlet 与指定的 `ForUpdateConfiguration` 参数一起使用来创建计划。 可以使用 [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) cmdlet，并将另一个租户中的计算机传递给 `NonAzureComputer` 参数。 以下示例演示如何执行此操作。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>流程自动化-Python runbook

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Azure 自动化支持哪个 Python 3 版本？

对于云作业，支持 Python 3.8。 如果代码在不同版本之间兼容，则任何1.x 版本中的脚本和包都可能起作用。

对于 Windows 混合 Runbook 辅助角色上的混合作业，你可以选择安装要使用的任何1.x 版本。 对于 Linux 混合 Runbook 辅助角色上的混合作业，我们依赖于计算机上安装的 Python 3 版本来运行 DSC OMSConfig 和 Linux 混合辅助角色。 建议安装版本 3.6;但是，如果在不同版本的 Python 3 之间没有重大更改，则不同版本也应该有效。

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Python 2 和 Python 3 runbook 是否可以在同一自动化帐户中运行？

是的，在同一自动化帐户中不存在使用 Python 2 和 Python 3 runbook 的限制。  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>将现有 Python 2 runbook 和包迁移到 Python 3 的计划是什么？

Azure Automation 不打算将 Python 2 runbook 和包迁移到 Python 3。 您必须自行执行此迁移。 现有和新的 Python 2 runbook 和包将继续工作。

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>在 Python 3 环境下，默认支持哪些包？

默认情况下，在 Python 3 自动化环境中安装 Azure package 4.0.0。 可以手动导入更高版本的 Azure 包以替代默认版本。

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>如果运行一个引用 Python 2 包的 Python 3 runbook，反之亦然？

Python 2 和 Python 3 具有不同的执行环境。 当 Python 2 runbook 正在运行时，仅可导入 Python 2 包，并将其与 Python 3 类似。

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>如何实现区分 Python 2 和 Python 3 runbook 和包吗？

Python 3 是新的 runbook 定义，可区分 Python 2 和 Python 3 runbook。 同样，为 Python 3 包引入了另一种包类型。

## <a name="next-steps"></a>后续步骤

如果此处未解答你的问题，你可以参考以下资源，了解更多问题和答案。

- [Azure 自动化](/answers/topics/azure-automation.html)
- [反馈论坛](https://feedback.azure.com/forums/905242-update-management)
