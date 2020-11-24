---
title: 使用 PowerShell 将混合计算机连接到 Azure
description: 本文介绍如何使用启用了 Azure Arc 的服务器安装代理并将计算机连接到 Azure。 可以使用 PowerShell 执行此操作。
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0218235179e1a8a883360d0061e685c04079cbf4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95492935"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>使用 PowerShell 将混合计算机连接到 Azure

对于使用 Azure Arc 启用的服务器，你可以采取手动步骤为你的环境中的一个或多个 Windows 或 Linux 计算机启用它们。 或者，可以使用 PowerShell cmdlet [AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) 下载连接的计算机代理、安装代理，并使用 Azure Arc 注册计算机。Cmdlet 将从 Microsoft 下载中心下载 Windows 代理包 (Windows Installer) ，并从 Microsoft 包存储库下载 Linux 代理包。

这种安装和配置代理的方法要求你在计算机上拥有管理员权限。 在 Linux 上，需使用 root 帐户；在 Windows 上，你需是“本地管理员组”的成员。 你可以使用 [PowerShell 远程处理](/powershell/scripting/learn/ps101/08-powershell-remoting)在 Windows server 上以交互方式或远程方式完成此过程。

在开始之前，请查看 [先决条件](agent-overview.md#prerequisites) ，并验证你的订阅和资源是否满足要求。 有关支持的区域和其他相关注意事项的信息，请参阅 [支持的 Azure 区域](overview.md#supported-regions)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

- 具有 Azure PowerShell 的计算机。 有关说明，请参阅[安装和配置 Azure PowerShell](/powershell/azure/)。

使用 PowerShell 管理由启用了 Azure Arc 的服务器管理的混合服务器上的 VM 扩展。 使用 PowerShell 之前，请安装该 `Az.ConnectedMachine` 模块。 在启用了 Azure Arc 的服务器上运行以下命令：

```powershell
Install-Module -Name Az.ConnectedMachine
```

安装完成后，会看到以下消息：

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>安装代理并连接到 Azure

1. 使用提升的权限打开 PowerShell 控制台。

2. 通过运行命令登录到 Azure `Connect-AzAccount` 。

3. 若要安装已连接的计算机代理，请将 `Connect-AzConnectedMachine` 与 `-Name` 、 `-ResourceGroupName` 和参数一起使用 `-Location` 。 使用 `-SubscriptionId` 参数重写默认订阅，作为在登录后创建的 Azure 上下文的结果。 运行下列命令之一：

    * 若要在可直接与 Azure 通信的目标计算机上安装已连接的计算机代理，请运行：

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * 若要在通过代理服务器进行通信的目标计算机上安装连接的计算机代理，请运行：
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

如果完成安装后代理无法启动，请检查日志以获取详细的错误信息。 在 Windows 上，检查以下文件： *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*。 在 Linux 上，检查以下文件： */var/opt/azcmagent/log/himds.log*。

## <a name="install-and-connect-by-using-powershell-remoting"></a>使用 PowerShell 远程处理进行安装和连接

下面介绍了如何配置一台或多台启用了 Azure Arc 的服务器的 Windows server。必须在远程计算机上启用 PowerShell 远程处理。 请使用 `Enable-PSRemoting` cmdlet 完成此操作。

1. 以管理员身份打开 PowerShell 控制台。

2. 通过运行命令登录到 Azure `Connect-AzAccount` 。

3. 若要安装已连接的计算机代理，请将 `Connect-AzConnectedMachine` 与 `-ResourceGroupName` 和参数一起使用 `-Location` 。 Azure 资源名称将自动使用每个服务器的主机名。 使用 `-SubscriptionId` 参数重写默认订阅，作为在登录后创建的 Azure 上下文的结果。

    * 若要在可直接与 Azure 通信的目标计算机上安装已连接的计算机代理，请运行以下命令：
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * 若要同时在多台远程计算机上安装连接的计算机代理，请添加一组远程计算机名称，每个名称之间用逗号分隔。

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    下面的示例显示针对单个计算机的命令的结果：
    
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

安装并配置要向启用了 Azure Arc 的服务器注册的代理后，请跳到 Azure 门户验证服务器是否已成功连接。 在 [Azure 门户](https://portal.azure.com)中查看你的计算机。

![服务器仪表板的屏幕截图，显示成功的服务器连接。](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>后续步骤

* 如有必要，请参阅有关 [连接的计算机代理的疑难解答指南](troubleshoot-agent-onboard.md)。

* 了解如何使用 [Azure 策略](../../governance/policy/overview.md)管理计算机。 你可以使用 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否已向预期的 Log Analytics 工作区进行报告，并使用 [vm Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md)启用监视。

* 详细了解 [Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)。 需要收集操作系统和工作负荷监视数据，或使用 Azure 自动化 runbook 或更新管理等功能来管理数据时，需要使用适用于 Windows 和 Linux 的 Log Analytics 代理。 使用其他 Azure 服务（例如 [Azure 安全中心](../../security-center/security-center-introduction.md)）时，也需要使用此代理。
