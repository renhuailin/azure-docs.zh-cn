---
title: Azure Database for PostgreSQL 中的防火墙规则 - 灵活服务器
description: 本文介绍如何使用防火墙规则通过公用网络部署选项连接到 Azure Database for PostgreSQL 灵活服务器。
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: bdda8163bacd596eafab2a9b2d10d914012a3efb
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866866"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL 中的防火墙规则 - 灵活服务器
运行 Azure Database for PostgreSQL 灵活服务器时，有两个主要的网络选项。 这两个选项是“专用访问(虚拟网络集成)”和“公共访问(允许的 IP 地址)”。 

利用公共访问，可通过公共终结点访问 Azure Database for PostgreSQL 服务器。 默认情况下，防火墙会阻止对服务器的所有访问。 要指定哪些 IP 主机可以访问服务器，请创建服务器级别的防火墙规则。 防火墙规则指定允许的公共 IP 地址范围。 防火墙基于每个请求的起始 IP 地址授予对服务器的访问权限。

可通过使用 Azure 门户或 Azure CLI 命令创建防火墙规则。 用户必须是订阅所有者或订阅参与者。

服务器级防火墙规则适用于 Azure Database for PostgreSQL 服务器上的所有数据库。 这些规则不会影响对 Azure 门户网站的访问。

以下示意图显示来自 Internet 和 Azure 的连接尝试必须首先通过防火墙，然后才能访问 PostgreSQL 数据库：

:::image type="content" source="../media/concepts-firewall-rules/1-firewall-concept.png" alt-text="概述防火墙工作原理的示意图。":::

## <a name="connect-from-the-internet"></a>通过 Internet 连接
如果该请求的源 IP 地址位于服务器级防火墙规则中指定的某个范围内，则授予连接权限。 否则，拒绝该请求。 

例如，如果应用程序与 PostgreSQL 的 Java Database Connectivity (JDBC) 驱动程序连接，则可能会遇到此错误，因为防火墙正在阻止连接：

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connect-from-azure"></a>从 Azure 进行连接
建议找到任何应用程序或服务的传出 IP 地址，并显式允许访问这些单个 IP 地址或范围。 例如，可以查找 Azure 应用服务应用的传出 IP 地址，或使用绑定到虚拟机的公共 IP。 

如果某个固定的传出 IP 地址不适用于 Azure 服务，可以考虑启用来自所有 Azure 数据中心 IP 地址的连接：

1. 在 Azure 门户中的“网络”窗格上，选择“允许从 Azure 内的任何 Azure 服务公开访问此服务器”复选框 。 
1. 选择“保存”  。 

> [!IMPORTANT]
> “允许从 Azure 内的任何 Azure 服务公开访问此服务器”选项将防火墙配置为允许来自 Azure 的所有连接，包括来自其他客户的订阅的连接。 如果使用此选项，请确保登录名和用户权限将访问权限限制为仅已授权用户。 

:::image type="content" source="./media/concepts-firewall-rules/allow-public-access.png" alt-text="显示允许在门户中访问 Azure 服务的选择的屏幕截图。":::

## <a name="programmatically-manage-firewall-rules"></a>以编程方式管理防火墙规则
除使用 Azure 门户外，还可使用 Azure CLI 通过编程方式管理防火墙规则。 

在 Azure CLI 中，起始地址和结束地址都等于 0.0.0.0 的防火墙规则设置与门户中“允许从 Azure 内的任何 Azure 服务公开访问此服务器”选项等效。 如果防火墙规则拒绝连接尝试，则应用不会访问 Azure Database for PostgreSQL 服务器。

## <a name="troubleshoot-firewall-problems"></a>排查防火墙问题
在对 Azure Database for PostgreSQL 服务的访问与预期不符时，请考虑以下可能性：

* **对允许列表的更改尚未生效**：更改 Azure Database for PostgreSQL 服务器的防火墙配置可能最多需要五分钟。

* **登录未授权或使用了错误的密码**：如果某个登录对 Azure Database for PostgreSQL 服务器没有权限或者密码不正确，则与服务器的连接会被拒绝。 创建防火墙设置仅向客户端提供尝试连接到服务器的机会。 每个客户端必须提供必需的安全凭据。

  例如，如果 JDBC 客户端的身份验证失败，可能会出现以下错误：

  > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **防火墙不允许动态 IP 地址**：如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，可尝试以下解决方法之一：

  * 向 Internet 服务提供商 (ISP) 询问分配给客户端计算机、用于访问 Azure Database for PostgreSQL 服务器的 IP 地址范围。 然后，将该 IP 地址范围作为防火墙规则添加。

  * 改为获取客户端计算机的静态 IP 地址，然后将该静态 IP 地址作为防火墙规则添加。

* **防火墙规则不适用于 IPv6 格式**：防火墙规则必须采用 IPv4 格式。 如果以 IPv6 格式指定防火墙规则，则会显示验证错误。


## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](how-to-manage-firewall-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则](how-to-manage-firewall-cli.md)
