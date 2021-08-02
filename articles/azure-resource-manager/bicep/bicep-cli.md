---
title: Bicep CLI 命令和概述
description: 介绍可在 Bicep CLI 中使用的命令。 这些命令包括从 Bicep 生成 Azure 资源管理器模板。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: dd1f292d4ce60353d2f8cecaaa83e38b26bdfaa3
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2021
ms.locfileid: "111540811"
---
# <a name="bicep-cli-commands"></a>Azure CLI 命令

本文介绍可在 Bicep CLI 中使用的命令。 必须[已安装 Bicep CLI](./install.md) 才能运行命令。

本文演示如何在 Azure CLI 中运行命令。 如果不使用 Azure CLI，则无需在命令开始部分使用 `az` 来运行命令。 例如，`az bicep version` 重命名为 ``bicep version``。

## <a name="build"></a>build

`build` 命令将 Bicep 文件转换为 Azure 资源管理器模板（ARM 模板）。 通常，无需运行此命令，因为它会在部署 Bicep 文件时自动运行。 如果想查看基于 Bicep 文件创建的 ARM 模板 JSON，请手动运行该命令。

下面的示例将名为 main.bicep 的 Bicep 文件转换为名为 main.json 的 ARM 模板 。 新文件的创建位置与 Bicep 文件所在的目录相同。

```azurecli
az bicep build --file main.bicep
```

下一个示例将 main.json 保存到其他目录。

```azurecli
 az bicep build --file main.bicep --outdir c:\jsontemplates
```

下一个示例指定要创建的文件的名称和位置。

```azurecli
az bicep build --file main.bicep --outfile c:\jsontemplates\azuredeploy.json
```

若要将文件输出到 `stdout`，请使用：

```azurecli
az bicep build --file main.bicep --stdout
```

## <a name="decompile"></a>反向编译

`decompile` 命令将 ARM 模板 JSON 转换为 Bicep 文件。

```azurecli
az bicep decompile --file main.json
```

有关如何使用此命令的详细信息，请参阅[将 ARM 模板 JSON 反向编译为 Bicep](decompile.md)。

## <a name="install"></a>安装

`install` 命令将 Bicep CLI 添加到本地环境。 有关详细信息，请参阅[安装 Bicep 工具](install.md)。

若要安装最新版本，请运行以下命令：

```azurecli
az bicep install
```

安装特定版本：

```azurecli
az bicep install --version v0.3.255
```

## <a name="list-versions"></a>list-versions

`list-versions` 命令返回 Bicep CLI 的所有可用版本。 使用此命令查看是否要[升级](#upgrade)或[安装](#install)新版本。

```azurecli
az bicep list-versions
```

该命令返回可用版本的数组。

```azurecli
[
  "v0.4.1",
  "v0.3.539",
  "v0.3.255",
  "v0.3.126",
  "v0.3.1",
  "v0.2.328",
  "v0.2.317",
  "v0.2.212",
  "v0.2.59",
  "v0.2.14",
  "v0.2.3",
  "v0.1.226-alpha",
  "v0.1.223-alpha",
  "v0.1.37-alpha",
  "v0.1.1-alpha"
]
```

## <a name="upgrade"></a>升级

`upgrade` 命令使用最新版本更新已安装的版本。

```azurecli
az bicep upgrade
```

## <a name="version"></a>版本

`version` 命令返回已安装的版本。

```azurecli
az bicep version
```

该命令显示版本号。

```azurecli
Bicep CLI version 0.4.1 (e2387595c9)
```

如果尚未安装 Bicep CLI，则会看到一个错误，指示找不到 Bicep CLI。

## <a name="next-steps"></a>后续步骤

若要了解如何部署 Bicep 文件，请参阅：

* [Azure CLI](deploy-cli.md)
* [Cloud Shell](deploy-cloud-shell.md)
* [PowerShell](deploy-powershell.md)
