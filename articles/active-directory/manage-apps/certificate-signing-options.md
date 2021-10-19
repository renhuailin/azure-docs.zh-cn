---
title: SAML 令牌中的高级证书签名选项
titleSuffix: Azure AD
description: 了解如何使用 Azure Active Directory 中预先集成的应用的 SAML 令牌中的高级证书签名选项
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: davidmu
ms.reviewer: saumadan
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3d1ccac8fa8ac0deddb8a581c583779b1fb2386
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129611793"
---
# <a name="advanced-certificate-signing-options-in-a-saml-token-in-azure-active-directory"></a>Azure Active Directory 中的 SAML 令牌中的高级证书签名选项

Azure Active Directory (Azure AD) 目前支持 Azure Active Directory 应用库中的数千个预先集成应用程序。 超过 500 个应用程序通过使用 [安全断言标记语言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 协议（如 [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) 应用程序）支持单一登录。 当用户通过 Azure AD 使用 SAML 对应用程序进行身份验证时，Azure AD 会将令牌发送到应用程序（通过 HTTP POST）。 然后，应用程序验证并使用该令牌来登录用户账号，而不是提示输入用户名和密码。 这些 SAML 令牌已使用 Azure AD 中生成的唯一证书根据特定的标准算法签名。

Azure AD 对库应用程序使用一些默认设置。 默认值是根据应用程序的要求设置的。

在 Azure AD 中，您可以设置证书签名选项和证书签名算法。

## <a name="certificate-signing-options"></a>证书签名选项

Azure AD 支持三个证书签名选项：

* **为 SAML 断言签名**。 已针对大多数库应用程序设置此默认选项。 如果选择此选项，则用作标识提供者 (IdP)  的 Azure AD 会使用应用程序的 [X.509](https://wikipedia.org/wiki/X.509)证书来为 SAML 断言和证书签名。

* **为 SAML 响应签名**。 如果选择此选项，则用作 IdP 的 Azure AD 会使用应用程序的 X.509 证书来为 SAML 响应签名。

* **为 SAML 响应和断言签名**。 如果选择此选项，则用作 IdP 的 Azure AD 会使用应用程序的 X.509 证书来为完整 SAML 令牌签名。

## <a name="certificate-signing-algorithms"></a>证书签名算法

Azure AD 支持使用两种签名算法或哈希算法 (SHAs) 来为 SAML 响应签名：

* **SHA-256**。 Azure AD 支持使用此默认算法来为 SAML 响应签名： 这是最新的算法，比 SHA-1 更安全。 大多数应用程序支持 SHA-256 算法。 如果应用程序仅支持将 SHA-1 用作签名算法，可以更改算法。 否则，我们建议使用 SHA-256 算法来为 SAML 响应签名。

* **SHA-1**。 这是较旧的算法，被认为不如 SH-256 安全。 如果应用程序仅支持此签名算法，可以在“签名算法”下拉列表选择此选项。 然后，Azure AD 会使用 SHA-1 算法为 SAML 响应签名。

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>更改证书签名选项和签名算法

若要更改应用程序的 SAML 证书签名选项和证书签名算法，请选择有问题的应用程序：

1. 在 [Azure Active Directory 门户](https://aad.portal.azure.com/)中，登录到你的帐户。 此时会显示“Azure Active Directory 管理中心”页。
1. 在左窗格中，选择“企业应用程序”。 此时将显示帐户中企业应用程序的列表。
1. 选择应用程序。 此时将显示应用程序的 "概览" 页面。 本示例使用 Salesforce 应用程序。

   ![示例：应用程序“”概览”页面](./media/certificate-signing-options/application-overview-page.png)

接下来，在该应用程序的 SAML 令牌中更改证书签名选项：

1. 在应用程序“概览”页面的左上角，选择 **单一登录**。
1. 如果 **出现“使用 SAML 预览设置单一登录”页面**，请转到步骤 5。
1. 如果未出现 " **选择单一登录方法** " 页面，请选择 " **更改单一登录模式** " 以显示该页面。
1. 在 **“选择单一登录方法”** 页上选择 **“SAML”** 。 (如果 **SAML** 不可用，则该应用程序不支持 SAML，你可以忽略此过程和文章的其余部分。 )
1. 在 " **使用 SAML 预览设置单一登录** " 页中，找到 " **SAML 签名证书** " 标题，并选择 " **编辑** " 图标 (铅笔) "。 此时将显示 " **SAML 签名证书** " 页。

   ![示例：SAML 签名证书页面](./media/certificate-signing-options/saml-signing-page.png)

1. 在“签名选项”下拉列表中，选择“为 SAML 响应签名”、“为 SAML 断言签名”或“为 SAML 响应和断言签名”。    本文前面的 [证书签名选项](#certificate-signing-options)中显示了这些选项的说明。
1. 在“签名算法”下拉列表中，选择“SHA-1”或“SHA-256”。   本文前面的 [证书签名算法](#certificate-signing-algorithms)一节中显示了这些选项的说明。
1. 如果你对选择满意，请选择 " **保存** " 以应用新的 SAML 签名证书设置。 否则，请选择 **X** 以放弃更改。

## <a name="next-steps"></a>后续步骤

* [针对不在 Azure Active Directory 应用库中的应用程序配置单一登录](./configure-saml-single-sign-on.md)
* [排查基于 SAML 的单一登录的问题](./debug-saml-sso-issues.md)
