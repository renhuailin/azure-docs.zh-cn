---
title: 防火墙规则 - Azure Database for PostgreSQL - 灵活服务器
description: 本文介绍如何使用防火墙规则连接到 Azure Database for PostgreSQL - 含公用网络部署选项的灵活服务器。
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: f1946f1a0483b2627018c32cf99ec503ed2cf5f5
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114732108"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 中的防火墙规则 - 灵活服务器
运行 Azure Database for PostgreSQL（灵活服务器）时，可选择两个主要网络选项。 这两个选项是“专用访问(VNet 集成)”和“公共访问(允许的 IP 地址)” 。 利用公共访问，可通过公共终结点访问灵活服务器。
选择了公用访问选项时，默认情况下，在指定允许哪些 IP 主机访问数据库服务器之前，Azure Database for PostgreSQL 服务器是安全的，防火墙会阻止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予对服务器的访问权限。
要配置防火墙，请创建防火墙规则，以指定可接受的 IP 地址的范围。 可以在服务器级别创建防火墙规则。
防火墙规则：这些规则允许客户端访问整个 Azure Database for PostgreSQL 服务器，即同一逻辑服务器内的所有数据库。 可以通过使用 Azure 门户或 Azure CLI 命令配置服务器级防火墙规则。 若要创建服务器级防火墙规则，用户必须是订阅所有者或订阅参与者。

## <a name="firewall-overview"></a>防火墙概述
默认情况下，防火墙将阻止对 Azure Database for PostgreSQL 服务器的所有访问。 若要从另一台计算机/客户端或应用程序访问服务器，需要指定一个或多个服务器级防火墙规则以允许访问服务器。 使用防火墙规则指定允许的公共 IP 地址范围。 对 Azure 门户网站本身的访问不受防火墙规则影响。
来自 Internet 和 Azure 的连接尝试必须首先通过防火墙，才能访问 PostgreSQL 数据库，如下图中所示：

:::image type="content" source="../media/concepts-firewall-rules/1-firewall-concept.png" alt-text="防火墙工作流示例":::

## <a name="connecting-from-the-internet"></a>从 Internet 连接
服务器级防火墙规则适用于 Azure Database for PostgreSQL 服务器上的所有数据库。 如果该请求的源 IP 地址位于服务器级防火墙规则中指定的某个范围内，则允许进行连接；如果不在范围内，则会被拒绝。 例如，如果应用程序与 PostgreSQL 的 JDBC 驱动程序连接，则在防火墙阻止连接时尝试进行连接可能会遇到此错误。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>从 Azure 连接
建议找到任何应用程序或服务的传出 IP 地址，并显式允许访问这些单个 IP 地址或范围。 例如，可以查找 Azure 应用服务的传出 IP 地址，或使用绑定到虚拟机或其他资源的公共 IP（请参阅下面的内容，了解如何通过服务终结点与虚拟机的专用 IP 进行连接）。 

如果某个固定的传出 IP 地址不适用于 Azure 服务，可以考虑启用来自所有 Azure 数据中心 IP 地址的连接。 可以从 Azure 门户启用此设置，方法是：从“连接安全性”窗格将“允许访问 Azure 服务”选项设为“启用”并点击“保存”。 在 Azure CLI 中，起始和结束地址为 0.0.0.0 的防火墙规则设置执行等效操作。 如果系统根据防火墙规则拒绝了该次连接尝试，则该请求将不会访问 Azure Database for PostgreSQL 服务器。

> [!IMPORTANT]
> “允许访问 Azure 服务”选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 选择该选项时，请确保登录名和用户权限将访问限制为仅允许授权用户访问。
> 

:::image type="content" source="../media/concepts-firewall-rules/allow-azure-services.png" alt-text="在门户中配置“允许访问 Azure 服务”":::
## <a name="programmatically-managing-firewall-rules"></a>以编程方式管理防火墙规则
除了 Azure 门户外，还可使用 Azure CLI 通过编程方式管理防火墙规则。


## <a name="troubleshooting-firewall-issues"></a>排查防火墙问题
在对 Microsoft Azure Database for PostgreSQL 服务器服务的访问与期望不符时，请考虑以下几点：

* 对允许列表的更改尚未生效：对 Azure Database for PostgreSQL 服务器防火墙配置所做的更改可能需要多达 5 分钟的延迟才可生效。

* 登录名未授权或使用了错误的密码：如果某个登录名对 Azure Database for PostgreSQL 服务器没有权限或者使用的密码不正确，则与 Azure Database for PostgreSQL 服务器的连接会被拒绝。 创建防火墙设置，仅向客户端提供尝试连接到服务器的机会；每个客户端必须提供必需的安全凭据。

   例如，使用 JDBC 客户端可能会出现以下错误。
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **动态 IP 地址：** 如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，则可以尝试以下解决方法之一：

   * 向 Internet 服务提供商 (ISP) 询问分配给客户端计算机、用于访问 Azure Database for PostgreSQL 服务器的 IP 地址范围，然后将该 IP 地址范围作为防火墙规则添加。

   * 改为获取客户端计算机的静态 IP 地址，然后将该静态 IP 地址作为防火墙规则添加。

  


* 防火墙规则不适用于 IPv6 格式：防火墙规则必须采用 IPv4 格式。 如果以 IPv6 格式指定防火墙规则，则会显示验证错误。


## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](how-to-manage-firewall-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则](how-to-manage-firewall-cli.md)
