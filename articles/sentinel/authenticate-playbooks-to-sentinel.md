---
title: 向 Azure Sentinel 验证 playbook | Microsoft Docs
description: 了解如何授予 playbook 对 Azure Sentinel 的访问权限，以及如何提供采取修正操作的授权。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: yelevin
ms.openlocfilehash: 6c3e4de61d59841f2856af2194ec22a63b407b5c
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123185428"
---
# <a name="authenticate-playbooks-to-azure-sentinel"></a>向 Azure Sentinel 验证 playbook

逻辑应用的工作方式是，必须单独连接，并单独对与之交互的每个类型的每个资源进行身份验证（包括对 Azure Sentinel 本身进行身份验证）。 逻辑应用使用[专用连接器](/connectors/connector-reference/)实现此目的，每个资源类型都有自己的连接器。 本文档介绍[逻辑应用 Azure sentinel 连接器](/connectors/azuresentinel/)中的连接和身份验证的类型，playbook 可以使用它们与 Azure Sentinel 进行交互，以便访问工作区表中的信息。

本文档以及[在 playbook 中使用触发器和操作](playbook-triggers-actions.md)的指南是另一个 playbook 文档[教程：在 Azure Sentinel 中结合自动化规则使用 playbook](tutorial-respond-threats-playbook.md) 的配套文档。

有关 playbook 的简介，请参阅[在 Azure Sentinel 中使用 playbook 实现威胁响应自动化](automate-responses-with-playbooks.md)。

有关 Azure Sentinel 连接器的完整规范，请参阅[逻辑应用连接器文档](/connectors/azuresentinel/)。

## <a name="authentication"></a>身份验证

逻辑应用中的 Azure Sentinel 连接器及其组件触发器和操作可以代表对相关工作区拥有必需权限（读取和/或写入）的任何标识进行操作。 连接器支持多种标识类型：

