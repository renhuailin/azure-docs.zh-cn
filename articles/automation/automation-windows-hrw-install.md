---
title: 在自动化中部署基于代理的 Windows 混合 Runbook 辅助角色
description: 本文介绍如何部署基于代理的混合 Runbook 辅助角色，你可使用该角色在本地数据中心或云环境的基于 Windows 的计算机上运行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 09/27/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 59d3b78ac09c253270279612598b2d42ac6a2204
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272060"
---
# <a name="deploy-an-agent-based-windows-hybrid-runbook-worker-in-automation"></a>在自动化中部署基于代理的 Windows 混合 Runbook 辅助角色

通过 Azure 自动化的用户混合 Runbook 辅助角色功能，可以直接在 Azure 或非 Azure 计算机上运行 runbook，包括在[已启用 Azure Arc 的服务器](../azure-arc/servers/overview.md)上注册的服务器。 在托管角色的计算机或服务器中，可以直接运行 runbook，并对环境中的资源运行 runbook，从而管理这些本地资源。

Azure 自动化将存储并管理 Runbook，然后将其传送到一台或多台选定的计算机。 本文介绍如何在 Windows 计算机上部署用户混合 Runbook 辅助角色、如何删除辅助角色，以及如何删除混合 Runbook 辅助角色组。 对于用户混合 Runbook 辅助角色，另请参阅[在自动化中部署基于扩展的 Windows 或 Linux 用户混合 Runbook 辅助角色](./extension-based-hybrid-runbook-worker-install.md)

成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保你具备以下内容。

### <a name="a-log-analytics-workspace"></a>Log Analytics 工作区

混合 Runbook 辅助角色依赖于 Azure Monitor Log Analytics 工作区来安装和配置角色。 你可通过 [Azure 资源管理器](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace)、[PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json) 或在 [Azure 门户](../azure-monitor/logs/quick-create-workspace.md)中创建该工作区。

如果没有 Azure Monitor Log Analytics 工作区，请在创建工作区前查看 [Azure Monitor 日志设计指南](../azure-monitor/logs/design-logs-deployment.md)。

### <a name="log-analytics-agent"></a>Log Analytics 代理

混合 Runbook 辅助角色要求在受支持的 Windows 操作系统上使用 [Log Analytics 代理](../azure-monitor/agents/log-analytics-agent.md)。 对于在 Azure 外部托管的服务器或计算机，你可以使用[已启用 Azure Arc 的服务器](../azure-arc/servers/overview.md)安装 Log Analytics 代理。

### <a name="supported-windows-operating-system"></a>支持的 Windows 操作系统

混合 Runbook 辅助角色功能支持以下操作系统：

* Windows Server 2019（包括 Server Core）
* Windows Server 2016、版本 1709 和 1803（不包括 Server Core）
* Windows Server 2012、2012 R2
* Windows Server 2008 SP2 (x64)、2008 R2
* Windows 10 Enterprise（包括多会话）和 Pro
* Windows 8 企业版和专业版
* Windows 7 SP1

### <a name="minimum-requirements"></a>最低要求

Windows 系统和用户混合 Runbook 辅助角色的最低要求如下：

* Windows PowerShell 5.1（[下载 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)）。 不支持 PowerShell Core。
* .NET Framework 4.6.2 或更高版本
* 双核
* 4 GB RAM
* 端口 443（出站）

### <a name="network-configuration"></a>网络配置

