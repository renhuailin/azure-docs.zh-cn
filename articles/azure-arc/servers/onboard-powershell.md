---
title: 使用 PowerShell 将混合计算机连接到 Azure
description: 本文介绍如何安装代理，并使用已启用 Azure Arc 的服务器将计算机连接到 Azure。 可以使用 PowerShell 执行此操作。
ms.date: 07/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e1ca9528af5b529dd844e566905b6aa7f92429d2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324568"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>使用 PowerShell 将混合计算机连接到 Azure

对于已启用 Azure Arc 的服务器，可以采取手动步骤为环境中的一个或多个 Windows 或 Linux 计算机启用这些服务器。 或者，可使用 PowerShell cmdlet [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) 下载 Connected Machine Agent，安装该代理，并将计算机注册到 Azure Arc。该 cmdlet 将从 Microsoft 下载中心下载 Windows 代理包 (Windows Installer)，并从 Microsoft 包存储库下载 Linux 代理包。

这种安装和配置代理的方法要求你在计算机上拥有管理员权限。 在 Linux 上，需使用 root 帐户；在 Windows 上，你需是“本地管理员组”的成员。 可以使用 [PowerShell 远程处理](/powershell/scripting/learn/ps101/08-powershell-remoting)在 Windows 服务器上以交互方式或远程方式完成此过程。

在开始之前，请查看[先决条件](agent-overview.md#prerequisites)，并验证你的订阅和资源是否符合要求。 有关支持的区域和其他相关注意事项的信息，请参阅 [支持的 Azure 区域](overview.md#supported-regions)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- 一台装有 Azure PowerShell 的计算机。 有关说明，请参阅[安装和配置 Azure PowerShell](/powershell/azure/)。

你将使用 PowerShell 来管理由已启用 Azure Arc 的服务器所管理的混合服务器上的 VM 扩展。 在使用 PowerShell 之前，请安装 `Az.ConnectedMachine` 模块。 在已启用 Azure Arc 的服务器上运行以下命令：

```powershell
Install-Module -Name Az.ConnectedMachine
```

安装完成后，你将看到以下消息：

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>安装代理并连接到 Azure

1. 使用提升的权限打开 PowerShell 控制台。

2. 运行命令 `Connect-AzAccount` 登录到 Azure。

3. 若要安装 Connected Machine Agent，请将 `Connect-AzConnectedMachine` 与 `-Name`、`-ResourceGroupName` 和 `-Location` 参数配合使用。 使用 `-SubscriptionId` 参数可替代默认订阅（登录后创建的 Azure 上下文的结果）。 运行下列命令之一：

    * 若要在可以直接与 Azure 通信的目标计算机上安装 Connected Machine Agent，请运行：

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * 若要在通过代理服务器通信的目标计算机上安装 Connected Machine Agent，请运行：
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

      使用此配置，代理使用 HTTP 协议通过代理服务器进行通信。

如果完成安装后代理无法启动，请检查日志以获取详细的错误信息。 在 Windows 上检查以下文件： *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*。 在 Linux 上检查以下文件： */var/opt/azcmagent/log/himds.log*。

## <a name="install-and-connect-by-using-powershell-remoting"></a>使用 PowerShell 远程处理进行安装和连接

下面介绍如何为一个或多个 Windows 服务器配置已启用 Azure Arc 的服务器。必须在远程计算机上启用 PowerShell 远程处理。 请使用 `Enable-PSRemoting` cmdlet 完成此操作。

1. 以管理员身份打开 PowerShell 控制台。

2. 运行命令 `Connect-AzAccount` 登录到 Azure。

3. 若要安装 Connected Machine Agent，请将 `Connect-AzConnectedMachine` 与 `-ResourceGroupName` 和 `-Location` 参数配合使用。 Azure 资源名称将自动使用每个服务器的主机名。 使用 `-SubscriptionId` 参数可替代默认订阅（登录后创建的 Azure 上下文的结果）。

    * 若要在可以直接与 Azure 通信的目标计算机上安装 Connected Machine Agent，请运行以下命令：
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * 若要同时在多个远程计算机上安装 Connected Machine Agent，请添加远程计算机名列表，并以逗号分隔每个计算机名。

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    以下示例显示了针对单个计算机运行该命令的结果：
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>验证是否与 Azure Arc 连接

安装并配置要注册到已启用 Azure Arc 的服务器的代理后，转到 Azure 门户以验证是否已成功连接服务器。 在 [Azure 门户](https://portal.azure.com)中查看你的计算机。

![“服务器”仪表板的屏幕截图，其中显示服务器连接成功。](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>后续步骤

* 如有必要，请参阅[排查 Connected Machine Agent 问题的指南](troubleshoot-agent-onboard.md)。

* 查看[规划和部署指南](plan-at-scale-deployment.md)，以便对按任意规模部署启用了 Azure Arc 的服务器进行规划，并实现集中管理和监视。

* 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机。 可以使用 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区报告，并使用 [VM 见解](../../azure-monitor/vm/vminsights-enable-policy.md)来启用监视。
