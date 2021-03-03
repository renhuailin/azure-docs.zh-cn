---
title: 创建启用了数据渗透保护的工作区
description: 本文介绍如何在 Azure Synapse Analytics 中创建具有数据渗透保护的工作区
author: NanditaV
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 6fd28c9392d760888eafde37471a49ffaa2e4423
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694132"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>创建启用了数据渗透保护的工作区
本文介绍如何创建启用了 data 渗透 protection 的工作区，以及如何为此工作区管理已批准的 Azure AD 租户。

>[!Note]
>创建工作区后，不能更改托管虚拟网络和数据渗透保护的工作区配置。

## <a name="prerequisites"></a>先决条件
- 在 Azure 中创建工作区资源的权限。
- Synapse 工作区权限，用于创建托管专用终结点。
- 为网络资源提供程序注册的订阅。 [了解详细信息。](../../azure-resource-manager/management/resource-providers-and-types.md)

按照 [快速入门：创建 Synapse 工作区](../quickstart-create-workspace.md) 中列出的步骤开始创建工作区。 在创建工作区之前，请使用下面的信息将数据渗透保护添加到工作区。

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>创建工作区时添加数据渗透保护
1. 在 "网络" 选项卡上，选中 "启用托管虚拟网络" 复选框。
1. 对于 "仅允许出站数据流量到已批准目标" 选项，选择 "是"。
1. 为此工作区选择已批准的 Azure AD 租户。
1. 查看配置并创建工作区。
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="显示 &quot;创建 Synapse&quot; 工作区的屏幕截图，其中选择了 &quot;启用管理虚拟网络&quot;。":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>管理工作区的已批准 Azure Active Directory 租户
1. 从工作区的 Azure 门户中，导航到 "批准的 Azure AD 租户"。 此工作区的已批准 Azure AD 租户列表将在此处列出。 默认情况下，工作区的租户包含并且未列出。
1. 使用 "+ 添加" 将新租户包含到已批准列表。
1. 若要从已批准列表中删除 Azure AD 租户，请选择租户，并选择 "删除"，然后选择 "保存"。
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="使用 data 渗透 protection 创建工作区":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>连接到已批准 Azure AD 租户中的 Azure 资源

你可以创建托管的专用终结点以连接到驻留在 Azure AD 租户中的 Azure 资源，这些资源已获批准工作区。 按照指南中列出的步骤 [创建托管专用终结点](./how-to-create-managed-private-endpoints.md)。

>[!IMPORTANT]
>除了工作区租户之外的租户中的资源不能有阻止防火墙规则来连接到它们。 工作区托管的虚拟网络中的资源（如 Spark 群集）可通过托管的专用链接连接到受防火墙保护的资源。

## <a name="known-limitations"></a>已知的限制
用户可以提供环境配置文件以从公共存储库（如 PyPI）安装 Python 包。 在数据渗透保护的工作区中，会阻止与出站存储库的连接。 因此，不支持从公共存储库（如 PyPI）安装 Python 库。 作为替代方法，用户可以在其主 Azure Data Lake Storage 帐户中创建专用通道，并在其 Conda 环境配置文件中引用。 
  
## <a name="next-steps"></a>后续步骤

[在 Synapse 工作区中了解有关 data 渗透 protection 的](./workspace-data-exfiltration-protection.md)详细信息

了解有关[托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)的详细信息

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)

[创建用于访问数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)
