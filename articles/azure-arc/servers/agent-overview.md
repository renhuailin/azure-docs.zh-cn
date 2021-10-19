---
title: Connected Machine 代理概述
description: 本文详细介绍了已启用 Azure Arc 的服务器代理，该代理支持监视混合环境中托管的虚拟机。
ms.date: 09/30/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e4251ceafd2ab06afc43f8c3ba84f167219d7e14
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713175"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>已启用 Azure Arc 的服务器代理概述

借助已启用 Azure Arc 的服务器 Connected Machine 代理，可以管理企业网络或其他云提供商中托管在 Azure 外部的 Windows 和 Linux 计算机。 本文提供该代理的详细概述、系统和网络要求以及不同的部署方法。

>[!NOTE]
> [Azure Monitor 代理](../../azure-monitor/agents/azure-monitor-agent-overview.md) (AMA) 不会取代 Connected Machine 代理。 Azure Monitor 代理将取代 Windows 和 Linux 计算机上的 Log Analytics 代理、诊断扩展和 Telegraf 代理。 有关更多详细信息，请查看有关新代理的 Azure Monitor 文档。

## <a name="agent-component-details"></a>代理组件详细信息

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="已启用 Azure Arc 的服务器代理概述。" border="false":::

Azure Connected Machine 代理包包含捆绑在一起的多个逻辑组件。

* Hybrid Instance Metadata Service (HIMDS) 管理 Azure 的连接和已连接的计算机的 Azure 标识。

* 来宾配置代理提供评估计算机是否符合所需的策略和强制实施符合性等的功能。

    对于已断开连接的计算机，请注意 Azure Policy [guest configuration](../../governance/policy/concepts/guest-configuration.md) 的以下行为：

    * 以断开连接的计算机为目标的 Azure Policy 分配不受影响。
    * 来宾分配在本地存储 14 天。 在 14 天的期限内，如果 Connected Machine 代理重新连接到服务，则重新应用策略分配。
    * 分配的策略将在 14 天后删除，并且在 14 天期限后不会重新分配到计算机。

* Extension 代理管理 VM 扩展，包括安装、卸载和升级。 扩展从 Azure 下载并复制到 `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` 文件夹（适用于 Windows），或者 `/opt/GC_Ext/downloads` 文件夹（适用于 Linux）。 在 Windows 上，扩展安装到以下路径：`%SystemDrive%\Packages\Plugins\<extension>`在 Linux 上，扩展安装到以下路径：`/var/lib/waagent/<extension>`。

## <a name="instance-metadata"></a>实例元数据

在 Connected Machine 代理向启用了 Azure Arc 的服务器注册后，将收集有关已连接计算机的元数据信息。 具体而言：

* 操作系统名称、类型和版本
* 计算机名称
* 计算机制造商和型号
* 计算机完全限定域名 (FQDN)
* 域名（如果已联接到 Active Directory 域）
* Connected Machine 代理版本
* Active Directory 和 DNS 完全限定的域名 (FQDN)
* UUID (BIOS ID)
* Connected Machine 代理程序检测信号
* Connected Machine 代理版本
* 托管标识的公钥
* 策略符合性状态和详细信息（如果使用来宾配置策略）
* 已安装 SQL Server（布尔值）
* 适用于 Azure Stack HCI 节点的群集资源 ID 

代理向 Azure 请求以下元数据信息：

* 资源位置（区域）
* 虚拟机 ID
* 标记
* Azure Active Directory 托管标识证书
* Guest Configuration 策略分配
* 扩展请求 - 安装、更新和删除。

## <a name="download-agents"></a>下载代理

可从以下位置下载适用于 Windows 和 Linux 的 Azure Connected Machine 代理包。

