---
title: 教程：使用 Azure 专用终结点连接到 Azure SQL 服务器 - Azure CLI
description: 使用本教程了解如何使用 Azure CLI 创建具有专用终结点的 Azure SQL 服务器
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 11/03/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: a5562c5f40a321f5737fea73f6d7964b402953cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889206"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-cli"></a>教程：使用 Azure 专用终结点连接到 Azure SQL 服务器 - Azure CLI

Azure 专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络和 Bastion 主机。
> * 创建虚拟机。
> * 创建 Azure SQL 服务器和专用终结点。
> * 测试到 SQL 服务器专用终结点的连接。

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 登录 Azure 门户，并通过运行 `az login` 来检查订阅是否处于活动状态。
* 通过运行 `az --version` 在终端或命令窗口中检查 Azure CLI 版本。 有关最新版本，请参阅[最新发行说明](/cli/azure/release-notes-azure-cli?tabs=azure-cli)。
  * 如果没有最新版本，请按照[适用于你操作系统或平台的安装指南](/cli/azure/install-azure-cli)来更新安装。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组：

* 命名为 CreateSQLEndpointTutorial-rg。 
* 在位置“eastus”中。

```azurecli-interactive
az group create \
    --name CreateSQLEndpointTutorial-rg \
    --location eastus
```

## <a name="create-a-virtual-network-and-bastion-host"></a>创建虚拟网络和堡垒主机

在本部分中，你将创建虚拟网络、子网和堡垒主机。 

堡垒主机将用于安全地连接到虚拟机，以测试专用终结点。

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络

* 命名为“myVNet”。
* 地址前缀为 10.0.0.0/16。
* 子网命名为“myBackendSubnet”。
* 子网前缀为 10.0.0.0/24。
* 在 CreateSQLEndpointTutorial-rg 资源组中。
* eastus 的位置。

```azurecli-interactive
az network vnet create \
    --resource-group CreateSQLEndpointTutorial-rg\
    --location eastus \
    --name myVNet \
    --address-prefixes 10.0.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.0.0.0/24
```

更新子网，以使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) 禁用专用终结点的专用终结点网络策略：

```azurecli-interactive
az network vnet subnet update \
    --name myBackendSubnet \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet \
    --disable-private-endpoint-network-policies true
```

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 为堡垒主机创建公共 IP：

* 创建名为“myBastionIP”的标准区域冗余公共 IP 地址。
* 在 CreateSQLEndpointTutorial-rg 中。

```azurecli-interactive
az network public-ip create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionIP \
    --sku Standard
```

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 创建堡垒子网：

* 命名为 AzureBastionSubnet。
* 地址前缀为 10.0.1.0/24。
* 在虚拟网络“myVNet”中。
* 在资源组 CreateSQLEndpointTutorial-rg 中。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.0.1.0/24
```

使用 [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) 创建堡垒主机：

* 命名为 myBastionHost。
* 在 CreateSQLEndpointTutorial-rg 中。
* 与公共 IP myBastionIP 相关联。
* 与虚拟网络 myVNet 相关联。
* 在位置“eastus”中。

```azurecli-interactive
az network bastion create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

部署 Azure Bastion 主机需要几分钟时间。

## <a name="create-test-virtual-machine"></a>创建测试虚拟机

在本部分中，你将创建将用来测试专用终结点的虚拟机。

