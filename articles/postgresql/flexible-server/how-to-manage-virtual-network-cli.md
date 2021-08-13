---
title: 管理虚拟网络 - Azure CLI - Azure Database for PostgreSQL 灵活服务器
description: 使用 Azure CLI 为 Azure Database for PostgreSQL 灵活服务器创建和管理虚拟网络
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: f00a1c8f8901d16a0aefa376f145fd2a4cbf2cf5
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952402"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>使用 Azure CLI 为 Azure Database for PostgreSQL 灵活服务器创建和管理虚拟网络

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

Azure Database for PostgreSQL 灵活服务器支持两种类型的互斥网络连接方法来连接到灵活服务器。 这两个选项如下：

* 公共访问（允许的 IP 地址）
* 专用访问（VNet 集成）

本文将重点介绍如何使用 Azure CLI 创建具有专用访问（VNet 集成）的 PostgreSQL 服务器。 通过专用访问（VNet 集成），可以将灵活服务器部署到你自己的 [Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)中。 Azure 虚拟网络提供专用的安全网络通信。 在专用访问中，与 PostgreSQL 服务器的连接仅限于你的虚拟网络。 若要了解详细信息，请参阅[专用访问（VNet 集成）](./concepts-networking.md#private-access-vnet-integration)。

在 Azure Database for PostgreSQL 灵活服务器中，在创建服务器期间，只能将服务器部署到虚拟网络和子网。 将灵活服务器部署到虚拟网络和子网后，无法将它移动到其他虚拟网络、子网或公共访问（允许的 IP 地址）。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以在单独的浏览器标签页中通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 打开 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后选择 Enter 来运行它。  

如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>先决条件

需要使用 [az login](/cli/azure/reference-index#az_login) 命令登录账户。 请注意 ID 属性，该属性指的是 Azure 帐户的订阅 ID 。

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account#az_account_set) 命令选择帐户下的特定订阅。 记下 az login 输出中的 ID 值，将其用作命令中 subscription 参数的值。 如果有多个订阅，请选择应计费的资源所在的相应订阅。 若要获取所有订阅，请使用 [az account list](/cli/azure/account#az_account_list)。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-postgresql---flexible-server-using-cli"></a>使用 CLI 创建 Azure Database for PostgreSQL 灵活服务器
可以使用 `az postgres flexible-server` 命令创建具有专用访问（VNet 集成）的灵活服务器。 此命令使用专用访问（VNet 集成）作为默认连接方法。 如果未提供虚拟网络和子网，则将创建虚拟网络和子网。 还可以使用子网 ID 提供现有的虚拟网络和子网。 <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> 使用 CLI 创建灵活服务器有多种选项，如以下示例所示。

>[!Important]
> 使用此命令会将子网委派给 Microsoft.DBforPostgreSQL/flexibleServers。 该委派意味着只有 Azure Database for PostgreSQL 灵活服务器才能使用该子网。 不能在委派子网中使用其他 Azure 资源类型。
>
请参阅 Azure CLI 参考文档 <!--FIXME --> 以获取可配置 CLI 参数的完整列表。 例如，在下面的命令中，可以选择指定资源组。

- 使用具有默认地址前缀的默认虚拟网络、子网创建灵活服务器
    ```azurecli-interactive
    az postgres flexible-server create
    ```
- 使用现有的虚拟网络和子网创建灵活服务器。 如果提供的虚拟网络和子网不存在，则将创建具有默认地址前缀的虚拟网络和子网。
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --subnet mySubnet
    ```
- 使用现有的虚拟网络、子网并使用子网 ID 创建灵活服务器。 提供的子网不应在其中部署任何其他资源，并且此子网将委派给 Microsoft.DBforPostgreSQL/flexibleServers（如果尚未委派）。
    ```azurecli-interactive
    az postgres flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > 虚拟网络和子网应与你的灵活服务器位于同一区域和订阅中。

    > [!IMPORTANT]
    > 包括 `AzureFirewallSubnet`、`AzureFirewallManagementSubnet`、`AzureBastionSubnet` 和 `GatewaySubnet` 在内的名称是 Azure 中的保留名称。 请不要使用这些名称作为子网名称。

- 使用具有非默认地址前缀的新虚拟网络、子网创建灵活服务器
    ```azurecli-interactive
    az postgres flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
请参阅 Azure CLI [参考文档](/cli/azure/postgres/flexible-server)以获取可配置 CLI 参数的完整列表。

>[!Important]
> 如果收到错误“`The parameter PrivateDnsZoneArguments is required, and must be provided by customer`”，则意味着你可能正在运行旧版本的 Azure CLI。 请[升级 Azure CLI](/cli/azure/update-azure-cli) 并重试操作。

## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure Database for PostgreSQL 灵活服务器中的网络](./concepts-networking.md)。
- [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-portal.md)。
- 详细了解 [Azure Database for PostgreSQL 灵活服务器虚拟网络](./concepts-networking.md#private-access-vnet-integration)。