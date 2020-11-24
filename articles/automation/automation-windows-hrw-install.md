---
title: 在 Azure 自动化中部署 Windows 混合 Runbook 辅助角色
description: 本文介绍如何部署混合 Runbook 辅助角色，你可使用该角色在本地数据中心或云环境的基于 Windows 的计算机上运行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 11/23/2020
ms.topic: conceptual
ms.openlocfilehash: cb501b954897beb73ae05bfdc7b5ded2221dc114
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95493921"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>部署 Windows 混合 Runbook 辅助角色

可以使用 Azure 自动化的用户混合 Runbook 辅助角色功能直接在 Azure 或非 Azure 计算机上运行 runbook，包括使用已 [启用 Azure Arc 的服务器](../azure-arc/servers/overview.md)注册的服务器。 在托管角色的计算机或服务器上，你可以直接对环境中的资源运行 runbook，从而管理这些本地资源。

Azure 自动化将存储并管理 Runbook，然后将其传送到一台或多台指定的计算机。 本文介绍如何在 Windows 计算机上部署用户混合 Runbook 辅助角色，如何删除辅助角色，以及如何删除混合 Runbook 辅助角色组。

成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保你具备以下内容。

### <a name="a-log-analytics-workspace"></a>Log Analytics 工作区

混合 Runbook 辅助角色依赖于 Azure Monitor Log Analytics 工作区来安装和配置角色。 你可通过 [Azure 资源管理器](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)、[PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json) 或在 [Azure 门户](../azure-monitor/learn/quick-create-workspace.md)中创建该工作区。

如果没有 Azure Monitor Log Analytics 工作区，请在创建工作区前查看 [Azure Monitor 日志设计指南](../azure-monitor/platform/design-logs-deployment.md)。

### <a name="log-analytics-agent"></a>Log Analytics 代理

混合 Runbook 辅助角色要求在受支持的 Windows 操作系统上使用 [Log Analytics 代理](../azure-monitor/platform/log-analytics-agent.md)。 对于托管在 Azure 外部的服务器或计算机，你可以使用 [启用了 Azure Arc 的服务器](../azure-arc/servers/overview.md)安装 Log Analytics 代理。

### <a name="supported-windows-operating-system"></a>支持的 Windows 操作系统

混合 Runbook 辅助角色功能支持以下操作系统：

* Windows Server 2019
* Windows Server 2016、版本 1709 和 1803
* Windows Server 2012、2012 R2
* Windows Server 2008 SP2 (x64)、2008 R2
* Windows 10 Enterprise（包括多会话）和 Pro
* Windows 8 企业版和专业版
* Windows 7 SP1

### <a name="minimum-requirements"></a>最低要求

Windows 系统和用户混合 Runbook 辅助角色的最低要求如下：

* Windows PowerShell 5.1 或更高版本（[下载 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)）
* .NET Framework 4.6.2 或更高版本
* 双核
* 4 GB RAM
* 端口 443（出站）

### <a name="network-configuration"></a>网络配置

