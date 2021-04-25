---
title: 添加和管理 Azure AD 应用程序 - Azure 市场
description: 了解如何在合作伙伴中心为商业市场计划添加和管理 Azure AD 应用程序。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107878"
---
# <a name="add-and-manage-azure-ad-applications"></a>添加和管理 Azure AD 应用程序

**相应的角色**

- 所有者
- Manager

可以允许属于公司 Azure Active Directory (Azure AD) 的应用程序或服务访问合作伙伴中心帐户。

## <a name="add-existing-azure-ad-applications"></a>添加现有的 Azure AD 应用程序

若要添加已存在于公司的 Azure Active Directory 中的应用程序，请执行以下操作：

1. 在“用户”页（在“帐户设置”下）上，选择“添加 Azure AD 应用程序”  。
1. 从显示的列表中选择一个或多个 Azure AD 应用程序。 可以使用搜索框搜索特定 Azure AD 应用程序。 *如果选择多个 Azure AD 应用程序添加到合作伙伴中心帐户，则必须为它们分配相同的角色或自定义权限集。 若要添加具有不同角色/权限的多个 Azure AD 应用程序，请对每个角色或自定义权限集重复这些步骤。
1. 选择 Azure AD 应用程序后，选择“添加所选项”。
1. 在“角色”部分中，为所选 Azure AD 应用程序指定角色或自定义权限。
1. 选择“保存”。

## <a name="add-new-azure-ad-applications"></a>添加新的 Azure AD 应用程序

如果要向合作伙伴中心授予对全新 Azure AD 应用程序帐户的访问权限，则可以在“用户”部分中创建一个。 这会在公司工作帐户（Azure AD 租户）中创建新帐户，而不仅仅是在合作伙伴中心帐户中创建。 如果你主要使用此 Azure AD 应用程序进行合作伙伴中心身份验证，并且不需要用户直接访问该应用程序，则可以为“回复 URL”和“应用 ID URI”输入任何有效地址，前提是你目录中的任何其他 Azure AD 应用程序都不使用这些值 。

1. 在“用户”页（在“帐户设置”下）上，选择“添加 Azure AD 应用程序”  。
1. 在下一页，选择“新建 Azure AD 应用程序”。
1. 为新的 Azure AD 应用程序输入“回复 URL”。 这是用户可在其中登录并使用 Azure AD 应用程序的 URL（有时也称为“应用 URL”或“登录 URL”）。 “回复 URL”的长度不得超过 256 个字符，并且在目录中必须是唯一的。
1. 为新的 Azure AD 应用程序输入“应用 ID URI”。 这是在向 Azure AD 发送单一登录请求时提供的 Azure AD 应用程序的逻辑标识符。 “应用 ID URI”对于目录中的每个 Azure AD 应用程序必须是唯一的。 此 ID 的长度不得超过 256 个字符。 有关应用 ID URI 的详细信息，请参阅[将应用程序与 Azure Active Directory 集成](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)。
1. 在“角色”部分中，为 Azure AD 应用程序指定角色或自定义权限。
1. 选择“保存”。

添加或创建 Azure AD 应用程序后，可以返回到“用户”部分并选择应用程序名称以查看应用程序的设置，包括“租户 ID”、“客户端 ID”、“回复 URL”和“应用 ID URI”。

## <a name="remove-an-azure-ad-application"></a>删除 Azure AD 应用程序

若要从工作帐户（Azure AD 租户）中删除应用程序，请转到“用户”（在“帐户设置”下），使用最右侧列中的复选框选择要删除的应用程序，然后从可用操作中选择“删除”  。 将出现一个弹出窗口，供你确认是否要删除所选应用程序。

## <a name="manage-keys-for-an-azure-ad-application"></a>管理 Azure AD 应用程序的密钥

如果 Azure AD 应用程序在 Microsoft Azure AD 中读取和写入数据，则将需要密钥。 可以通过在合作伙伴中心编辑 Azure AD 应用程序的信息，为其创建密钥。 还可以删除不再需要的密钥。

1. 在“用户”页（在“帐户设置”下）上，选择 Azure AD 应用程序的名称 。 你将看到 Azure AD 应用程序的所有活动密钥，包括密钥的创建日期和过期时间。
1. 若要删除不再需要的密钥，请选择“删除”。
1. 若要添加新密钥，请选择“添加新密钥”。
1. 你将看到一个屏幕，显示“客户端 ID”和“密钥值” 。 请务必打印或复制此信息，因为离开此页面后将无法再次访问。
1. 如果要创建更多密钥，请选择“添加另一个密钥”。
