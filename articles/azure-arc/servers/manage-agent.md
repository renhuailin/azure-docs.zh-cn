---
title: 管理已启用 Azure Arc 的服务器代理
description: 本文介绍在已启用 Azure Arc 的服务器 Connected Machine 代理的生命周期内通常会执行的不同管理任务。
ms.date: 05/18/2021
ms.topic: conceptual
ms.openlocfilehash: 728e67930366f1b62b405f503a775b6d14a90bd0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110068223"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>管理并维护 Connected Machine 代理

最开始为 Windows 或 Linux 部署已启用 Azure Arc 的服务器 Connected Machine 代理后，可能需要重新配置代理，升级该代理，或者将其从计算机中删除。 可以轻松地手动或自动管理这些日常维护任务，从而减少运行错误并降低费用。

## <a name="before-uninstalling-agent"></a>卸载代理之前

在从已启用 Arc 的服务器中删除 Connected Machine 代理之前，请考虑以下内容以避免意外问题或在 Azure 帐单中增添费用：

* 如果已将 Azure VM 扩展部署到已启用的服务器，并且删除了 Connected Machine 代理或该资源组中表示已启用 Arc 的服务器的资源，则这些扩展将继续运行并执行其常规操作。

* 如果在资源组中删除了表示已启用 Arc 的服务器的资源，但未卸载 VM 扩展，则在重新注册计算机时，将无法管理已安装的 VM 扩展。

对于不再希望通过已启用 Azure Arc 的服务器进行管理的服务器或计算机，需要执行以下步骤来成功停止对它们的管理：

1. 从计算机或服务器中删除 VM 扩展。 下面是步骤。

2. 使用以下方法之一从 Azure Arc 中断开计算机的连接：

    * 在计算机或者服务器上运行 `azcmagent disconnect` 命令。

    * 在 Azure 门户中已启用 Arc 的已注册的所选服务器上，选择顶部栏中的“删除”。

    * 使用 [Azure CLI](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) 或 [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource)。 对于 `ResourceType` 参数，请使用 `Microsoft.HybridCompute/machines`。

