---
title: 将数据从本地 MySQL 迁移到 Azure Database for MySQL 简介
description: 从本地 MySQL 到 Azure Data base for MySQL 的迁移指南
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 5023d53132985875fc0b927148a6efbfb0239cc3
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085121"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql"></a>将本地 MySQL 迁移到 Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

本迁移指南旨在为试图将 MySQL 工作负荷迁移到 [Azure Database for MySQL](../../overview.md) 的 MySQL 客户和软件集成商提供可堆叠且可操作的信息。 本指南提供应用于大多数情况的适用知识，并提供成功规划和执行 MySQL 到 Azure 的迁移的指导。

将现有数据库和 MySQL 工作负荷迁移到云的过程可能会对工作负荷功能和现有应用程序的连接性带来挑战。 本指南中的信息提供了有关成功迁移以及确保工作负荷和应用程序继续按最初预期运行的有用链接和建议。

提供的信息以客户旅程为中心，使用 Microsoft [云采用框架](/azure/cloud-adoption-framework/get-started/)对 Azure Database for MySQL 环境执行评估、迁移和优化后活动。

## <a name="mysql"></a>MySQL

MySQL 长久以来一直活跃在开放源代码社区中，并且全球企业普遍将它用于网站和其他业务关键应用程序业务。 本指南将为被要求对迁移进行范围限定、计划和执行的管理员提供支持。 刚接触 MySQL 的管理员还可以查看 [MySQL 文档](https://dev.mysql.com/doc/)以获取有关 MySQL 内部工作原理的更多详细信息。 此外，本指南的各个部分提供了一些参考文章的链接，可提供有用的信息和教程。

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

[Azure Database for MySQL](../../overview.md) 是 Microsoft 提供的一种平台即服务 (PaaS) 产品/服务，其中 MySQL 环境是完全托管的。 在此完全托管的环境中，将自动应用操作系统和软件更新，可实现高可用性和数据保护。

除了 PaaS 产品/服务，还可以在 Azure VM 中运行 MySQL。 参考[在 Azure 中选择合适的 MySQL 服务器选项](../../select-right-deployment-type.md)一文，确定最适合目标数据工作负荷的部署类型。

![MySQL 环境比较](./media/image3.jpg)

**MySQL 环境比较**

本指南重点介绍了将本地 MySQL 工作负荷迁移到平台即服务 Azure Database for MySQL 这一产品/服务，因为与基础结构即服务 (IaaS) 相比，平台即服务具有多种优势，例如纵向扩展和横向扩展、即用即付、高可用性、安全性和可管理性功能。  

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [代表性用例](./02-representative-use-case.md)
