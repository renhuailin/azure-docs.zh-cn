---
title: 使用 PowerShell 应用 Linux Azure 自动化 State Configuration
description: 本文介绍如何使用 PowerShell 通过 Azure 自动化 State Configuration 将 Linux 虚拟机配置为所需状态。
ms.topic: conceptual
services: automation
ms.subservice: dsc
ms.date: 08/31/2021
ms.openlocfilehash: e4352c5c2cca0391e4e795b537af16c28ded6f6e
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123354379"
---
# <a name="configure-linux-desired-state-with-azure-automation-state-configuration-using-powershell"></a>使用 PowerShell 通过 Azure 自动化 State Configuration 配置 Linux 所需状态

在本教程中，你将使用 PowerShell 将 Azure 自动化 State Configuration 应用于 Azure Linux 虚拟机，以检查其是否符合所需状态。 所需状态是确定节点上是否存在 apache2 服务。
使用 Azure 自动化 State Configuration 可以指定计算机配置，并确保这些计算机在一段时间后处于指定状态。 有关 State Configuration 的详细信息，请参阅 [Azure 自动化 State Configuration 概述](./automation-dsc-overview.md)。

在本教程中，你将了解如何：
> [!div class="checklist"]
> - 加入将由 Azure 自动化 DSC 管理的 Azure Linux VM
> - 撰写配置
> - 安装用于自动化的 PowerShell 模块
> - 将配置导入到 Azure 自动化
> - 将配置编译为节点配置
> - 将节点配置分配给托管节点
> - 修改节点配置映射
> - 检查托管节点的符合性状态

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- 一个 Azure 自动化帐户。 若要详细了解自动化帐户，请参阅[自动化帐户身份验证概述](./automation-security-overview.md)。
- 运行 Ubuntu 18.04 LTS 或更高版本的 Azure 资源管理器虚拟机 (VM)。 有关创建 Azure Linux VM 的说明，请参阅[使用 PowerShell 在 Azure 中创建 Linux 虚拟机](../virtual-machines/windows/quick-create-powershell.md)。
- 在要用于编写、编译状态配置以及将状态配置应用于目标 Azure Linux VM 的计算机上安装了 PowerShell [Az 模块](/powershell/azure/new-azureps-module-az)。 确保已安装了最新版本。 如有必要，请运行 `Update-Module -Name Az`。

## <a name="create-a-configuration"></a>创建配置

查看下面的代码并注意存在两个节点[配置](/powershell/scripting/dsc/configurations/configurations)：`IsPresent` 和 `IsNotPresent`。 此配置在每个节点块中调用一个资源：[nxPackage 资源](/powershell/scripting/dsc/reference/resources/linux/lnxpackageresource)。 此资源管理 apache2 包的存在。 然后，在文本编辑器中，将以下代码复制到本地文件，并将其命名为 `LinuxConfig.ps1`

```powershell
Configuration LinuxConfig
{
    Import-DscResource -ModuleName 'nx'

    Node IsPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Present'
            PackageManager    = 'Apt'
        }
    }

    Node IsNotPresent
    {
        nxPackage apache2
        {
            Name              = 'apache2'
            Ensure            = 'Absent'
        }
    }
}
```

## <a name="sign-in-to-azure"></a>登录 Azure

在计算机中，使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) PowerShell cmdlet 登录 Azure 订阅，然后按屏幕说明操作。

