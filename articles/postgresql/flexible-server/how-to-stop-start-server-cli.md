---
title: 停止/启动 - Azure CLI - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍如何通过 Azure CLI 在 Azure Database for PostgreSQL 中执行停止/启动操作。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 05/29/2021
ms.openlocfilehash: bebcc9e29620c8e739735553dd64e791c5bcdb00
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026014"
---
# <a name="stopstart-azure-database-for-postgresql---flexible-server-preview-using-azure-cli"></a>使用 Azure CLI 停止/启动 Azure Database for PostgreSQL 灵活服务器（预览版）

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器当前以公共预览版提供。

本文介绍如何使用 Azure CLI 执行重启、启动和停止灵活服务器。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
- 安装 Azure CLI 或将其升级到最新版本。 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
-  使用 [az login](/cli/azure/reference-index#az_login) 命令登录到 Azure 帐户。 请注意 id 属性，该属性指的是 Azure 帐户的订阅 ID。

    ```azurecli-interactive
    az login
    ````

- 如果有多个订阅，请选择要使用 ```az account set``` 命令在其中创建服务器的相应订阅。
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- 如果尚未使用 ```az postgres flexible-server create``` 命令创建 PostgreSQL 灵活服务器，请创建一个。

    ```azurecli
    az postgres flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>停止正在运行的服务器
若要停止服务器，请运行 ```az postgres flexible-server stop``` 命令。 如果使用的是[本地上下文](/cli/azure/config/param-persist)，则不需要提供任何参数。

**用法：**
```azurecli
az postgres flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**不包含本地上下文的示例：**
```azurecli
az postgres flexible-server stop  --resource-group --name myservername
```

**包含本地上下文的示例**：
```azurecli
az postgres flexible-server stop
```

## <a name="start-a-stopped-server"></a>启动已停止的服务器
若要启动服务器，请运行 ```az postgres flexible-server start``` 命令。 如果使用的是[本地上下文](/cli/azure/config/param-persist)，则不需要提供任何参数。

**用法：**
```azurecli
az postgres flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**不包含本地上下文的示例：**
```azurecli
az postgres flexible-server start  --resource-group --name myservername
```

**包含本地上下文的示例**：
```azurecli
az postgres flexible-server start
```

> [!IMPORTANT]
> 成功重启服务器后，所有管理操作现在都可用于灵活服务器。

## <a name="next-steps"></a>后续步骤
- 详细了解如何[重启 Azure Database for PostgreSQL 灵活服务器](./how-to-restart-server-cli.md)


