---
title: 创建启用了数据外泄保护功能的工作区
description: 本文介绍如何在 Azure Synapse Analytics 中创建具有数据外泄保护功能的工作区
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 59c2f5e5738b29aa11e9227b157f8b11d53f2b25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598076"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>创建启用了数据外泄保护功能的工作区
本文介绍如何创建启用了数据外泄保护功能的工作区，以及如何管理该工作区的经过批准的 Azure AD 租户。

>[!Note]
>创建工作区后，无法更改托管虚拟网络和数据外泄保护功能的工作区配置。

## <a name="prerequisites"></a>先决条件
- 用于在 Azure 中创建工作区资源的权限。
- 用于创建托管的专用终结点的 Synapse 工作区权限。
- 注册了网络资源提供程序的订阅。 [了解详细信息。](../../azure-resource-manager/management/resource-providers-and-types.md)

请按照[快速入门：创建 Synapse 工作区](../quickstart-create-workspace.md)中列出的步骤开始创建工作区。 创建工作区之前，请根据以下信息向工作区中添加数据外泄保护。

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>创建工作区时添加数据外泄保护
1. 在“网络”选项卡上，选中“启用托管的虚拟网络”复选框。
1. 对于“仅允许出站数据流量流向批准目标”选项，选择“是”。
1. 为该工作区选择批准的 Azure AD 租户。
1. 查看配置并创建工作区。
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="屏幕截图显示了已选中“启用托管的虚拟网络”的 Synapse 工作区。":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>管理工作区的批准的 Azure Active Directory 租户
1. 在工作区的 Azure 门户中，导航到“批准的 Azure AD 租户”。 此处将列出工作区的批准的 Azure AD 租户列表。 默认包含工作区的租户，但不会列出。
1. 使用“+添加”将新的租户加入批准列表。
1. 要从批准列表中删除 Azure AD 租户，请选中租户，然后依次选择“删除”和“保存”。
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="创建具有数据外泄保护功能的工作区":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>连接到批准的 Azure AD 租户中的 Azure 资源

可以创建托管的专用终结点，以连接到工作区批准的 Azure AD 租户中驻留的 Azure 资源。 请按照[创建托管的专用终结点](./how-to-create-managed-private-endpoints.md)指南中列出的步骤进行操作。

>[!IMPORTANT]
>非工作区租户中的资源不能设阻止防火墙规则，这样 SQL 池才能连接这些资源。 工作区的托管虚拟网络中的资源（例如 Spark 群集）可以通过托管的专用链接连接到受防火墙保护的资源。

## <a name="known-limitations"></a>已知的限制
用户可以提供环境配置文件，从 PyPI 等公共存储库安装 Python 包。 在具有数据外泄保护功能的工作区中，将阻止与出站存储库的连接。 因此，不支持从 PyPI 等公共存储库安装的 Python 库。 

作为替代方法，用户可以上传工作区包，或者在其主要 Azure Data Lake Storage 帐户中创建一个专用通道。 有关详细信息，请访问 [Azure Synapse Analytics 中的包管理](./spark/../../spark/apache-spark-azure-portal-add-libraries.md) 
  
## <a name="next-steps"></a>后续步骤

详细了解 [Synapse 工作区中的数据外泄保护功能](./workspace-data-exfiltration-protection.md)

详细了解[托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)

[创建用于访问数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)
