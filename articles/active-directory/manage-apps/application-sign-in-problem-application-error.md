---
title: 登录后应用页面上显示的错误消息
titleSuffix: Azure AD
description: 如何解决应用返回错误消息时的 Azure AD 登录问题。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 499ce4c7875a1805b5845614221ffef0641816d6
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617770"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in-azure-active-directory"></a>用户登录 Azure Active Directory 后，应用页面上会显示错误消息

在此场景中，Azure Active Directory (Azure AD) 会使用户登录。 但是应用程序会显示错误消息，并且不允许用户完成登录流程。 问题在于应用不接受 Azure AD 发出的响应。

应用不接受来自 Azure AD 的响应可能有几种原因。 如果错误消息没有明确指出响应中缺少的内容，请尝试以下操作：

- 如果应用是 Azure AD 库，请验证你是否遵循了[如何在 Azure AD 中调试对应用程序进行的基于 SAML 的单一登录](./debug-saml-sso-issues.md)中的步骤。

- 使用 [Fiddler](https://www.telerik.com/fiddler) 之类的工具捕获 SAML 请求、SAML 响应和 SAML 令牌。

- 将 SAML 响应发送给应用供应商，并向他们询问缺少的内容。

## <a name="attributes-are-missing-from-the-saml-response"></a>SAML 响应中缺少属性

若要在 Azure AD 配置中添加将在 Azure AD 响应中发送的属性，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2. 在左侧的导航窗格顶部，选择“所有服务”打开 Azure AD 扩展。

3. 在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”。

4. 在 Azure AD 导航窗格中选择“企业应用程序”。

5. 选择“所有应用程序”，以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用，请使用“所有应用程序列表”顶部的“筛选器”控件。 将“显示”选项设置为“所有应用程序”。

6. 选择要配置为单一登录的应用程序。

7. 应用加载后，在导航窗格中选择“单一登录”。

8. 在“用户属性”部分中，选择“查看和编辑所有其他用户属性” 。 在这里，你可以更改用户登录时要在 SAML 令牌中发送到应用的属性。

   若要添加属性：

   1. 选择“添加属性”。 输入“名称”，然后从下拉列表中选择“值” 。

   1. 选择“保存”。 然后就能在表中看到新属性了。

9. 保存配置。

   用户下次登录应用时，Azure AD 将发送 SAML 响应中的新属性。

## <a name="the-app-doesnt-identify-the-user"></a>应用无法识别用户

由于 SAML 响应缺少属性（例如 role），因此登录应用失败。 或者，它会失败的原因是应用期望针对 NameID（用户标识符）属性使用其他格式或值。

如果你使用 [Azure AD 自动用户预配](../app-provisioning/user-provisioning.md)在应用中创建、维护和删除用户，请验证是否已将用户预配到 SaaS 应用程序。 有关详细信息，请参阅[没有为任何用户预配 Azure AD 库应用程序](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)。

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>在 Azure AD 应用配置中添加属性

若要更改用户标识符值，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2. 在左侧的导航窗格顶部，选择“所有服务”打开 Azure AD 扩展。

3. 在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”。

4. 在 Azure AD 导航窗格中选择“企业应用程序”。

5. 选择“所有应用程序”，以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用，请使用“所有应用程序列表”顶部的“筛选器”控件。 将“显示”选项设置为“所有应用程序”。

6. 选择要配置为 SSO 的应用。

7. 应用加载后，在导航窗格中选择“单一登录”。

8. 在“用户属性”下的“用户标识符”下拉列表中，为用户选择唯一标识符 。

## <a name="change-the-nameid-format"></a>更改 NameID 格式

如果应用程序期望针对 NameID（用户标识符）属性使用其他格式，请参阅[编辑 nameID](../develop/active-directory-saml-claims-customization.md#editing-nameid) 以更改 NameID 格式。

Azure AD 会根据所选值或 SAML AuthRequest 中应用要求的格式，为 NameID 属性（用户标识符）选择格式。 有关详细信息，请参阅[单一登录 SAML 协议](../develop/single-sign-on-saml-protocol.md#nameidpolicy)的“NameIDPolicy”部分。

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>应用期望对 SAML 响应使用其他签名方法

若要更改 SAML 令牌的哪些部分由 Azure AD 进行数字签名，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2. 在左侧的导航窗格顶部，选择“所有服务”打开 Azure AD 扩展。

3. 在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”。

4. 在 Azure AD 导航窗格中选择“企业应用程序”。

5. 选择“所有应用程序”，以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件。 将“显示”选项设置为“所有应用程序”。

6. 选择要配置为单一登录的应用程序。

7. 应用程序加载后，在导航窗格中选择“单一登录”。

8. 在“SAML 签名证书”下，选择“显示高级证书签名设置” 。

9. 从以下选项中选择应用期望的“签名选项”：

   - 为 SAML 响应签名
   - 为 SAML 响应和断言签名
   - 为 SAML 断言签名

   用户下次登录应用时，Azure AD 会对所选择的 SAML 响应部分进行签名。

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>应用期望使用 SHA-1 签名算法

默认情况下，Azure AD 使用最安全的算法对 SAML 令牌进行签名。 我们的建议是，除非应用需要 SHA-1，否则不要将签名算法更改为 SHA-1。

若要更改签名算法，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2. 在左侧的导航窗格顶部，选择“所有服务”打开 Azure AD 扩展。

3. 在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”。

4. 在 Azure AD 导航窗格中选择“企业应用程序”。

5. 选择“所有应用程序”，以查看应用程序列表。

   > [!NOTE]
   > 如果看不到所需的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件。 将“显示”选项设置为“所有应用程序”。

6. 选择要配置为单一登录的应用。

7. 应用加载后，在应用左侧的导航窗格中选择“单一登录”。

8. 在“SAML 签名证书”下，选择“显示高级证书签名设置” 。

9. 选择“SHA-1”作为“签名算法” 。

   用户下次登录应用时，Azure AD 将使用 SHA-1 算法对 SAML 令牌进行签名。

## <a name="next-steps"></a>后续步骤

[如何在 Azure AD 中调试对应用程序进行的基于 SAML 的单一登录](./debug-saml-sso-issues.md)。
