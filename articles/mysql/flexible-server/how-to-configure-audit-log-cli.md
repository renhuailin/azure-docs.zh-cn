---
title: 通过 Azure CLI 配置审核日志 - Azure Database for MySQL 灵活服务器
description: 本文介绍如何从 Azure CLI 配置和访问 Azure Database for MySQL 灵活服务器中的审核日志。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: ac24c933cefda60f8ed0bb305c2d49800bd2d610
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122653423"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>使用 Azure CLI 配置和访问 Azure Database for MySQL 灵活服务器的审核日志

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

本文介绍如何使用 Azure CLI 为 MySQL 灵活服务器配置[审核日志](concepts-audit-logs.md)。

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

## <a name="configure-audit-logging"></a>配置审核日志记录

>[!IMPORTANT]
> 建议仅记录审核所需的事件类型和用户，以确保服务器的性能不会受到严重影响。

启用并配置审核日志记录。

```azurecli
# Enable audit logs

az mysql flexible-server parameter set \
--name audit_log_enabled \
--resource-group myresourcegroup \
--server-name mydemoserver \
--value ON
```

## <a name="next-steps"></a>后续步骤
- 详细了解[审核日志](concepts-audit-logs.md)
