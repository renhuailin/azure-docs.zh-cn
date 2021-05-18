---
title: 向用户授予基于角色的访问权限
titleSuffix: Azure Maps
description: 使用基于角色的访问控制，向用户授予对 Azure Maps 的授权
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 234c3358b98b7af677793fba58c1602a8bd976f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101102812"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>向用户授予对 Azure Maps 基于角色的访问权限

通过将 Azure AD 组或安全主体分配到一个或多个 Azure Maps 角色定义，授予 *Azure 基于角色的访问控制 (Azure RBAC)* 。 若要查看可用于 Azure Maps 的 Azure 角色定义，请转到 **访问控制 (IAM)** 。 选择“角色”，然后搜索以“Azure Maps”开头的角色。

* 若要高效地管理大量用户对 Azure Maps 的访问权限，请参阅 [Azure AD 组](../../active-directory/fundamentals/active-directory-manage-groups.md)。
* 对于允许进行身份验证以访问应用程序的用户，必须在 Azure AD 中创建用户。 请参阅[使用 Azure AD 添加或删除用户](../../active-directory/fundamentals/add-users-azure-active-directory.md)。

了解有关 [Azure AD](../../active-directory/fundamentals/index.yml) 的详细信息，以便有效地管理用户目录。

1. 转到 **Azure Maps 帐户**。 选择“访问控制(IAM)” > “角色分配”。

    ![使用 Azure RBAC 授予访问权限](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. 在“角色分配”选项卡上的“角色”下，选择一个内置 Azure Maps 角色定义，例如“Azure Maps 数据读取器”或“Azure Maps 数据参与者”。 在“分配访问权限至”下，选择“Azure AD 用户、组或服务主体”。 按名称选择主体。 再选择“保存”。

   * 有关详细信息，请参阅[分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

> [!WARNING]
> Azure Maps 内置角色定义提供了一种范围很广的授权访问权限，可以访问许多 Azure Maps REST API。 若要将用户 API 限制为最少，请参阅[创建自定义角色定义并分配用户](../../role-based-access-control/custom-roles.md)至该自定义角色定义。 这样，用户将拥有应用程序所需的最少特权。