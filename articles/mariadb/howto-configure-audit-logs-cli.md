---
title: 访问审核日志 - Azure CLI - Azure Database for MariaDB
description: 本文介绍如何从 Azure CLI 配置和访问 Azure Database for MariaDB 中的审核日志。
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: how-to
ms.date: 6/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 57fc79e88e3268c37dd932a5618bd661f000e09c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98665168"
---
# <a name="configure-and-access-azure-database-for-maria-db-audit-logs-in-the-azure-cli"></a>在 Azure CLI 中配置和访问 Azure Database for Maria DB 审核日志

可以从 Azure CLI 配置 [Azure Database for MariaDB 审核日志](concepts-audit-logs.md)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要完成本指南：

- 需要 [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-portal.md)。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="configure-audit-logging"></a>配置审核日志记录

>[!IMPORTANT]
> 建议仅记录审核所需的事件类型和用户，以确保服务器的性能不会受到严重影响。

使用以下步骤启用和配置审核日志记录： 

1. 通过将“audit_logs_enabled”参数设为“ON”来启用审核日志。 
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_enabled --resource-group myresourcegroup --server mydemoserver --value ON
    ```

1. 通过更新 **audit_log_events** 参数，选择要记录的 [事件类型](concepts-audit-logs.md#configure-audit-logging)。
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_events --resource-group myresourcegroup --server mydemoserver --value "ADMIN,CONNECTION"
    ```

1. 通过更新 **audit_log_exclude_users** 参数添加要从日志记录中排除的 MariaDB 用户。 通过提供 MariaDB 用户名来指定用户。
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_exclude_users --resource-group myresourcegroup --server mydemoserver --value "azure_superuser"
    ```

1. 通过更新“audit_log_include_users”参数，添加要包括在日志中的任何特定 MariaDB 用户。 通过提供 MariaDB 用户名来指定用户。
    ```azurecli-interactive
    az mariadb server configuration set --name audit_log_include_users --resource-group myresourcegroup --server mydemoserver --value "sampleuser"
    ```

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure Database for MariaDB 中的[审核日志](concepts-audit-logs.md)
- 了解如何在 [Azure 门户](howto-configure-audit-logs-portal.md)中配置审核日志