```powershell
# Sign in to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

## <a name="initialize-variables"></a>初始化变量

为了提高效率并降低执行 cmdlet 时出错的可能性，请根据需要按如下所述进一步修改 PowerShell 代码，然后执行。

| 变量 | 值 |
|---|---|
|$resourceGroup| 将 `yourResourceGroup` 替换为资源组的实际名称。|
|$automationAccount| 将 `yourAutomationAccount` 替换为自动化帐户的实际名称。|
|$VM| 将 `yourVM` 替换为 Azure Linux VM 的实际名称。|
|$configurationName| 原样保留 `LinuxConfig`。 本教程中使用的配置的名称。|
|$nodeConfigurationName0|原样保留 `LinuxConfig.IsNotPresent`。 本教程中使用的节点配置的名称。|
|$nodeConfigurationName1|原样保留 `LinuxConfig.IsPresent`。 本教程中使用的节点配置的名称。|
|$moduleName|原样保留 `nx`。 本教程中用于 DSC 的 PowerShell 模块的名称。|
|$moduleVersion| 从 [PowerShell 库](https://www.powershellgallery.com/packages/nx)获取 `nx` 的最新版本号。 本教程使用版本 `1.0`。|

```powershell
$resourceGroup = "yourResourceGroup"
$automationAccount = "yourAutomationAccount"
$VM = "yourVM"
$configurationName = "LinuxConfig"
$nodeConfigurationName0 = "LinuxConfig.IsNotPresent"
$nodeConfigurationName1 = "LinuxConfig.IsPresent"
$moduleName = "nx"
$moduleVersion = "1.0"
```

## <a name="install-nx-module"></a>安装 nx 模块

Azure 自动化使用许多 PowerShell 模块在 runbook DSC 配置中启用 runbook 和 DSC 资源中的 cmdlet。 nx 是包含适用于 Linux 的 DSC 资源的模块。 使用 [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) cmdlet 安装 nx 模块。 有关模块的详细信息，请参阅[在 Azure 自动化中管理模块](./shared-resources/modules.md)。 运行以下命令：

```powershell
New-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName `
    -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

输出应如下所示：

   :::image type="content" source="media/dsc-linux-powershell/new-azautomationmodule-output.png" alt-text="New-AzAutomationModule 命令的输出。":::

可以运行以下命令来验证安装：

```powershell
Get-AzAutomationModule `
    -ResourceGroupName $resourceGroup `
    -AutomationAccountName $automationAccount `
    -Name $moduleName 
```

## <a name="import-configuration-to-azure-automation"></a>将配置导入到 Azure 自动化

调用 [Import-AzAutomationDscConfiguration](/powershell/module/az.automation/import-azautomationdscconfiguration) cmdlet，将配置上传到自动化帐户。 使用实际路径修改 `-SourcePath` 的值，然后运行以下命令：

```powershell
Import-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -SourcePath "path\LinuxConfig.ps1" `
   -Published
```

输出应如下所示：

   :::image type="content" source="media/dsc-linux-powershell/import-azautomationdscconfiguration-output.png" alt-text="Import-AzAutomationDscConfiguration 命令的输出。":::

可以运行以下命令来查看自动化帐户的配置：

```powershell
Get-AzAutomationDscConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $configurationName
```

## <a name="compile-configuration-in-azure-automation"></a>编译 Azure 自动化中的配置

在将所需状态应用于某个节点之前，必须将定义该状态的配置编译成一个或多个节点配置。  调用 [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) cmdlet，以编译 Azure 自动化中的 `LinuxConfig` 配置。 有关编译的详细信息，请参阅[编译 DSC 配置](./automation-dsc-compile.md)。 运行以下命令：

```powershell
Start-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

输出应如下所示：

   :::image type="content" source="media/dsc-linux-powershell/start-azautomationdsccompilationjob-output.png" alt-text="Start-AzAutomationDscCompilationJob 命令的输出。":::

可以使用以下命令来查看自动化帐户的编译工作：

