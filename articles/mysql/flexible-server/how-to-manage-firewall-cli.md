---
title: 管理防火墙规则 - Azure CLI - Azure Database for MySQL 灵活服务器
description: 使用 Azure CLI 命令行在 Azure Database for MySQL 灵活服务器中创建和管理防火墙规则。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 9/21/2020
ms.openlocfilehash: 268ab28c5f5aa500a110abf7cca650d7b7f14914
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778385"
---
# <a name="create-and-manage-azure-database-for-mysql---flexible-server-firewall-rules-using-the-azure-cli"></a>使用 Azure CLI 创建和管理 Azure Database for MySQL 灵活服务器防火墙规则

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供

Azure Database for MySQL 灵活服务器支持两种类型的互斥网络连接方法来连接到灵活服务器。 这两个选项如下：

- 公共访问（允许的 IP 地址）
- 专用访问（VNet 集成）

本文重点介绍如何使用 Azure CLI 创建具有公共访问（允许的 IP 地址）的 MySQL 服务器，并将提供有关 Azure CLI 命令的概述，你可使用这些命令在创建服务器后创建、更新、删除、列出和显示防火墙规则。 对于公共访问（允许的 IP 地址），与 MySQL 服务器的连接仅限于允许的 IP 地址。 防火墙规则中需要允许客户端 IP 地址。 若要了解详细信息，请参阅[公共访问（允许的 IP 地址）](./concepts-networking-public.md#public-access-allowed-ip-addresses)。 可以在创建服务器时定义防火墙规则（建议），但也可以稍后添加。

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

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>使用 Azure CLI 在创建灵活服务器期间创建防火墙规则

可使用 `az mysql flexible-server --public access` 命令创建具有公共访问（允许的 IP 地址）的灵活服务器，并在创建灵活服务器期间配置防火墙规则。 可使用“--public-access”开关来提供能够连接到服务器的允许的 IP 地址。 可提供要包含在允许的 IP 列表中的单个或一系列 IP 地址。 IP 地址范围必须用短划线分隔，并且不包含任何空格。 使用 CLI 创建灵活服务器有多种选项，如以下示例所示。

请参阅 Azure CLI [参考文档](/cli/azure/mysql/flexible-server)以获取可配置 CLI 参数的完整列表。 例如，在下面的命令中，可以选择指定资源组。

- 创建具有公共访问的灵活服务器并添加能够访问服务器的客户端 IP 地址

    ```azurecli-interactive
    az mysql flexible-server create --public-access <my_client_ip>
    ```

- 创建具有公共访问的灵活服务器，并添加能够访问此服务器的 IP 地址范围

    ```azurecli-interactive
    az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
    ```

- 创建具有公共访问的灵活服务器，并允许来自 Azure IP 地址的应用程序连接到灵活服务器

    ```azurecli-interactive
    az mysql flexible-server create --public-access 0.0.0.0
    ```

    > [!IMPORTANT]
    > 该选项将防火墙配置为允许从 Azure 服务和 Azure 中资源对此服务器进行公共访问，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问限制为仅允许授权用户访问。

- 创建具有公共访问的灵活服务器，并允许所有 IP 地址

    ```azurecli-interactive
    az mysql flexible-server create --public-access all
    ```

    > [!Note]
    > 上述命令将创建一个起始 IP 地址为 0.0.0.0、结束 IP 地址为 255.255.255.255 且不会阻止任何 IP 地址的防火墙规则。 Internet 上的任何主机都可访问此服务器。 强烈建议仅在不包含敏感数据的测试服务器上临时使用此规则。

- 创建具有公共访问且没有 IP 地址的灵活服务器

    ```azurecli-interactive
    az mysql flexible-server create --public-access none
    ```

    >[!Note]
    > 建议不要创建一个没有任何防火墙规则的服务器。 如果未添加任何防火墙规则，则没有客户端能够连接到该服务器。

## <a name="create-and-manage-firewall-rule-after-server-create"></a>创建服务器后创建和管理防火墙规则

在 Azure CLI 中使用 az mysql flexible-server firewall-rule 命令，创建、删除、列出、显示和更新防火墙规则。

命令：

- **create**：创建灵活服务器防火墙规则。
- **list**：列出灵活服务器防火墙规则。
- **update**：更新灵活服务器防火墙规则。
- **show**：显示灵活服务器防火墙规则的详细信息。
- **delete**：删除灵活服务器防火墙规则。

请参阅 Azure CLI [参考文档](/cli/azure/mysql/flexible-server)以获取可配置 CLI 参数的完整列表。 例如，在下面的命令中，可以选择指定资源组。

### <a name="create-a-firewall-rule"></a>创建防火墙规则

使用 `az mysql flexible-server firewall-rule create` 命令在服务器上创建新的防火墙规则。
若要允许一系列 IP 地址的访问，请提供 IP 地址用作起始 IP 地址和结束 IP 地址，如此示例所示。

```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

若要允许单个 IP 地址的访问，只需提供单个 IP 地址，如此示例所示。

```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

若要允许应用程序从 Azure IP 地址连接到灵活服务器，请提供 IP 地址 0.0.0.0 作为起始 IP，如此示例所示。

```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

   > [!IMPORTANT]
   > 该选项将防火墙配置为允许从 Azure 服务和 Azure 中资源对此服务器进行公共访问，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问限制为仅允许授权用户访问。

成功后，每个 create 命令输出会列出已创建的防火墙规则的详细信息，默认采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

### <a name="list-firewall-rules"></a>列出防火墙规则

使用 `az mysql flexible-server firewall-rule list` 命令列出服务器上现有的服务器防火墙规则。 请注意，服务器名称属性是在“--name”开关中指定的。

```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver
```

输出会默认采用 JSON 格式列出规则（如果有）。 可使用“--output table”开关，以更具可读性的表格格式输出结果。

```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>更新防火墙规则

使用 `az mysql flexible-server firewall-rule update` 命令更新服务器上现有的防火墙规则。 提供现有防火墙规则的名称作为输入，并提供要更新的起始 IP 地址和结束 IP 地址属性。

```azurecli-interactive
az mysql flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```

成功后，命令输出会列出更新后的防火墙规则的详细信息，默认采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

> [!NOTE]
> 如果不存在防火墙规则，更新命令将创建规则。

### <a name="show-firewall-rule-details"></a>显示防火墙规则详细信息

使用 `az mysql flexible-server firewall-rule show` 命令显示服务器中现有的防火墙规则详细信息。 输入现有防火墙规则的名称。

```azurecli-interactive
az mysql flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```

成功后，命令输出会列出指定的防火墙规则的详细信息，默认采用 JSON 格式。 如果失败，输出会改为显示错误消息文本。

### <a name="delete-a-firewall-rule"></a>删除防火墙规则

使用 `az mysql flexible-server firewall-rule delete` 命令删除服务器中现有的防火墙规则。 输入现有防火墙规则的名称。

```azurecli-interactive
az mysql flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```

成功后没有任何输出。 如果失败，会显示错误消息文本。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)
- 详细了解 [Azure Database for MySQL 灵活服务器防火墙规则](./concepts-networking-public.md#public-access-allowed-ip-addresses)
- [使用 Azure 门户创建和管理 Azure Database for MySQL 灵活服务器防火墙规则](./how-to-manage-firewall-portal.md)