3. 按照以下步骤从计算机或服务器中[卸载代理](#remove-the-agent)。

## <a name="renaming-a-machine"></a>重命名计算机

如果更改连接到已启用 Azure Arc 的服务器的 Linux 或 Windows 计算机的名称，则不会自动识别新名称，因为 Azure 中的资源名称是不可变的。 与其他 Azure 资源一样，必须删除并重新创建资源，才能使用新名称。

对于已启用 Arc 的服务器，在重命名计算机之前，需要先删除 VM 扩展，然后才能继续。

> [!NOTE]
> 尽管在完成此过程后，已安装扩展将继续运行并执行其常规操作，但你无法管理这些扩展。 如果你尝试在计算机上重新部署扩展，可能会遇到不可预知的行为。

> [!WARNING]
> 建议你避免重命名计算机的计算机名，并且仅在绝对必要时才执行此过程。

1. 使用 [Azure CLI](manage-vm-extensions-cli.md#list-extensions-installed) 或 [Azure PowerShell](manage-vm-extensions-powershell.md#list-extensions-installed) 来审核计算机上安装的 VM 扩展并记录其配置。

2. 使用 [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension) 或 [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension) 删除通过 [Azure 门户](manage-vm-extensions-portal.md#uninstall-extension)安装的 VM 扩展。

3. 结合使用 azcmagent 工具与 [Disconnect](manage-agent.md#disconnect) 参数从 Azure Arc 中断开计算机的连接，然后从 Azure 中删除计算机资源。 从已启用 Arc 的服务器断开计算机的连接不会删除 Connected Machine 代理，并且在此过程中不需要删除该代理。 可以在以交互方式登录时手动运行此内容，或者使用用于加入多个代理的同一服务主体或使用 Microsoft 标识平台[访问令牌](../../active-directory/develop/access-tokens.md)来自动运行此内容。 如果你未使用服务主体将计算机注册到已启用 Azure Arc 的服务器，请参阅以下[文章](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)创建服务主体。

4. 重命名计算机的计算机名。

5. 将 Connected Machine 代理重新注册到已启用 Arc 的服务器。 使用 [Connect](manage-agent.md#connect) 参数运行 `azcmagent` 工具即可完成此步骤。

6. 重新部署最初部署到了已启用 Arc 的服务器中的计算机的 VM 扩展。 如果你使用 Azure 策略部署了用于 VM 的 Azure Monitor（见解）代理或 Log Analytics 代理，将在下一个[评估周期](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)后重新部署这些代理。

## <a name="upgrading-agent"></a>升级代理

Azure Connected Machine 代理会定期更新以处理 bug 修补程序、稳定性增强功能和新功能。 [Azure 顾问](../../advisor/advisor-overview.md)识别未使用最新版本的计算机代理的资源，并建议升级到最新版本。 当你选择已启用 Arc 的服务器，或通过 Azure 门户访问顾问时，它将通过在“概述”页上提供横幅来通知你。

适用于 Windows 和 Linux 的 Azure Connected Machine 代理可以手动或自动升级到最新版本，具体取决于你的要求。

下表介绍了执行代理升级所支持的方法。

| 操作系统 | 升级方法 |
|------------------|----------------|
| Windows | 手动<br> Windows 更新 |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise、Amazon、CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Windows 代理

可从以下位置获取适用于 Windows 的 Connected Machine 代理更新包：

* Microsoft Update

* [Microsoft 更新目录](https://www.catalog.update.microsoft.com/Home.aspx)

* Microsoft 下载中心提供的 [Windows 代理 Windows Installer 包](https://aka.ms/AzureConnectedMachineAgent)。

可通过多种方法升级代理，以支持软件更新管理过程。 除了从 Microsoft 更新获取，还可以通过执行 `AzureConnectedMachine.msi` 从命令提示符、脚本或其他自动化解决方案或 UI 向导手动下载并运行。

> [!NOTE]
> * 若要升级代理，必须拥有“管理员”权限。
> * 若要手动升级，必须先下载 Installer 包并将其复制到目标服务器上的某个文件夹，或者从共享网络文件夹下载。 

如果不熟悉 Windows Installer 包的命令行选项，请查看 [Msiexec 标准命令行选项](/windows/win32/msi/standard-installer-command-line-options)和 [Msiexec 命令行选项](/windows/win32/msi/command-line-options)。

#### <a name="to-upgrade-using-the-setup-wizard"></a>使用安装向导进行升级

1. 使用具有管理权限的帐户登录到计算机。

2. 执行“AzureConnectedMachineAgent.msi”以启动安装向导。

安装向导会发现是否存在先前版本，然后自动执行代理升级。 升级完成后，安装向导将自动关闭。

#### <a name="to-upgrade-from-the-command-line"></a>从命令行升级

1. 使用具有管理权限的帐户登录到计算机。

2. 若要无提示地升级代理并在 `C:\Support\Logs` 文件夹中创建安装日志文件，请运行以下命令。

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux 代理

若要将 Linux 计算机上的代理更新到最新版本，则会涉及两种命令。 一种命令用于使用存储库中的最新可用包列表更新本地包索引，另一种命令用于升级本地包。

可以从 Microsoft 的[包存储库](https://packages.microsoft.com/)下载最新的代理包。

> [!NOTE]
> 若要升级代理，必须拥有根访问权限，或者具有已使用 Sudo 提升权限的帐户。

#### <a name="upgrade-ubuntu"></a>升级 Ubuntu

1. 若要使用存储库中所进行的最新更改更新本地包索引，请运行以下命令：

    ```bash
    apt update
    ```

2. 若要升级系统，请运行以下命令：

    ```bash
    apt upgrade
    ```

[apt](https://help.ubuntu.com/lts/serverguide/apt.html) 命令的操作（如安装和删除包）记录在 `/var/log/dpkg.log` 日志文件中。

#### <a name="upgrade-red-hatcentosamazon-linux"></a>升级 Red Hat/CentOS/Amazon Linux

1. 若要使用存储库中所进行的最新更改更新本地包索引，请运行以下命令：

    ```bash
    yum check-update
    ```

2. 若要升级系统，请运行以下命令：

    ```bash
    yum update
    ```

[yum](https://access.redhat.com/articles/yum-cheat-sheet) 命令的操作（如安装和删除包）记录在 `/var/log/yum.log` 日志文件中。 

#### <a name="upgrade-suse-linux-enterprise"></a>升级 SUSE Linux Enterprise

1. 若要使用存储库中所进行的最新更改更新本地包索引，请运行以下命令：

    ```bash
    zypper refresh
    ```

2. 若要升级系统，请运行以下命令：

    ```bash
    zypper update
    ```

[zypper](https://en.opensuse.org/Portal:Zypper) 命令的操作（如安装和删除包）记录在 `/var/log/zypper.log` 日志文件中。

## <a name="about-the-azcmagent-tool"></a>关于 Azcmagent 工具

Azcmagent 工具 (Azcmagent.exe) 用于在安装过程中配置已启用 Azure Arc 的服务器 Connected Machine 代理，或在安装后修改代理的初始配置。 Azcmagent.exe 提供了用于自定义代理及查看其状态的命令行参数：

* **Connect** - 将计算机连接到 Azure Arc

* **Disconnect** - 断开计算机与 Azure Arc 的连接

* **Show** - 查看代理状态及其配置属性（资源组名称、订阅 ID、版本等），这有助于排查与代理相关的问题。 包括用于以 JSON 格式输出结果的 `-j` 参数。

* **Logs** - 在当前目录中创建一个 .zip 文件，该文件包含用于在进行故障排除时提供帮助的日志。

* **Version** - 显示 Connected Machine 代理版本。

* **-useStderr** - 将错误和详细输出定向到 stderr。 包括用于以 JSON 格式输出结果的 `-json` 参数。

* **-h or --help** - 显示可用的命令行参数

    例如，若要查看 Connect 参数的详细帮助，请键入 `azcmagent connect -h`。 

* **-v or --verbose** - 启用详细日志记录

你可以在以交互方式登录时手动执行“Connect”和“Disconnect”，也可以使用用于加入多个代理的相同服务主体或使用 Microsoft 标识平台[访问令牌](../../active-directory/develop/access-tokens.md)自动执行这些参数 。 如果你未使用服务主体将计算机注册到已启用 Azure Arc 的服务器，请参阅以下[文章](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)创建服务主体。

>[!NOTE]
>若要运行 azcmagent，必须具有对 Linux 计算机的根访问权限。

### <a name="connect"></a>连接

此参数指定 Azure 资源管理器中的资源，该资源表示已在 Azure 中创建计算机。 资源位于指定的订阅和资源组中，有关计算机的数据存储在由 `--location` 设置指定的 Azure 区域中。 如果未指定，则该计算机的主机名为默认资源名。

然后，将下载与系统分配的计算机标识相对应的证书，并将其存储在本地。 完成此步骤后，Azure Connected Machine 元数据服务和来宾配置代理将开始与已启用 Azure Arc 的服务器进行同步。

若要使用服务主体进行连接，请运行以下命令：

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

若要使用访问令牌进行连接，请运行以下命令：

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

若要使用提升的登录凭据（交互式）进行连接，请运行以下命令：

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>断开连接

此参数指定 Azure 资源管理器中的资源，该资源表示已在 Azure 中删除计算机。 它不会从计算机中删除代理，而是单独卸载代理。 断开计算机连接后，如果想要重新将其注册到已启用 Azure Arc 的服务器，请使用 `azcmagent connect`，以便在 Azure 中为其创建新资源。

> [!NOTE]
> 如果已将一个或多个 Azure VM 扩展部署到已启用 Arc 的服务器，并且在 Azure 中删除了其注册，则仍将安装这些扩展。 务必要了解它们正在主动执行其功能，具体取决于所安装的扩展。 如果计算机将要停用或不再由已启用 Arc 的服务器进行管理，则它们应首先删除这些扩展，再从 Azure 中删除其注册。

若要使用服务主体断开连接，请运行以下命令：

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

若要使用访问令牌断开连接，请运行以下命令：

`azcmagent disconnect --access-token <accessToken>`

若要使用提升的登录凭据（交互式）断开连接，请运行以下命令：

`azcmagent disconnect`

## <a name="remove-the-agent"></a>删除代理

执行以下方法之一，从计算机中卸载 Windows 或 Linux Connected Machine 代理。 删除代理不会在已启用 Arc 的服务器中注销计算机，也不会删除已安装的 Azure VM 扩展。 对于不再希望通过已启用 Azure Arc 的服务器进行管理的服务器或计算机，需要执行以下步骤来成功停止对它们的管理： 

1. 使用 [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension) 或使用 [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension) 删除从 [Azure 门户](manage-vm-extensions-portal.md#uninstall-extension)安装的不想保留在计算机上的 VM 扩展。
1. 通过运行 `azcmagent disconnect` 取消注册计算机，以删除 Azure 中已启用 Arc 的服务器资源。 如果此命令失败，可以在 Azure 中手动删除该资源。 否则，如果该资源已在 Azure 中删除，你需要在服务器上运行 `azcmagent disconnect --force-local-only` 以删除本地配置。

### <a name="windows-agent"></a>Windows 代理

以下两种方法都将删除代理，但不会删除计算机上的“C:\Program Files\AzureConnectedMachineAgent”文件夹。

#### <a name="uninstall-from-control-panel"></a>从控制面板卸载

1. 若要从计算机中卸载 Windows 代理，请执行以下操作：

    a. 使用拥有管理员权限的帐户登录到计算机。  
    b. 在“控制面板”中，选择“程序和功能”。   
    c. 在“程序和功能”中，依次选择“Azure Connected Machine Agent”、“卸载”、“是”。     

    >[!NOTE]
    > 也可以双击 **AzureConnectedMachineAgent.msi** 安装程序包运行代理安装向导。

#### <a name="uninstall-from-the-command-line"></a>从命令行卸载

若要从命令提示符手动卸载代理或使用自动化方法（如脚本），可以使用以下示例。 首先，需要从操作系统中检索产品代码，即应用程序包的主体标识符 GUID。 使用 Msiexec.exe 命令行 (`msiexec /x {Product Code}`) 执行卸载。

1. 打开注册表编辑器。

2. 在注册表项 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 下，查找并复制产品代码 GUID。

3. 然后，可以使用 Msiexec 卸载代理，示例如下：

   * 通过命令行类型：

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * 可使用 PowerShell 执行相同的步骤：

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux 代理

> [!NOTE]
> 若要卸载代理，必须拥有根访问权限，或者具有已使用 Sudo 提升权限的帐户。

卸载 Linux 代理时，要使用的命令取决于 Linux 操作系统。

- 对于 Ubuntu，请运行以下命令：

    ```bash
    sudo apt purge azcmagent
    ```

- 对于 RHEL、CentOS 和 Amazon Linux，请运行以下命令：

    ```bash
    sudo yum remove azcmagent
    ```

- 对于 SLES，请运行以下命令：

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>注销计算机

如果计划在 Azure 中停止使用支持服务来管理计算机，请执行以下步骤在已启用 Arc 的服务器中注销计算机。 可在从计算机中删除 Connected Machine 代理之前或之后执行这些步骤。

1. 转到 [Azure 门户](https://aka.ms/hybridmachineportal)以打开已启用 Azure Arc 的服务器。

2. 在列表中选择计算机，选择省略号图标 ( **...** )，然后选择“删除”。

## <a name="update-or-remove-proxy-settings"></a>更新或删除代理设置

若要将代理配置为在部署后通过代理服务器与服务进行通信或在部署后删除此配置，请使用以下任一方法来完成此任务。

> [!NOTE]
> 已启用 Arc 的服务器不支持使用 [Log Analytics 网关](../../azure-monitor/agents/gateway.md)作为 Connected Machine 代理的代理。
>

### <a name="windows"></a>Windows

若要设置代理服务器环境变量，请运行以下命令：

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

若要将代理配置为停止通过代理服务器进行通信，请运行以下命令删除代理服务器环境变量并重启代理服务：

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

若要设置代理服务器，请从目录中运行以下命令（代理安装包将下载到该目录中）：

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

若要将代理配置为停止通过代理服务器进行通信，请运行以下命令以删除代理配置：

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>后续步骤

* 在 [Connected Machine 代理故障排除指南](troubleshoot-agent-onboard.md)中可以找到故障排除信息。

* 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机，例如，进行 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)，验证计算机是否向预期的 Log Analytics 工作区报告，使用[用于 VM 的 Azure Monitor](../../azure-monitor/vm/vminsights-enable-policy.md) 启用监视等。

* 详细了解 [Log Analytics 代理](../../azure-monitor/agents/log-analytics-agent.md)。 如果需要收集操作系统和工作负载监视数据，使用自动化 runbook 或更新管理等功能管理这些数据，或使用 [Azure 安全中心](../../security-center/security-center-introduction.md)等其他 Azure 服务，则需要适用于 Windows 和 Linux 的 Log Analytics 代理。