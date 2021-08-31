---
title: 基础结构双重加密 - Azure 门户 - Azure Database for MySQL
description: 了解如何设置和管理适用于 Azure Database for MySQL 的基础结构双重加密。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 0666cce0668d315c34a9d6cade1f8d6e087f20dc
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674394"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>适用于 Azure Database for MySQL 的基础结构双重加密

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

了解如何使用“如何设置和管理适用于 Azure Database for MySQL 的基础结构双重加密”。

## <a name="prerequisites"></a>先决条件

* 必须有一个 Azure 订阅，并且是该订阅的管理员。
* Azure Database for MySQL - 单一服务器应位于常规用途或内存优化定价层和常规用途存储 v2 上。 在进一步继续之前，请参阅[基础结构双重加密](concepts-infrastructure-double-encryption.md#limitations)的限制。

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>创建使用基础结构双重加密的 Azure Database for MySQL 服务器 - 门户

请按照以下步骤在 Azure 门户中创建一个使用基础结构双重加密的 Azure Database for MySQL 服务器：

1. 在门户左上角选择“创建资源”  (+)。

2. 选择“数据库”   >   “Azure Database for MySQL”。 还可以在搜索框中输入“MySQL”  以查找该服务。

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Azure Database for MySQL 选项":::

3. 提供服务器的基本信息。 选择“其他设置”，并启用“基础结构双重加密”复选框，以设置参数。

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="Azure Database for MySQL 选择":::

4. 选择“查看 + 创建”  以预配服务器。

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="Azure Database for MySQL 摘要":::

5. 创建服务器后，可以通过检查“数据加密”服务器边栏选项卡中的状态来验证基础结构双重加密。

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="Azure Database for MySQL 验证":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>创建使用基础结构双重加密的 Azure Database for MySQL 服务器 - CLI

请按照以下步骤在 CLI 中创建一个使用基础结构双重加密的 Azure Database for MySQL 服务器：

以下示例将在 `westus` 位置创建一个名为 `myresourcegroup` 的资源组。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
下面的示例使用服务器管理员登录名 `myadmin` 在资源组 `myresourcegroup` 中创建位于“美国西部”区域的名为 `mydemoserver` 的 MySQL 5.7 服务器。 这是第 4 代常规用途服务器，带有 2 个 vCore 。 这样还会为创建的服务器启用基础结构双重加密。 用自己的值替换 `<server_admin_password>`。

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>后续步骤

 有关数据加密的详细信息，请参阅 [Azure Database for MySQL 数据基础结构双重加密](concepts-Infrastructure-double-encryption.md)"。
