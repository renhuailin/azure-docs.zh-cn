---
title: 设置 Bicep 开发和部署环境
description: 如何配置 Bicep 开发和部署环境
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0e62e6a4633bee09fcbe8b783118cc95ccd5702e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626095"
---
# <a name="install-bicep-preview"></a>安装 Bicep（预览版）

了解如何设置 Bicep 开发和部署环境。

## <a name="development-environment"></a>开发环境

要获得最佳 Bicep 创作体验，需要两个组件：

- 用于 Visual Studio Code 的 Bicep 扩展。 若要创建 Bicep 文件，需要一个很好的 Bicep 编辑器。 建议使用装有 [Bicep 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)的 [Visual Studio Code](https://code.visualstudio.com/)。 这些工具提供语言支持和资源自动完成功能。 它们可帮助创建和验证 Bicep 文件。 有关使用 Visual Studio Code 和 Bicep 扩展的详细信息，请参阅[快速入门：使用 Visual Studio Code 创建 Bicep 文件](./quickstart-create-bicep-use-visual-studio-code.md)。
- Bicep CLI。 使用 Bicep CLI 将 Bicep 文件编译到 ARM JSON 模板，并将 ARM JSON 模板反编译为 Bicep 文件。 有关安装说明，请参阅[安装 Bicep CLI](#install-manually)。

## <a name="deployment-environment"></a>部署环境

若要部署本地 Bicep 文件，需要两个组件：

- **Azure CLI 2.20.0 或更高版本，或者 Azure PowerShell 5.6.0 或更高版本**。 有关安装说明，请参阅：

  - [安装 Azure PowerShell](/powershell/azure/install-az-ps)
  - [在 Windows 上安装 Azure CLI](/cli/azure/install-azure-cli-windows)
  - [在 Linux 上安装 Azure CLI](/cli/azure/install-azure-cli-linux)
  - [在 macOS 上安装 Azure CLI](/cli/azure/install-azure-cli-macos)

  > [!NOTE]
  > 目前，Azure CLI 和 Azure PowerShell 只能部署本地 Bicep 文件。 有关使用 Azure CLI 部署 Bicep 文件的详细信息，请参阅[部署 - CLI](./deploy-cli.md#deploy-remote-template)。 有关使用 Azure PowerShell 部署 Bicep 文件的详细信息，请参阅[部署 - PowerShell]( ./deploy-powershell.md#deploy-remote-template)。

- Bicep CLI。 部署之前 Bicep CLI 需要将 Bicep 文件编译到 JSON 模板。 有关安装说明，请参阅[安装 Bicep CLI](#install-bicep-cli)。

安装组件后，可以使用以下内容部署 Bicep 文件：

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>安装 Bicep CLI

- 若要使用 Bicep CLI 编译和反向编译 Bicep 文件，请参阅[手动安装](#install-manually)。
- 若要使用 Azure CLI 部署 Bicep 文件，请参阅[与 Azure CLI 一起使用](#use-with-azure-cli)。
- 若要使用 Azure PowerShell 部署 Bicep 文件，请参阅[与 Azure PowerShell 一起使用](#use-with-azure-powershell)。

### <a name="use-with-azure-cli"></a>与 Azure CLI 一起使用

安装了 Azure CLI 2.20.0 或更高版本后，当执行依赖于 Bicep CLI 的命令时，会自动安装 Bicep CLI。 例如：

```azurecli
az deployment group create --template-file azuredeploy.bicep --resource-group myResourceGroup
```

或

```azurecli
az bicep ...
```

还可以使用内置命令手动安装 CLI：

```bash
az bicep install
```

升级到最新版本：

```bash
az bicep upgrade
```

安装特定版本：

```bash
az bicep install --version v0.3.126
```

> [!IMPORTANT]
> Azure CLI 安装独立版本的 Bicep CLI，该版本与你可能拥有的所有其他 Bicep 安装不冲突，并且 Azure CLI 不会将 Bicep CLI 添加到你的路径。 若要使用 Bicep CLI 编译/反向编译 Bicep 文件，或者若要使用 Azure PowerShell 部署 Bicep 文件，请参阅[手动安装](#install-manually)或[与 Azure PowerShell 一起使用](#use-with-azure-powershell)。

列出所有可用版本的 Bicep CLI：

```bash
az bicep list-versions
```

显示已安装的版本：

```bash
az bicep version
```

### <a name="use-with-azure-powershell"></a>与 Azure PowerShell 一起使用

Azure PowerShell 尚不能安装 Bicep CLI。 Azure PowerShell（v5.6.0 或更高版本）预计已安装 Bicep CLI 并在路径中提供。 遵循[手动安装方法](#install-manually)之一。

若要部署 Bicep 文件，需要 Bicep CLI 0.3.1 或更高版本。 检查 Bicep CLI 版本：

```cmd
bicep --version
```

> [!IMPORTANT]
> Azure CLI 安装其自包含 Bicep CLI 版本。 即使为 Azure CLI 安装了所需版本，Azure PowerShell 部署也会失败。

安装 Bicep CLI 后，只要部署 cmdlet 需要，就会调用 Bicep CLI。 例如：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile azuredeploy.bicep
```

### <a name="install-manually"></a>手动安装

以下方法安装 Bicep CLI 并将其添加到你的路径。

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

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>通过 homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
```

##### <a name="macos-manual-install"></a>macOS 手动安装

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

下载并运行[最新的 Windows 安装程序](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe)。 安装程序不需要管理权限。 安装完成后，会将 Bicep CLI 添加到用户路径。 关闭并重新打开任何打开的命令行界面窗口，以使路径更改生效。

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

## <a name="install-the-nightly-builds"></a>安装每日版

如果要在发布之前尝试最新的预发行版 Bicep，请参阅[安装每日版](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md)。

> [!WARNING]
> 这些预发布版本更有可能存在已知或未知 bug。

## <a name="next-steps"></a>后续步骤

开始浏览 [Bicep 快速入门](./quickstart-create-bicep-use-visual-studio-code.md)。
