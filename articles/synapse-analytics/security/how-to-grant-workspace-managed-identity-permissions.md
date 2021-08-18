---
title: 在 Synapse 工作区中向托管标识授予权限
description: 本文介绍如何在 Azure Synapse 工作区中配置托管标识的权限。
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.custom: subject-rbac-steps
ms.openlocfilehash: 10244038ca2dee9551def5e0abcd102d42daa235
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729723"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>向工作区托管标识授予权限

本文介绍如何在 Azure Synapse 工作区中向托管标识授予权限。 权限反过来允许通过 Azure 门户访问工作区中的专用 SQL 池和 ADLS Gen2 存储帐户。

>[!NOTE]
>在此文档的剩余部分中，我们将此工作区托管标识称为托管标识。

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>向托管标识授予专用 SQL 池的权限

托管标识授予工作区中专用 SQL 池的权限。 授予权限后，可以编排管道以执行与专用 SQL 池相关的活动。 使用 Azure 门户创建 Azure Synapse 工作区时，可向托管标识授予对专用 SQL 池的 CONTROL 权限。

创建 Azure Synapse 工作区时，选择“安全”。 然后选择“允许作为工作区的系统分配的标识运行的管道访问 SQL 池”。

![对专用 SQL 池的 CONTROL 权限](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>向托管标识授予 ADLS Gen2 存储帐户的权限

创建 Azure Synapse 工作区需要 ADLS Gen2 存储帐户。 若要在 Azure Synapse 工作区中成功启动 Spark 池，Azure Synapse 托管标识需要在此存储帐户上具有存储 Blob 数据参与者角色。 Azure Synapse 中的管道业务流程也从此角色中获益。

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>创建工作区时向托管标识授予权限

使用 Azure 门户创建 Azure Synapse 工作区后，Azure Synapse 将尝试向托管标识授予存储 Blob 数据参与者角色。 你在“基本信息”选项卡中提供 ADLS Gen2 存储帐户详细信息。

![工作区创建流中的“基本信息”选项卡](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

 在“帐户名称”和“文件系统名称”中选择 ADLS Gen2 存储帐户和文件系统。

![提供 ADLS Gen2 存储帐户详细信息](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

如果工作区创建者也是 ADLS Gen2 存储帐户的所有者，则 Azure Synapse 向托管标识分配存储 Blob 数据参与者角色。 将在输入的存储帐户详细信息下看到以下消息。

![成功分配存储 Blob 数据参与者](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

如果工作区创建者不是 ADLS Gen2 存储帐户的所有者，则 Azure Synapse 不向托管标识分配存储 Blob 数据参与者角色。 显示在存储帐户详细信息下面的消息通知工作区创建者他们没有足够的权限向托管标识授予存储 Blob 数据参与者角色。

![未成功分配存储 Blob 数据参与者](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

就像消息所说的，除非将存储 Blob 数据参与者分配给托管标识，否则无法创建 Spark 池。

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>创建工作区后向托管标识授予权限

创建工作区期间，如果未将存储 Blob 数据参与者分配给托管标识，则 ADLS Gen2 存储帐户的所有者手动将该角色分配给托管标识。 以下步骤帮助完成手动分配。

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>步骤 1：导航到 Azure 门户中的 ADLS Gen2 存储帐户

在 Azure 门户中，打开 ADLS Gen2 存储帐户，并从左侧导航栏中选择“概览”。 只需在容器或文件系统级别分配存储 Blob 数据参与者角色。 选择“容器”。  
![ADLS Gen2 存储帐户概述](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>步骤 2：选择容器

托管标识应对创建工作区时提供的容器（文件系统）具有数据访问权限。 可以在 Azure 门户中找到此容器或文件系统。 在 Azure 门户中打开 Azure Synapse 工作区，并从左侧导航栏中选择“概览”。
![ADLS Gen2 存储帐户容器](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


选择相同的容器或文件系统，向托管标识授予存储 Blob 数据参与者角色。
![屏幕截图显示应选择的容器或文件系统。](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-open-access-control-and-add-role-assignment"></a>步骤 3：打开“访问控制”并添加角色分配

1. 选择“访问控制 (IAM)”。

1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。

1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | 存储 Blob 参与者 |
    | 将访问权限分配到 | MANAGEDIDENTITY |
    | 成员 | 托管标识名称  |

    > [!NOTE]
    > 托管标识名称也是工作区名称。

    ![Azure 门户中的“添加角色分配”页。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

1. 选择“保存”以添加角色分配。

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>步骤 9：验证是否已将存储 Blob 数据参与者角色分配给托管标识

 依次选择“访问控制(IAM)”、“角色分配”。

![验证角色分配](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

应看到“存储 Blob 数据参与者”部分下列出了托管标识，并向其分配了“存储 blob 数据参与者”角色。 
![ADLS Gen2 存储帐户容器选择](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>后续步骤

详细了解[工作区托管标识](./synapse-workspace-managed-identity.md)
