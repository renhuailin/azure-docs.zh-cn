---
title: 管理防火墙规则 - Azure 门户 - Azure Database for MySQL 灵活服务器
description: 使用 Azure 门户创建和管理 Azure Database for MySQL 灵活服务器防火墙规则
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 426a83836a16fd3d4a403b3cda015dd5f49af22e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778344"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>使用 Azure 门户创建和管理 Azure Database for MySQL 灵活服务器防火墙规则

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

Azure Database for MySQL 灵活服务器支持两种类型的互斥网络连接方法来连接到灵活服务器。 这两个选项如下：

1. 公共访问（允许的 IP 地址）
2. 专用访问（VNet 集成）

本文重点介绍如何使用 Azure 门户创建具有公共访问（允许的 IP 地址）的 MySQL 服务器，并将提供关于创建灵活服务器后管理防火墙规则的概述。 对于公共访问（允许的 IP 地址），与 MySQL 服务器的连接仅限于允许的 IP 地址。 防火墙规则中需要允许客户端 IP 地址。 若要了解详细信息，请参阅[公共访问（允许的 IP 地址）](./concepts-networking-public.md#public-access-allowed-ip-addresses)。 可以在创建服务器时定义防火墙规则（建议），但也可以稍后添加。 本文将概述如何使用公共访问（允许的 IP 地址）创建和管理防火墙规则。

## <a name="create-a-firewall-rule-when-creating-a-server"></a>创建服务器时创建防火墙规则

1. 在门户左上角选择“创建资源”  (+)。
2. 选择“数据库”   >   “Azure Database for MySQL”。 还可以在搜索框中输入“MySQL”  以查找该服务。
3. 选择“灵活服务器”作为部署选项。
4. 填写“基本”窗体。
5. 转到“网络”选项卡以配置连接到服务器的方式。
6. 在“连接方法”中，选择“公共访问(允许的 IP 地址)”。 若要创建防火墙规则，请指定防火墙规则名称和单个 IP 地址或地址范围。 如果希望将规则限制为单个 IP 地址，请在起始 IP 地址和结束 IP 地址字段中输入相同的地址。 打开防火墙后，管理员、用户和应用程序可以访问 MySQL 服务器上他们拥有有效凭据的任何数据库。
   > [!Note]
   > Azure Database for MySQL 灵活服务器在服务器级别创建防火墙。 除非创建了规则来为特定的 IP 地址打开防火墙，否则此防火墙会阻止外部应用程序和工具连接到服务器和服务器上的任何数据库。

7. 选择“查看 + 创建”，查看你的灵活服务器配置。
8. 选择“创建”以预配服务器。 预配可能需要几分钟时间。

## <a name="create-a-firewall-rule-after-server-is-created"></a>创建服务器后创建防火墙规则

1. 在 [Azure 门户](https://portal.azure.com/)中，选择要对其添加防火墙规则的 Azure Database for MySQL 灵活服务器。
2. 在“灵活服务器”页的“设置”标题下，单击“网络”以打开灵活服务器的网络页面 。

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/1-connection-security.png" alt-text="Azure portal - click Connection Security":::-->

3. 在防火墙规则中单击“添加当前客户端 IP 地址”。 该操作会自动创建一条防火墙规则，其中包含计算机的公共 IP 地址（由 Azure 系统标识）。

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/2-add-my-ip.png" alt-text="Azure portal - click Add My IP":::-->

4. 验证 IP 地址，并保存配置。 在某些情况下，Azure 门户识别出的 IP 地址与访问 Internet 和 Azure 服务器时所使用的 IP 地址不同。 因此，可能需要更改起始 IP 地址和结束 IP 地址，以使规则按预期方式工作。

   可以使用搜索引擎或其他联机工具来查看自己的 IP 地址。 例如，搜索“我的 IP 是多少”。

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/3-what-is-my-ip.png" alt-text="Bing search for What is my IP":::-->

5. 添加其他地址范围。 在 Azure Database for MySQL 灵活服务器的防火墙规则中，可以指定单个 IP 地址，也可以指定某个范围的地址。 如果希望将规则限制为单个 IP 地址，请在起始 IP 地址和结束 IP 地址字段中输入相同的地址。 打开防火墙后，管理员、用户和应用程序可以访问 MySQL 服务器上他们拥有有效凭据的任何数据库。

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/4-specify-addresses.png" alt-text="Azure portal - firewall rules":::-->

6. 在工具栏上单击“保存”以保存此防火墙规则。 等待出现有关防火墙规则更新已成功的确认消息。

   <!--:::image type="content" source="./media/howto-manage-firewall-portal/5-save-firewall-rule.png" alt-text="Azure portal - click Save":::-->

## <a name="connect-from-azure"></a>从 Azure 进行连接

需要启用 Azure 中部署的资源或应用程序以连接到灵活服务器。 这包括托管在 Azure 应用服务中的 Web 应用程序、在 Azure VM 上运行的 Web 应用程序、Azure 数据工厂数据管理网关等。

当 Azure 中的应用程序尝试连接到你的服务器时，防火墙将验证是否允许 Azure 连接。 可以通过从门户中的“网络”选项卡中选择“允许从 Azure 服务和 Azure 中的资源到此服务器的公共访问”选项来启用此设置，然后点击“保存”  。

资源无需在同一虚拟网络 (VNet) 或资源组中，即可使用防火墙规则启用这些连接。 如果不允许该连接尝试，则该请求将不会访问 Azure Database for MySQL 灵活服务器。

> [!IMPORTANT]
>该选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问限制为仅允许授权用户访问。
>
> 我们建议选择专用访问（VNet 集成）来安全地访问灵活服务器。
>

## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>通过 Azure 门户管理现有防火墙规则

重复以下步骤来管理防火墙规则。

- 若要添加当前计算机，请在防火墙规则中单击“+ 添加当前客户端 IP 地址”。 单击“保存”以保存更改。
- 若要添加其他 IP 地址，请键入“规则名称”、“起始 IP 地址”和“结束 IP 地址”。 单击“保存”以保存更改。
- 若要修改现有规则，单击规则中的任意字段并修改。 单击“保存”以保存更改。
- 要删除现有规则，请单击省略号 […]，并单击“删除”即可删除该规则  。 单击“保存”以保存更改。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Database for MySQL 灵活服务器中的网络](./concepts-networking.md)
- 详细了解 [Azure Database for MySQL 灵活服务器防火墙规则](./concepts-networking-public.md#public-access-allowed-ip-addresses)
- [使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则](./how-to-manage-firewall-cli.md)
