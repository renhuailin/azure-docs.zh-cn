---
title: 自定义 DNS
titleSuffix: Azure SQL Managed Instance
description: 本主题介绍使用 Azure SQL 托管实例的自定义 DNS 的配置选项。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova
ms.date: 07/17/2019
ms.openlocfilehash: adcd6e88973ac9164eebbe57d92050b70f173d61
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722815"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>为 Azure SQL 托管实例配置自定义 DNS
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 托管实例必须部署在 Azure [虚拟网络 (VNet)](../../virtual-network/virtual-networks-overview.md) 中。 有几个方案（如数据库邮件，将服务器链接到云或混合环境中的其他 SQL Server 实例）需要从 SQL 托管实例解析专用主机名。 在这种情况下，需要在 Azure 中配置自定义 DNS。 

由于 SQL 托管实例对内部工作使用同一 DNS，因此需要对自定义 DNS 服务器进行配置，使之能够解析公共域名。

> [!IMPORTANT]
> 始终对邮件服务器、SQL Server 实例和其他服务使用完全限定的域名 (FQDN)，即使它们位于专用 DNS 区域内也是如此。 例如，请对邮件服务器使用 `smtp.contoso.com`，因为 `smtp` 无法正确解析。 创建引用同一虚拟网络中的 SQL Server VM 的链接服务器或复制也需要 FQDN 和默认 DNS 后缀。 例如，`SQLVM.internal.cloudapp.net`。 有关详细信息，请参阅[使用自己的 DNS 服务器的名称解析](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

> [!IMPORTANT]
> 更新虚拟网络 DNS 服务器不会立即影响 SQL 托管实例。 有关更多详细信息，请参阅[如何在 SQL 托管实例虚拟群集上同步虚拟网络 DNS 服务器设置](synchronize-vnet-dns-servers-setting-on-virtual-cluster.md)。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是 Azure SQL 托管实例](sql-managed-instance-paas-overview.md)。
- 有关演示如何新建托管实例的教程，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关为托管实例配置 VNet 的信息，请参阅[托管实例的 VNet 配置](connectivity-architecture-overview.md)。
