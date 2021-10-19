---
title: 应用程序管理证书常见问题
titleSuffix: Azure AD
description: 了解有关使用 Azure Active Directory 作为标识提供程序 (IdP) 管理应用证书的常见问题解答 (FAQ)。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: davidmu
ms.reviewer: sureshja, saumadan
ms.openlocfilehash: 3f94c7eae439303f2954b8ab62f499b789902e79
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619930"
---
# <a name="azure-active-directory-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory 应用程序管理证书常见问题

本页解答有关使用 Azure Active Directory (Azure AD) 作为标识提供程序 (IdP) 管理应用证书的常见问题。

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>是否有方法生成即将过期的 SAML 签名证书列表？

可通过 [PowerShell 脚本](app-management-powershell-samples.md)从目录中将包含指定应用的即将过期机密、证书及其所有者的所有应用注册信息导出到一个 CSV 文件中。

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>哪里可以找到即将到期的证书更新步骤信息？

可以在[此处](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire)找到步骤。

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>如何自定义 Azure AD 颁发的证书的到期日期？

默认情况下，Azure AD 将证书配置为在 SAML 单一登录配置期间自动创建证书的三年后过期。 由于在保存证书到期日期后无法更改该日期，因此需要创建新的证书。 具体操作步骤请参阅[自定义联合身份验证证书的到期日期以及滚动浏览到新证书](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)。

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>如何自动执行证书过期通知？

Azure AD 将在 SAML 证书到期前的 60 天、30 天和 7 天发送电子邮件通知。 你可添加多个用于接收通知的电子邮件地址。

> [!NOTE]
> 最多可在通知列表中添加 5 个电子邮件地址（包括添加应用程序的管理员的电子邮件地址）。 如果需要通知更多人员，请使用通讯组列表电子邮件。

若要指定发送通知的目标电子邮件地址，请参阅[添加证书到期的电子邮件通知地址](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)。

从 `aadnotification@microsoft.com` 接收到的这些电子邮件通知不可编辑或自定义。 不过，你可通过 [PowerShell 脚本](app-management-powershell-samples.md)导出包含即将过期的机密和证书的应用注册。

## <a name="who-can-update-the-certificates"></a>谁可以更新证书？

应用程序的所有者、全局管理员或应用程序管理员可通过 Azure 门户 UI、PowerShell 或 Microsoft Graph 更新证书。

## <a name="i-need-more-details-about-certificate-signing-options"></a>我需要有关证书签名选项的更多详细信息

在 Azure AD 中，可设置证书签名选项和证书签名算法。 如需了解详细信息，请参阅[适用于 Azure AD 应用程序的高级 SAML 令牌证书签名选项](certificate-signing-options.md)

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>我需要更换 Azure AD 应用程序代理应用的证书，并需要更多说明

若要更换 Azure AD 应用程序代理应用程序的证书，请参阅 [PowerShell 示例 - 更换应用程序代理应用中的证书](../app-proxy/scripts/powershell-get-custom-domain-replace-cert.md)。

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>如何管理 Azure AD 应用程序代理中的自定义域证书？

要将本地应用配置为使用自定义域，需具有已验证的 Azure Active Directory 自定义域、自定义域的 PFX 证书，以及要配置的本地应用。 如需了解详细信息，请参阅[Azure AD 应用程序代理中的自定义域](../app-proxy/application-proxy-configure-custom-domain.md)。

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>我需要更新应用程序端的令牌签名证书。 在哪里可以获得 Azure AD 端的该证书？

可以续订 SAML x.509 证书，请参阅 [SAML 签名证书](configure-saml-single-sign-on.md#saml-signing-certificate)。

## <a name="what-is-azure-ad-signing-key-rollover"></a>什么是 Azure AD 签名密钥滚动更新？

可在[此处](../develop/active-directory-signing-key-rollover.md)找到更多详细信息。

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>如何续订应用程序令牌加密证书？

若要续订应用程序令牌加密证书，请参阅[如何续订企业应用程序的令牌加密证书](howto-saml-token-encryption.md)。

## <a name="how-do-i-renew-application-token-signing-certificate"></a>如何续订应用程序令牌签名证书？

若要续订应用程序令牌签名证书，请参阅[如何为企业应用程序续订令牌签名证书](manage-certificates-for-federated-single-sign-on.md)。

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>如何在更改联合证书后更新 Azure AD？

若要在更改联合证书后更新 Azure AD，请参阅[续订 Microsoft 365 和 Azure Active Directory 的联合证书](../hybrid/how-to-connect-fed-o365-certs.md)。