使用  [az vm create](/cli/azure/vm#az_vm_create) 创建 VM。 出现提示时，请提供要用作 VM 凭据的密码：

* 名为 myVM。
* 在 CreateSQLEndpointTutorial-rg 中。
* 在网络 myVNet 中。
* 在子网“myBackendSubnet”中。
* 服务器映像 Win2019Datacenter。

```azurecli-interactive
az vm create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name myVM \
    --image Win2019Datacenter \
    --public-ip-address "" \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --admin-username azureuser
```

## <a name="create-an-azure-sql-server"></a>创建 Azure SQL 服务器

在本部分中，你将创建一个 SQL 服务器和数据库。

使用 [az sql server create](/cli/azure/sql/server#az_sql_server_create) 创建 SQL 服务器：

* 将 \<sql-server-name> 替换为唯一服务器名称。
* 使用你的密码替换 \<your-password>。
* 在 CreateSQLEndpointTutorial-rg 中。
* 在 eastus 区域中。

```azurecli-interactive
az sql server create \
    --name <sql-server-name> \
    --resource-group CreateSQLEndpointTutorial-rg \
    --location eastus \
    --admin-user sqladmin \
    --admin-password <your-password>
```

使用 [az sql db create](/cli/azure/sql/db#az_sql_db_create) 创建数据库：

* 命名为 myDataBase。
* 在 CreateSQLEndpointTutorial-rg 中。
* 将 \<sql-server-name> 替换为唯一服务器名称。

```azurecli-interactive
az sql db create \
    --resource-group CreateSQLEndpointTutorial-rg  \
    --server <sql-server-name> \
    --name myDataBase \
    --sample-name AdventureWorksLT
```

## <a name="create-private-endpoint"></a>创建专用终结点

在本部分中，你将创建专用终结点。

使用 [az sql server list](/cli/azure/sql/server#az_sql_server_list) 将 SQL 服务器的资源 ID 置于 shell 变量中。

使用 [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) 创建终结点和连接：

* 命名为 myPrivateEndpoint。
* 在资源组 CreateSQLEndpointTutorial-rg 中。
* 在虚拟网络“myVNet”中。
* 在子网“myBackendSubnet”中。
* 名为 myConnection 的连接。

```azurecli-interactive
id=$(az sql server list \
    --resource-group CreateSQLEndpointTutorial-rg \
    --query '[].[id]' \
    --output tsv)

az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group CreateSQLEndpointTutorial-rg \
    --vnet-name myVNet --subnet myBackendSubnet \
    --private-connection-resource-id $id \
    --group-ids sqlServer \
    --connection-name myConnection  
```

## <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域

在本部分中，你将使用 [az network private-dns zone create](/cli/azure/network/private-dns/zone#ext_privatedns_az_network_private_dns_zone_create) 创建和配置专用 DNS 区域。  

你将使用 [az network private-dns link vnet create](/cli/azure/network/private-dns/link/vnet#ext_privatedns_az_network_private_dns_link_vnet_create) 创建指向 dns 区域的虚拟网络链接。

你将使用 [az network private-endpoint dns-zone-group create](/cli/azure/network/private-endpoint/dns-zone-group#az_network_private_endpoint_dns_zone_group_create) 创建 dns 区域组。

* 区域名为 privatelink.database.windows.net
* 在虚拟网络“myVNet”中。
* 在资源组 CreateSQLEndpointTutorial-rg 中。
* 名为 myDNSLink 的 DNS 链接。
* 与 myPrivateEndpoint 相关联。
* 名为 MyZoneGroup 的区域组。

```azurecli-interactive
az network private-dns zone create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --name "privatelink.database.windows.net"

az network private-dns link vnet create \
    --resource-group CreateSQLEndpointTutorial-rg \
    --zone-name "privatelink.database.windows.net" \
    --name MyDNSLink \
    --virtual-network myVNet \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
   --resource-group CreateSQLEndpointTutorial-rg \
   --endpoint-name myPrivateEndpoint \
   --name MyZoneGroup \
   --private-dns-zone "privatelink.database.windows.net" \
   --zone-name sql
```

## <a name="test-connectivity-to-private-endpoint"></a>测试到专用终结点的连接

本部分将使用在上一步骤中创建的虚拟机通过专用终结点连接到 SQL 服务器。

1. 登录到 [Azure 门户](https://portal.azure.com) 
 
2. 在左侧导航窗格中选择“资源组”。

3. 选择“CreateSQLEndpointTutorial-rg”。

4. 选择“myVM”。

5. 在 **myVM** 的“概述”页上，选择“连接”，然后选择“堡垒”。

6. 选择蓝色的“使用堡垒”按钮。

7. 输入在创建虚拟机期间输入的用户名和密码。

8. 连接后，在服务器上打开 Windows PowerShell。

9. 输入 `nslookup <sqlserver-name>.database.windows.net`。 将 \<sqlserver-name> 替换为在上一步骤中创建的 SQL 服务器的名称。  你将收到类似于以下所示内容的消息：

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    将为 SQL 服务器名称返回专用 IP 地址 10.0.0.5。  此地址位于你之前创建的虚拟网络的子网中。


10. 在 myVM 上安装 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017)。

11. 打开 SQL Server Management Studio  。

12. 在“连接服务器”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 服务器类型 | 选择“数据库引擎”。|
    | 服务器名称 | 输入 \<sql-server-name>.database.windows.net |
    | 身份验证 | 选择“SQL Server 身份验证”。 |
    | 用户名 | 输入在服务器创建过程中所输入的用户名 |
    | Password | 输入在服务器创建过程中所输入的密码 |
    | 记住密码 | 请选择“是”。 |

13. 选择“连接”。

14. 浏览左侧菜单中的数据库。

15. （可选）创建或查询 mydatabase 中的信息。

16. 关闭到 myVM 的堡垒连接。 

## <a name="clean-up-resources"></a>清理资源 

用完专用终结点、SQL 服务器和 VM 之后，请删除资源组及其包含的所有资源： 

```azurecli-interactive
az group delete \
    --name CreateSQLEndpointTutorial-rg
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建：

* 虚拟网络和堡垒主机。
* 虚拟机。
* 具有专用终结点的 Azure SQL 服务器。

你使用虚拟机通过专用终结点安全测试了到 SQL 服务器的连接。

对于下一步，你可能还会对“与 Azure SQL 数据库建立专用连接的 Web 应用”体系结构场景感兴趣，该场景将虚拟网络以外的 Web 应用程序连接到数据库的专用终结点。
> [!div class="nextstepaction"]
> [与 Azure SQL 数据库建立专用连接的 Web 应用](/azure/architecture/example-scenario/private-web-app/private-web-app)