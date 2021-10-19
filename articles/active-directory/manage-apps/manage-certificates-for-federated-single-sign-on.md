---
title: 管理联合证书
description: 了解如何自定义联合证书的过期日期，以及如何续订即将过期的证书。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: davidmu
ms.reviewer: saumadan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 886defd9362d1001a32d3076623ffd87ce718fe3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617580"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>在 Azure Active Directory 中管理用于联合单一登录的证书

在本文中，我们将介绍 Azure Active Directory (Azure AD) 创建的证书的常见问题和相关信息，这些证书旨在与服务型软件 (SaaS) 应用程序建立联合单一登录 (SSO)。 从 Azure AD 应用库或使用非库应用程序模板添加应用程序。 使用联合 SSO 选项配置应用程序。

本文仅适用于配置为通过[安全断言标记语言](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 联合身份验证使用 Azure AD SSO 的应用。

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>为库和非库应用程序自动生成的证书

从库中添加新应用程序并配置基于 SAML 的登录时（通过从应用程序概述页选择“单一登录” > “SAML”），Azure AD 将为应用程序生成一个证书，有效期为 3 年。 要下载活动证书作为安全证书 (.cer) 文件，请返回该页面（“基于 SAML 的登录”），选择“SAML 签名证书”标题中的下载链接。 可以在原始（二进制）证书或 Base64（base 64 编码文本）证书之间进行选择。 对于库应用程序，此部分可能还会显示一个作为联合身份验证元数据 XML下载证书的链接（.xml 文件），具体取决于应用程序的要求。

![SAML 活动签名证书下载选项](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

此外，还可以通过选择“SAML 签名证书”标题的“编辑” 图标（铅笔）来下载活动或非活动证书，该操作将显示“SAML 签名证书”页面。 选择要下载的证书旁边的省略号 ( **...** )，然后选择所需的证书格式。 可以使用其他选项，以隐私增强邮件 (PEM) 格式下载证书。 这种格式等同于 Base64，但带有 .pem 文件扩展名，在 Windows 中不会将这种文件识别为证书格式。

![SAML 签名证书下载选项（活动和非活动）](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>自定义联合身份验证证书的过期日期以及滚动使用新证书

Azure AD 默认将证书配置为三年后过期，自在 SAML 单一登录配置期间自动创建之时开始算起。 由于证书在保存后无法再更改日期，因此必须：

1. 按照所需日期创建新证书。
1. 保存新证书。
1. 以正确格式下载证书。
1. 将新证书上传到应用程序。
1. 确保新证书在 Azure Active Directory 门户中处于活动状态。

以下两个部分将帮助你执行这些步骤。

### <a name="create-a-new-certificate"></a>创建新证书

首先，创建并保存具有不同到期日期的新证书：

1. 登录到 [Azure Active Directory 门户](https://aad.portal.azure.com/)。 此时会显示“Azure Active Directory 管理中心”页。
1. 在左窗格中，选择“企业应用程序”。 此时将显示帐户中企业应用程序的列表。
1. 选择受影响的应用程序。 此时将显示应用程序的概述页面。
1. 在应用程序概述页面的左上角，选择“单一登录”。
1. 显示“选择单一登录方法”页时，选择“SAML”。 
1. 在“使用 SAML 设置单一登录 - 预览版”页中，找到“SAML 签名证书”标题，并选择“编辑”图标（铅笔）。 此时将显示“SAML 签名证书”页，其中显示每个证书的状态（“活动”或“非活动”）、到期日期和缩略图（哈希字符串）。
1. 选择“新建证书”。 证书列表下面会显示一个新行，其中的到期日期默认为当前日期起三年之后。 （由于尚未保存更改，所以仍可以修改到期日期。）
1. 在新证书行中，将鼠标悬停在到期日期列上，然后选择“选择日期”图标（日历）。 此时将显示一个日历控件，其中显示新行当前到期日期的月份日期。
1. 使用日历控件设置新日期。 可以设置当前日期到当前日期之后三年之间的任何日期。
1. 选择“保存”。 现在，将显示新证书的状态“非活动”、所选的到期日期和缩略图。 备注 - 如果现有证书已过期，并且生成新证书，即使新证书尚未激活，也会将其视为签名令牌。
1. 选择 X 返回到“使用 SAML 设置单一登录 - 预览版”页。

### <a name="upload-and-activate-a-certificate"></a>上传并激活证书

接下来，以正确格式下载新证书，将其上传到应用程序，使其在 Azure Active Directory 中处于活动状态：

1. 通过以下任一方法，查看应用程序的其他 SAML 登录配置说明：

   - 选择“配置指南”链接，在单独的浏览器窗口或选项卡中查看，或者
   - 转到“设置”标题，然后选择“查看分步说明”，在边栏中查看它们。

1. 记下说明中所述的证书上传所需的编码格式。
1. 按照前面[为库和非库应用程序自动生成证书](#auto-generated-certificate-for-gallery-and-non-gallery-applications)部分的说明执行操作。 此步骤将以应用程序上传所需的编码格式下载证书。
1. 如果要滚动到新证书，请返回“SAML 签名证书”页，在新保存的证书行中，选择省略号 (...)，然后选择“激活证书”。 新证书的状态将变为“活动”，以前处于活动状态的证书将变为“非活动”。
1. 继续按照前面显示的应用程序的 SAML 登录配置说明执行操作，以便以正确的编码格式上传 SAML 签名证书。

如果你的应用程序未验证证书是否过期，并且证书在 Azure Active Directory 和应用程序中均匹配，则即使证书过期，仍可访问应用。 请确保应用程序可以验证证书的到期日期。

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>添加证书过期的电子邮件通知地址

Azure AD 将分别在 SAML 证书到期前 60 天、30 天和 7 天发送电子邮件通知。 可以添加多个电子邮件地址来接收通知。 要指定将通知发送到的电子邮件地址：

1. 在“SAML 签名证书”页中，转到“通知电子邮件地址”标题。 默认情况下，此标题仅使用添加该应用程序的管理员的电子邮件地址。
1. 在最后的电子邮件地址下，键入应接收证书过期通知的电子邮件地址，然后按 Enter。
1. 对于要添加的每个电子邮件地址重复上述步骤。
1. 如果要删除每个电子邮件地址，请选择电子邮件地址旁边的“删除”图标（垃圾桶）。
1. 选择“保存”。

最多可以在通知列表中添加 5 个电子邮件地址（包括添加应用程序的管理员的电子邮件地址）。 如果需要更多人员收到通知，请使用通讯组电子邮件。

你将接收来自 azure-noreply@microsoft.com 的通知电子邮件。 为避免电子邮件进入垃圾邮件位置，请将此电子邮件添加为联系人。

## <a name="renew-a-certificate-that-will-soon-expire"></a>续订即将过期的证书

如果证书即将过期，则可以按照一定的过程续订证书，以避免造成用户严重停机。 续订即将到期的证书：

1. 按照前面“[创建新证书](#create-a-new-certificate)”部分的说明执行操作，使用一个与现有证书重叠的日期。 该日期可限制证书过期导致的停机时间。
1. 如果应用程序可以自动滚动更新证书，请按照以下步骤将新证书设置为“活动”：
   1. 返回到“SAML 签名证书”页。
   1. 在新保存的证书行中，选择省略号 (...)，然后选择“激活证书”。
   1. 跳过接下来的两个步骤。

1. 如果应用一次只能处理一个证书，请选择执行下一步的停机时间间隔。 （或者，如果应用程序不会自动选取新证书，但可以处理多个签名证书，则可以随时执行下一步。）
1. 在旧证书过期之前，请按照前面“[上传和激活证书](#upload-and-activate-a-certificate)”部分的说明进行操作。 如果在 Azure Active Directory 中更新新证书后，应用程序证书未更新，则应用上的身份验证可能会失败。
1. 登录到应用程序，确保证书正常工作。

如果你的应用程序未验证 Azure Active Directory 中配置的证书是否过期，且证书在 Azure Active Directory 和应用程序中均匹配，则即使证书过期，应用仍可访问。 请确保应用程序可以验证证书是否过期。

## <a name="related-articles"></a>相关文章

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](../saas-apps/tutorial-list.md)
- [使用 Azure Active Directory 管理应用程序](what-is-application-management.md)
- [单一登录到 Azure Active Directory 中的应用程序](what-is-single-sign-on.md)
- [在 Azure Active Directory 中调试应用程序的基于 SAML 的单一登录](./debug-saml-sso-issues.md)
