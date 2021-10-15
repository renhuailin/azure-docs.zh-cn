---
title: Azure 自动化常见问题解答
description: 本文提供了有关 Azure 自动化的常见问题解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/25/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dfe04bb2fcb7022934ac699bb5c3bd90005bf3b3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356604"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自动化常见问题解答

本 Microsoft 常见问题解答列出了有关 Azure 自动化的常见问题解答。 如果对其功能还有其他任何问题，请转到论坛并发布问题。 如果某个问题经常被问到，我们会将其添加到本文中，以便可以快捷轻松地找到该问题。

## <a name="can-update-management-prevent-unexpected-os-level-upgrades"></a>更新管理是否可以阻止意外的 OS 级别升级？

是的。 有关详细信息，请参阅[排除更新](./update-management/manage-updates-for-vm.md#exclude-updates)。

## <a name="why-arent-criticalsecurity-updates-applied-to-a-linux-machine-with-update-management"></a>为何不通过更新管理将关键/安全更新应用于 Linux 计算机？

将更新部署到 Linux 计算机时，可以选择更新分类。 该选项会筛选满足指定条件的更新。 有关详细信息，请参阅 [Linux 更新分类](./update-management/manage-updates-for-vm.md#linux-update-classifications)。

## <a name="can-update-management-deploy-updates-across-azure-tenants"></a>更新管理是否可跨 Azure 租户部署更新？

是的。 有关说明，请参阅[跨 Azure 租户部署更新](./update-management/deploy-updates.md#deploy-updates-across-azure-tenants)。

## <a name="which-python-3-version-is-supported-in-azure-automation"></a>Azure 自动化支持 Python 3 的哪个版本？

对于云作业，支持 Python 3.8。 如果代码与不同的版本兼容，则任何 3.x 版本的脚本和包都可能起作用。

对于 Windows 混合 Runbook 辅助角色上的混合作业，可以选择安装想要使用的任何 3.x 版本。 对于 Linux 混合 Runbook 辅助角色上的混合作业，我们根据安装在计算机的 Python 3 版本来运行 DSC OMSConfig 和 Linux 混合辅助角色。 建议安装版本 3.6，但是如果 Python 3 不同版本间的方法签名或协定没有发生重大更改，则其他版本也应该会起作用。

## <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Python 2 Runbook 和 Python 3 Runbook 是否可以在同一自动化帐户中运行？

可以，对于在同一自动化帐户中使用 Python 2 Runbook 和 Python 3 Runbook 没有限制。  

## <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>将现有 Python 2 Runbook 和包迁移到 Python 3 的计划是什么？

Azure 自动化不计划将 Python 2 Runbook 和包迁移到 Python 3。 你必须自己执行此迁移。 现有以及新的 Python 2 Runbook 和包将继续工作。

## <a name="what-packages-are-supported-by-default-in-python-3-environment"></a>Python 3 环境中默认支持哪些包？

Azure 包 4.0.0。 有关详细信息，请参阅[管理 Python 3 包](python-3-packages.md)。

## <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-the-other-way-around"></a>如果要运行一个引用 Python 2 包的 Python 3 Runbook 呢？其他方式呢？

Python 2 和 Python 3 具有不同的执行环境。 当 Python 2 Runbook 运行时，仅可导入 Python 2 包，对于 Python 3 来说同样如此。

## <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>如何区分 Python 2 Runbook 和 Python 3 Runbook 及其包？

Python 3 是新的 Runbook 定义，可区分 Python 2 Runbook 和 Python 3 Runbook。 同样地，为 Python 3 包引入另一种包类型。

## <a name="how-does-a-hybrid-runbook-worker-know-which-version-of-python-to-run-when-both-python2-and-python3-are-installed"></a>混合 Runbook 辅助角色如何知道在安装 Python2 和 Python3 时要运行的 Python 版本？

对于 Windows Runbook 辅助角色，运行 Python 2 Runbook 时，它会首先查找环境变量 `PYTHON_2_PATH`，并验证它是否指向有效的可执行文件。 例如，如果安装文件夹为 `C:\Python2`，它将检查 `C:\Python2\python.exe` 是否是有效的路径。 如果未找到，它会查找 `PATH` 环境变量以执行类似的检查。

对于 Python 3，它首先查找 `PYTHON_3_PATH` 环境变量，然后返回到 `PATH` 环境变量。

请参阅[多个 Python 版本](automation-runbook-types.md#multiple-python-versions)。

## <a name="how-does-a-hybrid-runbook-worker-locate-the-python-interpreter"></a>混合 Runbook 辅助角色如何定位 Python 解释器？

如前文所述，Python 模块的定位由环境变量控制。

## <a name="is-python-3-supported-in-source-control"></a>源代码管理是否支持 Python 3？

否。 Python 3 目前不支持源代码管理。 默认情况下，Python Runbook 同步为 Python 2 Runbook。

## <a name="how-can-a-runbook-author-know-what-python-packages-are-available-in-an-azure-sandbox"></a>Runbook 作者如何知道 Azure 沙盒中提供了哪些 Python 包？

请参阅[确定沙盒中可用的包](python-3-packages.md#identify-available-packages-in-sandbox)。

## <a name="how-can-a-runbook-author-set-which-version-of-a-package-module-to-be-used-if-there-are-multiple-modules"></a>如果有多个模块，Runbook 作者如何设置使用哪个版本的包模块？

请参阅[管理 Python 3 包](python-3-packages.md)。

## <a name="next-steps"></a>后续步骤

如果未在此处找到问题的答案，则可以参考下方针对更多问题和答案的源。

- [Azure 自动化](/answers/topics/azure-automation.html)
- [反馈论坛](https://feedback.azure.com/forums/905242-update-management)
