---
title: 重启/停止/启动 Azure 门户 - Azure Database for MySQL 灵活服务器
description: 本文介绍如何通过 Azure CLI 在 Azure Database for MySQL 中执行重启/停止/启动操作。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: 93a9abc0b1da58592f45fb2ba4a317128629a370
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651877"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview"></a>重启/停止/启动 Azure Database for MySQL 灵活服务器（预览）

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

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

- 如果尚未使用 ```az mysql flexible-server create``` 命令创建 MySQL 灵活服务器，请创建一个。

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>停止正在运行的服务器
若要停止服务器，请运行 ```az mysql flexible-server stop``` 命令。 如果使用的是[本地上下文](/cli/azure/config/param-persist)，则不需要提供任何参数。

**用法：**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**不包含本地上下文的示例：**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**包含本地上下文的示例**：
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>启动已停止的服务器
若要启动服务器，请运行 ```az mysql flexible-server start``` 命令。 如果使用的是[本地上下文](/cli/azure/config/param-persist)，则不需要提供任何参数。

**用法：**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**不包含本地上下文的示例：**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**包含本地上下文的示例**：
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
>成功重启服务器后，所有管理操作现在都可用于灵活服务器。

## <a name="restart-a-server"></a>重启服务器
若要重启服务器，请运行 ```az mysql flexible-server restart``` 命令。 如果使用的是[本地上下文](/cli/azure/config/param-persist)，则不需要提供任何参数。

**用法：**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**不包含本地上下文的示例：**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**包含本地上下文的示例**：
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
>成功重启服务器后，所有管理操作现在都可用于灵活服务器。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)
- [使用 Azure 门户创建和管理 Azure Database for MySQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-portal.md)。