- [托管标识（预览版）](#authenticate-with-managed-identity)
- Azure AD 用户
- [服务主体（Azure AD 应用程序）](#authenticate-as-a-service-principal-azure-ad-application)

    ![身份验证选项](media/authenticate-playbooks-to-sentinel/auth-methods.png)

### <a name="permissions-required"></a>所需的权限

| 角色\连接器组件 | 触发器 | “获取”操作 | 更新事件，<br>添加注释 |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Azure Sentinel 读取者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| “Azure Sentinel [响应者](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)/[参与者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)” | &#10003; | &#10003; | &#10003; |
| 

[详细了解 Azure Sentinel 中的权限](./roles.md)。

### <a name="authenticate-with-managed-identity"></a>使用托管标识进行身份验证

使用此身份验证方法，可以直接向 playbook（逻辑应用工作流资源）授予权限，这样，playbook 所采取的 Azure Sentinel 连接器操作将代表 playbook 进行操作，就像它是一个独立的对象，对 Azure Sentinel 有自己的权限。 使用此方法可减少必须管理的标识数。 

> [!NOTE]
> 要授予托管标识对其他资源（如 Azure Sentinel 工作区）的访问权限，登录用户必须拥有有权编写角色分配的角色，例如 Azure Sentinel 工作区的所有者或用户访问管理员。

若要使用托管标识进行身份验证，请执行以下操作：


1. 在逻辑应用工作流资源上[启用托管标识](../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)。 总结：

    - 在逻辑应用菜单的“设置”下，选择“标识” 。 选择“系统分配”>“打开”>“保存”。 当 Azure 提示你进行确认时，选择“是”。

    - 逻辑应用现在可以使用系统分配的标识，该标识注册到 Azure AD，由对象 ID 表示。

1. [授予该标识](../logic-apps/create-managed-service-identity.md#give-identity-access-to-resources)对 Azure Sentinel 工作区的访问权限： 
    1. 在 Azure Sentinel 菜单中，选择“设置”。
    1. 选择“工作区设置”选项卡。从“工作区”菜单中，选择“访问控制(IAM)” 。
   1. 在顶部的按钮栏中，依次选择“添加”、“添加角色分配” 。 如果已禁用“添加角色分配”选项，那么你没有权限分配角色。
    1. 在显示的新面板中，分配适当的角色：
    
        | 角色 | 场景 |
        | --- | --- |
        | [**Azure Sentinel 响应方**](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) | Playbook 包含用于更新事件或播放列表的步骤 |
        | [**Azure Sentinel 读取者**](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) | Playbook 仅接收事件 |
        |
        
        详细了解 [Azure Sentinel 中的可用角色](./roles.md)。
    1. 在“将访问权限分配到”下，选择“逻辑应用” 。
    1. 选择 Playbook 所属的订阅，然后选择相应的 Playbook 名称。
    1. 选择“保存”  。
    
    
1. 在 Azure Sentinel 逻辑应用连接器中启用托管标识身份验证方法：

    1. 在逻辑应用设计器中，添加 Azure Sentinel 逻辑应用连接器步骤。 如果已为现有连接启用连接器，请单击“更改连接”链接。

        ![更改连接](media/authenticate-playbooks-to-sentinel/change-connection.png)

    1. 在连接的结果列表中，选择底部的“新增”。 

    1. 通过选择“通过托管标识连接（预览版）”来创建新连接。

        ![“托管标识”选项](media/authenticate-playbooks-to-sentinel/auth-methods-msi-choice.png)

    1. 填写此连接的名称，选择“系统分配的托管标识”，然后选择“创建”。

        ![通过托管标识连接](media/authenticate-playbooks-to-sentinel/auth-methods-msi.png)

### <a name="authenticate-as-an-azure-ad-user"></a>以 Azure AD 用户身份进行身份验证

若要建立连接，请选择“登录”。 系统会提示你提供帐户信息。 完成此操作后，按照屏幕上的其余说明创建连接。

### <a name="authenticate-as-a-service-principal-azure-ad-application"></a>作为服务主体进行身份验证（Azure AD 应用程序）

可以通过注册 Azure AD 应用程序来创建服务主体。 最好使用已注册的应用程序作为连接器的标识，而不是使用用户帐户，因为这样可以更好地控制权限、管理凭据，并对连接器的使用启用某些限制。

若要将自己的应用程序与 Azure Sentinel 连接器一起使用，请执行以下步骤：

1. 向 Azure AD 注册应用程序并创建服务主体。 [了解操作方法](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)。

1. 获取凭据（供将来进行身份验证）。

    在“已注册的应用程序”边栏选项卡中，获取用于登录的应用程序凭据：

    - 客户端 ID：位于“概述”下
    - “客户端密码”：位于“客户端和密码”下。

1. 授予对 Azure Sentinel 工作区的权限。

    在此步骤中，应用将获得使用 Azure Sentinel 工作区的权限。

    1. 在 Azure Sentinel 工作区中，转到“设置” -> “工作区设置” -> “访问控制(IAM)”

    1. 选择“添加角色分配”。

    1. 选择要分配到应用程序的角色。 例如，若要允许应用程序执行将在 Sentinel 工作区中进行更改的操作（如更新事件），请选择“Azure Sentinel 参与者”角色。 对于仅读取数据的操作，“Azure Sentinel 读者”角色就足够了。 [详细了解 Azure Sentinel 中的可用角色](./roles.md)。

    1. 查找所需的应用程序并保存。 默认情况下，可用选项中不显示 Azure AD 应用程序。 若要查找应用程序，请搜索其名称并选中它。

1. Authenticate

    在此步骤中，我们使用应用凭据对逻辑应用中的 Sentinel 连接器进行身份验证。

    - 选择“通过服务主体进行连接”。

        ![服务主体选项](media/authenticate-playbooks-to-sentinel/auth-methods-spn-choice.png)

    - 填写必需的参数（可以在“已注册应用程序”边栏选项卡中找到）
        - 租户：位于“概述”下
        - 客户端 ID：位于“概述”下
        - “客户端密码”：位于“客户端和密码”下
        
        ![通过服务主体进行连接](media/authenticate-playbooks-to-sentinel/auth-methods-spn.png)

### <a name="manage-your-api-connections"></a>管理 API 连接

每当首次创建身份验证时，都会创建一个类型为“API 连接”的新 Azure 资源。 相同的 API 连接可以在同一资源组中的所有 Azure Sentinel 操作和触发器中使用。

可以在“API 连接”边栏选项卡中找到所有 API 连接（在 Azure 门户中搜索“API 连接”）。

还可以通过转到“资源”边栏选项卡并按类型“API 连接”筛选显示内容来查找它们。 这样可以为批量操作选择多个连接。

若要更改现有连接的授权，请输入连接资源，然后选择“编辑 API 连接”。

## <a name="next-steps"></a>后续步骤

在本文中，你了解了向 Azure Sentinel 验证基于逻辑应用的 playbook 的不同方法。
- 详细了解如何[在 playbook 中使用触发器和操作](playbook-triggers-actions.md)。
