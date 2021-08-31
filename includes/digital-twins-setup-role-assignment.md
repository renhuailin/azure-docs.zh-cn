---
author: baanders
description: Azure 数字孪生安装程序中的访问权限步骤的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 8e5c39cbd31757116ec17df502c0958830841b07
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468683"
---
Azure 数字孪生使用 [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) 进行基于角色的访问控制 (RBAC)。 这意味着，在用户可以对 Azure 数字孪生实例进行数据平面调用之前，需要为该用户分配具有相应权限的角色。

对于 Azure 数字孪生，此角色是“Azure 数字孪生数据所有者”。 要详细了解角色和安全性，可参阅 [Azure 数字孪生解决方案的安全性](../articles/digital-twins/concepts-security.md)。

> [!NOTE]
> 此角色与 Azure AD“所有者”角色不同，后者也可以在 Azure 数字孪生实例范围内分配。 这是两个不同的管理角色，所有者不授予对数据平面功能的访问权限，而 Azure 数字孪生数据所有者则可以授予。

本部分介绍如何为 Azure 数字孪生实例中的用户创建角色分配（在 Azure AD 租户的 Azure 订阅中使用该用户的电子邮件）。 根据你在组织中的角色，你可以为自己设置此权限，或者以将要管理 Azure 数字孪生实例的其他人的名义设置此权限。