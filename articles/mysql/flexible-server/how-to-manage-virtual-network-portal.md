---
title: 管理虚拟网络 - Azure 门户 - Azure Database for MySQL 灵活服务器
description: 使用 Azure 门户在 Azure Database for MySQL 灵活服务器中创建和管理虚拟网络
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b317dc91f8d4768e767a93d618e9c768d4039659
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778339"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>使用 Azure 门户在 Azure Database for MySQL 灵活服务器中创建和管理虚拟网络

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

Azure Database for MySQL 灵活服务器支持两种类型的互斥网络连接方法来连接到灵活服务器。 这两个选项如下：

- 公共访问（允许的 IP 地址）
- 专用访问（VNet 集成）

本文将重点介绍使用 Azure 门户创建具有专用访问（VNet 集成）的 MySQL 服务器。 通过专用访问（VNet 集成），可以将灵活服务器部署到你自己的 [Azure 虚拟网络](../../virtual-network/virtual-networks-overview.md)中。 Azure 虚拟网络提供专用的安全网络通信。 对于专用访问，与 MySQL 服务器的连接仅限于你的虚拟网络。 若要了解详细信息，请参阅[专用访问（VNet 集成）](./concepts-networking-vnet.md#private-access-vnet-integration)。

>[!Note]
>在服务器创建过程中，可以将灵活服务器部署到虚拟网络和子网中。 部署灵活服务器后，无法将它移动到其他虚拟网络、子网或公共访问（允许的 IP 地址）。

## <a name="prerequisites"></a>先决条件

若要在虚拟网络中创建灵活服务器，你需要：

- [虚拟网络](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > 虚拟网络和子网应与你的灵活服务器位于同一区域和订阅中。

- [将子网委派](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service)给 Microsoft.DBforMySQL/flexibleServers。 该委派意味着只有 Azure Database for MySQL 灵活服务器才能使用该子网。 不能在委派子网中使用其他 Azure 资源类型。

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>在现有的虚拟网络中创建 Azure Database for MySQL 灵活服务器

1. 在门户左上角选择“创建资源”  (+)。
2. 选择“数据库”   >   “Azure Database for MySQL”。 还可以在搜索框中输入“MySQL”  以查找该服务。
3. 选择“灵活服务器”作为部署选项。
4. 填写“基本信息”表单
5. 转到“网络”选项卡。
6. 在“连接方法”中，选择“专用访问（VNet 集成）” 。 转到“虚拟网络”部分，可以选择已有的“虚拟网络”和委托给 Microsoft.DBforMySQL/flexibleServers 的“子网”，也可通过单击“创建虚拟网络”链接创建新的虚拟网络   。
    > [!Note]
    > 下拉列表中只列出同一区域和订阅中的虚拟网络和子网。 </br>
    > 所选子网将委托给 Microsoft.DBforMySQL/flexibleServers。 这意味着只有 Azure Database for MySQL 灵活服务器才能使用该子网。</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="VNet 集成":::

7. 创建新的或选择现有的“专用 DNS 区域”。
    > [!NOTE]
    > 专用 DNS 区域名称必须以 `mysql.database.azure.com` 结尾。 </br>
    > 如果没有看到用于创建新的专用 DNS 区域的选项，请在“基本信息”选项卡上输入服务器名称。</br>
    > 将灵活服务器部署到虚拟网络和子网后，无法将它移动到公共访问（允许的 IP 地址）。</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="dnsconfiguration":::
8. 选择“查看 + 创建”，查看你的灵活服务器配置。
9. 选择“创建”以预配服务器。 预配可能需要几分钟时间。

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 创建和管理 Azure Database for MySQL 灵活服务器虚拟网络](./how-to-manage-virtual-network-cli.md)。
- 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)
- 详细了解 [Azure Database for MySQL 灵活服务器虚拟网络](./concepts-networking-vnet.md#private-access-vnet-integration)。
