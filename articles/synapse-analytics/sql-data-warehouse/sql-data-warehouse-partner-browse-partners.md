---
title: 通过 Synapse Studio 发现 Azure Synapse 合作伙伴的第三方解决方案
description: 了解如何发现与 Azure Synapse 合作伙伴紧密集成的全新第三方解决方案
services: synapse-analytics
manager: santoshb
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/14/2021
author: periclesrocha
ms.author: procha
ms.reviewer: omafnan, wiassaf
ms.custom: seo-lt-2019
ms.openlocfilehash: e23fd7d6c94eeff926009281aa3a7c74c17fa71c
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294163"
---
# <a name="discover-partner-solutions-through-synapse-studio"></a>通过 Synapse Studio 发现合作伙伴解决方案

Synapse Studio 允许发现扩展 Azure Synapse 功能的解决方案合作伙伴。 从数据连接器，到数据整理工具和业务流程引擎，再到其他工作负载，“浏览合作伙伴”页是各种第三方 ISV 应用程序和解决方案的汇集中心，这些应用程序和解决方案经验证可与 Azure Synapse Analytics 配合使用。 Synapse Studio 简化了开始使用这些合作伙伴解决方案的过程，许多情况下，与合作伙伴平台的初始连接均为自动设置。

## <a name="participating-partners"></a>参与合作伙伴
下表列出了当前支持的合作伙伴解决方案。 表中会定期新增合作伙伴，因此请时常查看。 

| Partner | 解决方案名称 |
| ------- | ------------- |
| ![Incorta](./media/sql-data-warehouse-partner-data-integration/incorta-logo.png) | 适用于 Azure Synapse 的 Incorta Intelligent Ingest |
| ![Informatica](./media/sql-data-warehouse-partner-data-integration/informatica_logo.png) | Informatica 智能数据管理云 |
| ![Qlik Data Integration（以前称为 Attunity）](./media/sql-data-warehouse-partner-business-intelligence/qlik_logo.png) | Qlik Data Integration（以前称为 Attunity） |

## <a name="requirements"></a>要求
在选择合作伙伴应用程序后，Azure Synapse Studio 将提供可用于本次试用的沙箱环境，确保你在决定将其用于生产数据之前，快速体验一下合作伙伴解决方案。 以下是创建的对象： 

|  对象  |    详细信息    |
| -------- | ------------- |
| 名为 Partner_[PartnerName]_pool 的[专用 SQL 池](./sql-data-warehouse-overview-what-is.md) | DW100c 性能级别。 |
| 名为 Partner_[PartnerName]_login 的 [SQL 登录名](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) | 在 `master` 数据库上创建。 此 SQL 登录名的密码由你在创建试用版时指定。|
| [数据库用户](../../azure-sql/database/logins-create-manage.md?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) | 映射到新 SQL 登录名的新数据库用户。 此用户将添加到新创建数据库的 db_owner 角色。 |

任何情况下，[PartnerName] 为试用版提供方，即第三方 ISV 的名称。 

### <a name="security"></a>安全 
创建所需对象之后，Synapse Studio 会将新沙盒环境相关的信息发送到合作伙伴应用程序，以此提供自定义的试用体验。 以下信息会发送给我们的合作伙伴： 
- 名字
- 姓氏
- 电子邮件地址
-  有关建立连接所需 Synapse 环境的详细信息：     
    - Synapse 工作区的 DNS 名称（服务器名称）
    - SQL 池名称（数据库）
    - SQL 登录名（仅用户名）
    
我们从不会与合作伙伴应用程序共享任何密码，包括新创建 SQL 登录名的密码。 你需要在合作伙伴应用程序中键入密码。

### <a name="costs"></a>成本
针对合作伙伴试用版创建的专用 SQL 池会持续产生成本，这些成本基于运行的 DWU 块数和小时数。 因此，请确保在未使用此合作伙伴试用版时暂停为其创建的 SQL 池，以避免产生不必要的费用。 

## <a name="starting-a-new-partner-trial"></a>开启新的合作伙伴试用版 

1) 在 Synapse Studio 主页的“发现更多”中选择“浏览合作伙伴”。 
2) “浏览合作伙伴”页显示了目前提供试用版（允许直连 Azure Synapse）的所有合作伙伴。 选择一个合作伙伴解决方案。
3) “合作伙伴详细信息”页显示了此应用程序的相关信息及可供详细了解合作伙伴解决方案的链接。 准备好开启试用版后，选择“连接到合作伙伴”。
4) 请注意“连接到 [PartnerName] 解决方案”页中有关连接到合作伙伴的要求。 可根据需要更改 SQL 池名称和 SQL 登录名参数（或接受默认值），然后键入新 SQL 登录名的密码，并选择“连接”。

然后，系统将创建合作伙伴试用版的所需对象。 接下来页面将跳转至合作伙伴页，你可在上面提供其他信息（如必要）并开启试用版。 

> [!NOTE]
> Microsoft 不会对合作伙伴试用版体验进行控制。 合作伙伴根据自己的条款提供产品试用版，试用体验、试用机会和功能可能会因合作伙伴而异。 Microsoft 不对 Synapse Studio 中的第三方应用程序提供支持。 

## <a name="next-steps"></a>后续步骤

若要详细了解我们的其他一些合作伙伴，请参阅[数据集成合作伙伴](sql-data-warehouse-partner-data-integration.md)、[数据管理合作伙伴](sql-data-warehouse-partner-data-management.md)和[机器学习和 AI 合作伙伴](sql-data-warehouse-partner-machine-learning-ai.md)。