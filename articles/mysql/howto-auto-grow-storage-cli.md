---
title: 自动增长存储 - Azure CLI - Azure Database for MySQL
description: 本文介绍如何使用 Azure CLI 在 Azure Database for MySQL 中启用自动增长存储。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3126adbae6cb719bf19dc549e83dfc55af56f7d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110009"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>使用 Azure CLI 自动增长 Azure Database for MySQL 存储
本文介绍如何将 Azure Database for MySQL 服务器存储配置为在不影响工作负荷的情况下增长。

[达到存储限制](./concepts-pricing-tiers.md#reaching-the-storage-limit)的服务器将设置为只读。 如果启用了存储自动增长，则对于预配的存储大小小于 100 GB 的服务器，可用存储空间一旦小于 1 GB 或预配存储的 10%（以这二者中的较大值为准），预配的存储大小就会立即增加 5 GB。 对于预配的存储大小大于 100 GB 的服务器，可用存储空间小于预配的存储大小的 5% 时，预配的存储大小就会增加 5%。 [此处](./concepts-pricing-tiers.md#storage)所指定的最大存储限制适用。

## <a name="prerequisites"></a>必备条件

若要完成本操作说明指南：

- 需要 [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="enable-mysql-server-storage-auto-grow"></a>启用 MySQL 服务器存储自动增长

使用以下命令在现有服务器上启用服务器自动增长存储：

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

使用以下命令创建新服务器时启用服务器自动增长存储：

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>后续步骤

了解[如何基于指标创建警报](howto-alert-on-metric.md)。