---
title: 快速入门：使用 Azure CLI 创建 Synapse SQL 池
description: 通过 Azure CLI 使用服务器级防火墙规则快速创建 Synapse SQL 池。
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.custom: azure-synapse, devx-track-azurecli
ms.openlocfilehash: 3903aa0be5ffa63bc4292371c59002846ec9363c
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892087"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>快速入门：使用 Azure CLI 创建 Synapse SQL 池

使用 Azure CLI 在 Azure Synapse Analytics 中创建 Synapse SQL 池（数据仓库）。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>入门

使用这些命令登录到 Azure 并设置资源组。

1. 如果使用的是本地安装，请运行 [az login](/cli/azure/reference-index#az_login) 命令登录 Azure：

   ```azurecli
   az login
   ```

1. 如有必要，请使用 [az account set](/cli/azure/account#az_account_set) 命令选择订阅：

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. 运行 [az group create](/cli/azure/group#az_group_create) 命令创建资源组：

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. 使用 [az sql server create](/cli/azure/sql/server#az_sql_server_create) 命令创建[逻辑 SQL 服务器](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)：

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   服务器包含作为组进行管理的一组数据库。

## <a name="configure-a-server-level-firewall-rule"></a>配置服务器级防火墙规则

创建[服务器级防火墙规则](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 服务器级防火墙规则允许外部应用程序（例如 SQL Server Management Studio 或 SQLCMD 实用程序）通过 SQL 池服务防火墙连接到 SQL 池。

运行 [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) 命令创建防火墙规则：

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

在本示例中，防火墙仅对其他 Azure 资源开放。 要启用外部连接，请将 IP 地址更改为适合你环境的地址。 若要开放所有 IP 地址，请使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束地址。

> [!NOTE]
> SQL 终结点通过端口 1433 进行通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到服务器，除非 IT 部门打开了端口 1433。
>

## <a name="create-and-manage-your-sql-pool"></a>创建和管理 SQL 池

创建 SQL 池。 本示例将 DW100c 用作服务目标，这是针对 SQL 池的低成本起点。

> [!NOTE]
> 你需要一个之前创建的工作区。 有关详细信息，请参阅[快速入门：使用 Azure CLI 创建 Azure Synapse 工作区](../quickstart-create-workspace-cli.md)。

使用 [az synapse sql pool create](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_create) 命令创建 SQL 池：

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

有关参数选项的详细信息，请参阅 [az synapse sql 池](/cli/azure/synapse/sql/pool)。

可使用 [az synapse sql pool list](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_list) 命令查看 SQL 池：

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

使用 [az synapse sql pool update](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_update) 命令更新现有池：

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

使用 [az synapse sql pool pause](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_pause) 命令暂停池：

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

使用 [az synapse sql pool resume](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_resume) 命令启动暂停的池：

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

若要删除现有的 SQL 池，请使用 [az synapse sql pool delete](/cli/azure/synapse/sql/pool#az_synapse_sql_pool_delete) 命令：

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>清理资源

本系列中的其他快速入门教程是在本快速入门的基础上制作的。

> [!TIP]
> 如何打算继续学习后续快速入门教程，请不要清除本快速入门中创建的资源。 如果不打算继续学习，请使用 [az group delete](/cli/azure/group#az_group_delete) 命令删除在本快速入门中创建的所有资源。
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

现已创建 SQL 池，创建防火墙规则并连接到 SQL 池。 若要了解详细信息，请继续阅读[将数据加载到 SQL 池](./load-data-from-azure-blob-storage-using-copy.md)一文。
