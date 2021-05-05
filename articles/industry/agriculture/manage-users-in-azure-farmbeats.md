---
title: 在 Azure FarmBeats 中管理用户
description: 本文介绍如何在 Azure FarmBeats 中管理用户。
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-ummehabiba
ms.openlocfilehash: de9a11cac9856417422bd72579cbca44233ab422
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179895"
---
# <a name="manage-users"></a>管理用户

Azure FarmBeats 包括对属于 Azure Active Directory (Azure AD) 实例的人员进行用户管理的功能。 可以将用户添加到 Azure FarmBeats 实例，以访问 API、查看生成的映射，以及访问来自场的传感器遥测数据。

## <a name="prerequisites"></a>先决条件

- 需要安装 Azure FarmBeats。 有关详细信息，请参阅[安装 Azure FarmBeats](install-azure-farmbeats.md)。
- 需要在 Azure FarmBeats 实例中添加或删除的用户的电子邮件 ID。

## <a name="manage-azure-farmbeats-users"></a>管理 Azure FarmBeats 用户

Azure FarmBeats 将 Azure AD 用于身份验证、访问控制和角色。 可以将 Azure AD 租户中的用户添加为 Azure FarmBeats 中的用户。

> [!NOTE]
> 如果用户不是 Azure AD 租户用户，请按照“添加 Azure AD 用户”部分的说明来完成设置。

Azure FarmBeats 支持两种类型的用户角色：

 - 管理员：具有 Azure FarmBeats Datahub API 的完全访问权限。 此角色的用户可以查询所有的 Azure FarmBeats Datahub 对象，并且可以从 FarmBeats 加速器执行所有操作。
 - 只读：具有 FarmBeats Datahub API 的只读访问权限。 用户可以查看 Datahub API、加速器仪表板和映射。 具有只读访问权限的用户无法执行生成映射、关联设备或创建场等操作。

## <a name="add-users-to-azure-farmbeats"></a>将用户添加到 Azure FarmBeats

若要将用户添加到 Azure FarmBeats，请执行以下操作：

1. 登录到加速器，然后选择“设置”图标。
2. 选择“访问控制”。

    ![“场设置”窗格](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. 输入要向其授予访问权限的用户的电子邮件 ID。
4. 选择所需角色：“管理员”或“只读” 。
5. 选择“添加角色”。

添加的用户现在可以访问 Azure FarmBeats（Datahub 和加速器）。

## <a name="delete-users-from-azure-farmbeats"></a>从 Azure FarmBeats 删除用户

若要从 Azure FarmBeats 系统删除用户，请执行以下操作：

1. 登录到加速器，然后选择“设置”图标。
2. 选择“访问控制”。
3. 选择“删除”。 

   该用户会被从系统中删除。 你会收到以下确认消息：

   ![Azure FarmBeats 确认消息](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>添加 Azure AD 用户

> [!NOTE]
> 在将 Azure FarmBeats 用户分配到应用程序和角色之前，这些用户需要在 Azure AD 租户中存在。 如果某个用户在 Azure AD 租户中不存在，请按照本部分的说明进行操作。 如果某个用户在 Azure AD 租户中已经存在，请跳过这些说明。

按照以下步骤将用户添加到 Azure AD：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在右上角选择帐户，然后切换到与 FarmBeats 关联的 Azure AD 租户。
3. 选择“Azure Active Directory” > “用户”。 

    此时会显示 Azure AD 用户的列表。

4. 若要向该目录添加用户，请选择“新建用户”。 若要添加外部用户，请选择“新建来宾用户”。

    ![“所有用户”窗格](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. 选择新用户的名称，然后完成该用户的必填字段。
6. 选择“创建”  。

若要了解如何管理 Azure AD 用户，请参阅[在 Azure AD 中添加或删除用户](../../active-directory/fundamentals/add-users-azure-active-directory.md)。

## <a name="next-steps"></a>后续步骤

你已成功将用户添加到 Azure FarmBeats 实例。 现在，请了解如何[创建和管理场](manage-farms-in-azure-farmbeats.md#create-farms)。