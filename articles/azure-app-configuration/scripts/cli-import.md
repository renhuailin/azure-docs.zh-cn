---
title: Azure CLI 脚本示例 - 导入到应用配置存储区
titleSuffix: Azure App Configuration
description: 使用 Azure CLI 脚本 - 将配置导入到 Azure 应用配置
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2a8bd22629bf0aa269125187a77710f6dc9fd93e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931297"
---
# <a name="import-to-an-azure-app-configuration-store"></a>导入 Azure 应用配置存储区

此示例脚本将密钥值设置导入 Azure 应用程序配置存储区。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="sample-script"></a>示例脚本

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令导入应用程序配置存储区。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | 导入应用程序配置存储区资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。

可在 [Azure 应用程序配置 CLI 示例](../cli-samples.md)中找到其他应用程序配置 CLI 脚本示例。
