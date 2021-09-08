---
title: 如何测试来宾配置包项目
description: 有关创建和测试对计算机进行审计或应用配置的包的体验。
ms.date: 07/20/2021
ms.topic: how-to
ms.openlocfilehash: 927e048f59d74b4137710c2f0a1f284adec0cdcb
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868354"
---
# <a name="how-to-test-guest-configuration-package-artifacts"></a>如何测试来宾配置包项目

PowerShell 模块 `GuestConfiguration` 包含用于自动测试 Azure 外部的配置包的工具。 在 Azure 或 Arc 连接的环境中继续进行测试之前，使用这些工具查找问题并快速进行循环访问。

在开始进行测试之前，请按照[如何设置来宾配置创作环境](./guest-configuration-create-setup.md)和[如何创建自定义来宾配置包项目](./guest-configuration-create.md)页面中的步骤创建和发布自定义来宾配置包。

> [!IMPORTANT]
> 审核环境状态的自定义包为正式发布版，但应用配置的包为预览版。 **以下限制适用：**
> 
> 若要使用应用配置的来宾配置包，需要安装 Azure VM 来宾配置扩展版本 1.29.24 或更高版本，或者 Arc 代理 1.10.0 或更高版本 。
> 
> 若要测试在 Linux 上创建和应用配置，`GuestConfiguration` 模块仅在 Ubuntu 18 上可用，但该模块生成的包和策略可用于 Azure 或 Arc 支持的所有 Linux 发行版/版本。
>
> MacOS 上的测试包不可用。

你可以从工作站或持续集成和持续部署 (CI/CD) 环境测试该包。  `GuestConfiguration` 模块包含用于开发环境的代理，该代理与在已启用 Azure 或 Arc 的计算机中使用的代理相同。 代理包括适用于 Windows 的 PowerShell 7.1.3 的独立实例和适用于 Linux 的 7.2.0-preview.7，因此测试包的脚本环境与使用来宾配置管理的计算机一致。

Azure 和已启用 Arc 的计算机中的代理服务在 Windows 中以 LocalSystem 帐户运行，在 Linux 中以根帐户运行。 在特权安全上下文中运行以下命令，以获得最佳结果。

要在 Windows 中以 LocalSystem 帐户运行 PowerShell，请使用 SysInternals 工具 [PSExec](/sysinternals/downloads/psexec)。

要在 Linux 中以根帐户运行 PowerShell，请使用 [Su 命令](https://manpages.ubuntu.com/manpages/man1/su.1.html)。

## <a name="validate-the-configuration-package-meets-requirements"></a>验证配置包是否满足要求

首先使用 `Get-GuestConfigurationPacakgeComplianceStatus ` 测试配置包是否满足基本要求。 该命令会验证以下包要求。

- MOF 在正确的位置存在且有效
- 所需模块/依赖项以正确的版本存在，没有重复项
- 验证包是否签名（可选）
- 测试 `Test` 和 `Get` 是否返回有关合规性状态的信息

`Get-GuestConfigurationPackageComplianceStatus ` cmdlet 的参数：

- **包**：来宾配置包的文件路径或 URI。
- Parameter：以哈希表格式提供的策略参数。

首次运行此命令时，来宾配置代理会安装在测试计算机上，Windows 上的路径为 `c:\programdata\GuestConfig\bin`，Linux 上的路径为 `/var/lib/GuestConfig/bin`。 用户帐户无法访问此路径，因此该命令需要特权提升。

运行以下命令以测试包：

在 Windows 中，通过提升的 PowerShell 7 会话运行。

```powershell
# Get the current compliance results for the local machine
Get-GuestConfigurationPackageComplianceStatus -Package ./MyConfig.zip
```

在 Linux 中，通过使用 sudo 运行 PowerShell。

```bash
# Get the current compliance results for the local machine
sudo pwsh -command 'Get-GuestConfigurationPackageComplianceStatus -Package ./MyConfig.zip'
```

该命令输出一个对象，其中包含每个资源的合规性状态和详细信息。

```powershell
  complianceStatus  resources
  ----------------  ---------
  True              @{BuiltInAccount=localSystem; ConfigurationName=MyConfig; Credential=; Dependencies=System.Obje…
```

#### <a name="test-the-configuration-package-can-apply-a-configuration"></a>测试配置包是否可以应用配置

最后，如果配置包模式为 `AuditandSet`，则使用命令 `Set` 测试 `Start-GuestConfigurationPackageRemediation` 方法是否可以对本地计算机应用设置。

> [!IMPORTANT]
> 此命令尝试在其运行的本地环境中做出更改。

`Start-GuestConfigurationPackageRemediation` cmdlet 的参数：

- **包**：来宾配置包的完整路径。

在 Windows 中，通过提升的 PowerShell 7 会话运行。

```powershell
# Test applying the configuration to local machine
Start-GuestConfigurationPackageRemediation -Package ./MyConfig.zip
```

在 Linux 中，通过使用 sudo 运行 PowerShell。

```bash
# Test applying the configuration to local machine
sudo pwsh -command 'Start-GuestConfigurationPackageRemediation -Package ./MyConfig.zip'
```

除非发生错误，否则该命令不会返回输出。 要对在 `Set` 期间发生的事件进行详细的故障排除，请使用 `-verbose` 参数。

运行命令 `Start-GuestConfigurationPackageRemediation` 后，可以再次运行命令 `Get-GuestConfigurationComplianceStatus`，以确认计算机现在处于正确的状态。

## <a name="next-steps"></a>后续步骤

- [发布包项目](./guest-configuration-create-publish.md)，以便计算机可以访问它。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](./guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](./determine-non-compliance.md#compliance-details-for-guest-configuration)。
