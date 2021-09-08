---
title: 配置应用的发布者域 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何配置应用程序的发布者域，以告知用户其信息将发送到何处。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/23/2021
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: contperf-fy21q4, aaddev
ms.openlocfilehash: 7cf9918216c70bcbabf9c64c9b0ebf91c02b45fd
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029236"
---
# <a name="configure-an-applications-publisher-domain"></a>配置应用程序的发布者域

应用程序的发布者域将在[应用程序的许可提示](application-consent-experience.md)中显示给用户，以告知用户其信息将发送到何处。 在 2019 年 5 月 21 日之后注册的且没有发布者域的多租户应用程序将显示为 **unverified**。 多租户应用程序是指支持单个组织目录以外帐户的应用程序；例如，支持所有 Azure AD 帐户，或支持所有 Azure AD 帐户和 Microsoft 个人帐户。

## <a name="new-applications"></a>新应用程序

注册新应用时，应用的发布者域可设置为默认值。 该值取决于应用的注册位置，具体而言，取决于该应用是否已在某个租户中注册，以及该租户是否具有租户验证的域。

如果存在租户验证的域，则应用的发布者域将默认为该租户的主要已验证域。 如果不存在租户验证的域（应用程序未在租户中注册时会存在这种情况），则应用的发布者域将设置为 null。

下表汇总了发布者域值的默认行为。  

| 租户验证的域 | 发布者域的默认值 |
|-------------------------|----------------------------|
| Null | Null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com（主要） | domain2.com |

如果多租户应用程序的发布者域未设置，或者设置为以 .onmicrosoft.com 结尾的域，则应用的许可提示将在发布者域的位置显示“未验证”。

## <a name="grandfathered-applications"></a>祖父应用程序

如果应用是在 2019 年 5 月 21 日之前注册的，而你尚未设置发布者域，则应用程序的许可提示将不显示 **unverified**。 我们建议设置发布者域值，使用户能够在应用的许可提示中看到此信息。

## <a name="configure-publisher-domain-using-the-azure-portal"></a>使用 Azure 门户配置发布者域

若要设置应用的发布者域，请执行以下步骤。

1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，选择在其中注册应用的租户。
1. 导航到[“Azure Active Directory”>“应用注册”](https://go.microsoft.com/fwlink/?linkid=2083908)，找到并选择要配置的应用。

   选择应用后，会看到该应用的“概述”页。 
1. 在“管理”下，选择“品牌打造” 。
1. 找到“发布者域”字段并选择以下选项之一： 

   - 如果尚未配置域，请选择“配置域”。 
   - 如果已配置域，请选择“更新域”。 

如果应用已在租户中注册，则你会看到两个可供选择的选项卡：“选择已验证的域”和“验证新域”。  

如果域尚未在租户中注册，则你只会看到用来为应用程序验证新域的选项。

### <a name="to-verify-a-new-domain-for-your-app"></a>验证应用的新域

1. 创建名为 `microsoft-identity-association.json` 的文件并粘贴以下 JSON 代码片段。

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. 请将占位符 *{YOUR-APP-ID-HERE}* 替换为对应于应用的应用程序（客户端）ID。
1. 将该文件托管在 `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json` 中。 请替换占位符 *{YOUR-DOMAIN-HERE}* ，使之与已验证的域相匹配。
1. 单击“验证并保存域”按钮。 

在验证域后，无需维护用于验证的资源。 验证完成后，可以删除托管文件。

### <a name="to-select-a-verified-domain"></a>选择已验证的域

如果租户包含已验证的域，请从“选择已验证的域”下拉列表中选择一个域。 

> [!NOTE]
> 应该返回预期的 `Content-Type` 头 `application/json`。 如果使用类似 `application/json; charset=utf-8` 的其他任何内容，则可能会收到错误：
>
> `Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value.`
>

## <a name="implications-on-the-app-consent-prompt"></a>对应用许可提示的影响

配置发布者域会影响用户在应用许可提示中看到的内容。 若要完全了解许可提示的组件，请参阅[了解应用程序许可体验](application-consent-experience.md)。

下表描述了在 2019 年 5 月 21 日之前创建的应用程序的行为。

![在 2019 年 5 月 21 日之前创建的应用的许可提示](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

在 2019 年 5 月 21 日之后创建的新应用程序的行为取决于发布者域和应用程序的类型。 下表描述了使用不同的配置组合时预期看到的更改。

![在 2019 年 5 月 21 日之后创建的应用的许可提示](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>对重定向 URI 的影响

指定重定向 URI 时，允许用户使用任何工作或学校帐户或 Microsoft 个人帐户（多租户）登录的应用程序存在几项限制。

### <a name="single-root-domain-restriction"></a>单个根域限制

当多租户应用的发布者域值设置为 null 时，应用仅限于共享重定向 URI 的单个根域。 例如，不允许以下值的组合，因为根域 contoso.com 与 fabrikam.com 不匹配。

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>子域限制

允许子域，但必须显式注册根域。 例如，尽管以下 URI 共享单个根域，但不允许将其组合。

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

但是，如果开发人员显式添加根域，则允许组合。

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>异常

以下方案存在单个根域的限制：

- 单租户应用，或者面向单个目录中的帐户的应用
- 使用 localhost 作为重定向 URI
- 使用自定义方案（非 HTTP 或 HTTPS）的重定向 URI

## <a name="configure-publisher-domain-programmatically"></a>以编程方式配置发布者域

目前，不支持使用 REST API 或 PowerShell 以编程方式配置发布者域。
