---
title: 通过 Azure CLI 还原 Azure Database for MySQL 灵活服务器
description: 本文介绍如何通过 Azure CLI 在 Azure Database for MySQL 中执行还原操作。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: ac6a6964c738cfb970b7bd65a6d7e3b12f796b2a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128653716"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>通过 Azure CLI 的 Azure Database for MySQL 灵活服务器的时间点还原

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍使用备份在灵活服务器中执行时间点恢复的分步过程。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
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

## <a name="restore-a-server-from-backup-to-a-new-server"></a>将服务器从备份还原到新服务器

你可以运行以下命令来将服务器还原到最早的现有备份。

**使用情况**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

示例：从此 ```2021-03-03T13:10:00Z``` 备份快照中还原服务器。

```azurecli
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
还原所需的时间取决于服务器中存储的数据的大小。

## <a name="perform-post-restore-tasks"></a>执行还原后任务
完成还原后，应执行以下任务，然后用户和应用程序才能重新运行：

- 如果需要使用新服务器来替换原始服务器，则请将客户端和客户端应用程序重定向到新服务器
- 对于要进行连接的用户，请确保设置适当的 VNet 规则。 不会从源服务器复制这些规则。
- 确保设置适当的登录名和数据库级权限
- 根据需要为新还原服务器配置警报

## <a name="next-steps"></a>后续步骤
详细了解[业务连续性](concepts-business-continuity.md)

