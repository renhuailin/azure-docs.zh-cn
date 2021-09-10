---
title: 设置 Bicep 开发和部署环境
description: 如何配置 Bicep 开发和部署环境
ms.topic: conceptual
ms.date: 08/26/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: c95c05923d9685232c50d694f2b858e2de9e4776
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099559"
---
# <a name="install-bicep-tools"></a>安装 Bicep 工具

确保你的环境已针对开发和部署 Bicep 文件进行设置。

## <a name="development-environment"></a>开发环境

若要创建 Bicep 文件，需要一个很好的 Bicep 编辑器。 我们建议：

- Visual Studio Code - 如果尚未安装 Visual Studio Code，请[安装](https://code.visualstudio.com/)。
- 用于 Visual Studio Code 的 Bicep 扩展。  装有 Bicep 扩展的 Visual Studio Code 提供语言支持和资源自动完成功能。 该扩展有助于你创建和验证 Bicep 文件。

  要安装该扩展，请在“扩展”选项卡或 [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) 中搜索“bicep”。

  选择“安装”。

  :::image type="content" source="./media/install/install-extension.png" alt-text="安装 Bicep 扩展":::

要验证是否已安装该扩展，请打开任何文件扩展名为 `.bicep` 的文件。 在右下角，应该会看到语言模式更改为 Bicep。

:::image type="content" source="./media/install/language-mode.png" alt-text="Bicep 语言模式":::

## <a name="deployment-environment"></a>部署环境

要获取部署 Bicep 文件所需的命令，最简单的方法是安装最新版本的 Azure CLI。 还可以使用 PowerShell，但它需要额外的安装。

- [Azure CLI](#azure-cli)
- [Azure PowerShell](#azure-powershell)
- [手动安装](#install-manually)

### <a name="azure-cli"></a>Azure CLI

必须安装 Azure CLI 2.20.0 或更高版本。 要安装或更新 Azure CLI，请参阅：

- [在 Windows 上安装 Azure CLI](/cli/azure/install-azure-cli-windows)
- [在 Linux 上安装 Azure CLI](/cli/azure/install-azure-cli-linux)
- [在 macOS 上安装 Azure CLI](/cli/azure/install-azure-cli-macos)

要验证当前的版本，请运行：

```azurecli
az --version
```

现在，你已拥有[部署](deploy-cli.md)和[反向编译](decompile.md) Bicep 文件所需的一切。 一切已准备就绪，因为在执行需要 Bicep CLI 的命令时，Azure CLI 2.20.0 或更高版本会自动安装 Bicep CLI。

要手动启动 Bicep CLI 安装，请使用：

```azurecli
az bicep install
```

要升级到最新版本，请使用：

```azurecli
az bicep upgrade
```

若要验证安装，请使用：

```azurecli
az bicep version
```

有关更多命令，请参阅 [Bicep CLI](bicep-cli.md)。

> [!IMPORTANT]
> Azure CLI 安装独立的 Bicep CLI 实例。 此实例不会与你手动安装的任何版本发生冲突。 Azure CLI 不会将 Bicep CLI 添加到你的路径。

#### <a name="install-on-an-air-gapped-cloud"></a>在气隙云中安装

若要在气隙环境中安装 Bicep CLI，需要手动下载 Bicep CLI 可执行文件并将其保存到特定位置。

- **Linux**

    1. 在非气隙环境中，从 [Bicep 发布页](https://github.com/Azure/bicep/releases/latest/)下载 bicep-linux-x64。
    1. 将可执行文件复制到气隙计算机上的 $HOME/.azure/bin 目录中。

- **macOS**

    1. 在非气隙环境中，从 [Bicep 发布页](https://github.com/Azure/bicep/releases/latest/)下载 bicep-osx-x64。
    1. 将可执行文件复制到气隙计算机上的 $HOME/.azure/bin 目录中。

- **Windows**

    1. 在非气隙环境中，从 [Bicep 发布页](https://github.com/Azure/bicep/releases/latest/)下载 bicep-win-x64.exe。
    1. 将可执行文件复制到气隙计算机上的 %UserProfile%/.azure/bin 目录中。

注意 `bicep install` 和 `bicep upgrade` 命令在气隙环境中不起作用。

### <a name="azure-powershell"></a>Azure PowerShell

必须安装 Azure PowerShell 5.6.0 或更高版本。 要更新或安装 Azure PowerShell，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。

Azure PowerShell 不会自动安装 Bicep CLI。 你必须[手动安装 Bicep CLI](#install-manually)。

> [!IMPORTANT]
> Azure CLI 安装的 Bicep CLI 的独立实例不适用于 PowerShell 命令。 如果你没有手动安装 Bicep CLI，Azure PowerShell 部署将失败。

手动安装 Bicep CLI 时，请使用 `bicep` 语法运行 Bicep 命令，不要使用 Azure CLI 的 `az bicep` 语法。

要部署 Bicep 文件，请使用 Bicep CLI 0.3.1 或更高版本。 要检查你的 Bicep CLI 版本，请运行：

```cmd
bicep --version
```

### <a name="install-manually"></a>手动安装

以下方法安装 Bicep CLI 并将其添加到你的路径。 对于 Azure CLI 以外的任何用途，必须手动安装。

- [Linux](#linux)
- [macOS](#macos)
- [Windows](#windows)

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!
```

> [!NOTE]
> 对于像 [Alpine](https://alpinelinux.org/) 这样的轻量级 Linux 分发版，请在前面的脚本中使用 bicep-linux-musl-x64，而不是 bicep-linux-x64。

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>通过 homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
```

##### <a name="via-bash"></a>通过 BASH

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows Installer

下载并运行[最新的 Windows 安装程序](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe)。 该安装程序不需要管理权限。 安装完成后，会将 Bicep CLI 添加到用户路径。 关闭并重新打开任何打开的命令 shell 窗口，以使路径更改生效。

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>通过 PowerShell 手动

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

### <a name="install-the-nightly-builds"></a>安装每日版

如果要在发布之前尝试最新的预发行版 Bicep，请参阅[安装每日版](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md)。

> [!WARNING]
> 这些预发布版本更有可能存在已知或未知 bug。

## <a name="next-steps"></a>后续步骤

有关使用 Visual Studio Code 和 Bicep 扩展的详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。