有关混合 Runbook 辅助角色的网络要求，请参阅[配置网络](automation-hybrid-runbook-worker.md#network-planning)。

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>将计算机添加到混合 Runbook 辅助角色组

可将辅助角色计算机添加到其中一个自动化帐户中的混合 Runbook 辅助角色组。 对于托管系统混合 Runbook 辅助角色（由更新管理进行管理）的计算机，可以将其添加到混合 Runbook 辅助角色组。 但必须对更新管理和混合 Runbook 辅助角色组成员身份使用同一自动化帐户。

>[!NOTE]
>Azure 自动化[更新管理](./update-management/overview.md)会自动在启用了更新管理的 Azure 或非 Azure 计算机上安装系统混合 Runbook 辅助角色。 但是，此辅助角色未注册到自动化帐户中的任何混合 Runbook 辅助角色组。 若要在这些计算机上运行 runbook，需将其添加到混合 Runbook 辅助角色组。 按照[手动部署](#manual-deployment)部分下的步骤 6 将其添加到组中。

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>允许通过 Azure 自动化 State Configuration 进行管理

若要了解如何让计算机能够通过 Azure Automation State Configuration 进行管理，请参阅[使计算机通过 Azure Automation State Configuration 进行管理](automation-dsc-onboarding.md)。

> [!NOTE]
> 对于支持将混合 Runbook 辅助角色用于 Desired State Configuration (DSC) 的计算机，必须将计算机添加为 DSC 节点才能管理计算机的配置。

## <a name="installation-options"></a>安装选项

若要安装和配置 Windows 用户混合 Runbook 辅助角色，可使用下述方法中的一种。

* 请使用提供的 PowerShell 脚本将一台或多台 Windows 计算机的配置过程完全[自动化](#automated-deployment)。 对于数据中心或其他云环境中的计算机，建议使用此方法。
* 手动导入自动化解决方案，安装适用于 Windows 的 Log Analytics 代理，并在计算机上配置辅助角色。

## <a name="automated-deployment"></a>自动化部署

有两种方法可自动部署混合 Runbook 辅助角色。 可从 Azure 门户中的 Runbook 库导入 runbook 并运行它，也可从 PowerShell 库手动下载脚本。

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>从 Runbook 库导入 Runbook

若要详细了解导入过程，请查看[使用 Azure 门户从 GitHub 导入 runbook](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal)。 待导入的 runbook 的名称为“创建自动化 Windows HybridWorker”。

runbook 使用以下参数。

| 参数 | 状态 | 说明 |
| ------- | ----- | ----------- |
| `Location` | 必需 | 在其中执行脚本的自动化帐户的位置。 |
| `ResourceGroupName` | 必需 | 自动化帐户的资源组。 |
| `AccountName` | 必需 | 要在其中注册混合 Runbook 辅助角色的自动化帐户的名称。 |
| `CreateLA` | 必需 | 如果为 true，则使用 `WorkspaceName` 的值创建 Log Analytics 工作区。 如果为 false，则 `WorkspaceName` 的值必须引用现有工作区。 |
| `LAlocation` | 可选 | 将在其中创建 Log Analytics 工作区的位置或现有 Log Analytics 工作区所在的位置。 |
| `WorkspaceName` | 可选 | 要使用的 Log Analytics 工作区的名称。 |
| `CreateVM` | 必需 | 如果为 true，则使用 `VMName` 的值作为新 VM 的名称。 如果为 false，则使用 `VMName` 查找并注册现有 VM。 |
| `VMName` | 可选 | 创建或注册的虚拟机的名称取决于 `CreateVM` 的值。 |
| `VMImage` | 可选 | 待创建的 VM 映像的名称。 |
| `VMlocation` | 可选 | 创建或注册的 VM 位置。 如果未指定此位置，则使用 `LAlocation` 值。 |
| `RegisterHW` | 必需 | 如果为 true，则将 VM 注册为混合辅助角色。 |
| `WorkerGroupName` | 必需 | 混合辅助角色组的名称。 |

### <a name="download-a-script-from-the-powershell-gallery"></a>从 PowerShell 库下载脚本

自动部署方法使用 PowerShell 脚本 New-OnPremiseHybridWorker.ps1 来自动化和配置 Windows 混合 Runbook 辅助角色。 它将执行以下任务：

* 安装所需的模块
* 使用 Azure 帐户登录
* 验证是否存在指定的资源组和自动化帐户
* 创建对自动化帐户属性的引用
* 如果未指定，请创建 Azure Monitor Log Analytics 工作区
* 在工作区中启用 Azure 自动化解决方案
* 下载并安装适用于 Windows 的 Log Analytics 代理
* 将计算机注册为混合 Runbook 辅助角色

执行以下步骤，使用脚本在 Windows 计算机上安装角色。

1. 从 [PowerShell 库](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)下载 New-OnPremiseHybridWorker.ps1 脚本。 下载该脚本后，在目标计算机上复制或运行它。 此脚本使用以下参数。

    | 参数 | 状态 | 说明 |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | 必需 | 与自动化帐户关联的资源组的名称。 |
    | `AutomationAccountName` | 必需 | 自动化帐户的名称。
    | `Credential` | 可选 | 登录到 Azure 环境时要使用的凭据。 |
    | `HybridGroupName` | 必需 | 混合 Runbook 辅助角色组的名称，可将其指定为支持此方案的 runbook 的目标。 |
    | `OMSResourceGroupName` | 可选 | Log Analytics 工作区的资源组的名称。 如果未指定此资源组，则使用 `AAResourceGroupName` 值。 |
    | `SubscriptionID` | 必需 | 与自动化帐户关联的 Azure 订阅的标识符。 |
    | `TenantID` | 可选 | 与自动化帐户关联的租户组织的标识符。 |
    | `WorkspaceName` | 可选 | Log Analytics 工作区名称。 如果没有 Log Analytics 工作区，该脚本会创建并配置一个。 |

1. 打开权限提升的 64 位 PowerShell 命令提示符。

1. 从 PowerShell 命令提示符，浏览到包含已下载脚本的文件夹。 更改参数 `AutomationAccountName`、`AAResourceGroupName`、`OMSResourceGroupName`、`HybridGroupName`、`SubscriptionID` 和 `WorkspaceName` 的值。 然后运行脚本。

    运行脚本后，系统会提示在 Azure 上进行身份验证。 必须以具有“订阅管理员”角色成员身份和订阅共同管理员身份的帐户登录。

    ```powershell-interactive
    $NewOnPremiseHybridWorkerParameters = @{
      AutomationAccountName = <nameOfAutomationAccount>
      AAResourceGroupName   = <nameOfResourceGroup>
      OMSResourceGroupName  = <nameOfResourceGroup>
      HybridGroupName       = <nameOfHRWGroup>
      SubscriptionID        = <subscriptionId>
      WorkspaceName         = <nameOfLogAnalyticsWorkspace>
    }
    .\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
    ```

1. 系统会提示用户同意安装 NuGet 并使用 Azure 凭据进行身份验证。 如果没有最新的 NuGet 版本，可从[可用的 NuGet 发行版](https://www.nuget.org/downloads)下载。

1. 完成脚本后，验证部署。 从自动化帐户中的“混合 Runbook 辅助角色组”页面，在“用户混合 runbook 辅助角色组”选项卡下，会显示新组和成员数。  如果这是现有的组，则成员数会递增。 可从该页上的列表中选择此组，从左侧菜单中选择“混合辅助角色”。 在“混合辅助角色”页上，可以查看列出组的每个成员。

## <a name="manual-deployment"></a>手动部署

若要安装和配置 Windows 混合 Runbook 辅助角色，请执行以下步骤。

1. 通过在提升的 PowerShell 命令提示符中或在 [Azure 门户](https://portal.azure.com)的 Cloud Shell 中运行以下命令，在 Log Analytics 工作区中启用 Azure 自动化解决方案。

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

1. 将 Log Analytics 代理部署到目标计算机。

    * 对于 Azure VM，使用[适用于 Windows 的虚拟机扩展](../virtual-machines/extensions/oms-windows.md)安装适用于 Windows 的 Log Analytics 代理。 该扩展在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有的 Log Analytics 工作区中。 可以使用一个 Azure 资源管理器模板、PowerShell 或 Azure Policy 分配[为 Linux 或 Windows VM 内置策略定义部署 Log Analytics 代理 ](../governance/policy/samples/built-in-policies.md#monitoring)。 安装代理后，可将计算机添加到自动化帐户中的混合 Runbook 辅助角色组。
    
    * 对于非 Azure 计算机，可以使用[已启用 Azure Arc 的服务器](../azure-arc/servers/overview.md)安装 Log Analytics 代理。 已启用 Arc 的服务器支持使用以下方法部署 Log Analytics 代理：
    
        - 使用 VM 扩展框架。
        
            利用已启用 Azure Arc 的服务器中的这项功能，可以将 Log Analytics 代理 VM 扩展部署到非 Azure Windows 和/或 Linux 服务器。 在由已启用 Arc 的服务器管理的混合计算机或服务器上，可以使用以下方法来管理 VM 扩展：
        
            - [Azure 门户](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [资源管理器模板](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - 使用 Azure Policy。
        
            在使用此方法时，请使用 Azure Policy [将 Log Analytics 代理部署到 Linux 或 Windows Azure Arc 计算机](../governance/policy/samples/built-in-policies.md#monitoring)内置策略定义来审核已启用 Arc 的服务器是否已安装 Log Analytics 代理。 如果该代理未安装，则使用修正任务来自动部署该代理。 如果你计划通过用于 VM 的 Azure Monitor 来监视计算机，请改为使用[启用用于 VM 的 Azure Monitor](../governance/policy/samples/built-in-initiatives.md#monitoring) 计划来安装和配置 Log Analytics 代理。

    建议使用 Azure Policy 来安装适用于 Windows 或 Linux 的 Log Analytics 代理。

1. 验证代理是否向工作区报告

    适用于 Windows 的 Log Analytics 代理会将计算机连接到 Azure Monitor Log Analytics 工作区。 在计算机上安装代理并将其连接到工作区时，代理会自动下载混合 Runbook 辅助角色所需的组件。

    几分钟后，如果代理已成功连接到 Log Analytics 工作区，则可以运行以下查询，验证是否正在向工作区发送检测信号数据。

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    在搜索结果中，应会看到计算机的检测信号记录，它们指示计算机已连接到服务并将该结果报告给服务。 默认情况下，每个代理都会将一个检测信号记录转发到其分配的工作区。 按照以下步骤可完成代理安装和设置。

1. 在托管 Log Analytics 代理的计算机上确认混合 Runbook 辅助角色的版本，浏览到 `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` 并注意 version 子文件夹。 在工作区中启用解决方案几分钟后，此文件夹将出现在计算机上。

1. 安装 Runbook 环境并连接到 Azure 自动化。 将代理配置为向 Log Analytics 工作区报告，并导入“自动化”解决方案时，该解决方案会向下推送 `HybridRegistration` PowerShell 模块。 此模块包含 `Add-HybridRunbookWorker` cmdlet。 使用此 cmdlet 将 Runbook 环境安装到计算机上，并将其注册到 Azure 自动化。

    在管理员模式下打开 PowerShell 会话，并运行以下命令以导入模块。

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

1. 运行 `Add-HybridRunbookWorker` cmdlet，指定参数 `Url`、`Key` 和 `GroupName` 的值。

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    你可从自动化帐户中的“密钥”页面获取参数 `Url` 和 `Key` 所需的信息。 从页面左侧的“帐户设置”部分下，选择“密钥” 。

    ![“管理密钥”页](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * 对于 `Url` 参数，复制 URL 的值。

    * 对于 `Key` 参数，复制主访问密钥的值。

    * 对于 `GroupName` 参数，请使用混合 Runbook 辅助角色组的名称。 如果自动化帐户中已存在该组，则会将当前计算机添加到其中。 如果该组不存在，则将添加该组。

    * 如果需要，请设置 `Verbose` 参数以接收有关安装的详细信息。

1. 完成命令后，验证部署。 在自动化帐户的“混合 Runbook 辅助角色组”页的“使用混合 Runbook 辅助角色组”选项卡下，会显示新组或现有组以及成员数量 。 如果这是现有的组，则成员数会递增。 可从该页上的列表中选择此组，从左侧菜单中选择“混合辅助角色”。 在“混合辅助角色”页上，可以查看列出组的每个成员。

## <a name="install-powershell-modules"></a>安装 PowerShell 模块

Runbook 可以使用在 Azure 自动化环境中安装的模块中定义的任何活动和 cmdlet。 这些模块不会自动部署到本地计算机，必须手动安装。 例外情况是 Azure 模块。 此模块是默认安装的，并可用于访问所有 Azure 服务的 cmdlet 以及 Azure 自动化的活动。

由于混合 Runbook 辅助角色的主要用途是管理本地资源，很可能需要安装支持这些资源的模块，尤其是 `PowerShellGet` 模块。 有关安装 Windows PowerShell 模块的信息，请参阅 [Windows PowerShell](/powershell/scripting/developer/windows-powershell)。

安装的模块必须位于 `PSModulePath` 环境变量所引用的位置，以便混合辅助角色自动将其导入。 有关详细信息，请参阅[在 PSModulePath 中安装模块](/powershell/scripting/developer/module/installing-a-powershell-module)。

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>删除混合 Runbook 辅助角色

1. 在 Azure 门户中，转到自动化帐户。

1. 在“帐户设置”下，选择“密钥”并记下“URL”和“主访问密钥”的值   。

1. 在管理员模式下打开 PowerShell 会话，并使用 URL 和主访问密钥值运行以下命令。 可使用 `Verbose` 参数获取删除过程的详细日志。 若要从混合辅助角色组中删除过时的计算机，请使用可选的 `machineName` 参数。

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>删除混合辅助角色组

若要删除混合 Runbook 辅助角色组，首先需要从每台计算机上删除该组中包含的混合 Runbook 辅助角色。 然后，使用以下步骤删除该组：

1. 在 Azure 门户中打开自动化帐户。

1. 在“流程自动化”下选择“混合辅助角色组”。 选择要删除的组。 将显示该组的属性页。

   ![“属性”页](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. 在所选组的属性页中，选择“删除”。 系统会显示一条消息，要求确认此操作。 如果确定要继续，请选择“是”。

   ![确认消息](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   完成此过程可能需要数秒钟的时间。 可以在菜单中的“通知”下面跟踪操作进度。

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[排查混合 Runbook 辅助角色问题](troubleshoot/hybrid-runbook-worker.md#general)。
