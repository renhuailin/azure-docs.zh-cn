---
title: 教程：在同一虚拟网络中创建 Azure Database for PostgreSQL 灵活服务器和 Azure 应用服务 Web 应用
description: 在虚拟网络中创建 Azure Database for PostgreSQL 灵活服务器和 Web 应用的快速入门指南
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/30/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 58e7357552be9c209dec81cc9ace23fcfd82ea2d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467127"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>教程：在虚拟网络中创建 Azure Database for PostgreSQL 灵活服务器和应用服务 Web 应用

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本教程介绍如何在[虚拟网络](../../virtual-network/virtual-networks-overview.md)中创建 Azure 应用服务 Web 应用和 Azure Database for PostgreSQL 灵活服务器（预览版）。

在本教程中，将了解如何：
>[!div class="checklist"]
> * 在虚拟网络中创建 PostgreSQL 灵活服务器
> * 创建 Web 应用
> * 将 Web 应用添加到虚拟网络
> * 从 Web 应用连接到 Postgres 

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
- 在本地[安装 Azure CLI](/cli/azure/install-azure-cli) 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 
- 使用 [az login](/cli/azure/authenticate-azure-cli) 命令登录到你的帐户。 记下与订阅名称相对应的命令输出中的 **id** 属性。

  ```azurecli
  az login
  ```
- 如果有多个订阅，请选择应计费的资源所在的相应订阅。 使用 [az account set](/cli/azure/account) 命令选择帐户下的特定订阅 ID。

  ```azurecli
  az account set --subscription <subscription ID>
  ```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>在新的虚拟网络中创建 PostgreSQL 灵活服务器

使用以下命令在虚拟网络 (VNET) 中创建专用灵活服务器：

```azurecli
az postgres flexible-server create --resource-group demoresourcegroup --name demoserverpostgres --vnet demoappvnet --location westus2
```
此命令将执行以下操作，可能需要花几分钟的时间：

- 创建资源组（如果尚不存在）。
- 生成服务器名称（如果未提供）。
- 为新的 PostgreSQL 服务器创建新的虚拟网络，并在该虚拟网络中为数据库服务器创建子网。
- 创建服务器的管理员用户名和密码（如果未提供）。
- 创建一个名为 postgres 的空数据库

下面是示例输出。

```json
Local context is turned on. Its information is saved in working directory /home/jane. You can run `az local-context off` to turn it off.
Command argument values from local context: --resource-group demoresourcegroup, --location: eastus
Command group 'postgres flexible-server' is in preview. It may be changed/removed in a future release.
Checking the existence of the resource group ''...
Creating Resource group 'demoresourcegroup ' ...
Creating new vnet "demoappvnet" in resource group "demoresourcegroup" ...
Creating new subnet "Subnet095447391" in resource group "demoresourcegroup " and delegating it to "Microsoft.DBforPostgreSQL/flexibleServers"...
Creating PostgreSQL Server 'demoserverpostgres' in group 'demoresourcegroup'...
Your server 'demoserverpostgres' is using sku 'Standard_D2s_v3' (Paid Tier). Please refer to https://aka.ms/postgres-pricing for pricing details
Make a note of your password. If you forget, you would have to resetyour password with 'az postgres flexible-server update -n demoserverpostgres --resource-group demoresourcegroup -p <new-password>'.
{
  "connectionString": "postgresql://generated-username:generated-password@demoserverpostgres.postgres.database.azure.com/postgres?sslmode=require",
  "host": "demoserverpostgres.postgres.database.azure.com",
  "id": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/demoserverpostgres",
  "location": "East US",
  "password": "generated-password",
  "resourceGroup": "demoresourcegroup",
  "skuname": "Standard_D2s_v3",
  "subnetId": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.Network/virtualNetworks/VNET095447391/subnets/Subnet095447391",
  "username": "generated-username",
  "version": "12"
}
```

## <a name="create-a-web-app"></a>创建 Web 应用
在本部分中，你将在应用服务应用中创建应用主机，将此应用连接到 Postgres 数据库，然后将代码部署到该主机。 在终端中，请确保你位于应用程序代码的存储库根路径。 请注意，基本计划不支持 VNET 集成。 请使用“标准”或“高级”。 

使用 az webapp up 命令创建应用服务应用（主机进程）

```azurecli
az webapp up --resource-group demoresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - 对于 --location 参数，请使用与上一节中数据库相同的位置。
> - 将 <app-name> 替换为在整个 Azure 中唯一的名称。 <app-name> 允许的字符包括“A-Z”、“0-9”和“-”。 良好的模式是结合使用公司名称和应用标识符。

此命令将执行以下操作，可能需要花几分钟的时间：

- 创建资源组（如果尚不存在）。 （在此命令中，你将使用之前在其中创建数据库的同一资源组。）
- 创建应用服务应用（如果不存在）。
- 为应用启用默认日志记录（如果尚未启用）。
- 在启用了生成自动化的情况下，使用 ZIP 部署上传存储库。

### <a name="create-subnet-for-web-app"></a>为 Web 应用创建子网
在启用 VNET 集成之前，需要具备委托给应用服务 Web 应用的子网。 创建子网前，请查看数据库子网地址以避免对 Web 应用子网使用相同的地址前缀。 

```azurecli
az network vnet show --resource-group demoresourcegroup -n demoappvnet
```

运行以下命令，在创建数据库服务器的同一虚拟网络中创建一个新的子网。 更新地址前缀，避免与数据库子网冲突。

```azurecli
az network vnet subnet create --resource-group demoresourcegroup --vnet-name demoappvnet --name webappsubnet  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms
```

## <a name="add-the-web-app-to-the-virtual-network"></a>将 Web 应用添加到虚拟网络
使用 az webapp vnet-integration 命令向 webapp 添加区域虚拟网络集成。 

```azurecli
az webapp vnet-integration add --resource-group demoresourcegroup -n  mywebapp --vnet demoappvnet --subnet webappsubnet
```

## <a name="configure-environment-variables-to-connect-the-database"></a>配置环境变量以连接数据库
将代码部署到应用服务后，下一步是将应用连接到 Azure 中的灵活服务器。 应用代码需要在多个环境变量中查找数据库信息。 若要在应用服务中设置环境变量，需要使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 命令。

  
```azurecli
  
az webapp config appsettings set  --name mywebapp --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>" 
```
- 为新创建的灵活服务器命令替换 postgres-server-name、username 和 password  。
- 将 **<username>** 和 **<password>** 替换为命令也为你生成的凭据。
- 资源组和应用名称是从“.azure/config”文件中的缓存值中提取的。
- 此命令会创建名为 DBHOST、DBNAME、DBUSER 和 DBPASS 的设置  。 如果应用程序代码对数据库信息使用了不同的名称，则如代码中所述，对应用设置使用这些名称。

将 Web 应用配置为允许来自虚拟网络中的所有出站连接。
```azurecli
az webapp config set --name mywebapp --resource-group demoresourcegroup --generic-configurations '{"vnetRouteAllEnabled": true}'
```

## <a name="clean-up-resources"></a>清理资源

使用以下命令清除在此教程中创建的所有资源。 此命令将删除这一资源组中的所有资源。

```azurecli
az group delete -n demoresourcegroup
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure 应用服务](../../app-service/app-service-web-tutorial-custom-domain.md)
