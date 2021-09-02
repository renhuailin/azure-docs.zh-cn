---
title: 使用 Azure CLI 连接到 Azure 德国 | Microsoft Docs
description: 有关使用 Azure CLI 管理 Azure 德国中的订阅的信息
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs, devx-track-azurecli
ms.openlocfilehash: b5fb2f9b55fbe949e2c171ee7bea989c1741ab5d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029087"
---
# <a name="connect-to-azure-germany-by-using-azure-cli"></a>使用 Azure CLI 连接到 Azure 德国

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

若要使用 Azure 命令行接口 (Azure CLI)，需要连接到 Azure 德国，而不是全局 Azure。 Azure CLI 可以用于通过脚本管理大型订阅或访问当前在 Azure 门户中不可用的功能。 如果已在全球 Azure 中使用过 Azure CLI，会发现它们之间基本相同。  

## <a name="azure-cli"></a>Azure CLI
可通过多种方式来[安装 Azure CLI](/cli/azure/install-az-cli2)。  

若要连接到 Azure 德国，请设置云：

```azurecli
az cloud set --name AzureGermanCloud
```

设置云后，即可登录：

```azurecli
az login --username your-user-name@your-tenant.onmicrosoft.de
```

若要确认云已正确设置为 AzureGermanCloud，请运行以下任一命令，然后验证 AzureGermanCloud 项的 `isActive` 标志是否设置为 `true`：

```azurecli
az cloud list
```

```azurecli
az cloud list --output table
```

## <a name="azure-classic-cli"></a>Azure 经典 CLI
可通过多种方式来[安装 Azure 经典 CLI](/cli/azure/install-azure-cli)。 如果已安装节点，则最简单的方法是安装 npm 包。

若要通过 npm 包安装 CLI，请确保已下载并安装[最新的 Node.js 和 npm](https://nodejs.org/en/download/package-manager/)。 然后，运行 npm install 以安装 azure-cli 包 ：

```bash
npm install -g azure-cli
```

在 Linux 分发版中，可能需要使用 **sudo** 才能成功运行 **npm** 命令，如下所示：

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> 如果需要在 Linux 分发版或 OS 中安装或更新 Node.js 和 npm，建议安装最新的 Node.js LTS 版本 (4.x)。 如果使用旧版本，可能会遇到安装错误。


安装 Azure CLI 后，登录到 Azure 德国：

```console
azure login --username your-user-name@your-tenant.onmicrosoft.de  --environment AzureGermanCloud
```

登录后，即可照常运行以下 Azure CLI 命令：

```console
azure webapp list my-resource-group
```

## <a name="next-steps"></a>后续步骤
有关连接到 Azure 德国的详细信息，请参阅以下资源：

* [使用 PowerShell 连接到 Azure 德国](./germany-get-started-connect-with-ps.md)
* [使用 Visual Studio 连接到 Azure 德国](./germany-get-started-connect-with-vs.md)
* [使用 Azure 门户连接到 Azure 德国](./germany-get-started-connect-with-portal.md)