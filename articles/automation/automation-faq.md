---
title: Azure 自动化常见问题解答 | Microsoft Docs
description: 本文提供了有关 Azure 自动化的常见问题解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 06/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e9168f7ef3cf49f4c13fdc67807061f23b6b402
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111984007"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自动化常见问题解答

本 Microsoft 常见问题解答列出了有关 Azure 自动化的常见问题解答。 如果对其功能还有其他任何问题，请转到论坛并发布问题。 如果某个问题经常被问到，我们会将其添加到本文中，以便可以快捷轻松地找到该问题。

## <a name="update-management"></a>更新管理

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>能否阻止意外的 OS 级别升级？

在某些 Linux 版本（例如 Red Hat Enterprise Linux）中，可以通过包执行 OS 级别的升级。 这可能会导致运行更新管理并更改 OS 版本号。 由于更新管理使用相同的方法来更新管理员将在 Linux 计算机本地使用的包，因此，此行为是有意实施的。

若要避免通过更新管理部署来更新 OS 版本，可以使用“排除”功能。

在 Red Hat Enterprise Linux 中，要排除的包名称为 `redhat-release-server.x86_64`。

### <a name="why-arent-criticalsecurity-updates-applied"></a>为什么没有应用关键更新/安全更新？

将更新部署到 Linux 计算机时，可以选择更新分类。 该选项会筛选满足指定条件的更新。 部署更新时，会在计算机本地应用此筛选器。

由于更新管理在云中执行更新扩充，因此可以将更新管理中的某些更新标记为存在安全影响，但是本地计算机上不会显示该信息。 如果向 Linux 计算机应用关键更新，可能有些更新不会被标记为对该计算机具有安全影响，因此不会应用这些更新。 但是，更新管理仍可能报告该计算机不合规，因为其中包含相关更新的其他信息。

在 CentOS 的 RTM 版本上无法按更新分类部署更新。 要为 CentOS 正确部署更新，请选择所有分类以确保应用更新。 对于 SUSE，如果首先要求与 zypper（包管理器）或其依赖项相关的安全更新，则仅选择“其他更新”作为分类可以安装某些其他安全更新。 此行为是 zypper 的一项限制。 在某些情况下，可能需要重新运行更新部署，然后通过更新日志验证部署。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>能否在 Azure 租户之间部署更新？

如果你在向“更新管理”进行报告的另一个 Azure 租户中存在需要修补的计算机，则必须使用以下解决方法来计划它们。 可以将 [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) cmdlet 与指定的 `ForUpdateConfiguration` 参数一起使用来创建计划。 可以使用 [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) cmdlet，并将另一个租户中的计算机传递给 `NonAzureComputer` 参数。 以下示例演示如何执行此操作。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>流程自动化 - Python Runbook

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Azure 自动化支持 Python 3 的哪个版本？

对于云作业，支持 Python 3.8。 如果代码与不同的版本兼容，则任何 3.x 版本的脚本和包都可能起作用。

对于 Windows 混合 Runbook 辅助角色上的混合作业，可以选择安装想要使用的任何 3.x 版本。 对于 Linux 混合 Runbook 辅助角色上的混合作业，我们根据安装在计算机的 Python 3 版本来运行 DSC OMSConfig 和 Linux 混合辅助角色。 建议安装版本 3.6，但是如果 Python 3 不同版本间的方法签名或协定没有发生重大更改，则其他版本也应该会起作用。

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Python 2 Runbook 和 Python 3 Runbook 是否可以在同一自动化帐户中运行？

可以，对于在同一自动化帐户中使用 Python 2 Runbook 和 Python 3 Runbook 没有限制。  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>将现有 Python 2 Runbook 和包迁移到 Python 3 的计划是什么？

Azure 自动化不计划将 Python 2 Runbook 和包迁移到 Python 3。 你必须自己执行此迁移。 现有以及新的 Python 2 Runbook 和包将继续工作。

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>默认情况下，在 Python 3 环境中受支持的包是哪些？

Azure 包 4.0.0 会默认安装在 Python 3 自动化环境中。 你可手动导入更高版本的 Azure 包来替代默认版本。

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-the-other-way-around"></a>如果要运行一个引用 Python 2 包的 Python 3 Runbook 呢？其他方式呢？

Python 2 和 Python 3 具有不同的执行环境。 当 Python 2 Runbook 运行时，仅可导入 Python 2 包，对于 Python 3 来说同样如此。

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>如何区分 Python 2 Runbook 和 Python 3 Runbook 及其包？

Python 3 是新的 Runbook 定义，可区分 Python 2 Runbook 和 Python 3 Runbook。 同样地，为 Python 3 包引入另一种包类型。

### <a name="how-does-a-hybrid-runbook-worker-know-which-version-of-python-to-run-when-both-python2-and-python3-are-installed"></a>混合 Runbook 辅助角色如何知道在安装 Python2 和 Python3 时要运行的 Python 版本？

对于 Windows Runbook 辅助角色，运行 Python 2 Runbook 时，它会首先查找环境变量 `PYTHON_2_PATH`，并验证它是否指向有效的可执行文件。 例如，如果安装文件夹为 `C:\Python2`，它将检查 `C:\Python2\python.exe` 是否是有效的路径。 如果未找到，它会查找 `PATH` 环境变量以执行类似的检查。

对于 Python 3，它首先查找 `PYTHON_3_PATH` 环境变量，然后返回到 `PATH` 环境变量。

如果只使用一个版本的 Python，可以将安装路径添加到 `PATH` 变量。 如果要在 Runbook 辅助角色上同时使用这两个版本，请为这些版本将 `PYTHON_2_PATH` 和 `PYTHON_3_PATH` 设置为模块的位置。

### <a name="how-does-a-hybrid-runbook-worker-locate-the-python-interpreter"></a>混合 Runbook 辅助角色如何定位 Python 解释器？

如前文所述，Python 模块的定位由环境变量控制。

### <a name="is-python-3-supported-in-source-control"></a>源代码管理是否支持 Python 3？

否。 Python 3 目前不支持源代码管理。 默认情况下，Python Runbook 同步为 Python 2 Runbook。

### <a name="how-can-a-runbook-author-know-what-python-packages-are-available-in-an-azure-sandbox"></a>Runbook 作者如何知道 Azure 沙盒中提供了哪些 Python 包？

使用以下代码列出默认已安装的模块：

```python
#!/usr/bin/env python3

import pkg_resources
installed_packages = pkg_resources.working_set
installed_packages_list = sorted(["%s==%s" % (i.key, i.version)
   for i in installed_packages])

for package in installed_packages_list:
    print(package)
```

### <a name="how-can-a-runbook-author-set-which-version-of-a-package-module-to-be-used-if-there-are-multiple-modules"></a>如果有多个模块，Runbook 作者如何设置使用哪个版本的包模块？

可以通过在自动化帐户中导入 Python 包来替代默认版本。 为自动化帐户中导入的版本提供首选项。

## <a name="next-steps"></a>后续步骤

如果未在此处找到问题的答案，则可以参考下方针对更多问题和答案的源。

- [Azure 自动化](/answers/topics/azure-automation.html)
- [反馈论坛](https://feedback.azure.com/forums/905242-update-management)
