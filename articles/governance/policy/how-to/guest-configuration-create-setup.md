---
title: 如何安装来宾配置创作模块
description: 了解如何安装用于创建和测试来宾配置策略定义和分配的 PowerShell 模块。
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: c32c405cffb71527e61b68f7fb532487d695743f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868336"
---
# <a name="how-to-setup-a-guest-configuration-authoring-environment"></a>如何设置来宾配置创作环境

PowerShell 模块 `GuestConfiguration` 自动执行创建自定义内容的过程，包括：

- 创建来宾配置内容项目 (.zip)
- 验证包是否符合要求
- 在本地安装来宾配置代理以进行测试
- 验证包是否可用于审核计算机中的设置
- 验证包是否可用于配置计算机中的设置
- 将包发布到 Azure 存储
- 创建策略定义
- 发布策略

版本 `3.4.2` 中引入了对通过来宾配置应用配置的支持。

> [!IMPORTANT]
> 审核环境状态的自定义包为正式发布版，但应用配置的包为预览版。 **以下限制适用：**
> 
> 若要测试在 Linux 上创建和应用配置，`GuestConfiguration` 模块仅在 Ubuntu 18 上可用，但该模块生成的包和策略定义可用于 Azure 或 Arc 支持的所有 Linux 发行版/版本。
>
> MacOS 上的测试包不可用。

### <a name="base-requirements"></a>基本要求

可以安装模块的操作系统：

- Ubuntu 18
- Windows

该模块可以安装在运行 PowerShell 7 的计算机上。 安装下面列出的 PowerShell 版本。

| OS | PowerShell 版本 |
|-|-|
|Windows|[PowerShell 7.1.3](https://github.com/PowerShell/PowerShell/releases/tag/v7.1.3)|
|Ubuntu 18|[PowerShell 7.2.0-preview.6](https://github.com/PowerShell/PowerShell/releases/tag/v7.2.0-preview.6)|

`GuestConfiguration` 模块需要以下软件：

- Azure PowerShell 5.9.0 或更高版本。 所需的 Az 模块随 `GuestConfiguration` 模块一起自动安装，也可以按照[这些说明](/powershell/azure/install-az-ps)进行操作。
  - 只需要 Az 模块“Az.Accounts”、“Az.Resources”和“Az.Storage”。
- `PSDesiredStateConfiguration` 模块。

### <a name="install-the-module-from-the-powershell-gallery"></a>从 PowerShell 库安装模块

若要在 Windows 或 Linux 上安装 `GuestConfiguration` 模块，请在 PowerShell 7 中运行以下命令。

```powershell
# Install the guest configuration DSC resource module from PowerShell Gallery
Install-Module -Name GuestConfiguration
```

验证模块是否已导入：

```powershell
# Get a list of commands for the imported GuestConfiguration module
Get-Command -Module 'GuestConfiguration'
```

## <a name="update-and-import-the-psdesiredstateconfiguration-module-on-linux"></a>在 Linux 中更新并导入 PSDesiredStateConfiguration 模块

从 PowerShell 7.2 预览版 6 开始，DSC 作为 PowerShell 库中的模块独立于 PowerShell 发布。 若要在 Linux 上的 PowerShell 环境中安装 DSC 版本 3，请运行以下命令。

```powershell
# Install the DSC module before compiling using the Configuration keyword
Install-Module PSDesiredStateConfiguration -AllowPreRelease -Force
```

## <a name="next-steps"></a>后续步骤

- 为来宾配置[创建包项目](./guest-configuration-create.md)。
- 测试开发环境中的[包项目](./guest-configuration-create-test.md)。
- 使用 `GuestConfiguration` 模块[创建 Azure Policy 定义](./guest-configuration-create-definition.md)，用于大规模管理环境。
- 使用 Azure 门户[分配自定义策略定义](../assign-policy-portal.md)。
- 了解如何查看[来宾配置策略分配的符合性详细信息](./determine-non-compliance.md#compliance-details-for-guest-configuration)。
