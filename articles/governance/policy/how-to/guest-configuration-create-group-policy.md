---
title: 如何根据组策略创建来宾配置策略
description: 了解如何将组策略转换为策略定义。
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 12bd1c905c254f16da170cde4a4426a2aa0cb263
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772292"
---
# <a name="how-to-create-a-guest-configuration-policy-from-group-policy"></a>如何根据组策略创建来宾配置策略

开始之前，最好阅读[来宾配置](../concepts/guest-configuration.md)的概述页面，以及[如何为来宾配置配置修正选项](../concepts/guest-configuration-policy-effects.md)中有关来宾配置策略影响的详细信息。

> [!IMPORTANT]
> 将组策略转换为来宾配置为预览版。 并非所有类型的组策略设置都具有可用于 PowerShell 7 的对应 DSC 资源。
>
> 此页上的所有命令都必须在 Windows PowerShell 5.1 中运行。
> 然后应使用 PowerShell 7.1.3 或更高版本中的 `GuestConfiguration` 模块打包生成的输出 MOF 文件。
> 
> 使用 AuditIfNotExists 的自定义来宾配置策略定义已正式发布，但使用 DeployIfNotExists 的来宾配置定义为预览版  。
> 
> Azure 虚拟机需要来宾配置扩展。 若要在所有计算机上大规模部署此扩展，请分配以下策略计划：`Deploy prerequisites to enable guest configuration policies on
> virtual machines`
>
> 不要在自定义内容包中使用机密或保密信息。

开源社区已发布了 [BaselineManagement](https://github.com/microsoft/BaselineManagement) 模块，用于将导出的[组策略](/support/windows-server/group-policy/group-policy-overview)模板转换为 PowerShell DSC 格式。 结合 `GuestConfiguration` 模块，可以通过导出的组策略对象为 Windows 创建来宾配置包。 然后，可以使用来宾配置包通过本地策略来审核或配置服务器，即使它们没有加入域也是如此。

在本指南中，我们将逐步介绍根据组策略对象 (GPO) 创建 Azure Policy 来宾配置包的过程。

## <a name="download-required-powershell-modules"></a>下载所需的 PowerShell 模块

在 PowerShell 中安装所有必需的模块：

```powershell
Install-Module guestconfiguration
Install-Module baselinemanagement
```

要从 Active Directory 环境备份组策略对象 (GPO)，需要远程服务器管理工​​具包 (RSAT) 中提供的 PowerShell 命令。

在 Windows 10 上为组策略管理控制台启用 RSAT：

```powerShell
Add-WindowsCapability -Online -Name 'Rsat.GroupPolicy.Management.Tools~~~~0.0.1.0'
Add-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0'
```

## <a name="export-and-convert-group-policy-to-guest-configuration"></a>导出组策略并将其转换为来宾配置

有三个选项可用于导出组策略文件并将它们转换为 DSC，以在来宾配置中使用。

- 导出单个组策略对象
- 导出 OU 的合并的组策略对象
- 从计算机内导出合并的组策略对象

### <a name="single-group-policy-object"></a>单个组策略对象

使用 `Group Policy` 模块中的命令来标识要导出的组策略对象的 GUID。 在大型环境中，请考虑通过管道将输出传递到 `where-object` 并按名称进行筛选。

在已加入域的 Windows 计算机上的 Windows PowerShell 5.1 环境中运行以下每条命令： 

```powershell
# List all Group Policy Objects
Get-GPO -all
```

将组策略备份到文件。 该命令也接受“Name”参数，但使用策略的 GUID 更不容易出错。

```powershell
Backup-GPO -Guid 'f0cf623e-ae29-4768-9bb4-406cce1f3cff' -Path C:\gpobackup\
```

```

The output of the command returns the details of the files.

ConfigurationScript                   Configuration                   Name
-------------------                   -------------                   ----
C:\convertfromgpo\myCustomPolicy1.ps1 C:\convertfromgpo\localhost.mof myCustomPolicy1
```

查看导出的 PowerShell 脚本，以确保所有设置都已填充并且未显示错误消息。 按照[如何创建自定义来宾配置包项目](./guest-configuration-create.md)页中的指导进行操作，使用 MOF 文件创建新的配置包。
应在 PowerShell 7 环境中运行创建和测试来宾配置包的步骤。

### <a name="merged-group-policy-objects-for-an-ou"></a>OU 的合并的组策略对象

在指定的组织单位导出组策略对象的合并组合（类似于策略的结果集）。 合并操作将考虑链接状态、强制状态和访问权限，但不会考虑 WMI 筛选器。

```powershell
Merge-GPOsFromOU -Path C:\mergedfromou\ -OUDistinguishedName 'OU=mySubOU,OU=myOU,DC=mydomain,DC=local' -OutputConfigurationScript
```

此命令的输出将返回文件的详细信息。

```powershell
Configuration                                Name    ConfigurationScript
-------------                                ----    -------------------
C:\mergedfromou\mySubOU\output\localhost.mof mySubOU C:\mergedfromou\mySubOU\output\mySubOU.ps1
```

### <a name="merged-group-policy-objects-from-within-a-machine"></a>来自计算机的合并的组策略对象

还可以通过从 Windows PowerShell 运行 `Merge-GPOs` 命令来合并应用于特定计算机的策略。 仅当从计算机内部进行合并时，才会评估 WMI 筛选器。

```powershell
Merge-GPOs -OutputConfigurationScript -Path c:\mergedgpo
```

此命令的输出将返回文件的详细信息。

```powershell
Configuration              Name                  ConfigurationScript                    PolicyDetails
-------------              ----                  -------------------                    -------------
C:\mergedgpo\localhost.mof MergedGroupPolicy_ws1 C:\mergedgpo\MergedGroupPolicy_ws1.ps1 {@{Name=myEnforcedPolicy; Ap...
```

## <a name="optional-download-sample-group-policy-files-for-testing"></a>可选：下载示例组策略文件进行测试

如果尚未准备好从 Active Directory 环境导出组策略文件，可以从 Windows 安全与合规工具包中下载 Windows Server 安全基线。

创建目录并从 Windows 安全合规性工具包下载 Windows Server 2019 安全基线。

```azurepowershell-interactive
# Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
```

取消阻止并展开下载的 Server 2019 基线。

```azurepowershell-interactive
Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
```

使用 MapGuidsToGpoNames.ps1 验证 Server 2019 基线内容。

```azurepowershell-interactive
# Show content details of downloaded GPOs
C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
```

## <a name="next-steps"></a>后续步骤

- 为来宾配置[创建包项目](./guest-configuration-create.md)。
- 从开发环境[测试包项目](./guest-configuration-create-test.md)。
- [发布包项目](./guest-configuration-create-publish.md)，以便计算机可以访问它。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](./guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](./determine-non-compliance.md#compliance-details-for-guest-configuration)。
