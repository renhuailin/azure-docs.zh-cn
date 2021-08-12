---
title: Azure Data Share 的角色和要求
description: 了解使用 Azure Data Share 共享和接收数据所需的权限。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a832c8956f7a3d4f8669209d7ed311e7555e1e75
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644254"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share 的角色和要求 

本文介绍了使用 Azure Data Share 服务共享和接收数据所需的角色和权限。 

## <a name="roles-and-requirements"></a>角色和要求

使用 Azure Data Share 服务，无需在数据提供程序和使用者之间交换凭据即可共享数据。 对于基于快照的共享，Azure Data Share 服务使用托管标识（以前称为 MSI）对 Azure 数据存储进行身份验证。 需要为 Azure Data Share 资源的托管标识授予对 Azure 数据存储的访问权限，以便读取或写入数据。

若要从 Azure 数据存储共享或接收数据，用户需要至少具有以下权限。 

* 写入 Azure 数据存储的权限。 通常，“参与者”角色有此权限。

对于存储和基于数据湖快照的共享，你还需获取在 Azure 数据存储中创建角色分配的权限。 通常，“所有者” 角色、“用户访问管理员”角色或被分配了“Microsoft.Authorization/role assignments/write”权限的自定义角色都有创建角色分配的权限。 如果已为 Data Share 资源的托管标识分配了对 Azure 数据存储的访问权限，则不需要此权限。 下面汇总了分配给 Data Share 资源的托管标识的角色：

|数据存储类型|数据提供程序源数据存储|数据使用者目标数据存储|
|---|---|---|
|Azure Blob 存储| 存储 Blob 数据读取者 | 存储 Blob 数据参与者
|Azure Data Lake Gen1 | “所有者” | 不支持
|Azure Data Lake Gen2 | 存储 Blob 数据读取者 | 存储 Blob 数据参与者
|

对于基于 SQL 快照的共享，需要从 Azure SQL 数据库中的外部提供程序创建一个与 Azure Data Share 资源同名的 SQL 用户。 需要 Azure Active Directory 管理员权限才能创建此用户。 下面汇总了 SQL 用户所需的权限。

|SQL 数据库类型|数据提供程序 SQL 用户权限|数据使用者 SQL 用户权限|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader、db_datawriter、db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader、db_datawriter、db_ddladmin
|

### <a name="data-provider"></a>数据提供程序
对于存储和基于数据湖快照的共享，若要在 Azure Data Share 中添加数据集，需要为提供程序 Data Share 资源的托管身份授予对源 Azure Data Share 的访问权限。 例如，如为存储帐户，则需要为 Data Share 资源的托管标识授予“存储 Blob 数据读取者”角色。 当用户通过 Azure 门户添加数据集，并且用户具有适当的权限时，Azure Data Share 服务会自动完成此操作。 例如，用户是 Azure 数据存储的所有者，或者是被分配了“Microsoft.Authorization/role assignments/write”权限的自定义角色的成员。 

或者，用户可以让 Azure 数据存储的所有者将 Data Share 资源的托管标识手动添加到 Azure 数据存储。 每个 Data Share 资源只需要执行一次此操作。 若要为 Data Share 资源的托管标识手动创建角色分配，请执行以下步骤。  

1. 导航到 Azure 数据存储。
1. 选择“访问控制 (IAM)”。
1. 选择“添加角色分配”。
1. 在“角色”下，选择上述角色分配表中的角色（例如，对于存储帐户，请选择“存储 Blob 数据读取者”）。 
1. 在“选择”下，键入 Azure Data Share 资源的名称。
1. 单击“保存”  。

若要了解有关角色分配的详细信息，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。 如果要使用 REST API 共享数据，可以通过参阅[使用 REST API 分配 Azure 角色](../role-based-access-control/role-assignments-rest.md)来使用 API 创建角色分配。 

对于基于 SQL 快照的共享，在使用 Azure Active Directory 身份验证连接到 SQL 数据库时，需要从 SQL 数据库中的外部提供程序创建一个与 Azure Data Share 资源同名的 SQL 用户。 需要为此用户授予 db_datareader 权限。 可以在[从 Azure SQL 数据库或 Azure Synapse Analytics 共享](how-to-share-from-sql.md)教程中找到示例脚本以及基于 SQL 共享的其他先决条件。 

### <a name="data-consumer"></a>数据使用者
若要将数据接收到存储帐户，需要向使用者数据共享资源的托管标识授予对目标存储帐户的访问权限。 需要向数据共享资源的托管标识授予“存储 Blob 数据参与者”角色。 如果用户通过 Azure 门户指定目标数据存储，并且用户具有适当的权限，则 Azure Data Share 服务会自动完成此操作。 例如，用户是 Azure 数据存储的所有者，或者是被分配了“Microsoft.Authorization/role assignments/write”权限的自定义角色的成员。 

或者，用户可以要求存储帐户的所有者将数据共享资源的托管标识手动添加到存储帐户。 每个 Data Share 资源只需要执行一次此操作。 若要为 Data Share 资源的托管标识手动创建角色分配，请执行以下步骤。 

1. 导航到 Azure 数据存储。
1. 选择“访问控制 (IAM)”。
1. 选择“添加角色分配”。
1. 在“角色”下，选择上述角色分配表中的角色（例如，对于存储帐户，请选择“存储 Blob 数据读取者”）。 
1. 在“选择”下，键入 Azure Data Share 资源的名称。
1. 单击“保存”  。

若要了解有关角色分配的详细信息，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。 如果要使用 REST API 接收数据，可以通过参阅[使用 REST API 分配 Azure 角色](../role-based-access-control/role-assignments-rest.md)来使用 API 创建角色分配。 

对于基于 SQL 的目标，在使用 Azure Active Directory 身份验证连接到 SQL 数据库时，需要从 SQL 数据库中的外部提供程序创建一个与 Azure Data Share 资源同名的 SQL 用户。 需要为此用户授予 db_datareader、db_datawriter、db_ddladmin 权限。 可以在[从 Azure SQL 数据库或 Azure Synapse Analytics 共享](how-to-share-from-sql.md)教程中找到示例脚本以及基于 SQL 共享的其他先决条件。 

## <a name="resource-provider-registration"></a>资源提供程序注册 

在以下场景中，可能需要将 Microsoft.DataShare 资源提供程序手动注册到你的 Azure 订阅： 

* 第一次在 Azure 租户中查看 Azure Data Share 邀请
* 在与 Azure Data Share 资源不同的 Azure 订阅中从 Azure 数据存储共享数据
* 在与 Azure Data Share 资源不同的 Azure 订阅中将数据接收到 Azure 数据存储

按照以下步骤将 Microsoft.DataShare 资源提供程序注册到你的 Azure 订阅。 需要对 Azure 订阅的“参与者”访问权限才能注册资源提供程序。

1. 在 Azure 门户中，导航到“订阅”。
1. 选择用于 Azure Data Share 的订阅。
1. 单击“资源提供程序”。
1. 搜索“Microsoft.DataShare”。
1. 单击“注册”。
 
若要详细了解资源提供程序，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的角色 - [了解 Azure 角色定义](../role-based-access-control/role-definitions.md)