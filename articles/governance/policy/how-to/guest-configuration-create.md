---
title: 如何创建自定义来宾配置包项目
description: 了解如何创建来宾配置包文件。
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: 12767e40ef99cf218666b6dc540a5ae1c2e2bffa
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772535"
---
# <a name="how-to-create-custom-guest-configuration-package-artifacts"></a>如何创建自定义来宾配置包项目

在开始之前，最好先阅读[来宾配置](../concepts/guest-configuration.md)的概述页面。

审核/配置 Windows 和 Linux 时，来宾配置使用 [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC)。 DSC 配置定义了计算机应处于的条件。

> [!IMPORTANT]
> 审核环境状态的自定义包为正式发布版，但应用配置的包为预览版。 **以下限制适用：**
> 
> 若要使用应用配置的来宾配置包，需要 Azure VM 来宾配置扩展版本 1.29.24 或更高版本，或者 Arc 代理 1.10.0 或更高版本 。
> 
> 若要测试在 Linux 上创建和应用配置，`GuestConfiguration` 模块仅在 Ubuntu 18 上可用，但该模块生成的包和策略可用于 Azure 或 Arc 支持的所有 Linux 发行版/版本。
>
> MacOS 上的测试包不可用。
> 
> 不要在自定义内容包中使用机密或保密信息。

请使用以下步骤来创建自己的配置，用于管理 Azure 或非 Azure 计算机的状态。

## <a name="install-powershell-7-and-required-powershell-modules"></a>安装 PowerShell 7 和所需的 PowerShell 模块

首先，请确保已按照[如何设置来宾配置创作环境](./guest-configuration-create-setup.md)页上的所有步骤，安装了操作系统所需的 PowerShell 版本、`GuestConfiguration` 模块，如果需要，还要安装 `PSDesiredStateConfiguration` 模块。

## <a name="author-a-configuration"></a>创作配置

创建配置包之前，请创作和编译 DSC 配置。
如果需要，可以使用针对 Windows 和 Linux 提供示例配置。

> [!IMPORTANT]
> 编译 Windows 的配置时，请使用 `PSDesiredStateConfiguration` 的版本 `2.0.5`（稳定版本）。 在为 Linux 编译配置时，请安装预发布版本 `3.0.0`。

Windows 版 DSC 的[入门文档](/powershell/scripting/dsc/getting-started/wingettingstarted#define-a-configuration-and-generate-the-configuration-document)中提供了示例。

对于 Linux，需要使用 [PowerShell 类](/powershell/scripting/dsc/resources/authoringResourceClass)来创建自定义 DSC 资源模块。
PowerShell 文档页[使用 PowerShell 类编写自定义 DSC 资源](/powershell/scripting/dsc/resources/authoringResourceClass)中提供了自定义资源和配置的完整示例（并已使用来宾配置进行了测试）。

## <a name="create-a-configuration-package-artifact"></a>创建配置包项目

编译 MOF 后，支持文件必须打包在一起。
来宾配置使用已完成的包来创建 Azure Policy 定义。

`New-GuestConfigurationPackage` cmdlet 创建包。 配置所需的模块必须在开发环境的 `$Env:PSModulePath` 中提供，以便模块中的命令可以将它们添加到包中。

创建 Windows 内容时 `New-GuestConfigurationPackage` cmdlet 的参数：

- 名称：来宾配置包名称。
- **配置**：已编译 DSC 配置文档完整路径。
- **路径**：输出文件夹路径。 此参数是可选的。 如果未指定，则在当前目录中创建包。
- 类型：（Audit 或 AuditandSet）确定配置是应当只审核，还是应用配置并更改计算机的状态。 默认设置为“审核”。

此步骤不需要提升特权。 如果多次运行该命令，则可以使用 Force cmdlet 覆盖现有包。

以下命令创建包项目：

```powershell
# Create a package that will only audit compliance
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type Audit `
  -Force
```

```powershell
# Create a package that will audit and apply the configuration (Set)
New-GuestConfigurationPackage `
  -Name 'MyConfig' `
  -Configuration './Config/MyConfig.mof' `
  -Type AuditAndSet `
  -Force
```

将返回一个对象，其中包含已创建的包的名称和路径。

```
Name      Path                                                    
----      ----                                                    
MyConfig  /Users/.../MyConfig/MyConfig.zip
```

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>来宾配置项目的预期内容

来宾配置使用已完成的包来创建 Azure Policy 定义。 包中包含：

- 作为 MOF 的已编译 DSC 配置
- 模块文件夹
  - GuestConfiguration 模块
  - DscNativeResources 模块
  - MOF 所需的 DSC 资源模块
- 一个元配置文件，存储包 `type` 和 `version`

PowerShell cmdlet 可创建包 .zip 文件。 不需要根级别文件夹或版本文件夹。 包格式必须是 .zip 文件，未压缩时总大小不能超过 100 MB。

## <a name="extending-guest-configuration-with-third-party-tools"></a>使用第三方工具扩展来宾配置

可以扩展来宾配置的项目包以包含第三方工具。 扩展来宾配置要求开发两个组件。

- 处理与管理第三方工具相关的所有活动的 Desired State Configuration 资源
  - 安装
  - Invoke
  - 转换输出
- 供工具以本机方式使用的格式正确的内容

如果社区解决方案尚不存在，则 DSC 资源需要自定义开发。 可以通过在 PowerShell 库中搜索标记 [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22) 来发现社区解决方案。

> [!NOTE]
> 来宾配置扩展性是“自带许可”方案。 使用之前，请确保已满足任何第三方工具的条款和条件。

在开发环境中安装 DSC 资源之后，使用 `New-GuestConfigurationPackage` 的 FilesToInclude 参数在内容项目中包含第三方平台的内容。

## <a name="next-steps"></a>后续步骤

- 从开发环境[测试包项目](./guest-configuration-create-test.md)。
- [发布包项目](./guest-configuration-create-publish.md)，以便计算机可以访问它。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](./guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的合规性详细信息](./determine-non-compliance.md#compliance-details-for-guest-configuration)。
