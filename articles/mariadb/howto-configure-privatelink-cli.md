---
title: 专用链接 - Azure CLI - Azure Database for MariaDB
description: 了解如何使用 Azure CLI 为 Azure Database for MariaDB 配置专用链接
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55f375c83affea8585ec7ebf881a80315ff7a38c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361310"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-cli"></a>使用 CLI 创建和管理用于 Azure Database for MariaDB 的专用链接

专用终结点是 Azure 中专用链接的构建基块。 它使 Azure 资源（例如虚拟机 (VM)）能够以私密方式来与专用链接资源通信。 在本文中，你将学习如何使用 Azure CLI 在 Azure 虚拟网络和带有 Azure 专用终结点的 Azure Database for MariaDB 服务器中创建 VM。

> [!NOTE]
> 专用链接功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MariaDB 服务器。 请确保数据库服务器位于其中一个定价层中。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 需要 [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-cli.md)。 

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

在创建任何资源之前，必须创建一个资源组以托管虚拟网络。 使用 [az group create](/cli/azure/group) 创建资源组。 此示例在 " *westeurope* " 位置创建名为 " *myResourceGroup* " 的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>创建虚拟网络
使用 [az network vnet create](/cli/azure/network/vnet) 创建虚拟网络。 此示例创建名为 *myVirtualNetwork* 的默认虚拟网络，它具有一个名为 *mySubnet* 的子网：

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>禁用子网专用终结点策略 
Azure 会将资源部署到虚拟网络中的子网，因此，你需要创建或更新子网，以禁用专用终结点[网络策略](../private-link/disable-private-endpoint-network-policy.md)。 使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) 更新名为 *mySubnet* 的子网配置：

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>创建 VM 
使用 az vm create 创建 VM。 出现提示时，请提供要用作 VM 登录凭据的密码。 此示例创建名为 *myVm* 的 VM： 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 记下 VM 的公共 IP 地址。 在下一步中，此地址将用于从 Internet 连接到 VM。

## <a name="create-an-azure-database-for-mariadb-server"></a>创建 Azure Database for MariaDB 服务器 
使用 az mariadb server create 命令创建 Azure Database for MariaDB。 请记住，你的 MariaDB 服务器名称必须在 Azure 中是唯一的，因此请将括号中的占位符值替换为你自己的唯一值： 

```azurecli-interactive
# Create a server in the resource group 
az mariadb server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

> [!NOTE]
> 在某些情况下，Azure Database for MariaDB 和 VNet 子网位于不同的订阅中。 在这些情况下，必须确保以下配置：
> - 确保两个订阅都注册了 Microsoft.DBforMariaDB 资源提供程序。 有关详细信息，请参阅[资源管理器注册][resource-manager-portal]

## <a name="create-the-private-endpoint"></a>创建专用终结点 
为虚拟网络中的 MariaDB 服务器创建专用终结点： 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforMariaDB/servers" --query "id" -o tsv) \    
    --group-id mariadbServer \  
    --connection-name myConnection  
 ```


## <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域 
为 MariDB 服务器域创建专用 DNS 区域，并创建一个与虚拟网络关联的链接。 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mariadb.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mariadb.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MariaDB name 
 
 
#Create DNS records 
az network private-dns record-set a create --name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name mydemoserver --zone-name privatelink.mariadb.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> 客户 DNS 设置中的 FQDN 未解析为配置的专用 IP。 你必须为已配置的 FQDN 设置一个 DNS 区域，如[此处](../dns/dns-operations-recordsets-portal.md)所示。

## <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 Internet 连接到 VM *myVm*，如下所示：

1. 在门户的搜索栏中，输入 *myVm*。

1. 选择“连接”按钮。 选择“连接”按钮后，“连接到虚拟机”随即打开 。

1. 选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( *.rdp*) 文件，并将其下载到计算机。

1. 打开 downloaded.rdp 文件。

    1. 出现提示时，选择“连接”。

    1. 输入在创建 VM 时指定的用户名和密码。

        > [!NOTE]
        > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据 。

1. 选择“确定”。

1. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续” 。

1. VM 桌面出现后，将其最小化以返回到本地桌面。  

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>以私密方式从 VM 访问 MariaDB 服务器

1. 在  *myVM* 的远程桌面中打开 PowerShell。

2. 输入  `nslookup mydemoserver.privatelink.mariadb.database.azure.com`。 

    将收到类似于下面的消息：
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. 使用任何可用的客户端测试 MariaDB 服务器的专用链接连接。 在下面的示例中，我使用了 [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) 来执行该操作。

4. 在“新建连接”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 连接名称| 选择所选的连接名称。|
    | 主机名 | 选择 *mydemoserver.privatelink.mariadb.database.azure.com* |
    | 用户名 | 输入用户名 username@servername，该用户名是在创建 MariaDB 服务器期间提供的。 |
    | 密码 | 输入创建 MariaDB 服务器期间提供的密码。 |
    ||

5. 选择“测试连接”或“确定” 。

6. （可选）从左侧菜单浏览数据库，并从 MariaDB 数据库创建或查询信息

8. 关闭与 myVm 的远程桌面连接。

## <a name="clean-up-resources"></a>清理资源 
如果不再需要资源组及其所有资源，可以使用 az group delete 将其删除： 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>后续步骤
了解[什么是 Azure 专用终结点的](../private-link/private-endpoint-overview.md)详细信息

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