有关混合 Runbook 辅助角色的网络要求，请参阅 [配置网络](automation-hybrid-runbook-worker.md#network-planning)。

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>将计算机添加到混合 Runbook 辅助角色组

可以将工作线程添加到某个自动化帐户中的混合 Runbook 辅助角色组。 对于托管由更新管理管理的系统混合 Runbook 辅助角色的计算机，可以将其添加到混合 Runbook 辅助角色组。 但必须同时对更新管理和混合 Runbook 辅助角色组成员身份使用同一自动化帐户。

>[!NOTE]
>Azure 自动化 [更新管理](update-management/update-mgmt-overview.md) 会自动在为更新管理启用的 azure 或非 azure 计算机上安装系统混合 Runbook 辅助角色。 但是，此辅助角色未注册到自动化帐户中的任何混合 Runbook 辅助角色组。 若要在这些计算机上运行 runbook，需将其添加到混合 Runbook 辅助角色组。 按照 [手动部署](#manual-deployment) 部分下面的步骤6将其添加到组中。

## <a name="enable-for-management-with-azure-automation-state-configuration"></a>启用以使用 Azure 自动化状态配置进行管理

若要了解如何让计算机能够通过 Azure Automation State Configuration 进行管理，请参阅[使计算机通过 Azure Automation State Configuration 进行管理](automation-dsc-onboarding.md)。

> [!NOTE]
> 对于支持将混合 Runbook 辅助角色用于 Desired State Configuration (DSC) 的计算机，必须将计算机添加为 DSC 节点才能管理计算机的配置。

## <a name="installation-options"></a>安装选项

若要安装和配置 Windows 用户混合 Runbook 辅助角色，可以使用以下方法之一。

* 请使用提供的 PowerShell 脚本将一台或多台 Windows 计算机的配置过程完全[自动化](#automated-deployment)。 对于数据中心或其他云环境中的计算机，建议使用此方法。
* 手动导入自动化解决方案，安装适用于 Windows 的 Log Analytics 代理，并在计算机上配置辅助角色。

## <a name="automated-deployment"></a>自动化部署

自动部署方法使用 PowerShell 脚本 **New-OnPremiseHybridWorker.ps1** 来自动执行和配置 Windows 混合 Runbook 辅助角色。 它执行以下操作：

* 安装所需的模块
* 使用 Azure 帐户登录
* 验证是否存在指定的资源组和自动化帐户
* 创建对自动化帐户属性的引用
* 如果未指定，请创建 Azure Monitor Log Analytics 工作区
* 在工作区中启用 Azure 自动化解决方案
* 下载并安装适用于 Windows 的 Log Analytics 代理
* 将计算机注册为混合 Runbook 辅助角色

执行以下步骤，在 Windows 计算机上使用脚本安装角色。

1. 从 [PowerShell 库](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)下载 New-OnPremiseHybridWorker.ps1 脚本。 下载该脚本后，在目标计算机上复制或运行它。 **New-OnPremiseHybridWorker.ps1** 脚本在执行期间使用以下参数。

    | 参数 | 状态 | 说明 |
    | --------- | ------ | ----------- |
    | `AAResourceGroupName` | 必需 | 与自动化帐户关联的资源组的名称。 |
    | `AutomationAccountName` | 必需 | 自动化帐户的名称。
    | `Credential` | 可选 | 登录到 Azure 环境时要使用的凭据。 |
    | `HybridGroupName` | 必需 | 混合 Runbook 辅助角色组的名称，可将其指定为支持此方案的 runbook 的目标。 |
    | `OMSResourceGroupName` | 可选 | Log Analytics 工作区的资源组的名称。 如果未指定此资源组，则使用 `AAResourceGroupName` 的值。 |
    | `SubscriptionID` | 必需 | 与自动化帐户关联的 Azure 订阅的标识符。 |
    | `TenantID` | 可选 | 与自动化帐户关联的租户组织的标识符。 |
    | `WorkspaceName` | 可选 | Log Analytics 工作区名称。 如果没有 Log Analytics 工作区，该脚本会创建并配置一个。 |

2. 打开权限提升的 64 位 PowerShell 命令提示符。

3. 在 PowerShell 命令提示符下，浏览到包含你下载的脚本的文件夹。 更改参数 `AutomationAccountName`、`AAResourceGroupName`、`OMSResourceGroupName`、`HybridGroupName`、`SubscriptionID` 和 `WorkspaceName` 的值。 然后运行脚本。

    运行脚本后，系统会提示在 Azure 上进行身份验证。 必须以订阅 **管理员** 角色成员和订阅共同管理员的帐户身份登录。

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

4. 系统会提示用户同意安装 NuGet 并使用 Azure 凭据进行身份验证。 如果没有最新的 NuGet 版本，可从[可用的 NuGet 发行版](https://www.nuget.org/downloads)下载。

5. 完成脚本后，验证部署。 在自动化帐户的 " **混合 Runbook 辅助角色组** " 页中，在 " **用户混合 runbook 辅助角色组** " 选项卡下，会显示新组和成员数。 如果这是现有的组，则成员数会递增。 你可以从页面上的列表中选择组，从左侧菜单中选择 " **混合辅助角色** "。 在 " **混合辅助角色** " 页上，可以看到列出的组的每个成员。

## <a name="manual-deployment"></a>手动部署

若要安装和配置 Windows 混合 Runbook 辅助角色，请执行以下步骤。

1. 通过在提升的 PowerShell 命令提示符下运行以下命令，或者在 [Azure 门户](https://portal.azure.com)的 Cloud Shell 中运行以下命令，在 Log Analytics 工作区中启用 Azure 自动化解决方案。

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. 将 Log Analytics 代理部署到目标计算机。

    * 对于 Azure VM，使用[适用于 Windows 的虚拟机扩展](../virtual-machines/extensions/oms-windows.md)安装适用于 Windows 的 Log Analytics 代理。 该扩展在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有的 Log Analytics 工作区中。 可以使用 Azure 资源管理器模板、PowerShell 或 Azure 策略为 [ *Linux* 或 *Windows* vm 内置策略分配部署 Log Analytics 代理](../governance/policy/samples/built-in-policies.md#monitoring) 。 安装代理后，可以将计算机添加到自动化帐户的混合 Runbook 辅助角色组。
    
    * 对于非 Azure 计算机，你可以使用 [启用了 Azure Arc 的服务器](../azure-arc/servers/overview.md)安装 Log Analytics 代理。 启用 Arc 的服务器支持使用以下方法部署 Log Analytics 代理：
    
        - 使用 VM 扩展框架。
        
            使用启用了 Azure Arc 的服务器中的此功能，可以将 Log Analytics 代理 VM 扩展部署到非 Azure Windows 和/或 Linux 服务器。 可以在混合计算机上或通过启用了 Arc 的服务器管理的服务器上使用以下方法来管理 VM 扩展：
        
            - [Azure 门户](../azure-arc/servers/manage-vm-extensions-portal.md)
            - [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [资源管理器模板](../azure-arc/servers/manage-vm-extensions-template.md)
        
        - 使用 Azure 策略。
        
            使用此方法时，请使用 Azure Policy [Deploy Log Analytics 代理到 Linux 或 Windows Azure Arc 计算机](../governance/policy/samples/built-in-policies.md#monitoring) 的内置策略来审核启用了 Arc 的服务器是否安装了 Log Analytics 代理。 如果未安装代理，则它将使用修正任务自动部署。 或者，如果你计划使用用于 VM 的 Azure Monitor 来监视计算机，则改为使用 [启用用于 VM 的 Azure Monitor](../governance/policy/samples/built-in-initiatives.md#monitoring) 计划安装和配置 Log Analytics 代理。

    建议使用 Azure 策略安装适用于 Windows 或 Linux 的 Log Analytics 代理。

3. 验证代理是否正在向工作区报告

    适用于 Windows 的 Log Analytics 代理会将计算机连接到 Azure Monitor Log Analytics 工作区。 在计算机上安装代理并将其连接到工作区时，代理会自动下载混合 Runbook 辅助角色所需的组件。

    几分钟后，如果代理已成功连接到 Log Analytics 工作区，则可以运行以下查询，验证是否正在向工作区发送检测信号数据。

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    在搜索结果中，应会看到计算机的检测信号记录，它们指示计算机已连接到服务并将该结果报告给服务。 默认情况下，每个代理都会将一个检测信号记录转发到其分配的工作区。 按照以下步骤可完成代理安装和设置。

4. 确认承载 Log Analytics 代理的计算机上的混合 Runbook 辅助角色版本，浏览到 `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` 并记下 **版本** 子文件夹。 此文件夹将在工作区中启用解决方案几分钟后出现在计算机上。

5. 安装 runbook 环境并连接到 Azure 自动化。 将代理配置为向 Log Analytics 工作区报告并导入 **自动化** 解决方案时，该解决方案会向下推送 `HybridRegistration` PowerShell 模块。 此模块包含 `Add-HybridRunbookWorker` cmdlet。 使用此 cmdlet 将 Runbook 环境安装到计算机上，并将其注册到 Azure 自动化。

    在管理员模式下打开 PowerShell 会话，并运行以下命令以导入模块。

    ```powershell-interactive
    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module .\HybridRegistration.psd1
    ```

6. 运行 `Add-HybridRunbookWorker` 指定参数、和的值的 cmdlet `Url` `Key` `GroupName` 。

    ```powershell-interactive
    Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
    ```

    你可从自动化帐户中的“密钥”页面获取参数 `Url` 和 `Key` 所需的信息。 从页面左侧的“帐户设置”部分下，选择“密钥” 。

    ![“管理密钥”页](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * 对于 `Url` 参数，复制 URL 的值。

    * 对于 `Key` 参数，复制主访问密钥的值。

    * 对于 `GroupName` 参数，请使用混合 Runbook 辅助角色组的名称。 如果自动化帐户中已存在该组，则会将当前计算机添加到其中。 如果该组不存在，则将添加该组。

    * 如果需要，请设置 `Verbose` 参数以接收有关安装的详细信息。

7. 完成命令后，验证部署。 在自动化帐户的 " **混合 Runbook 辅助角色组** " 页中，在 " **用户混合 runbook 辅助角色组** " 选项卡下，会显示新的或现有的组以及成员数。 如果这是现有的组，则成员数会递增。 你可以从页面上的列表中选择组，从左侧菜单中选择 " **混合辅助角色**"。 在 " **混合辅助角色** " 页上，可以看到列出的组的每个成员。

## <a name="install-powershell-modules"></a>安装 PowerShell 模块

Runbook 可以使用在 Azure 自动化环境中安装的模块中定义的任何活动和 cmdlet。 这些模块不会自动部署到本地计算机，必须手动安装。 例外情况是 Azure 模块。 此模块是默认安装的，并可用于访问所有 Azure 服务的 cmdlet 以及 Azure 自动化的活动。

由于混合 Runbook 辅助角色的主要用途是管理本地资源，很可能需要安装支持这些资源的模块，尤其是 `PowerShellGet` 模块。 有关安装 Windows PowerShell 模块的信息，请参阅 [Windows PowerShell](/powershell/scripting/developer/windows-powershell)。

安装的模块必须位于 `PSModulePath` 环境变量所引用的位置，以便混合辅助角色自动将其导入。 有关详细信息，请参阅[在 PSModulePath 中安装模块](/powershell/scripting/developer/module/installing-a-powershell-module)。

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-windows-hybrid-runbook-worker"></a>删除混合 Runbook 辅助角色

1. 在 Azure 门户中，转到自动化帐户。

2. 在“帐户设置”下，选择“密钥”并记下“URL”和“主访问密钥”的值   。

3. 在管理员模式下打开 PowerShell 会话，并使用 URL 和主访问密钥值运行以下命令。 可使用 `Verbose` 参数获取删除过程的详细日志。 若要从混合辅助角色组中删除过时的计算机，请使用可选的 `machineName` 参数。

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>删除混合辅助角色组

若要删除混合 Runbook 辅助角色组，首先需要从每台计算机上删除该组中包含的混合 Runbook 辅助角色。 然后，使用以下步骤删除该组：

1. 在 Azure 门户中打开自动化帐户。

2. 在“流程自动化”下选择“混合辅助角色组”。 选择要删除的组。 将显示该组的属性页。

   ![“属性”页](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. 在所选组的属性页中，选择“删除”。 系统会显示一条消息，要求确认此操作。 如果确定要继续，请选择“是”。

   ![确认消息](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   完成此过程可能需要数秒钟的时间。 可以在菜单中的“通知”下面跟踪操作进度。

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[排查混合 Runbook 辅助角色问题](troubleshoot/hybrid-runbook-worker.md#general)。
