---
title: 防火墙规则 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 本文介绍了 Azure Database for PostgreSQL - 超大规模 (Citus) 的防火墙规则。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 559c5eca6fa8a6eceb37ade003d4f1983c0a1a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "90902092"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - 超大规模 (Citus) 上的防火墙规则
Azure Database for PostgreSQL 服务器防火墙会阻止所有对超大规模 (Citus) 协调器节点的访问，除非你指定哪些计算机有权访问。 防火墙基于每个请求的起始 IP 地址授予对服务器的访问权限。
要配置防火墙，请创建防火墙规则，以指定可接受的 IP 地址的范围。 可以在服务器级别创建防火墙规则。

防火墙规则：借助这些规则，客户端可以访问超大规模 (Citus) 协调器节点，即同一逻辑服务器内的所有数据库。 服务器级别防火墙规则可以使用 Azure 门户进行配置。 若要创建服务器级防火墙规则，用户必须是订阅所有者或订阅参与者。

## <a name="firewall-overview"></a>防火墙概述
默认情况下，防火墙会阻止所有对协调器节点的数据库访问。 若要从另一台计算机开始使用服务器，需要指定一个或多个服务器级防火墙规则以允许访问服务器。 使用防火墙规则指定要允许的来自 Internet 的 IP 地址范围。 对 Azure 门户网站本身的访问不受防火墙规则影响。
来自 Internet 和 Azure 的连接尝试必须首先通过防火墙，才能访问 PostgreSQL 数据库，如下图中所示：

:::image type="content" source="media/concepts-hyperscale-firewall-rules/1-firewall-concept.png" alt-text="防火墙工作流示例":::

## <a name="connecting-from-the-internet-and-from-azure"></a>从 Internet 和 Azure 连接

超大规模 (Citus) 服务器组防火墙控制谁可以连接到组的协调器节点。 防火墙通过咨询可配置的规则列表来确定访问权限。 每个规则都是允许进入访问的 IP 地址或地址范围。

当防火墙阻止连接时，可能会导致应用程序错误。 例如，使用 PostgreSQL JDBC 驱动程序会引发如下错误：

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "citus", database "citus", SSL

若要了解规则是如何定义的，请参阅[创建和管理防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)。

## <a name="troubleshooting-the-database-server-firewall"></a>数据库服务器防火墙故障排除
如果访问 Microsoft Azure Database for PostgreSQL - 超大规模 (Citus) 服务时的行为不符合预期，请考虑以下几点：

* 对允许列表的更改还没有生效：对超大规模 (Citus) 防火墙配置所做的更改可能需要 5 分钟的延迟才能生效。

* 用户未获得授权或使用的密码不正确：如果用户对服务器没有权限，或使用的密码不正确，则与服务器的连接会被拒绝。 创建防火墙设置，仅向客户端提供尝试连接到服务器的机会；每个客户端必须提供必需的安全凭据。

例如，使用 JDBC 客户端可能会出现以下错误。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **动态 IP 地址：** 如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，则可以尝试以下解决方法之一：

* 向 Internet 服务提供商 (ISP) 询问分配给客户端计算机的将用来访问超大规模 (Citus) 协调器节点的 IP 地址范围，然后将此 IP 地址范围添加为防火墙规则。

* 改为获取客户端计算机的静态 IP 地址，然后将该静态 IP 地址作为防火墙规则添加。

## <a name="next-steps"></a>后续步骤
有关如何创建服务器级和数据库级防火墙规则的文章，请参阅：
* [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)
