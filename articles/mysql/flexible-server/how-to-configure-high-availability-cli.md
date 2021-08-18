---
title: 管理区域冗余高可用性 - Azure CLI - Azure Database for MySQL 灵活服务器
description: 本文介绍如何通过 Azure CLI 在 Azure Database for MySQL 灵活服务器中配置区域冗余高可用性。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 0327e725534f7b56171814e99098cee365785d8c
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114205006"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>通过 Azure CLI 在 Azure Database for MySQL 灵活服务器中管理区域冗余高可用性

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!NOTE]
> Azure Database for MySQL 灵活服务器以公共预览版提供。

本文介绍在灵活服务器中创建服务器时，如何启用或禁用区域冗余高可用性配置。 也可以在创建服务器后禁用区域冗余高可用性。 不支持在创建服务器后启用区域冗余高可用性。

高可用性功能预配在不同区域中物理分隔主副本和备用副本。 有关详细信息，请参阅[高可用性概念文档](./concepts/../concepts-high-availability.md)。 启用或禁用高可用性不会更改其他设置，包括 VNET 配置、防火墙设置和备份保留。 禁用高可用性不会影响应用程序的连接和操作。

> [!IMPORTANT]
> 区域冗余高可用性在一组有限的区域中提供。 请在[此处](./overview.md#azure-regions)查看受支持的区域。 

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
- 安装 Azure CLI 或将其升级到最新版本。 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
- 使用 [az login](/cli/azure/reference-index#az_login) 命令登录到 Azure 帐户。 请注意 id 属性，该属性指的是 Azure 帐户的订阅 ID。

    ```azurecli-interactive
    az login
    ````

- 如果有多个订阅，请选择要使用 ```az account set``` 命令在其中创建服务器的相应订阅。

    ```azurecli
    az account set --subscription <subscription id>
    ```

## <a name="enable-high-availability-during-server-creation"></a>在服务器创建过程中启用高可用性

只能使用具有高可用性的常规用途或内存优化定价层创建服务器。 只能在创建时为服务器启用高可用性。

**用法：**

   ```azurecli
    az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                    [--resource-group]
                                    [--name]
   ```

**示例：**

   ```azurecli
    az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
   ```

## <a name="disable-high-availability"></a>禁用高可用性

你可以使用 [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) 命令禁用高可用性。 请注意，仅当创建了具有高可用性的服务器时才支持禁用高可用性。 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**示例：**

   ```azurecli
    az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
   ```

## <a name="next-steps"></a>后续步骤

- 了解[业务连续性](./concepts-business-continuity.md)
- 了解[区域冗余高可用性](./concepts-high-availability.md)