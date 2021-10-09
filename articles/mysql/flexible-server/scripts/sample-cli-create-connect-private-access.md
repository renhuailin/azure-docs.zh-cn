---
title: CLI 脚本 - 在 VNet 中创建 Azure Database for MySQL 灵活服务器（预览版）
description: 此 Azure CLI 示例脚本演示了如何在 VNet 中创建 Azure Database for MySQL 灵活服务器（专用访问连接方法）并从 VNet 中的 VM 连接到该服务器。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: eef5a814fd25d30a96ad3fd445d30e9daeadc596
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604612"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-preview-in-a-vnet-using-azure-cli"></a>使用 Azure CLI 在 VNet 中创建 Azure Database for MySQL 灵活服务器（预览版）

此示例 CLI 脚本在 VNet 中创建 Azure Database for MySQL 灵活服务器（[专用访问连接方法](../concepts-networking-vnet.md)），并从 VNet 中的 VM 连接到该服务器。

> [!NOTE] 
> 创建服务器后，无法更改连接方法。 例如，如果使用“专用访问(VNet 集成)”创建服务器，则在创建后，不能更改为“公共访问(允许的 IP 地址)” 。 若要详细了解连接方法，请参阅[网络概念](../concepts-networking.md)。

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。 

## <a name="sample-script"></a>示例脚本

使用变量的值编辑脚本中突出显示的行。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/create-connect-server-in-vnet.sh?highlight=7,10 "Create and Connect to an Azure Database for MySQL - Flexible Server (General Purpose SKU) in VNet")]

## <a name="clean-up-deployment"></a>清理部署

运行示例脚本后，可使用下面的代码片段来清理资源。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/clean-up-resources.sh "Clean up resources.")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| **命令** | **说明** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|创建用于存储所有资源的资源组|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|创建托管数据库的灵活服务器。|
|[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)|在 VNet 中创建子网。|
|[az vm create](/cli/azure/vm#az_vm_create)|创建 Azure 虚拟机。|
|[az vm open-port](/cli/azure/vm#az_vm_open_port)|针对指定端口上的入站流量打开一个 VM。|
|[az group delete](/cli/azure/group#az_group_delete) | 删除资源组，包括所有嵌套的资源。|

## <a name="next-steps"></a>后续步骤

- 尝试其他脚本：[Azure Database for MySQL 灵活服务器（预览版）的 Azure CLI 示例](../sample-scripts-azure-cli.md)
- 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。