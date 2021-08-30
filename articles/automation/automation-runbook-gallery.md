---
title: 在 PowerShell 库中使用 Azure 自动化 Runbook 和模块
description: 本文介绍如何使用来自 Microsoft GitHub 存储库和 PowerShell 库的 runbook 和模块。
services: automation
ms.subservice: process-automation
ms.date: 04/07/2021
ms.topic: conceptual
ms.openlocfilehash: 1f7a3f31ed67817252f7ba33b9f4a83ef8ccd5c6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114455615"
---
# <a name="use-existing-runbooks-and-modules"></a>使用现有的 runbook 和模块

无需在 Azure 自动化中创建自己的 Runbook 和模块，即可访问 Microsoft 和社区构建的方案。 可从 Azure 门户中的 Runbook 库获取与 Azure 相关的 PowerShell 和 Python runbook，并从 PowerShell 库中获取[模块](#modules-in-the-powershell-gallery)和 [runbook](#runbooks-in-the-powershell-gallery)（可能是也可能不是特定于 Azure 的）。 还可以通过共享[开发的方案](#contribute-to-the-community)来为社区做出贡献。

> [!NOTE]
> TechNet 脚本中心即将退役。 脚本中心的 Runbook 库中的所有 runbook 都已转移到[自动化 GitHub 组织](https://github.com/azureautomation)。有关详细信息，请参阅[将 Azure 自动化 runbook 移动到 GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)。

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>使用 Azure 门户从 GitHub 导入 runbook

1. 在 Azure 门户中，打开自动化帐户。
2. 在“过程自动化”下，选择“Runbook 库”。 
3. 选择“源: GitHub”。
4. 可以使用列表上方的筛选器按发布者、类型和排序方式来缩小显示范围。 找到所需的库项，选择它以查看其详细信息。

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="浏览 Runbook 库。" lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

5. 若要导入某个项，请单击详细信息页上的“导入”。

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="库项导入。":::

6. （可选）在导入边栏选项卡更改 runbook 的名称，然后单击“确定”以导入该 runbook。

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import-blade.png" alt-text="库项导入边栏选项卡。":::

7. Runbook 将出现在自动化帐户的“Runbook”选项卡中。
 
## <a name="runbooks-in-the-powershell-gallery"></a>PowerShell 库中的 runbook

> [!IMPORTANT]
> 应验证从 PowerShell 库获取的任何 runbook 的内容。 在生产环境中安装和运行这些 runbook 时，请谨慎操作。

[PowerShell 库](https://www.powershellgallery.com/packages)提供来自 Microsoft 和社区的各种 runbook，可将其导入 Azure 自动化。 若要使用 Runbook，可以从库下载 Runbook，也可以直接从库或 Azure 门户中的自动化帐户导入 Runbook。

> [!NOTE]
> PowerShell 库不支持图形 Runbook。

仅可使用 Azure 门户直接从 PowerShell 库导入。 不能使用 PowerShell 执行此功能。 此过程与[使用 Azure 门户从 GitHub 导入 runbook](#import-runbooks-from-github-with-the-azure-portal)所述的相同，不同之处在于源为 PowerShell 库 。

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="显示 runbook 库源选择。" lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>PowerShell 库中的模块

PowerShell 模块包含可在 Runbook 中使用的 cmdlet。 [PowerShell 库](https://www.powershellgallery.com)中提供了可在 Azure 自动化中安装的现有模块。 可以从 Azure 门户启动此库，并将这些模块直接安装到 Azure 自动化中，也可以下载并手动安装这些模块。

你还可以在 Azure 门户中找到要导入的模块。 在“共享资源”下的“模块库”中为自动化帐户列出了这些模块 。

> [!IMPORTANT] 
> 不要在任何专门使用 Az 模块执行的脚本中包含关键字“AzureRm”。 即使在注释中包含关键字，也可能导致加载 AzureRm，然后与 Az 模块冲突。

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>PowerShell 库中提供的常见场景

下面的列表包含一些支持常见方案的 Runbook。 有关由 Azure 自动化团队创建的 Runbook 的完整列表，请参阅 [AzureAutomationTeam 配置文件](https://www.powershellgallery.com/profiles/AzureAutomationTeam)。

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - 从 PowerShell 库导入自动化帐户中所有模块的最新版本。
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - 配置 Azure 诊断和日志分析，以接收包含作业状态和作业流的 Azure 自动化日志。
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - 从 Windows Azure 虚拟机复制远程文件。
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - 将本地文件复制到 Azure 虚拟机。

## <a name="contribute-to-the-community"></a>为社区做贡献

我们强烈建议你为 Azure 自动化做贡献，帮助它成长。 与社区分享你构建的出色 runbook。 感谢你的贡献！

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>将 runbook 添加到 GitHub Runbook 库

可以通过此 GitHub 工作流将新的 PowerShell 或 Python runbook 添加到 Runbook 库中。

1. 在 GitHub 上创建一个公共存储库，并添加 runbook 和任何其他必要的文件（如 readme.md、description 等）。
1. 添加主题 `azureautomationrunbookgallery`，以确保服务可发现存储库，以便在自动化 Runbook 库中显示它。
1. 如果创建的 runbook 为 PowerShell 工作流，请添加主题 `PowerShellWorkflow`。 如果为 Python 3 runbook，请添加 `Python3`。 Azure runbook 不需要其他特定主题，但建议你添加其他可用于在 Runbook 库中进行分类和搜索的主题。

   >[!NOTE]
   >查看库中现有的 runbook，了解你可能会使用的格式设置、标头和现有标记等内容（如 `Azure Automation` 或 `Linux Azure Virtual Machines`）。

要对现有 runbook 提出更改建议，请对其提交拉取请求。 

如果决定克隆和编辑现有 runbook，最佳做法是为其指定一个不同的名称。 如果重用旧名称，它将在 Runbook 库列表中显示两次。

>[!NOTE]
>对于更新的和新的 runbook，请在 GitHub 和自动化 Runbook 库之间留出至少 12 小时的同步时间。

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>将 PowerShell runbook 添加到 PowerShell 库

Microsoft 建议将 Runbook 添加到你认为对其他客户有用的 PowerShell 库中。 PowerShell 库接受 PowerShell 模块和 PowerShell 脚本。 可以通过[将 Runbook 上传到 PowerShell 库](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)来添加 Runbook。

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>从 Azure 门户中的模块库导入模块

1. 在 Azure 门户中，打开自动化帐户。
1. 在“共享资源”下，选择“模块库”以打开模块列表 。

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="模块库视图。" lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. 在“浏览库”页，可以按以下字段进行搜索：

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

## <a name="request-a-runbook-or-module"></a>请求 Runbook 或模块

可以将请求发送到[用户之声](https://feedback.azure.com/forums/246290-azure-automation/)。  如果编写 Runbook 时需要帮助，或对 PowerShell 存有疑问，请将问题发布到我们的 [Microsoft 问答页面](/answers/topics/azure-automation.html)。

## <a name="next-steps"></a>后续步骤

* 若要开始使用 PowerShell Runbook，请参阅[教程：创建 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)。
* 若要使用 runbook，请参阅[在 Azure 自动化中管理 runbook](manage-runbooks.md)。
* 有关 PowerShell 脚本的详细信息，请参阅 [Powershell Docs](/powershell/scripting/overview)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation)。
