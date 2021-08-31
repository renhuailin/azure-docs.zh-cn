---
title: 管理 VNet 终结点 - Azure CLI - Azure Database for MySQL
description: 本文介绍如何使用 Azure CLI 命令行创建和管理 Azure Database for MySQL VNet 服务终结点和规则。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 02dc34f6d2e47a0a035f2706236906dc8c50a684
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779778"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>使用 Azure CLI 创建和管理 Azure Database for MySQL VNet 服务终结点

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
虚拟网络 (VNet) 服务终结点和规则将虚拟网络的专用地址空间扩展到 Azure Database for MySQL 服务器。 使用便捷的 Azure 命令行接口 (CLI) 命令，可创建、更新、删除、列出和显示 VNet 服务终结点和规则，用于管理服务器。 若要概览 Azure Database for MySQL VNet 服务终结点（包括限制），请参阅 [Azure Database for MySQL 服务器 VNet 服务终结点](concepts-data-access-and-security-vnet.md)。 在 Azure Database for MySQL 的所有支持区域中，VNet 服务终结点均可用。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 需要 [Azure Database for MySQL 服务器和数据库](quickstart-create-mysql-server-database-using-azure-cli.md)。
 
- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

> [!NOTE]
> 只有常规用途和内存优化服务器才支持 VNet 服务终结点。
> 在 VNet 对等互连的情况下，如果流量通过具有服务终结点的公共 VNet 网关流动，并且应该流向对等机，请创建 ACL/VNet 规则，以便网关 VNet 中的 Azure 虚拟机能够访问 Azure Database for MySQL 服务器。

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 配置 Vnet 服务终结点
[az network vnet](/cli/azure/network/vnet) 命令用于配置虚拟网络。

如果有多个订阅，请选择应计费的资源所在的相应订阅。 使用 [az account set](/cli/azure/account#az_account_set) 命令选择帐户下的特定订阅 ID。 用 az login 输出中你的订阅的 ID 属性替换订阅 ID 占位符 。

- 该帐户必须拥有创建虚拟网络和服务终结点所需的必要权限。

对虚拟网络拥有写入访问权限的用户可在虚拟网络上单独配置服务终结点。

若要在 VNet 中保护 Azure 服务资源，用户必须对所添加的子网拥有“Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/”权限。 此权限默认包含在内置的服务管理员角色中，可以通过创建自定义角色进行修改。

详细了解[内置角色](../role-based-access-control/built-in-roles.md)以及将特定的权限分配到[自定义角色](../role-based-access-control/custom-roles.md)。

VNet 和 Azure 服务资源可以位于相同或不同的订阅中。 如果 VNet 和 Azure 服务资源位于不同的订阅中，资源应在相同的 Active Directory (AD) 租户下。 确保两个订阅都注册了 Microsoft.Sql 和 Microsoft.DBforMySQL 资源提供程序。  有关详细信息，请参阅[资源管理器注册][resource-manager-portal]

> [!IMPORTANT]
> 强烈建议在运行下面的示例脚本或配置服务终结点前先阅读本文有关服务终结点配置和注意事项的内容。 **虚拟网络服务终结点：** [虚拟网络服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)是一个子网，其属性值包括一个或多个正式的 Azure 服务类型名称。 VNet 服务终结点使用服务类型名称 Microsoft.Sql，可引用名为“SQL 数据库”的 Azure 服务。 此服务标记也适用于 Azure SQL 数据库、Azure Database for PostgreSQL 和 MySQL 服务。 请务必要注意，对 VNet 服务终结点应用 Microsoft.Sql 服务标记时，它会为所有 Azure 数据库服务配置服务终结点流量，其中包括 Azure SQL 数据库、Azure Database for PostgreSQL 和子网上的 Azure Database for MySQL 服务器。 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>此示例脚本演示了如何创建 Azure Database for MySQL 数据库、创建 VNet 和 VNet 服务终结点，以及如何使用 VNet 规则在子网中保护服务器
在此示例脚本中，更改突出显示的行，以自定义管理员用户名和密码。 将 `az account set --subscription` 命令中使用的 SubscriptionID 替换为你自己的订阅标识符。
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>清理部署
运行脚本示例后，可以使用以下命令删除资源组以及与其关联的所有资源。
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
