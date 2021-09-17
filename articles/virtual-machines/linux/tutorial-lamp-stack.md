---
title: 教程 - 在 VM 上部署 LAMP 和 WordPress
description: 本教程介绍如何在 Azure 中的 Linux 虚拟机上安装 LAMP 堆栈和 WordPress。
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: ec73bc6c6f7563f1513f4575b5ee3c9b4a244e7c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698995"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>教程：在 Azure Linux VM 上安装 LAMP 堆栈

适用于：:heavy_check_mark: Linux VM 

本文逐步讲解如何在 Azure 中的 Ubuntu VM 上部署 Apache Web 服务器、MySQL 和 PHP（LAMP 堆栈）。 若要了解 LAMP 服务器的运作情况，可以选择性地安装并配置 WordPress 站点。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 Ubuntu VM 
> * 为 Web 流量打开端口 80
> * 安装 Apache、MySQL 和 PHP
> * 验证安装和配置
> * 安装 WordPress 

此设置用于快速测试或概念证明。 有关 LAMP 堆栈的详细信息，包括针对生产环境的建议，请参阅 [Ubuntu 文档](https://help.ubuntu.com/community/ApacheMySQLPHP)。

本教程在 [Azure Cloud Shell](../../cloud-shell/overview.md) 中使用 CLI，后者已不断更新到最新版本。 若要打开 Cloud Shell，请从任何代码块的顶部选择“试一试”  。

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm) 命令创建 VM。 

下面的示例创建一个名为 *myVM* 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。 该命令还会将 *azureuser* 设置为管理员用户名。 稍后要使用此名称连接到 VM。 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 后，Azure CLI 会显示类似于以下示例的信息。 记下 `publicIpAddress`。 在后续步骤中，将使用此地址访问 VM。

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80 

默认情况下，仅允许通过 SSH 连接登录到 Azure 中部署的 Linux VM。 由于此 VM 将用作 Web 服务器，因此需要从 Internet 打开端口 80。 使用 [az vm open-port](/cli/azure/vm) 命令打开所需端口。  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

有关向 VM 打开端口的详细信息，请参阅[打开端口](nsg-quickstart.md)。

## <a name="ssh-into-your-vm"></a>通过 SSH 连接到 VM

如果还不知道 VM 的公共 IP 地址，请运行 [az network public-ip list](/cli/azure/network/public-ip) 命令。 在多个后续步骤中都需要用到此 IP 地址。

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

使用以下命令创建与虚拟机的 SSH 会话。 使用虚拟机的正确的公共 IP 地址进行替换。 在此示例中，IP 地址为 *40.68.254.142*。 *azureuser* 是创建 VM 时设置的管理员用户名。

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>安装 Apache、MySQL 和 PHP

运行以下命令更新 Ubuntu 包源并安装 Apache、MySQL 和 PHP。 请注意命令末尾的脱字号 (^)，它是 `lamp-server^` 包名称的一部分。 


```bash
sudo apt update && sudo apt install lamp-server^
```

系统会提示安装包和其他依赖项。 此股从会安装最低要求的 PHP 扩展，这些扩展是通过 MySQL 使用 PHP 所必需的。  

## <a name="verify-apache"></a>验证 Apache

使用以下命令检查 Apache 版本：
```bash
apache2 -v
```

安装 Apache 并向 VM 打开端口 80 之后，即可通过 Internet 访问 Web 服务器。 若要查看 Apache2 Ubuntu 默认页，请打开 Web 浏览器并输入 VM 的公共 IP 地址。 使用通过 SSH 连接到 VM 时所用的公共 IP 地址：

![Apache 默认页][3]


## <a name="verify-and-secure-mysql"></a>验证并保护 MySQL

使用以下命令检查 MySQL 版本（请注意大写的 `V` 参数）：

```bash
mysql -V
```

若要帮助保护 MySQL 安装，包括设置 root 密码，请运行 `mysql_secure_installation` 脚本。 

```bash
sudo mysql_secure_installation
```

还可以选择设置“验证密码”插件（推荐）。 然后，为 MySQL root 用户设置一个密码，并为您的环境配置剩余的安全设置。 建议对所有问题都回答“Y”（是）。

如果想要试用 MySQL 功能（创建 MySQL 数据库、添加用户或更改配置设置），请登录到 MySQL。 此步骤非本教程必需步骤。

```bash
sudo mysql -u root -p
```

完成后，键入 `\q` 退出 mysql 提示符。

## <a name="verify-php"></a>验证 PHP

使用以下命令检查 PHP 版本：

```bash
php -v
```

如果想要进一步测试，请创建一个可在浏览器中查看的快速 PHP 信息页。 以下命令创建 PHP 信息页：

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

现在，可以检查创建的 PHP 信息页。 打开浏览器并转到 `http://yourPublicIPAddress/info.php`。 替换 VM 的公共 IP 地址。 应如下图所示。

![PHP 信息页][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>后续步骤

本教程在 Azure 中部署了一台 LAMP 服务器。 你已了解如何：

> [!div class="checklist"]
> * 创建 Ubuntu VM
> * 为 Web 流量打开端口 80
> * 安装 Apache、MySQL 和 PHP
> * 验证安装和配置
> * 在 LAMP 服务器上安装 WordPress

转到下一教程，了解如何使用 TLS/SSL 证书保护 Web 服务器。

> [!div class="nextstepaction"]
> [使用 TLS 保护 Web 服务器](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
