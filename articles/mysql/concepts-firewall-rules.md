---
title: 防火墙规则 - Azure Database for MySQL
description: 了解如何使用防火墙规则启用到 Azure Database for MySQL 服务器的连接。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 83b2d95e875ee25137a69ff310de244e846f1753
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122651964"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database for MySQL 服务器防火墙规则

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

在指定哪些计算机具有访问权限之前，防火墙将禁止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予对服务器的访问权限。

要配置防火墙，请创建防火墙规则，指定可接受的 IP 地址的范围。 可以在服务器级别创建防火墙规则。

防火墙规则：这些规则允许客户端访问整个 Azure Database for MySQL 服务器，即同一逻辑服务器内的所有数据库。 可使用 Azure 门户或 Azure CLI 命令配置服务器级的防火墙规则。 若要创建服务器级防火墙规则，用户必须是订阅所有者或订阅参与者。

## <a name="firewall-overview"></a>防火墙概述
防火墙将默认阻止对 Azure Database for MySQL 服务器的所有数据库访问。 若要从另一台计算机开始使用服务器，需要指定一个或多个服务器级防火墙规则以允许访问服务器。 使用防火墙规则指定要允许的来自 Internet 的 IP 地址范围。 对 Azure 门户网站本身的访问不受防火墙规则影响。

来自 Internet 和 Azure 的连接尝试必须首先通过防火墙，才能访问 Azure Database for MySQL 数据库，如下图中所示：

:::image type="content" source="./media/concepts-firewall-rules/1-firewall-concept.png" alt-text="防火墙工作流示例":::

## <a name="connecting-from-the-internet"></a>从 Internet 连接
服务器级防火墙规则适用于 Azure Database for MySQL 服务器上的所有数据库。

如果该请求的 IP 地址位于服务器级防火墙规则中指定的某个范围内，则授予连接权限。

如果该请求的 IP 地址位于任何数据库级或服务器级防火墙规则中指定的范围外，则连接请求失败。

## <a name="connecting-from-azure"></a>从 Azure 连接
建议找到任何应用程序或服务的传出 IP 地址，并显式允许访问这些单个 IP 地址或范围。 例如，可以查找 Azure 应用服务的传出 IP 地址，或使用绑定到虚拟机或其他资源的公共 IP（请参阅下面的内容，了解如何通过服务终结点与虚拟机的专用 IP 进行连接）。 

如果某个固定的传出 IP 地址不适用于 Azure 服务，可以考虑启用来自所有 Azure 数据中心 IP 地址的连接。 可以从 Azure 门户启用此设置，方法是：从“连接安全性”窗格将“允许访问 Azure 服务”选项设为“启用”并点击“保存”。 在 Azure CLI 中，起始和结束地址为 0.0.0.0 的防火墙规则设置执行等效操作。 如果不允许该连接尝试，则该请求将不会访问 Azure Database for MySQL 服务器。

> [!IMPORTANT]
> “允许访问 Azure 服务”选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问限制为仅允许授权用户访问。
> 

:::image type="content" source="./media/concepts-firewall-rules/allow-azure-services.png" alt-text="在门户中配置“允许访问 Azure 服务”":::

### <a name="connecting-from-a-vnet"></a>从 VNet 连接
若要从 VNet 安全连接到 Azure Database for MySQL 服务器，请考虑使用 [VNet 服务终结点](./concepts-data-access-and-security-vnet.md)。 

## <a name="programmatically-managing-firewall-rules"></a>以编程方式管理防火墙规则
除了 Azure 门户外，还可使用 Azure CLI 通过编程方式管理防火墙规则。 另请参阅[使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>排查防火墙问题
对 Microsoft Azure Database for MySQL 服务器服务的访问与预期不符时，请考虑以下几点：

* 对允许列表的更改尚未生效：对 Azure Database for MySQL 服务器防火墙配置所做的更改可能需要多达 5 分钟的延迟才可生效。

* 登录名未授权或使用了错误的密码：如果某个登录名对 Azure Database for MySQL 服务器没有权限或者使用的密码不正确，则与 Azure Database for MySQL 服务器的连接会被拒绝。 创建防火墙设置仅向客户端提供尝试连接到服务器的机会；每个客户端必须提供必需的安全凭据。

* 动态 IP 地址：如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，可尝试以下解决方法之一：

   * 向 Internet 服务提供商 (ISP) 询问分配给客户端计算机、用于访问 Azure Database for MySQL 服务器的 IP 地址范围，然后将该 IP 地址范围作为防火墙规则添加。

   * 改为获取客户端计算机的静态 IP 地址，并将该 IP 地址作为防火墙规则添加。

* **服务器 IP 似乎为公共 IP：** 到 Azure Database for MySQL 服务器的连接通过可公开访问的 Azure 网关进行路由。 但是，实际的服务器 IP 受防火墙保护。 有关详细信息，请参阅[连接体系结构文章](concepts-connectivity-architecture.md)。 

* **无法使用允许的 IP 从 Azure 资源连接：** 检查是否为你连接时所在的子网启用了 Microsoft.Sql 服务终结点。 如果启用了 Microsoft.Sql，则表示你只想在该子网上使用 [VNet 服务终结点规则](concepts-data-access-and-security-vnet.md)。

   例如，如果从启用了 Microsoft.Sql 的子网中的 Azure VM 进行连接，但没有相应的 VNet 规则，则可能会看到以下错误：`FATAL: Client from Azure Virtual Networks is not allowed to access the server`

* 防火墙规则不适用于 IPv6 格式：防火墙规则必须采用 IPv4 格式。 如果以 IPv6 格式指定防火墙规则，则会显示验证错误。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-cli.md)
* [Azure Database for MySQL 中的 VNet 服务终结点](./concepts-data-access-and-security-vnet.md)
