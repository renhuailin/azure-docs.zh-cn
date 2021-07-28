---
title: Azure 单一注销 SAML 协议
description: 本文介绍 Azure Active Directory 中的单一注销 SAML 协议
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 040e49ee870704107e1d4118f1484119d98a9ebf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781205"
---
# <a name="single-sign-out-saml-protocol"></a>单一注销 SAML 协议

Azure Active Directory (Azure AD) 支持 SAML 2.0 Web 浏览器单一注销配置文件。 要使单一注销正常工作，必须在应用程序注册期间向 Azure AD 显式注册应用程序的 **LogoutURL**。 如果将应用[添加到 Azure 应用库](v2-howto-app-gallery-listing.md)，则默认情况下可以设置此值。 否则，必须由将应用添加到其 Azure AD 租户的人员确定和设置此值。 Azure AD 使用 LogoutURL 在用户注销后将用户重定向。 

Azure AD 支持重定向绑定 (HTTP GET)，而不支持非 HTTP POST 绑定。

下图显示了 Azure AD 单一注销过程的工作流。

![Azure AD 单一注销工作流](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
云服务将 `LogoutRequest` 消息发送到 Azure AD，以指示会话已终止。 以下摘录显示了一个示例 `LogoutRequest` 元素。

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
发送到 Azure AD 的 `LogoutRequest` 元素需要以下属性：

* `ID` - 此项标识注销请求。 `ID` 的值不能以数字开头。 典型的做法是在 GUID 的字符串表示形式前面追加 **ID**。
* `Version` - 将此元素的值设置为 **2.0**。 此值是必需的。
* `IssueInstant` - 这是一个 `DateTime` 字符串，它包含协调世界时 (UTC) 值并采用[往返格式 ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings)。 Azure AD 需要此类型的值，但不强制要求。

### <a name="issuer"></a>颁发者
`LogoutRequest` 中的 `Issuer` 元素必须与 Azure AD 中云服务的 **ServicePrincipalNames** 之一完全匹配。 通常，此参数设置为应用程序注册期间指定的 **应用 ID URI**。

### <a name="nameid"></a>NameID
`NameID` 元素的值必须与所要注销的用户的 `NameID` 完全匹配。

## <a name="logoutresponse"></a>LogoutResponse
Azure AD 在响应 `LogoutRequest` 元素时发送 `LogoutResponse`。 以下摘录显示了一个示例 `LogoutResponse`。

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD 将设置 `LogoutResponse` 元素中的 `ID`、`Version` 和 `IssueInstant` 值。 它还将 `InResponseTo` 元素设置为获取响应的 `LogoutRequest` 的 `ID` 属性值。

### <a name="issuer"></a>颁发者
Azure AD 将此值设为 `https://login.microsoftonline.com/<TenantIdGUID>/`，其中，\<TenantIdGUID> 是 Azure AD 租户的租户 ID。

若要评估 `Issuer` 元素的值，请使用应用程序注册期间提供的 **应用 ID URI** 值。

### <a name="status"></a>状态
Azure AD 使用 `Status` 元素中的 `StatusCode` 元素来指示注销的成功或失败。当注销尝试失败时，`StatusCode` 元素还可能包含自定义错误消息。
