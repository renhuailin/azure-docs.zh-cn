---
title: 使用 Windows PowerShell DSC 安装 Connected Machine Agent
description: 本文介绍如何使用 Windows PowerShell DSC 通过已启用 Azure Arc 的服务器将计算机连接到 Azure。
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 0a6e955df43e3589c97091cb111699ce402723d0
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323797"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>如何使用 Windows PowerShell DSC 安装 Connected Machine Agent

使用 [Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted) (DSC)，可以自动为 Windows 计算机安装和配置软件。 本文介绍如何使用 DSC 在混合 Windows 计算机上安装已启用 Azure Arc 的服务器 Connected Machine Agent。

## <a name="requirements"></a>要求

- Windows PowerShell 版本 4.0 或更高版本

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc) DSC 模块

- 用于以非交互方式将计算机连接到已启用 Azure Arc 的服务器的服务主体。 如果尚未为已启用 Arc 的服务器创建服务主体，请按照[为大规模加入创建服务主体](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)部分中的步骤进行操作。

## <a name="install-the-connectedmachine-dsc-module"></a>安装 ConnectedMachine DSC 模块

1. 若要手动安装模块，请下载源代码并将项目目录的内容解压缩到 `$env:ProgramFiles\WindowsPowerShell\Modules folder`。 或者，运行以下命令以使用 PowerShellGet（在 PowerShell 5.0 中）从 PowerShell 库中进行安装：

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. 若要确认安装，请运行以下命令，并确保看到可用的 Azure Connected Machine DSC 资源。

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   在输出中，应会看到类似于下面的信息：

   ![确认 Connected Machine DSC 模块安装示例](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>安装代理并连接到 Azure

此模块中的资源旨在管理 Azure Connected Machine Agent 配置。 还包括一个 PowerShell 脚本 `AzureConnectedMachineAgent.ps1`，位于 `AzureConnectedMachineDsc\examples` 文件夹中。 它使用社区资源自动执行下载和安装，并建立与 Azure Arc 的连接。此脚本执行[从 Azure 门户将混合计算机连接到 Azure](onboard-portal.md) 一文中所述的类似步骤。

如果计算机需要通过代理服务器来与服务进行通信，则在安装代理后，需要运行[此处](manage-agent.md#update-or-remove-proxy-settings)所述的某个命令。 此命令将设置代理服务器系统环境变量 `https_proxy`。 可以使用 [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc) 模块通过 DSC 执行此步骤，而不是手动运行该命令。 使用此配置，代理使用 HTTP 协议通过代理服务器进行通信。

>[!NOTE]
>即使在运行 localhost 配置的情况下，也需要将 Windows 配置为接收 PowerShell 远程命令，以允许 DSC 运行。 在提升的 PowerShell 终端中运行 `Set-WsManQuickConfig -Force`，即可轻松地正确配置环境。
>

可以使用 `Start-DscConfiguration` cmdlet 将配置文档（MOF 文件）应用于计算机。

下面是传递给要使用的 PowerShell 脚本的参数。

- `TenantId`：表示 Azure AD 专用实例的唯一标识符 (GUID)。

- `SubscriptionId`：计算机要属于的 Azure 订阅的订阅 ID (GUID)。

- `ResourceGroup`：连接的计算机要属于的资源组的名称。

- `Location`：请参阅[支持的 Azure 区域](overview.md#supported-regions)。 此位置可以与资源组的位置相同或不同。

- `Tags`：应该应用于已连接计算机资源的标记的字符串数组。

- `Credential`：具有 ApplicationId 和密码的 PowerShell 凭证对象，用于使用[服务主体](onboard-service-principal.md)大规模注册计算机。

1. 在 PowerShell 控制台中，导航到保存 `.ps1` 文件的文件夹。

2. 运行以下 PowerShell 命令来编译 MOF 文档（有关编译 DSC 配置的信息，请参阅 [DSC 配置](/powershell/scripting/dsc/configurations/configurations)：

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. 这将在名为 `C:\dsc` 的新文件夹中创建 `localhost.mof file`。

安装代理并将其配置为连接到已启用 Azure Arc 的服务器后，请转到 Azure 门户，验证是否已成功连接服务器。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

## <a name="adding-to-existing-configurations"></a>添加到现有配置

可以将此资源添加到现有 DSC 配置，以表示计算机的端到端配置。 例如，你可能希望将此资源添加到设置安全操作系统设置的配置中。

PowerShell 库中的 [CompositeResource](https://www.powershellgallery.com/packages/compositeresource) 模块可用于创建示例配置的[复合资源](/powershell/scripting/dsc/resources/authoringResourceComposite)，以进一步简化组合配置。

## <a name="next-steps"></a>后续步骤

* 在 [Connected Machine 代理故障排除指南](troubleshoot-agent-onboard.md)中可以找到故障排除信息。

* 查看[规划和部署指南](plan-at-scale-deployment.md)，以便对按任意规模部署启用了 Azure Arc 的服务器进行规划，并实现集中管理和监视。

* 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机，例如，进行 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)、验证计算机是否向预期的 Log Analytics 工作区报告、使用 [VM 见解](../../azure-monitor/vm/vminsights-enable-policy.md)启用监视，等等。