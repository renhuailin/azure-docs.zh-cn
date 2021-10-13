---
title: 如何删除用户对应用程序的访问权限
description: 了解如何删除用户对 Azure Active Directory 中应用程序的访问权限
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/02/2020
ms.author: davidmu
ms.reviewer: phsignor
ms.openlocfilehash: 9b0334c3766a789af7ed8c29fac3e76aaae25476
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613788"
---
# <a name="how-to-remove-a-users-access-to-an-application-in-azure-active-directory"></a>如何删除用户对 Azure Active Directory 中应用程序的访问权限

本文介绍如何删除用户对应用程序的访问权限。

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>我要删除特定用户或组到应用程序的分配

若要删除用户或组对应用程序的分配，请按照[在 Azure Active Directory 中从企业应用删除用户或组分配](./assign-user-or-group-access-portal.md)一文中所列的步骤进行操作。

## <a name="i-want-to-disable-all-access-to-an-application-for-every-user"></a>我要禁用每个用户对应用程序的所有访问权限

若要禁用所有用户对应用程序的登录，请遵循[在 Azure Active Directory 中对企业应用禁用用户登录](./disable-user-sign-in-portal.md)一文中所列出的步骤。

## <a name="i-want-to-delete-an-application-entirely"></a>我要彻底删除应用程序

[应用程序管理快速入门系列](delete-application-portal.md)包括有关从 Azure Active Directory 租户中删除应用程序的指南。

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>我要禁用用户未来针对应用程序的所有同意操作

针对整个目录禁用用户同意操作，可防止最终用户同意任何应用程序。 管理员仍可代表用户执行同意操作。 有关应用程序同意的详细信息，以及可能希望或可能不希望这样做的原因，请阅读[了解用户和管理员同意](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)。 此外，请参阅[权限和许可](../develop/v2-permissions-and-consent.md)。

若要 **禁用用户未来在整个目录中执行的所有同意操作**，请根据以下说明进行操作：

1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。

2. 打开“Azure Active Directory 扩展”

3. 在导航菜单中，单击“企业应用程序”。

4. 单击“用户设置”。

5. 将“用户可以允许应用代表他们访问公司数据”切换为“否”并单击“保存”按钮。

## <a name="next-steps"></a>后续步骤

[管理对应用的访问](what-is-access-management.md)
