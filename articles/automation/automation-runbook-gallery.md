---
title: 在 PowerShell 库中使用 Azure 自动化 Runbook 和模块
description: 本文介绍如何在 PowerShell 库中使用来自 Microsoft 和社区的 Runbook 和模块。
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: b8f1fbdcb3b268c24eb19517a0686c6c72c50842
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294002"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>使用 PowerShell 库中的 Runbook 和模块

无需在 Azure 自动化中创建自己的 Runbook 和模块，即可访问 Microsoft 和社区构建的方案。 可以从 PowerShell 库获取 PowerShell Runbook 和[模块](#modules-in-powershell-gallery)，从 Azure 自动化 GitHub 组织获取 [Python Runbook](#use-python-runbooks)。 还可以通过共享[开发的方案](#add-a-powershell-runbook-to-the-gallery)来为社区做出贡献。

> [!NOTE]
> TechNet 脚本中心即将退役。 脚本中心的 Runbook 库中的所有 Runbook 都已转移到[自动化 GitHub 组织](https://github.com/azureautomation)。有关详细信息，请参阅[此文](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)。

## <a name="runbooks-in-powershell-gallery"></a>PowerShell 库中的 Runbook

[PowerShell 库](https://www.powershellgallery.com/packages)提供来自 Microsoft 和社区的各种 Runbook，可将其导入 Azure 自动化。 若要使用 Runbook，可以从库下载 Runbook，也可以直接从库或 Azure 门户中的自动化帐户导入 Runbook。

> [!NOTE]
> PowerShell 库不支持图形 Runbook。

仅可使用 Azure 门户直接从 PowerShell 库导入。 不能使用 PowerShell 执行此功能。

> [!NOTE]
> 应验证从 PowerShell 库获取的任何 Runbook 的内容，在生产环境中安装和运行这些 Runbook 时，请谨慎操作。

## <a name="modules-in-powershell-gallery"></a>PowerShell 库中的模块

PowerShell 模块包含可在 Runbook 中使用的 cmdlet。 [PowerShell 库](https://www.powershellgallery.com)中提供了可在 Azure 自动化中安装的现有模块。 可以从 Azure 门户启动此库，并将这些模块直接安装到 Azure 自动化中，也可以下载并手动安装这些模块。

## <a name="common-scenarios-available-in-powershell-gallery"></a>PowerShell 库中提供的常见方案

下面的列表包含一些支持常见方案的 Runbook。 有关由 Azure 自动化团队创建的 Runbook 的完整列表，请参阅 [AzureAutomationTeam 配置文件](https://www.powershellgallery.com/profiles/AzureAutomationTeam)。

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - 从 PowerShell 库导入自动化帐户中所有模块的最新版本。
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - 配置 Azure 诊断和日志分析，以接收包含作业状态和作业流的 Azure 自动化日志。
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - 从 Windows Azure 虚拟机复制远程文件。
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - 将本地文件复制到 Azure 虚拟机。

## <a name="import-a-powershell-runbook-from-github-with-the-azure-portal"></a>使用 Azure 门户从 GitHub 导入 PowerShell runbook

1. 在 Azure 门户中，打开自动化帐户。
1. 在“过程自动化”下，选择“Runbook 库”。 
1. 选择“源: GitHub”。
1. 可以使用列表上方的筛选器按发布者、类型和排序方式来缩小显示范围。 找到所需的库项，选择它以查看其详细信息。

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-github-sm.png" alt-text="浏览 GitHub 库。" lightbox="media/automation-runbook-gallery/browse-gallery-github-lg.png":::

1. 若要导入某个项，请单击详细信息边栏选项卡上的“导入”。

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-github-sm.png" alt-text="GitHub 库中的 Runbook 的详细视图。" lightbox="media/automation-runbook-gallery/gallery-item-details-blade-github-lg.png":::

1. 可选择更改 Runbook 的名称，并单击“确定”导入该 Runbook。
1. Runbook 将出现在自动化帐户的“Runbook”选项卡中。

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>使用 Azure 门户从 Runbook 库导入 PowerShell Runbook

1. 在 Azure 门户中，打开自动化帐户。
1. 在“过程自动化”下，选择“Runbook 库”。 
1. 选择“源:PowerShell 库”。 此时会显示可供浏览的可用 Runbook 列表。
1. 可以使用列表上方的搜索框来缩小列表范围，或者使用筛选器来按发布者、类型和排序方式来缩小显示范围。 找到所需的库项，选择它以查看其详细信息。

   :::image type="content" source="media/automation-runbook-gallery/browse-gallery-sm.png" alt-text="浏览 Runbook 库。" lightbox="media/automation-runbook-gallery/browse-gallery-lg.png":::

1. 若要导入某个项，请单击详细信息边栏选项卡上的“导入”。

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-detail-sm.png" alt-text="显示 Runbook 库项详细信息。" lightbox="media/automation-runbook-gallery/gallery-item-detail-lg.png":::

1. 可选择更改 Runbook 的名称，并单击“确定”导入该 Runbook。
1. Runbook 将出现在自动化帐户的“Runbook”选项卡中。

## <a name="add-a-powershell-runbook-to-the-gallery"></a>将 PowerShell Runbook 添加到库

Microsoft 建议将 Runbook 添加到你认为对其他客户有用的 PowerShell 库中。 PowerShell 库接受 PowerShell 模块和 PowerShell 脚本。 可以通过[将 Runbook 上传到 PowerShell 库](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)来添加 Runbook。

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>使用 Azure 门户从模块库导入模块

1. 在 Azure 门户中，打开自动化帐户。
1. 在“共享资源”中选择“模块”，打开模块列表 。
1. 请单击页面顶部的“浏览全部”。

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="模块库视图。" lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. 在“浏览库”页上，可以使用搜索框在以下任意字段中查找匹配项：

   * 模块名称
   * Tags
   * 作者
   * Cmdlet/DSC 资源名称

1. 找到感兴趣的模块并选择它以查看其详细信息。

   深入了解特定模块时，可以查看更多信息。 此信息包括返回 PowerShell 库的链接、任何所需的依赖项以及该模块包含的所有 cmdlet 或 DSC 资源。

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="库中模块的详细视图。" lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. 若要直接将模块安装到 Azure 自动化中，请单击“导入”。
1. 在“导入”窗格上，可以看到要导入的模块的名称。 如果安装了所有依赖项，“确定”按钮将处于活动状态。 如果缺少依赖项，则需要在导入此模块前导入这些依赖项。
1. 在“导入”窗格上，单击“确定”导入模块。 Azure 自动化将模块导入帐户时，它提取有关该模块和 cmdlet 的元数据。 此操作可能需要几分钟才能完成，因为需要提取每个活动。
1. 将收到正在部署该模块的初始通知；完成此过程后，还会收到另一通知。
1. 导入模块后，可以看到可用的活动。 可以在 Runbook 和 DSC 资源中使用模块资源。

> [!NOTE]
> 仅支持 PowerShell 核心的模块将在 Azure 自动化中不受支持，无法在 Azure 门户中导入，或直接从 PowerShell 库部署。

## <a name="use-python-runbooks"></a>使用 Python Runbook

[Azure 自动化 GitHub 组织](https://github.com/azureautomation)中提供了 Python Runbook。 在你向我们的 GitHub 存储库贡献内容时，请在上传所贡献的内容时添加“（GitHub 主题）: Python3”标记。

## <a name="request-a-runbook-or-module"></a>请求 Runbook 或模块

可以将请求发送到[用户之声](https://feedback.azure.com/forums/246290-azure-automation/)。  如果编写 Runbook 时需要帮助，或对 PowerShell 存有疑问，请将问题发布到我们的 [Microsoft 问答页面](/answers/topics/azure-automation.html)。

## <a name="next-steps"></a>后续步骤

* 若要开始使用 PowerShell Runbook，请参阅[教程：创建 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)。
* 若要使用 runbook，请参阅[在 Azure 自动化中管理 runbook](manage-runbooks.md)。
* 有关 PowerShell 的详细信息，请参阅 [PowerShell 文档](/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation)。