* Microsoft 下载中心提供的 [Windows 代理 Windows Installer 包](https://aka.ms/AzureConnectedMachineAgent)。

* Linux 代理包通过 Microsoft 的[包存储库](https://packages.microsoft.com/)使用分发版的首选包格式（.RPM 或 .DEB）进行分发。

适用于 Windows 和 Linux 的 Azure Connected Machine 代理可以手动或自动升级到最新版本，具体取决于你的要求。 有关详细信息，请参阅[此文](manage-agent.md)。

## <a name="prerequisites"></a>先决条件

### <a name="supported-environments"></a>支持的环境

已启用 Azure Arc 的服务器支持在 Azure 外部托管的任何物理服务器和虚拟机上安装 Connected Machine 代理。 包括在 VMware、Azure Stack HCI 和其他云环境等平台上运行的虚拟机。 已启用 Azure Arc 的服务器不支持在 Azure 中运行的虚拟机上安装代理，也不支持在 Azure Stack Hub 或 Azure Stack Edge 上运行的虚拟机上安装代理，因为它们已作为 Azure VM 建模。

### <a name="supported-operating-systems"></a>支持的操作系统

Azure Connected Machine 代理正式支持以下版本的 Windows 和 Linux 操作系统：

- Windows Server 2008 R2 SP1、Windows Server 2012 R2、2016、2019 和 2022（包括 Server Core）
- Ubuntu 16.04、18.04 和 20.04 LTS (x64)
- CentOS Linux 7 和 8 (x64)
- SUSE Linux Enterprise Server (SLES) 12 和 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 和 8 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7

> [!WARNING]
> Linux 主机名或 Windows 计算机名不能使用名称中的保留字或商标之一，否则尝试使用 Azure 注册连接的计算机将失败。 若要获取保留字的列表，请参阅[解决保留的资源名称错误](../../azure-resource-manager/templates/error-reserved-resource-name.md)。

> [!NOTE]
> 虽然启用了 Azure Arc 的服务器支持 Amazon Linux，但以下内容不支持此发行版：
> * Azure Monitor VM 见解所用的依赖关系代理
> * Azure 自动化更新管理

### <a name="software-requirements"></a>软件要求

* 需要 .NET Framework 4.6 或更高版本。 [下载 .NET Framework](/dotnet/framework/install/guide-for-developers)。
* 需要 Windows PowerShell 5.1。 [下载 Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。

### <a name="required-permissions"></a>所需的权限

* 若要加入计算机，你需要是资源组中“Azure Connected Machine 加入”或[参与者](../../role-based-access-control/built-in-roles.md#contributor)角色的成员。

* 若要读取、修改和删除计算机，你需是资源组中“Azure Connected Machine 资源管理员”角色的成员。

* 若要在使用“生成脚本”方法时从下拉列表中选择资源组，你至少需要是该资源组的[读者](../../role-based-access-control/built-in-roles.md#reader)角色的成员。

### <a name="azure-subscription-and-service-limits"></a>Azure 订阅和服务限制

在为计算机配置已启用 Azure Arc 的服务器之前，应查看 Azure 资源管理器[订阅限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)和[资源组限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)，以规划要连接的计算机数。

已启用 Azure Arc 的服务器在一个资源组中支持多达 5,000 个计算机实例。

### <a name="register-azure-resource-providers"></a>注册 Azure 资源提供程序

已启用 Azure Arc 的服务器依赖于通过订阅中的以下 Azure 资源提供程序来使用此服务：

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

如果未注册这些提供程序，可使用以下命令注册：

Azure PowerShell：

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI：

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

此外，还可以按照 [Azure 门户](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)下的步骤，在 Azure 门户中注册资源提供程序。

### <a name="transport-layer-security-12-protocol"></a>传输层安全性 1.2 协议

为了确保传输到 Azure 的数据的安全性，我们强烈建议你将计算机配置为使用传输层安全性 (TLS) 1.2。 我们发现旧版 TLS/安全套接字层 (SSL) 容易受到攻击，尽管目前出于向后兼容，这些协议仍可正常工作，但我们 **不建议使用**。

|平台/语言 | 支持 | 更多信息 |
| --- | --- | --- |
|Linux | Linux 分发版往往依赖于 [OpenSSL](https://www.openssl.org) 来提供 TLS 1.2 支持。 | 请检查 [OpenSSL 变更日志](https://www.openssl.org/news/changelog.html)，确认你的 OpenSSL 版本是否受支持。|
| Windows Server 2012 R2 和更高版本 | 受支持，并且默认已启用。 | 确认是否仍在使用[默认设置](/windows-server/security/tls/tls-registry-settings)。|

## <a name="networking-configuration"></a>网络配置

适用于 Linux 和 Windows 的 Connected Machine 代理通过 TCP 端口 443 安全地与 Azure Arc 进行出站通信。 如果计算机需要通过防火墙或代理服务器进行连接以使用 Internet 通信，代理将改为使用 HTTP 协议进行出站通信。 由于流量已加密，代理服务器使 Connected Machine 代理更安全。

若要进一步确保你的网络连接到 Azure Arc，而不是使用公共网络和代理服务器，可以实现 [Azure Arc 专用链接范围](private-link-security.md)（预览版）。

> [!NOTE]
> 已启用 Azure Arc 的服务器不支持使用 [Log Analytics 网关](../../azure-monitor/agents/gateway.md)作为 Connected Machine 代理的代理。
>

如果防火墙或代理服务器限制了出站连接，请确保不要阻止下面列出的 URL。 如果只允许代理与服务进行通信所需的 IP 范围或域名，则还需要允许访问以下服务标记和 URL。

服务标记：

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure
* 存储

URL：

| 代理资源 | 说明 |
|---------|---------|
|`management.azure.com`|Azure 资源管理器|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |来宾配置|
|`*.his.arc.azure.com`|混合标识服务|
|`*.blob.core.windows.net`|下载启用了 Azure Arc 的服务器扩展的源|

预览版代理（版本 0.11 和更低版本）还要求访问以下 URL：

| 代理资源 | 说明 |
|---------|---------|
|`agentserviceapi.azure-automation.net`|来宾配置|
|`*-agentservice-prod-1.azure-automation.net`|来宾配置|

有关每个服务标记/区域的 IP 地址列表，请参阅 JSON 文件 - [Azure IP 范围和服务标记 - 公有云](https://www.microsoft.com/download/details.aspx?id=56519)。 Microsoft 每周将发布包含每个 Azure 服务及其使用的 IP 范围的更新。 JSON 文件中的这个信息是与每个服务标记对应的 IP 地址的当前实时列表。 IP 地址可能会变化。 如果防火墙配置需要 IP 地址范围，则应使用 **AzureCloud** 服务标记允许对所有 Azure 服务的访问。 请勿禁用对这些 URL 的安全监视或检查，但就像允许其他 Internet 流量一样允许这些 URL。

有关详细信息，请查看[服务标记概述](../../virtual-network/service-tags-overview.md)。

## <a name="installation-and-configuration"></a>安装和配置

可以根据要求使用不同的方法，将混合环境中的计算机直接连接到 Azure。 下表详细介绍了每种方法，让你确定最适合组织的方法。

> [!IMPORTANT]
> Azure Windows 虚拟机上无法安装 Connected Machine 代理。 如果尝试此操作，安装过程将检测到此情况并回滚。

| 方法 | 说明 |
|--------|-------------|
| 交互式 | 遵循[从 Azure 门户连接计算机](onboard-portal.md)中的步骤，在一台或多台计算机上手动安装代理。<br> 在 Azure 门户中，可以生成一个脚本并在计算机上执行，以自动完成代理安装和配置的步骤。|
| 大规模 | 遵循[使用服务主体连接计算机](onboard-service-principal.md)中的步骤，为多台计算机安装并配置代理。<br> 此方法创建一个服务主体来以非交互方式连接计算机。|
| 大规模 | 按照[从自动化更新管理将混合计算机连接到 Azure](onboard-update-management-machines.md) 方法为多台计算机安装并配置代理。<br> 此方法创建一个服务主体，并为使用 Azure 自动化更新管理来管理的多台计算机安装和配置代理，从而以非交互方式连接计算机。 |
| 大规模 | 按照[使用 Windows PowerShell DSC](onboard-dsc.md) 方法为多台计算机安装和配置代理。<br> 此方法使用服务主体以非交互方式将计算机与 PowerShell DSC 连接。 |

## <a name="connected-machine-agent-technical-overview"></a>Connected Machine 代理技术概述

### <a name="windows-agent-installation-details"></a>Windows 代理安装详细信息

可以使用以下三种方法之一安装适用于 Windows 的 Connected Machine 代理：

* 双击文件 `AzureConnectedMachineAgent.msi`。
* 通过从命令外壳运行 Windows Installer 包 `AzureConnectedMachineAgent.msi` 来手动进行安装。
* 从 PowerShell 会话中使用脚本化方法。

安装适用于 Windows 的 Connected Machine 代理后，将应用下列系统范围的配置更改。

* 安装期间将创建以下安装文件夹。

    |Folder |说明 |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |包含代理支持文件的默认安装路径。|
    |%ProgramData%\AzureConnectedMachineAgent |包含代理配置文件。|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |包含获取的令牌。|
    |%ProgramData%\AzureConnectedMachineAgent\Config |包含代理配置文件 `agentconfig.json`，该文件记录其在服务中的注册信息。|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | 包含来宾配置代理文件的安装路径。 |
    |%ProgramData%\GuestConfig |包含 Azure 中（应用的）策略。|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | 扩展从 Azure 下载，并在此处复制。|

* 安装代理期间，将在目标计算机上创建以下 Windows 服务。

    |Service name |显示名称 |进程名称 |说明 |
    |-------------|-------------|-------------|------------|
    |himds |Azure 混合实例元数据服务 |himds |此服务实现 Azure Instance Metadata Service (IMDS)，以管理 Azure 的连接和已连接计算机的 Azure 标识。|
    |GCArcService |来宾配置 Arc 服务 |gc_service |监视计算机所需的状态配置。|
    |ExtensionService |来宾配置扩展服务 | gc_service |安装以计算机为目标的所需扩展。|

* 安装代理期间，将创建以下环境变量。

    |名称 |默认值 |说明 |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* 有几个日志文件可用于故障排除。 下表对它们进行了说明。

    |日志 |说明 |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |记录代理 (HIMDS) 服务的详细信息以及与 Azure 的交互。|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |当使用 verbose (-v) 参数时，包含 azcmagent 工具命令的输出。|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |记录 DSC 服务活动的详细信息，<br> 特别是 HIMDS 服务与 Azure Policy 之间的连接。|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |记录有关 DSC 服务遥测和详细日志记录的详细信息。|
    |%ProgramData%\GuestConfig\ext_mgr_logs|记录有关 Extension 代理组件的详细信息。|
    |%ProgramData%\GuestConfig\extension_logs\<Extension>|记录已安装扩展中的详细信息。|

* 将创建本地安全组“混合代理扩展应用程序”。

* 卸载代理期间，不会删除以下项目。

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent 和子目录
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux 代理安装详细信息

适用于 Linux 的 Connected Machine 代理以 Microsoft [包存储库](https://packages.microsoft.com/)中分发版的首选包格式（.RPM 或 .DEB）提供。 可以使用 shell 脚本捆绑包 [Install_linux_azcmagent.sh](https://aka.ms/azcmagent) 安装和配置该代理。

安装适用于 Linux 的 Connected Machine 代理后，将应用下列系统范围的配置更改。

* 安装期间将创建以下安装文件夹。

    |Folder |说明 |
    |-------|------------|
    |/var/opt/azcmagent/ |包含代理支持文件的默认安装路径。|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | 包含来宾配置代理文件的安装路径。|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |包含获取的令牌。|
    |/var/lib/GuestConfig |包含 Azure 中（应用的）策略。|
    |/opt/GC_Ext/downloads|扩展从 Azure 下载，并在此处复制。|

* 安装代理期间，将在目标计算机上创建以下守护程序。

    |Service name |显示名称 |进程名称 |说明 |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure Connected Machine Agent Service |himds |此服务实现 Azure Instance Metadata Service (IMDS)，以管理 Azure 的连接和已连接计算机的 Azure 标识。|
    |gcad.service |GC Arc Service |gc_linux_service |监视计算机所需的状态配置。 |
    |extd.service |Extension Service |gc_linux_service | 安装以计算机为目标的所需扩展。|

* 有几个日志文件可用于故障排除。 下表对它们进行了说明。

    |日志 |说明 |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |记录代理 (HIMDS) 服务的详细信息以及与 Azure 的交互。|
    |/var/opt/azcmagent/log/azcmagent.log |当使用 verbose (-v) 参数时，包含 azcmagent 工具命令的输出。|
    |/opt/logs/dsc.log |记录 DSC 服务活动的详细信息，<br> 特别是 himds 服务与 Azure Policy 之间的连接。|
    |/opt/logs/dsc.telemetry.txt |记录有关 DSC 服务遥测和详细日志记录的详细信息。|
    |/var/lib/GuestConfig/ext_mgr_logs |记录有关 Extension 代理组件的详细信息。|
    |/var/lib/GuestConfig/extension_logs|记录已安装扩展中的详细信息。|

* 安装代理期间，将创建以下环境变量。 这些变量在 `/lib/systemd/system.conf.d/azcmagent.conf` 中设置。

    |名称 |默认值 |说明 |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* 卸载代理期间，不会删除以下项目。

    * /var/opt/azcmagent
    * /opt/logs

### <a name="agent-resource-governance"></a>代理资源治理

已启用 Azure Arc 的服务器连接的计算机代理旨在管理代理和系统资源消耗。 在下列情况下，该代理会采取资源监管：

- 来宾配置代理最多可限制 5% 的 CPU 来评估策略。
- 扩展服务代理最多只能使用 5% 的 CPU。

   - 这只适用于安装/卸载/升级操作。 安装完成后，扩展将负责其资源利用率，但无需遵守 5% 的 CPU 限制。
   - 在 Red Hat Linux、CentOS 和其他企业 Linux 变体上安装/升级/卸载 Log Analytics 代理和 Azure Monitor 代理时，最多允许使用 60% 的 CPU。 为适应 [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux) 在这些系统上的性能影响，此扩展和操作系统组合的限制将会更严苛。

## <a name="next-steps"></a>后续步骤

* 若要开始评估已启用 Azure Arc 的服务器，请参阅[连接混合计算机与已启用 Azure Arc 的服务器](learn/quick-enable-hybrid-vm.md)一文。

* 在部署已启用 Azure Arc 的服务器代理并与其他 Azure 管理和监视服务集成之前，请先查看[规划和部署指南](plan-at-scale-deployment.md)。

* 在 [Connected Machine 代理故障排除指南](troubleshoot-agent-onboard.md)中可以找到故障排除信息。
