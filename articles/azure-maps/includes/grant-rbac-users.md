---
title: 为用户授予基于角色的访问权限
titleSuffix: Azure Maps
description: 使用基于角色的访问控制向用户授予授权 Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 234c3358b98b7af677793fba58c1602a8bd976f0
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101102812"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>向用户授予 Azure Maps 的基于角色的访问权限

可以通过将 Azure AD 组或安全主体分配给一个或多个 Azure Maps 角色定义，向 azure *RBAC) 授予 azure 基于角色的访问控制 (* 。 若要查看可用于 Azure Maps 的 Azure 角色定义，请 **访问 (IAM) 的 "访问控制**"。 选择 " **角色**"，然后搜索以 *Azure Maps* 开头的角色。

* 若要有效地管理大量用户对 Azure Maps 的访问权限，请参阅 [Azure AD 组](../../active-directory/fundamentals/active-directory-manage-groups.md)。
* 对于允许用户对应用程序进行身份验证的用户，必须在 Azure AD 中创建用户。 请参阅 [添加或删除使用 Azure AD 的用户](../../active-directory/fundamentals/add-users-azure-active-directory.md)。

阅读有关 [Azure AD](../../active-directory/fundamentals/index.yml) 的详细信息，以便有效地管理用户的目录。

1. 中转到你的 **Azure Maps 帐户**。 **(IAM)**  >  **角色分配** 选择 "访问控制"。

    ![使用 Azure RBAC 授予访问权限](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在 " **角色分配** " 选项卡上的 " **角色**" 下，选择内置 Azure Maps 角色定义，如 **Azure Maps 数据读取器** 或 **Azure Maps 数据参与者**。 在“分配访问权限至”下，选择“Azure AD 用户、组或服务主体”。 按名称选择主体。 再选择“保存”。

   * 请参阅 [分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)的详细信息。

> [!WARNING]
> Azure Maps 内置角色定义提供对许多 Azure Maps REST Api 的一种非常大的授权访问权限。 若要限制用户至少使用 Api，请参阅 [创建自定义角色定义和将用户分配](../../role-based-access-control/custom-roles.md) 到自定义角色定义。 这样，用户就可以拥有应用程序所需的最小特权。