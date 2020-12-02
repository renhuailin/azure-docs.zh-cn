---
title: Azure Data Share 的角色和要求
description: 了解使用 Azure 数据共享来共享和接收数据所需的权限。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ed4b4d9c1de1e9024e8ea86d4661b42d6c68b0ae
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460996"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Azure Data Share 的角色和要求 

本文介绍使用 Azure 数据共享服务共享和接收数据所需的角色和权限。 

## <a name="roles-and-requirements"></a>角色和要求

使用 Azure 数据共享服务，无需在数据访问接口和使用者之间交换凭据即可共享数据。 Azure 数据共享服务使用) 之前称为 Msi 的托管 (标识对 Azure 数据存储进行身份验证。 

需要为 azure 数据共享资源的托管标识授予对 Azure 数据存储的访问权限。 然后，Azure 数据共享服务使用此托管标识来读取和写入数据，以进行基于快照的共享，并建立用于就地共享的符号链接。 

若要在 Azure 数据存储中共享或接收数据，用户需要至少具有以下权限。 对于基于 SQL 的共享，需要其他权限。

* 写入 Azure 数据存储的权限。 通常，此权限存在于 **参与者** 角色中。
* 在 Azure 数据存储中创建角色分配的权限。 通常，创建角色分配的权限存在于 **所有者** 角色、用户访问管理员角色或分配了 Microsoft 的自定义角色中。 如果数据共享资源的托管标识已被授予对 Azure 数据存储的访问权限，则不需要此权限。 请参阅下表了解必需的角色。

下面是分配给数据共享资源的托管标识的角色的摘要：

|**数据存储类型**|**数据访问接口源数据存储**|**数据使用者目标数据存储**|
|---|---|---|
|Azure Blob 存储| 存储 Blob 数据读取者 | 存储 Blob 数据参与者
|Azure Data Lake Gen1 | 所有者 | 不支持
|Azure Data Lake Gen2 | 存储 Blob 数据读取者 | 存储 Blob 数据参与者
|Azure 数据资源管理器群集 | 参与者 | 参与者
|

对于基于 SQL 的共享，需要在 Azure SQL 数据库中使用与 Azure 数据共享资源相同的外部提供程序创建 SQL 用户。 需要 Azure Active Directory 管理员权限才能创建此用户。 下面汇总了 SQL 用户所需的权限。

|**SQL 数据库类型**|**数据访问接口 SQL 用户权限**|**数据使用者 SQL 用户权限**|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader、db_datawriter db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader、db_datawriter db_ddladmin
|

### <a name="data-provider"></a>数据提供程序

若要在 Azure 数据共享中添加数据集，需要授予访问接口数据共享资源的托管标识访问源 Azure 数据存储的权限。 例如，在存储帐户的情况下，会向数据共享资源的托管标识授予存储 Blob 数据读取者角色。 

当用户通过 Azure 门户添加数据集，并且用户具有适当的权限时，Azure 数据共享服务会自动完成此操作。 例如，用户是 Azure 数据存储的所有者，或者是分配了 Microsoft. Authorization/role 分配/写入权限的自定义角色的成员。 

或者，用户可以将数据共享资源的托管标识手动添加到 Azure 数据存储中。 每个数据共享资源只需要执行一次此操作。

若要手动创建数据共享资源的托管标识的角色分配，请执行以下步骤。  

1. 导航到 Azure 数据存储。
1. 选择“访问控制 (IAM)”。
1. 选择 " **添加角色分配**"。
1. 在 " *角色*" 下，选择上述角色分配表中的角色 (例如，对于 "存储帐户"，选择 " *存储 Blob 数据读取器* ") 。
1. 在 " *选择*" 下，键入 Azure 数据共享资源的名称。
1. 单击“保存”  。

若要了解有关角色分配的详细信息，请参阅 [使用 Azure 门户添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)。 如果你使用 REST Api 共享数据，则可以通过使用 [REST API 引用添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-rest.md)来使用 API 创建角色分配。 

对于基于 SQL 的源，需要在使用 Azure Active Directory 身份验证连接到 SQL 数据库时，通过与 Azure 数据共享资源同名的 SQL 数据库中的外部提供程序创建 SQL 用户。 需要授予此用户 *db_datareader* 权限。 可以在 [AZURE SQL 数据库或 Azure Synapse Analytics](how-to-share-from-sql.md) 的 "共享" 教程中找到与基于 SQL 的共享一起使用的示例脚本和其他先决条件。 

### <a name="data-consumer"></a>数据使用者
若要接收数据，需要向使用者数据共享资源的托管标识授予对目标 Azure 数据存储的访问权限。 例如，在存储帐户的情况下，会向数据共享资源的托管标识授予存储 Blob 数据参与者角色。 

如果用户通过 Azure 门户指定目标数据存储，并且用户具有适当的权限，则 Azure 数据共享服务会自动完成此操作。 例如，用户是 Azure 数据存储的所有者，或者是分配了 Microsoft. Authorization/role 分配/写入权限的自定义角色的成员。 

或者，用户可以将数据共享资源的托管标识手动添加到 Azure 数据存储中。 每个数据共享资源只需要执行一次此操作。

若要手动创建数据共享资源的托管标识的角色分配，请执行以下步骤。 

1. 导航到 Azure 数据存储。
1. 选择“访问控制 (IAM)”。
1. 选择 " **添加角色分配**"。
1. 在 " *角色*" 下，选择上述角色分配表中的角色 (例如，对于 "存储帐户"，选择 " *存储 Blob 数据读取器* ") 。
1. 在 " *选择*" 下，键入 Azure 数据共享资源的名称。
1. 单击“保存”  。

若要了解有关角色分配的详细信息，请参阅 [使用 Azure 门户添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)。 如果使用 REST Api 接收数据，则可以通过使用 [REST API 引用添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-rest.md)来使用 API 创建角色分配。 

对于基于 SQL 的目标，需要在使用 Azure Active Directory authentication 连接到 SQL 数据库时，通过与 Azure 数据共享资源相同的名称，从 SQL 数据库中的外部提供程序创建 SQL 用户。 此用户需要被授予 *db_datareader、db_datawriter db_ddladmin* 权限。 可以在 [AZURE SQL 数据库或 Azure Synapse Analytics](how-to-share-from-sql.md) 的 "共享" 教程中找到与基于 SQL 的共享一起使用的示例脚本和其他先决条件。 

## <a name="resource-provider-registration"></a>资源提供程序注册 

在以下情况下，你可能需要在 Azure 订阅中手动注册 DataShare 资源提供程序： 

* 第一次在 Azure 租户中查看 Azure 数据共享邀请
* 使用 Azure 数据共享资源从其他 Azure 订阅中的 Azure 数据存储共享数据
* 将数据从 Azure 数据共享资源传入不同 Azure 订阅中的 Azure 数据存储

按照以下步骤将 DataShare 资源提供程序注册到你的 Azure 订阅。 需要对 Azure 订阅的 *参与者* 访问权限才能注册资源提供程序。

1. 在 Azure 门户中，导航到 " **订阅**"。
1. 选择要用于 Azure 数据共享的订阅。
1. 单击 " **资源提供程序**"。
1. 搜索 DataShare。
1. 单击“注册”。
 
若要详细了解资源提供程序，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 中的角色 - [了解角色定义](../role-based-access-control/role-definitions.md)