---
title: 快速入门：创建 Synapse 工作区
description: 遵循本指南中的步骤创建 Synapse 工作区。
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.custom: subject-rbac-steps
ms.openlocfilehash: 0f593d801bdcc477d6084a395630211393ffa9c7
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217280"
---
# <a name="quickstart-create-a-synapse-workspace"></a>快速入门：创建 Synapse 工作区
本快速入门介绍了使用 Azure 门户创建 Azure Synapse 工作区的步骤。

## <a name="create-a-synapse-workspace"></a>创建 Synapse 工作区

1. 打开 [Azure 门户](https://portal.azure.com)，然后在顶部搜索“Synapse”。
1. 在“服务”下的搜索结果中，选择“Azure Synapse Analytics” 。
1. 选择“添加”以创建工作区。
1. 在“基础”选项卡中，为工作区指定唯一的名称。 在本文档中，我们将使用 myworkspace
1. 需要 ADLSGEN2 帐户才能创建工作区。 最简单的选择是创建一个新工作区。 如果要重复使用现有工作区，需要执行一些其他配置。 
1. 选项 1 创建新的 ADLSGEN2 帐户 
    1. 在“选择 Data Lake Storage Gen 2”下，单击“新建”并将其命名为 contosolake  。
    1. 在“选择 Data Lake Storage Gen 2”下，单击“文件系统”并将其命名为 users  。
1. 选项 2 参阅本文档底部的“准备存储帐户”说明。
1. Azure Synapse 工作区将此存储帐户用作“主要”存储帐户，并使用容器来存储工作区数据。 工作区将数据存储在 Apache Spark 表中。 它将 Spark 应用程序日志存储在名为 /synapse/workspacename 的文件夹下。
1. 选择“查看 + 创建” > “创建”。 你的工作区将在几分钟内准备就绪。

> [!NOTE]
> 创建 Azure Synapse 工作区后，将无法将工作区移动到另一个 Azure Active Directory 租户。 如果通过订阅迁移或其他操作执行此过程，可能会失去对工作区中项目的访问权限。
> 另外，当前无法在[云解决方案提供商 (CSP)](/partner-center/csp-overview) 订阅中创建 Synapse Analytics 工作区。

## <a name="open-synapse-studio"></a>打开 Synapse Studio

创建 Azure Synapse 工作区后，可以通过两种方式打开 Synapse Studio：

* 在 [Azure 门户](https://portal.azure.com)中打开 Synapse 工作区。 在“概述”部分的顶部，选择“启动 Synapse Studio” 。
* 转到 `https://web.azuresynapse.net`，然后登录到工作区。

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>准备与 Azure Synapse Analytics 配合使用的现有存储帐户

1. 打开 [Azure 门户](https://portal.azure.com)。
1. 导航到现有的 ADLSGEN2 存储帐户
1. 选择“访问控制 (IAM)”。
1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。
1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | 所有者和存储 Blob 数据所有者 |
    | 将访问权限分配到 | [USER |
    | 成员 | 你的用户名 |

    ![Azure 门户中的“添加角色分配”页。](../../includes/role-based-access-control/media/add-role-assignment-page.png)
1. 在左窗格中选择“容器”并创建容器。
1. 可以为此容器指定任何名称。 在本文档中，我们将此容器命名为“users”。
1. 接受默认设置“公共访问级别”，然后选择“创建” 。

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>配置从工作区访问存储帐户的权限

Azure Synapse 工作区的托管标识可能已具有对存储帐户的访问权限。 请执行以下步骤，以确保：

1. 打开 [Azure 门户](https://portal.azure.com)和为工作区所选的主存储帐户。
1. 选择“访问控制 (IAM)”。
1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。
1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | 存储 Blob 数据参与者 |
    | 将访问权限分配到 | MANAGEDIDENTITY |
    | 成员 | myworkspace  |

    > [!NOTE]
    > 托管标识名称也是工作区名称。

    ![Azure 门户中的“添加角色分配”页。](../../includes/role-based-access-control/media/add-role-assignment-page.png)
1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

* [创建专用 SQL 池](quickstart-create-sql-pool-studio.md) 
* [创建无服务器 Apache Spark 池](quickstart-create-apache-spark-pool-portal.md)
* [使用无服务器 SQL 池](quickstart-sql-on-demand.md)