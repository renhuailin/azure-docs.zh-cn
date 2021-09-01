---
title: 快速入门：创建服务器 - Azure CLI - Azure Database for MySQL 灵活服务器
description: 本快速入门介绍如何使用 Azure CLI 在 Azure 资源组中创建 Azure Database for MySQL 灵活服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0e5b5a2fb64c803c76f663d506dec8d750a4af29
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122643097"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure Database for MySQL 灵活服务器

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


本快速入门教程介绍如何使用 [Azure Cloud Shell](https://shell.azure.com) 中的 [Azure CLI](/cli/azure/get-started-with-azure-cli) 在大约 5 分钟内创建 Azure Database for MySQL 灵活服务器。 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以在单独的浏览器标签页中通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 打开 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后选择 Enter 来运行它。  

如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="prerequisites"></a>先决条件

你将需要使用 [az login](/cli/azure/reference-index#az_login) 命令登录到你的帐户。 请注意 id 属性，该属性指的是 Azure 帐户的订阅 ID。

```azurecli-interactive
az login
```

使用 [az account set](/cli/azure/account#az_account_set) 命令选择帐户下的特定订阅。 记下 az login 输出中的 id 值，以用作命令中订阅参数的值。 如果有多个订阅，请选择应计费的资源所在的相应订阅。 若要获取所有订阅，请使用 [az account list](/cli/azure/account#az_account_list)。

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>创建灵活服务器

使用 `az group create` 命令创建 [Azure 资源组](../../azure-resource-manager/management/overview.md)，然后在此资源组中创建 MySQL 灵活服务器。 应提供唯一名称。 以下示例在 `eastus2` 位置创建名为 `myresourcegroup` 的资源组。

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

使用 `az mysql flexible-server create` 命令创建灵活服务器。 一个服务器可以包含多个数据库。 以下命令使用服务默认值和 Azure CLI [本地上下文](/cli/azure/local-context)中的值创建服务器： 

```azurecli-interactive
az mysql flexible-server create
```

创建的服务器具有以下属性： 
- 自动生成的服务器名称、管理员用户名、管理员密码、资源组名称（如果尚未在本地上下文中指定），并且与资源组位于同一位置 
- 其余服务器配置的服务默认值：计算层（可突增）、计算大小/SKU (B1MS)、备份保持期（7 天）和 MySQL 版本 (5.7)
- 默认的连接方法是设置了自动生成的虚拟网络和子网的“专用访问(VNet 集成)”

> [!NOTE] 
> 创建服务器后，无法更改连接方法。 例如，如果在创建过程中选择了“专用访问(VNet 集成)”，则在创建后无法将其更改为“公共访问(允许的 IP 地址)” 。 强烈建议创建采用专用访问的服务器，以使用 VNet 集成安全地访问你的服务器。 若要详细了解专用访问，请参阅[概念文章](./concepts-networking.md)。

如果要更改任何默认设置，请参阅 Azure CLI [参考文档](/cli/azure/mysql/flexible-server)以获取可配置 CLI 参数的完整列表。 

下面是一些示例输出： 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

如果要更改任何默认设置，请参阅 Azure CLI [参考文档](/cli/azure/mysql/flexible-server)以获取可配置 CLI 参数的完整列表。 

## <a name="create-a-database"></a>创建数据库
如果尚未创建数据库，请运行 **newdatabase** 命令进行创建。

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> 连接到 Azure Database for MySQL 时，经端口 3306 进行通信。 如果尝试从企业网络内部进行连接，则可能不允许经端口 3306 的出站流量。 如果是这样，则无法连接到服务器，除非 IT 部门打开了端口 3306。

## <a name="get-the-connection-information"></a>获取连接信息

若要连接到服务器，需要提供主机信息和访问凭据。

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

结果采用 JSON 格式。 记下 fullyQualifiedDomainName  和 administratorLogin  。 下面是 JSON 输出的示例： 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>使用 Azure CLI 连接并测试连接

借助 Azure Database for MySQL 灵活服务器，你可使用 Azure CLI ```az mysql flexible-server connect``` 命令连接到 MySQL 服务器。 使用此命令可以测试与数据库服务器的连接情况，创建快速入门数据库并直接对服务器运行查询，无需安装 mysql.exe 或 MySQL Workbench。  还可以使用在交互模式下运行命令来运行多个查询。

运行以下脚本，在开发环境中测试和验证与数据库的连接。

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**示例：**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
对于成功的连接，应看到以下输出：

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
如果连接失败，请尝试以下解决方案：
- 检查是否已在客户端计算机上打开端口 3306。
- 如果服务器管理员用户名和密码正确
- 如果已为客户端计算机配置防火墙规则
- 如果已在虚拟网络中为服务器配置了专用访问权限，请确保客户端计算机位于同一虚拟网络中。

运行以下命令，以使用 ```--querytext``` 参数 ```-q``` 执行单个查询。

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**示例：**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
若要详细了解如何使用 ```az mysql flexible-server connect``` 命令，请参阅[连接和查询](connect-azure-cli.md)文档。

## <a name="connect-using-mysql-command-line-client"></a>使用 mysql 命令行客户端进行连接

如果使用“专用访问(VNet 集成)”创建了灵活服务器，需要从与服务器相同的虚拟网络中的资源连接到服务器。 可以创建虚拟机并将其添加到使用灵活服务器创建的虚拟网络中。 有关详细信息，请参阅[配置专用访问文档](how-to-manage-virtual-network-portal.md)。

如果使用“公共访问(允许的 IP 地址)”创建了灵活服务器，可以将本地 IP 地址添加到服务器上的防火墙规则列表中。 有关分步指南，请参阅[创建或管理防火墙规则文档](how-to-manage-firewall-portal.md)。

可以使用 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL Workbench](./connect-workbench.md) 从本地环境连接到服务器。 Azure Database for MySQL 灵活服务器支持使用传输层安全性 (TLS)（以前称为安全套接字层 (SSL)）将客户端应用程序连接到 MySQL 服务。 TLS 是一种行业标准协议，可确保数据库服务器和客户端应用程序之间的加密网络连接，让你能够遵守合规要求。若要通过 MySQL 灵活服务器进行连接，你需要下载用于证书颁发机构验证的[公用 SSL 证书](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)。 若要详细了解如何通过加密连接进行连接或禁用 SSL，请参阅[连接到具有加密连接的 Azure Database for MySQL — 灵活服务器](how-to-connect-tls-ssl.md)文档。

以下示例演示如何使用 mysql 命令行接口连接到你的灵活服务器。 如果尚未安装 mysql 命令行，请先进行安装。 你将下载 SSL 连接所需的 DigiCertGlobalRootCA 证书。 使用 --ssl-mode=REQUIRED 连接字符串设置强制实施 TLS/SSL 证书验证。 将本地证书文件路径传递给 --ssl-ca 参数。 将值替换为实际的服务器名称和密码。

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

如果已使用公共访问权限预配了灵活服务器，还可使用 [Azure Cloud Shell](https://shell.azure.com/bash)，通过预安装的 mysql 客户端连接到你的灵活服务器，如下所示：

若要使用 Azure Cloud Shell 连接到灵活服务器，则将需要允许从 Azure Cloud Shell 到灵活服务器的网络访问。 若要实现此目的，你可在 Azure 门户上访问 MySQL 灵活服务器的“网络”边栏选项卡，选中“防火墙”部分下的“允许从 Azure 中的任何 Azure 服务公开访问此服务器”这一对话框（如下图所示），然后单击“保存”以保存设置。

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="屏幕截图：显示如何允许 Azure Cloud Shell 访问 MySQL 灵活服务器以进行公共访问网络配置。":::
 
 
> [!NOTE]
> 选中“允许从 Azure 中的任何 Azure 服务公共访问此服务器”应仅用于开发或测试目的。 该选项可将防火墙配置为允许来自分配给任何 Azure 服务或资产的 IP 地址的连接，包括来自其他客户的订阅的连接。

单击“尝试”以启动 Azure Cloud Shell，并使用以下命令连接到你的灵活服务器。 在命令中使用服务器名称、用户名和密码。 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
>使用 Azure Cloud Shell 连接到灵活服务器时，你需要使用 --ssl=true 参数，而非 --ssl-mode=REQUIRED。
> 主要原因是 MariaDB 分发中预安装的 mysql.exe 客户端 Azure Cloud Shell，它需要 --ssl  参数，而来自 Oracle 分发的 mysql 客户端需要 --ssl 模式参数。

如果按照之前的命令连接到灵活服务器时看到以下错误消息，则说明未使用前面提到过的“允许从 Azure 中的任何 Azure 服务公共访问此服务器”设置防火墙规则，或该选项未保存。 请尝试重新设置防火墙，然后重试。

错误 2002 (HY000)：无法连接到 <servername> 上的 MySQL 服务器 (115)

## <a name="clean-up-resources"></a>清理资源

如果不需要将这些资源用于其他快速入门/教程，则可通过执行以下命令将其删除：

```azurecli-interactive
az group delete --name myresourcegroup
```

如果只想删除这一新创建的服务器，可以运行 `az mysql server delete` 命令。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
> [使用 Azure CLI 连接和查询](connect-azure-cli.md)
> [连接到具有加密连接的 Azure Database for MySQL — 灵活服务器](how-to-connect-tls-ssl.md)
> [使用 MySQL 构建 PHP (Laravel) Web 应用](tutorial-php-database-app.md)
