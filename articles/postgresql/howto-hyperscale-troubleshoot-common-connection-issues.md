---
title: 排查连接问题 - 超大规模 (Citus) - Azure Database for PostgreSQL
description: 了解如何排查 Azure Databases for PostgreSQL 的连接问题 - 超大规模 (Citus)
keywords: postgresql 连接, 连接字符串, 连接问题, 暂时性错误, 连接错误
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/8/2019
ms.openlocfilehash: e1c6825820ae943d10157279dfe93922a7521b75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91295611"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>排查 Azure Databases for PostgreSQL 的连接问题 - 超大规模 (Citus)

连接问题可能由多种因素造成，例如：

* 防火墙设置
* 连接超时
* 登录信息不正确
* 已达到服务器组的连接限制
* 服务的基础结构出现问题
* 服务维护
* 协调器节点故障转移到新硬件

一般超大规模 (Citus) 的连接问题可按以下方式分类：

* 暂时性错误（短暂或间歇性）
* 持久或非暂时性错误（定期重复发生的错误）

## <a name="troubleshoot-transient-errors"></a>对暂时性错误进行故障排除

出现暂时性错误的原因有多种。 最常见的原因包括系统维护、硬件或软件错误以及协调器节点 vCore 升级。

为超大规模 (Citus) 服务器组节点启用高可用性可以自动缓解这些类型的问题。 但应用程序仍应为暂时性失去连接作好准备。 并且其他事件可能需要更长的时间才能缓解，例如，某个大型事务导致需要长时间才能恢复运行。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解决暂时性连接问题的步骤

1. 查看 [Microsoft Azure 服务仪表板](https://azure.microsoft.com/status)，了解应用程序报错期间出现的任何已知中断。
2. 连接到云服务的应用程序，例如超大规模 (Citus)，应会出现暂时性错误并应作出正确反应。 例如，应用程序应实现重试逻辑来处理这些错误，而不是向用户显示应用程序错误。
3. 当服务器组即将达到其资源限制时，错误可能会看起来像是暂时性连接问题。 增加节点 RAM 或添加辅助节点和重新均衡数据可能会有所帮助。
4. 如果连接问题仍然存在，或持续时间超过 60 秒，或每天发生一次以上，请提交 Azure 支持请求，方法是在 [Azure 支持](https://azure.microsoft.com/support/options)网站上选择“获取支持”。

## <a name="troubleshoot-persistent-errors"></a>排查一再出现的错误

如果应用程序持久未能连接超大规模 (Citus)，最常见的原因是防火墙配置错误或用户错误。

* 协调器节点防火墙配置：请确保超大规模 (Citus) 服务器防火墙配置为允许来自你的客户端（包括代理服务器和网关）的连接。
* 客户端防火墙配置：客户端的防火墙必须允许连接到数据库服务器。 某些防火墙要求不仅要允许应用程序（按名称），同时要允许服务器的 IP 地址和端口。
* 用户错误：仔细检查连接字符串。 可能输入了错误的参数值，如服务器名称。 可以在 Azure 门户中找到各种语言框架和 psql 的连接字符串。 在超大规模 (Citus) 服务器组中转到“连接字符串”页。 另外请记住，超大规模 (Citus) 群集只具有一个数据库，其预定义的名称为“Citus”。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解决永久性连接问题的步骤

1. 设置[防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)以允许客户端 IP 地址。 （仅出于临时测试目的）使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束 IP 地址，来设置一个防火墙规则。 该规则会向所有 IP 地址打开服务器。 如果该规则可以解决连接性问题，请将其删除，再针对有相应限制的 IP 地址或地址范围创建防火墙规则。
2. 在客户端与 Internet 之间的所有防火墙上，确保为出站连接打开端口 5432。
3. 验证连接字符串和其他连接设置。
4. 在仪表板中检查服务运行状况。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure Database for PostgreSQL - 超大规模 (Citus) 中的防火墙规则](concepts-hyperscale-firewall-rules.md)的相关概念
* 了解如何[为 Azure Database for PostgreSQL - 超大规模 (Citus) 管理防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)
