---
title: 如何在 Azure AD 中使用自助应用程序访问
description: 启用自助服务以方便用户在 Azure AD 中查找应用
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: lenalepa
ms.openlocfilehash: 017f43152ed041d0b3b9a24bab151bb06acb9d3f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124748477"
---
# <a name="how-to-use-self-service-application-access"></a>如何使用自助服务应用程序访问

在你的用户能够从“我的应用”页中自行发现应用程序之前，你需要先对你希望其允许用户自行发现并请求访问权限的任何应用程序启用“自助应用程序访问”。

对于 IT 组，此功能是节省时间和金钱的绝佳方式，我们强烈建议将其作为 Azure Active Directory 的新式应用程序部署的一部分。

若要从最终用户的角度了解如何使用“我的应用”，请参阅[“我的应用”门户帮助](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

使用此功能，可以：

- 允许用户从[我的应用](https://myapps.microsoft.com/)中自行发现应用程序，而无需求助 IT 组。
- 将这些用户添加到预配置组，以便查看请求访问的用户、删除访问权限以及管理分配给用户的角色。
- （可选）允许某人审批应用访问请求，为 IT 组省去麻烦。
- （可选）最多配置 10 个可以批准访问此应用程序的人员。
- （可选）允许某人设置用户登录应用程序时可以使用的密码。
- （可选）自动将自助服务分配的用户直接分配到应用程序角色。

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>启用自助服务应用程序访问以允许用户查找自己的应用程序

自助应用程序访问是帮助用户自己发现应用程序的绝佳方式，可选择性地允许业务组批准对这些应用程序的访问。 你可以允许业务组直接从“我的应用”页管理为这些用户分配的密码单一登录应用程序的凭据。

若要启用应用程序的自助应用程序访问，请执行以下步骤：

1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2. 在左侧主导航菜单顶部选择“所有服务”，打开“Azure Active Directory 扩展”。
3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4. 在 Azure Active Directory 的左侧导航菜单中，选择“企业应用程序”。
5. 选择“所有应用程序”，查看所有应用程序的列表。
   - 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。
6. 从列表中选择要对其启用自助访问的应用程序。
7. 加载应用程序后，在应用程序的左侧导航菜单中，选择“自助服务”。
8. 要对此应用程序启用自助应用程序访问，请将“允许用户请求对此应用程序的访问权限?”切换到“是”。
9. 接下来，若要选择一个组，以便将请求访问此应用程序的用户添加到其中，请选择“分配的用户应添加到哪个组?”标签旁边的选择器，然后选择一个组。
10. **可选：** 如果希望获得业务批准之后才允许用户访问，请将“需要获得批准才能授权访问此应用程序?”切换到“是”。
11. **可选：对于仅使用密码单一登录的应用程序，** 如果要允许业务审批人为已批准的用户指定发送到此应用程序的密码，请将“允许审批人设置此应用程序的用户密码?”切换到“是”。
12. 可选：指定获允审批对此应用的访问权限的业务审批者。 选择“谁可以批准访问此应用程序?” 然后，选择最多 10 个单独的业务审批者。
    - 不支持组。
13. 可选：对于公开角色的应用程序，如果想要向角色分配已获批准使用自助服务的用户，请选择“此应用程序中应向用户分配何种角色?”旁边的选择器，选择应向其分配这些用户的角色。
14. 选择顶部的“保存”按钮以完成此操作。

完成自助应用程序配置后，用户可以导航到[我的应用](https://myapps.microsoft.com/)，选择“+添加”按钮以查找你已允许对其进行自助访问的应用。 业务审批者还会在[我的应用](https://myapps.microsoft.com/)页中看到通知。 可以启用电子邮件，在用户请求需要审批人批准的应用程序的访问权限时，向审批人发送电子邮件通知。

这些批准仅支持单个审批工作流，意味着如果指定多个审批人，任何一个审批人都可以批准对该应用程序的访问。

## <a name="things-to-check-if-self-service-isnt-working"></a>在自助服务不起作用的情况下需要检查的内容

- 确保已让用户或组能够请求自助应用程序访问。
- 确保用户在进行自助应用程序访问时所访问的位置正确。 用户可以导航到[我的应用](https://myapps.microsoft.com/)页，选择“+添加”按钮以查找你已允许对其进行自助访问的应用。
- 如果最近配置了自助应用程序访问，请尝试在几分钟后再次登录并注销用户的“我的应用”，看看自助访问更改是否已显示。

## <a name="next-steps"></a>后续步骤

[为自助组管理设置 Azure Active Directory](../enterprise-users/groups-self-service-management.md)