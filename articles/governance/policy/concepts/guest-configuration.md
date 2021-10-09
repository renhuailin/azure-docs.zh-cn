---
title: 了解 Azure Policy 的来宾配置功能
description: 了解 Azure Policy 如何使用来宾配置功能来审核或配置虚拟机内部的设置。
ms.date: 07/15/2021
ms.topic: conceptual
ms.openlocfilehash: d9da1454fa531bcc6526cc11dda3b341be0688df
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092616"
---
# <a name="understand-the-guest-configuration-feature-of-azure-policy"></a>了解 Azure Policy 的来宾配置功能

Azure Policy 可以审核或配置计算机（Azure 中运行的计算机和[已启用 Arc 的计算机](../../../azure-arc/servers/overview.md)）内部的设置。
每个任务由 Windows 和 Linux 中的来宾配置代理执行。
来宾配置扩展通过该代理管理如下所述的设置：

- 操作系统的配置
- 应用程序配置或状态
- 环境设置

[提供本文档的视频演练](https://youtu.be/t9L8COY-BkM)。

## <a name="enable-guest-configuration"></a>启用来宾配置

若要管理环境中计算机（包括 Azure 中的计算机和已启用 Arc 的服务器）的状态，请查看以下详细信息。

## <a name="resource-provider"></a>资源提供程序

在使用 Azure Policy 的来宾配置功能之前，必须先注册 `Microsoft.GuestConfiguration` 资源提供程序。 如果来宾配置策略的分配是通过门户完成的，或者如果订阅已在 Azure 安全中心内注册，则会自动注册该资源提供程序。 可以通过[门户](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 或 [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) 手动注册。

## <a name="deploy-requirements-for-azure-virtual-machines"></a>部署 Azure 虚拟机的要求

若要管理计算机内部的设置，需要启用[虚拟机扩展](../../../virtual-machines/extensions/overview.md)，并且该计算机必须具有系统托管标识。 该扩展下载适用的来宾配置分配和相应的依赖项。 该标识用于在计算机读取和写入来宾配置服务时对计算机进行身份验证。 已启用 Arc 的服务器不需要该扩展，因为已连接 Arc 的计算机代理中包含该扩展。

> [!IMPORTANT]
> 需要使用来宾配置扩展和托管标识来管理 Azure 虚拟机。

若要跨多个计算机大规模部署该扩展，请将策略计划 `Deploy prerequisites to enable guest configuration policies on virtual machines` 分配到
包含你要管理的计算机的管理组、订阅或资源组。

如果你希望将该扩展和托管标识部署到单个计算机，请按照该扩展和托管标识的指南进行操作：

- [Azure 策略来宾配置扩展的概述](../../../virtual-machines/extensions/guest-configuration.md)
- [使用 Azure 门户在 VM 上配置 Azure 资源托管标识](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

若要使用来宾配置包以应用配置，需要安装 Azure VM 来宾配置扩展版本 1.29.24 或更高版本。

### <a name="limits-set-on-the-extension"></a>对扩展设置的限制

为了限制该扩展对计算机内运行的应用程序造成影响，来宾配置代理的 CPU 消耗不得超过 CPU 总容量的 5%。 对于内置和自定义的定义都存在此限制。 对于已连接 Arc 的计算机代理中的来宾配置服务也是如此。

### <a name="validation-tools"></a>验证工具

在计算机内，来宾配置代理使用本地工具执行任务。

下表列出了每个受支持的操作系统上使用的本地工具。 对于内置内容，来宾配置会自动处理这些工具的加载。

|操作系统|验证工具|说明|
|-|-|-|
|Windows|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v3| 侧加载到仅由 Azure Policy 使用的文件夹。 不会与 Windows PowerShell DSC 冲突。 PowerShell Core 不会添加到系统路径。|
|Linux|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v3| 侧加载到仅由 Azure Policy 使用的文件夹。 PowerShell Core 不会添加到系统路径。|
|Linux|[Chef InSpec](https://www.chef.io/inspec/) | 在默认位置安装 Chef InSpec 版本 2.2.61，并将其添加到系统路径。 还会安装 InSpec 包的依赖项，包括 Ruby 和 Python。 |

### <a name="validation-frequency"></a>验证频率

来宾配置代理每 5 分钟检查一次新的或更改的来宾分配。 在收到来宾分配后，将按 15 分钟的时间间隔重新检查该配置的设置。 如果分配了多个配置，将按顺序评估每个配置。 长时间运行的配置会影响所有配置的间隔，因为只有在前一个配置完成之后，才会运行下一个配置。

审核完成后，结果将发送到来宾配置服务。
当策略[评估触发器](../how-to/get-compliance-data.md#evaluation-triggers)执行时，会将计算机状态写入到来宾配置资源提供程序。 此更新会使 Azure Policy 评估 Azure 资源管理器属性。 按需 Azure Policy 评估从来宾配置资源提供程序检索最新值。 但是，它不会在计算机中触发新活动。 然后，状态将写入 Azure Resource Graph。

## <a name="supported-client-types"></a>支持的客户端类型

来宾配置策略定义包含新版本。 如果来宾配置客户端不兼容，则会排除 Azure 市场中提供的旧版操作系统。 下表显示了 Azure 映像上支持的操作系统列表。
“.x”文本是表示 Linux 分发的新次要版本的符号。

|发布者|名称|版本|
|-|-|-|
|Amazon|Linux|2|
|Canonical|Ubuntu Server|16.04 - 20.x|
|Credativ|Debian|9 - 10.x|
|Microsoft|Windows Server|2012 - 2019|
|Microsoft|Windows 客户端|Windows 10|
|Oracle|Oracle-Linux|7.x-8.x|
|OpenLogic|CentOS|7.3 -8.x|
|Red Hat|Red Hat Enterprise Linux\*|7.4 - 8.x|
|SUSE|SLES|12 SP3-SP5、15.x|

\* 不支持 Red Hat CoreOS。

来宾配置策略定义支持自定义虚拟机映像，只要它们是上表中的操作系统之一。

## <a name="network-requirements"></a>网络要求

Azure 中的虚拟机可以使用其本地网络适配器或专用链接与来宾配置服务通信。

Azure Arc 计算机使用本地网络基础结构连接到 Azure 服务并报告符合性状态。

### <a name="communicate-over-virtual-networks-in-azure"></a>通过 Azure 中的虚拟网络进行通信

若要与 Azure 中的来宾配置资源提供程序通信，计算机需要对端口 443 上的 Azure 数据中心进行出站访问。 如果 Azure 中的网络不允许出站流量，请使用[网络安全组](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)规则配置异常。 通过[服务标记](../../../virtual-network/service-tags-overview.md)“AzureArcInfrastructure”和“Storage”，可引用来宾配置和存储服务，而不必手动维护 Azure 数据中心的 [IP 范围列表](https://www.microsoft.com/download/details.aspx?id=56519)。 这两个标记都是必需的，因为来宾配置内容包由 Azure 存储托管。

### <a name="communicate-over-private-link-in-azure"></a>通过 Azure 中的专用链接通信

虚拟机可以使用[专用链接](../../../private-link/private-link-overview.md)与来宾配置服务通信。 应用名称为 `EnablePrivateNetworkGC` 且值为 `TRUE` 的标签以启用此功能。 在将来宾配置策略定义应用到计算机之前或之后，可以应用该标记。

流量使用 Azure [虚拟公共 IP 地址](../../../virtual-network/what-is-ip-address-168-63-129-16.md)进行路由，以便使用 Azure 平台资源建立经过身份验证的安全通道。

### <a name="azure-arc-enabled-servers"></a>已启用 Azure Arc 的服务器

位于 Azure 外部、通过 Azure Arc 连接的节点需要连接到来宾配置服务。 有关 [Azure Arc 文档](../../../azure-arc/servers/overview.md)中提供的网络和代理要求的详细信息。

对于专用数据中心内已启用 Arc 的服务器，允许使用以下模式的流量：

- 端口：只需使用 TCP 443 即可进行出站 Internet 访问
- 全局 URL：`*.guestconfiguration.azure.com`

## <a name="assigning-policies-to-machines-outside-of-azure"></a>将策略分配给 Azure 之外的计算机

适用于来宾配置的审核策略定义包括 Microsoft.HybridCompute/machines 资源类型。 将自动包括载入到策略分配范围内的 [Azure Arc for servers](../../../azure-arc/servers/overview.md) 的任何计算机。

## <a name="managed-identity-requirements"></a>托管标识要求

“部署必备组件以在虚拟机上启用来宾配置策略”计划中的策略定义会启用系统分配的托管标识（如果不存在）。 计划中有两个管理标识创建的策略定义。 策略定义中的 IF 条件基于 Azure 中计算机资源的当前状态确保行为正确。

如果计算机当前没有任何托管标识，则有效策略是：[添加系统分配的托管标识，在没有标识的虚拟机上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

如果计算机当前具有用户分配的系统标识，则有效策略是：[添加系统分配的托管标识，在具有用户分配的标识的 VM 上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="availability"></a>可用性

设计高度可用解决方案的客户应考虑[虚拟机](../../../virtual-machines/availability.md)的冗余计划要求，因为来宾分配是 Azure 中计算机资源的扩展。 将来宾分配资源预配到[配对](../../../best-practices-availability-paired-regions.md)的 Azure 区域时，只要该对中至少有一个区域可用，来宾分配报告就可用。 如果 Azure 区域未配对并且变得不可用，则在还原区域之前，无法访问来宾分配的报告。

考虑高可用性应用程序的体系结构时，尤其是在负载均衡器解决方案后面的[可用性集](../../../virtual-machines/availability.md#availability-sets)中预配虚拟机以提供高可用性时，最佳做法是将具有相同参数的相同策略定义分配到解决方案中的所有计算机。 如果可能，跨所有计算机的单个策略分配将提供最少的管理开销。

对于受 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 保护的计算机，请确保辅助站点中的计算机与主站点中的计算处于使用相同参数值的相同定义的 Azure Policy 分配范围内。

## <a name="data-residency"></a>数据驻留

来宾配置会存储/处理客户数据。 客户数据默认复制到[配对区域。](../../../best-practices-availability-paired-regions.md)
对于单个驻留区域，所有客户数据都将在该区域中进行存储和处理。

## <a name="troubleshooting-guest-configuration"></a>来宾配置故障排除

有关对来宾配置进行故障排除的详细信息，请参阅 [Azure Policy 故障排除](../troubleshoot/general.md)。

### <a name="multiple-assignments"></a>多个分配

当策略分配使用不同的参数时，来宾配置策略定义目前仅支持为每台计算机分配一次相同的来宾分配。

### <a name="assignments-to-azure-management-groups"></a>到 Azure 管理组的分配

仅当效果为“AuditIfNotExists”时，才能将“来宾配置”类别中的 Azure Policy 定义分配到管理组。 不支持将效果为“DeployIfNotExists”的策略定义用作到管理组的分配。

### <a name="client-log-files"></a>客户端日志文件

来宾配置扩展将日志文件写入以下位置：

Windows： `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux

- Azure VM：`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`
- 已启用 Arc 的服务器：`/var/lib/GuestConfig/arc_policy_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>远程收集日志

对来宾配置相关配置或模块进行故障排除的第一步应该是按照[如何测试来宾配置包项目](../how-to/guest-configuration-create-test.md)中的步骤使用 cmdlet。
如果未成功，则收集客户端日志有助于诊断问题。

#### <a name="windows"></a>Windows

使用 [Azure VM 运行命令](../../../virtual-machines/windows/run-command.md)从日志文件中捕获信息，下面的示例 PowerShell 脚本可能会很有帮助。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

使用 [Azure VM 运行命令](../../../virtual-machines/linux/run-command.md)从日志文件中捕获信息，下面的示例 Bash 脚本可能会很有帮助。

```bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="agent-files"></a>代理文件

来宾配置代理将内容包下载到计算机中并提取其内容。 若要验证已下载和存储的内容，请查看下面给出的文件夹位置。

Windows： `c:\programdata\guestconfig\configuration`

Linux：`/var/lib/GuestConfig/Configuration`

## <a name="guest-configuration-samples"></a>来宾配置示例

以下位置提供了来宾配置内置策略示例：

- [内置策略定义 - 来宾配置](../samples/built-in-policies.md#guest-configuration)
- [内置计划 - 来宾配置](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy 示例 GitHub 存储库](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>后续步骤

- 设置自定义来宾配置包[开发环境](../how-to/guest-configuration-create-setup.md)。
- 为来宾配置[创建包项目](../how-to/guest-configuration-create.md)。
- 从开发环境[测试包项目](../how-to/guest-configuration-create-test.md)。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](../how-to/guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)。