```powershell
Get-AzAutomationDscCompilationJob `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -ConfigurationName $configurationName
```

等待编译工作完成，然后再继续。 必须先将配置编译为节点配置，然后才能将它分配给节点。 执行以下代码，每 5 秒检查一次状态：

```powershell
while ((Get-AzAutomationDscCompilationJob `
         -ResourceGroupName $resourceGroup `
         -AutomationAccountName $automationAccount `
         -ConfigurationName $configurationName).Status -ne "Completed") 
{
   Write-Output "Wait"
   Start-Sleep -Seconds 5
}
Write-Output "Compilation complete"
```

编译作业完成后，还可以使用以下命令查看节点配置元数据：

```powershell
Get-AzAutomationDscNodeConfiguration `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount
```

## <a name="register-the-azure-linux-vm-for-an-automation-account"></a>为自动化帐户注册 Azure Linux VM

将 Azure Linux VM 注册为 Azure 自动化帐户的 Desired State Configuration (DSC) 节点。 [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) cmdlet 仅支持运行 Windows OS 的 VM。 首先需要为 DSC 配置 Azure Linux VM。 有关详细步骤，请参阅[适用于 Linux 的 Desired State Configuration (DSC) 入门](/powershell/scripting/dsc/getting-started/lnxgettingstarted)。

1. 通过运行以下代码，使用 PowerShell 构造包含注册命令的 Python 脚本，供以后在 Azure Linux VM 上执行：

   ```powershell
    $primaryKey = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).PrimaryKey
    
    $URL = (Get-AzAutomationRegistrationInfo `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount).Endpoint
    
    Write-Output "sudo /opt/microsoft/dsc/Scripts/Register.py $primaryKey $URL"
   ```

   这些命令获取自动化帐户的主访问密钥和 URL，并将其连接到注册命令。 确保删除输出中的所有回车符。 此命令会在后面的步骤中使用。

1. 连接到你的 Azure Linux VM。 如果使用了密码，可以使用以下语法。 如果使用了公共-专用密钥对，请参阅 [Linux 上的 SSH](./../virtual-machines/linux/mac-create-ssh-keys.md) 以了解详细步骤。 其他命令检索有关可以安装哪些包的信息（包括当前安装的包有哪些可用更新），并安装 Python。

   ```cmd
   ssh user@IP

   sudo apt-get update
   sudo apt-get install -y python
   ```

1. 安装开放式管理基础结构 (OMI)。 有关 OMI 的详细信息，请参阅[开放式管理基础结构](https://github.com/Microsoft/omi)。 验证最新[版本](https://github.com/Microsoft/omi/releases)。 根据需要修改以下发布版本，然后在 ssh 会话中执行命令：

   ```bash
   wget https://github.com/microsoft/omi/releases/download/v1.6.8-0/omi-1.6.8-0.ssl_110.ulinux.x64.deb

   sudo dpkg -i ./omi-1.6.8-0.ssl_110.ulinux.x64.deb
   ```

1. 安装适用于 Linux 的 PowerShell Desired State Configuration。 有关详细信息，请参阅 [Linux 上的 DSC](https://github.com/microsoft/PowerShell-DSC-for-Linux)。 验证最新[版本](https://github.com/microsoft/PowerShell-DSC-for-Linux/releases)。 根据需要修改以下发布版本，然后在 ssh 会话中执行命令：

   ```bash
   wget https://github.com/microsoft/PowerShell-DSC-for-Linux/releases/download/v1.2.1-0/dsc-1.2.1-0.ssl_110.x64.deb

   sudo dpkg -i ./dsc-1.2.1-0.ssl_110.x64.deb
   ```

1. 现在，可以使用步骤 1 中创建的 `sudo /opt/microsoft/dsc/Scripts/Register.py <Primary Access Key> <URL>` Python 脚本注册节点。 在 ssh 会话中运行命令，输出应如下所示：

   ```output
   instance of SendConfigurationApply
   {
        ReturnValue=0
   }
   
   ```

1. 可以使用以下命令在 PowerShell 中验证注册情况：

   ```powershell
     Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM  
   ```

   输出应如下所示：

      :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnode-output.png" alt-text="Get-AzAutomationDscNode 命令的输出。":::

## <a name="assign-a-node-configuration"></a>分配节点配置

调用 [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) cmdlet 以设置节点配置映射。 运行以下命令：

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -Name $VM

# Set node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName0 `
   -NodeId $node.Id `
   -Force
```

输出应如下所示：

   :::image type="content" source="media/dsc-linux-powershell/set-azautomationdscnode-output.png" alt-text="Set-AzAutomationDscNode 命令的输出。":::

## <a name="modify-the-node-configuration-mapping"></a>修改节点配置映射

调用 [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) cmdlet 以修改节点配置映射。 在此处，将当前节点配置映射从 `LinuxConfig.IsNotPresent` 修改为 `LinuxConfig.IsPresent`。 运行以下命令：

```powershell
# Modify node configuration mapping
Set-AzAutomationDscNode `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeConfigurationName $nodeConfigurationName1 `
   -NodeId $node.Id `
   -Force
```

## <a name="check-the-compliance-status-of-a-managed-node"></a>检查托管节点的符合性状态

每当 State Configuration 在托管节点上执行一致性检查时，该节点就会将状态报表发送回拉取服务器。 以下示例使用 [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport) cmdlet 报告托管节点的合规性状态。

```powershell
Get-AzAutomationDscNodeReport `
   -ResourceGroupName $resourceGroup `
   -AutomationAccountName $automationAccount `
   -NodeId $node.Id `
   -Latest
```

输出应如下所示：

   :::image type="content" source="media/dsc-linux-powershell/get-azautomationdscnodereport-output.png" alt-text="Get-AzAutomationDscNodeReport 命令的输出。":::

第一个报表可能无法立即提供，在启用某个节点后可能需要最多 30 分钟才能提供。 有关报表数据的详细信息，请参阅[使用 DSC 报表服务器](/powershell/scripting/dsc/pull-server/reportserver)。

## <a name="clean-up-resources"></a>清理资源

以下步骤可帮助你删除为本教程创建的资源，这些资源将不再需要。

1. 通过自动化帐户从管理中删除 DSC 节点。 尽管不能通过 PowerShell 注册节点，但是可以使用 PowerShell 进行注销。 运行以下命令：

    ```powershell
    # Get the ID of the DSC node
    $NodeID = (Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM).Id
    
    Unregister-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Id $NodeID `
       -Force

    # Verify using the same command from Register the Azure Linux VM for an Automation account. A blank response indicates success.
    Get-AzAutomationDscNode `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $VM
    ```

1. 从自动化的 DSC 节点配置中删除元数据。 运行以下命令：

    ```powershell
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0 `
       -IgnoreNodeMappings `
       -Force
    
    Remove-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1 `
       -IgnoreNodeMappings `
       -Force

    # Verify using the same command from Compile configuration in Azure Automation.
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName0
    
    Get-AzAutomationDscNodeConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $nodeConfigurationName1
    ```

      如下所示的输出表示删除成功：`Get-AzAutomationDscNodeConfiguration : NodeConfiguration LinuxConfig.IsNotPresent not found`。

1. 从自动化中删除 DSC 配置。 运行以下命令：

    ```powershell
    Remove-AzAutomationDscConfiguration `
       -AutomationAccountName $automationAccount `
       -ResourceGroupName $resourceGroup `
       -Name $configurationName `
       -Force

    # Verify using the same command from Import configuration to Azure Automation.
    Get-AzAutomationDscConfiguration `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $configurationName
    ```

   如下所示的输出表示删除成功：`Get-AzAutomationDscConfiguration : Operation returned an invalid status code 'NotFound'`。

1. 从自动化中删除 nx 模块。 运行以下命令：

    ```powershell
    Remove-AzAutomationModule `
       -ResourceGroupName $resourceGroup `
       -AutomationAccountName $automationAccount `
       -Name $moduleName -Force

    # Verify using the same command from Install nx module.
    Get-AzAutomationModule `
        -ResourceGroupName $resourceGroup `
        -AutomationAccountName $automationAccount `
        -Name $moduleName
    ```

   如下所示的输出表示删除成功：`Get-AzAutomationModule : The module was not found. Module name: nx.`。

## <a name="next-steps"></a>后续步骤

在本教程中，你使用了 PowerShell 将 Azure 自动化 State Configuration 应用于 Azure Linux VM，以检查其是否符合所需状态。 有关配置组合的更全面说明，请参阅：

> [!div class="nextstepaction"]
> [编写 DSC 配置](./compose-configurationwithcompositeresources.md)