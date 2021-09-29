---
title: 转换链接和 URL Azure Active Directory 应用程序代理
description: 了解如何重定向使用 Azure Active Directory 应用程序代理发布的应用的硬编码链接。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 08b730b0196aa5b78ebafc0208accd51e544e8d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787483"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-active-directory-application-proxy"></a>重定向使用 Azure Active Directory 应用程序代理发布的应用的硬编码链接

Azure AD 应用程序代理使本地应用对远程或在自己设备上的用户可用。 但是，某些应用已通过嵌入到 HTML 中的本地链接开发。 远程使用应用时，这些链接将无法正常跳转。 如果有多个指向彼此的本地应用程序，你的用户会需要这些链接在他们不在办公室时能继续正常使用。 

确保链接在企业网络内部和外部都能够正常工作的最佳方式是，将应用的外部 URL 与其内部 URL 配置为相同的。 使用[自定义域](application-proxy-configure-custom-domain.md)来配置你的外部 URL，以获取企业域名，而不是默认的应用程序代理域。


如果无法在租户中使用自定义域，还有其他几个选项可提供此功能。 所有这些选项也都与自定义域兼容且彼此兼容，因此可以根据需要配置自定义域和其他解决方案。

> [!NOTE]
> 通过 JavaScript 生成的硬编码内部 URL 不支持链接转换。

选项 1：使用 Microsoft Edge - 此解决方案仅适用于你计划建议或要求用户通过 Microsoft Edge 浏览器访问应用程序的情况。 它处理所有已发布的 URL。 

**选项 2：使用 MyApps 扩展** - 此解决方案要求用户安装客户端浏览器扩展，但它将处理所有已发布的 URL，并可与大多数热门浏览器一起使用。 

**选项 3：使用链接转换设置** - 这是一种管理端设置，对用户不可见。 但是，它只会处理 HTML 和 CSS 中的 URL。   

无论用户位于何处，这三个功能都可使链接保持正常工作。 如果具有直接指向内部终结点或端口的应用，可以将这些内部 URL 映射到已发布的外部应用程序代理服务器 URL 中。 

 
> [!NOTE]
> 最后一个选项只适用于出于任何原因无法使用自定义域来使其应用具有相同的内部和外部 URL 的租户。 启用此功能之前，请查看 [Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)对你是否适用。 
> 
> 或者，如果需要配置链接转换的应用程序是 SharePoint，请参阅[配置 SharePoint 2013 的备用访问映射](/SharePoint/administration/configure-alternate-access-mappings)，以获取映射链接的另一种方法。 

 
### <a name="option-1-microsoft-edge-integration"></a>选项 1：Microsoft Edge 集成 

可以使用 Microsoft Edge 进一步保护应用程序和内容。 若要使用此解决方案，需要求/建议用户通过 Microsoft Edge 访问应用程序。 使用应用程序代理发布的所有内部 URL 都会被 Edge 识别并重定向到相应的外部 URL。 这可确保所有硬编码内部 URL 正常运行，并且如果用户在浏览器中直接键入该内部 URL，即使该用户是远程用户，该 URL 也可正常运行。  

若要了解详细信息（包括如何配置此选项），请参阅[在 Microsoft Intune 中使用适用于 iOS 和 Android 的 Edge 管理 Web 访问](/mem/intune/apps/manage-microsoft-edge)文档。  

### <a name="option-2-myapps-browser-extension"></a>选项 2：MyApps 浏览器扩展 

通过 MyApps 浏览器扩展，随应用程序代理一起发布的所有内部 URL 都将由该扩展识别并重定向到相应的外部 URL。 这可确保所有硬编码内部 URL 正常运行，并且如果用户在浏览器的地址栏中直接键入该内部 URL，即使该用户是远程用户，该 URL 也可正常运行。  

若要使用此功能，用户需要下载该扩展并登录。 管理员或用户不需要进行其他配置。 

若要了解详细信息（包括如何配置此选项），请参阅 [MyApps 浏览器扩展](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510#download-and-install-the-my-apps-secure-sign-in-extension)文档。

> [!NOTE]
> MyApps 浏览器扩展不支持通配符 URL 的链接转换。

### <a name="option-3-link-translation-setting"></a>选项 3：链接转换设置 

如果启用了链接转换，应用程序代理服务通过 HTML 和 CSS 搜索发布的内部链接并对它们进行转换，以便让用户获得不间断的体验。 使用 MyApps 浏览器扩展优先于链接转换设置，因为它可以为用户提供更高性能的体验。

> [!NOTE]
> 如果使用的是选项 2 或选项 3，则一次只应启用其中之一。

## <a name="how-link-translation-works"></a>链接转换工作原理

进行身份验证后，当代理服务器将应用程序数据传递给用户时，应用程序代理会扫描应用程序以获取硬编码的链接，并将其替换为各自的已发布外部 URL。

应用程序代理假定应用程序采用 UTF-8 编码。 如果不是这种情况，则在 HTTP 响应头中指定编码类型（如 `Content-Type:text/html;charset=utf-8`）。

### <a name="which-links-are-affected"></a>哪些链接受影响？

链接转换功能仅查找位于应用正文的代码标记中的链接。 应用程序代理具有一个单独的功能，用于转换标头中的 cookie 或 URL。 

本地应用程序中有两种常见类型的内部链接：

- 相对内部链接，可指向本地文件结构（如 `/claims/claims.html`）中的共享资源。 这些链接在通过应用程序代理发布的应用中自动运行，并且在有无链接转换时均能继续运行。 
- 指向其他本地应用（如 `http://expenses`）或已发布文件（如 `http://expenses/logo.jpg`）的硬编码内部链接。 链接转换功能会对硬编码的内部链接进行操作，将其更改为指向远程用户需要访问的外部 URL。

应用程序代理支持对其进行链接转换且存在于 HTML 代码标记中的属性的完整列表包括：
* a (href)
* audio (src)
* base (href)
* button (formaction)
* div (data-background, style, data-src)
* embed (src)
* form (action)
* frame (src)
* head (profile)
* html (manifest)
* iframe (longdesc, src)
* img (longdesc, src)
* input (formaction, src, value)
* link (href)
* menuitem (icon)
* meta (content)
* object (archive, data, codebase)
* script (src)
* source (src)
* track (src)
* video (src, poster)

此外，在 CSS 中，URL 属性也会被转换。

### <a name="how-do-apps-link-to-each-other"></a>应用如何彼此链接？

链接转换对每个应用程序均启用，以便可以按应用级别控制用户体验。 如果要将该应用的链接，而不是指向该应用的链接进行转换，请启用应用的链接转换功能。 

例如，假设有三个通过应用程序代理发布的应用程序，这些应用程序均彼此链接：Benefits、Expenses 和 Travel。 还有第四个应用，Feedback，但它不是通过应用程序代理发布的。

如果启用 Benefits 应用的链接转换，Expenses 和 Travel 的链接将重定向到这些应用的外部 URL，但不会重定向 Feedback 的链接，因为没有任何外部 URL。 从 Expenses 和 Travel 返回 Benefits 的链接无法运行，因为尚未启用这两个应用的链接转换。

![启用链接转换后，Benefits 到其他应用的链接](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>哪些链接不转换？

为了提高性能和安全性，不会转换某些链接：

- 代码标记外的链接。 
- 不在 HTML 或 CSS 中的链接。 
- URL 编码格式中的链接。
- 从其他程序打开的内部链接。 不会转换通过电子邮件或即时消息发送或其他文档中包含的链接。 用户需要了解转到外部 URL。

如果需要支持这两个方案之一，请使用相同的内部和外部 URL，而不是链接转换。  

## <a name="enable-link-translation"></a>启用链接转换

链接转换入门就像单击按钮一样简单：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 转到“Azure Active Directory” > “企业应用程序” > “所有应用程序”选择要管理的应用，再单击“应用程序代理”。
3. 将“转换应用程序主体中的 URL”改为“是”。

   ![选择“是”可转换应用程序主体中的 URL](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. 单击“保存”应用所做的更改。

现在，当用户访问此应用程序时，代理将自动扫描租户上通过应用程序代理发布的内部 URL。

## <a name="send-feedback"></a>发送反馈

我们希望获得你的帮助，使此功能适用于所有应用。 在 HTML 和 CSS 中搜索 30 多个标记。 如果有尚未进行转换的生成链接示例，请向[应用程序代理反馈](mailto:aadapfeedback@microsoft.com)发送一个代码片段。 

## <a name="next-steps"></a>后续步骤
[通过 Azure AD 应用程序代理使用自定义域](application-proxy-configure-custom-domain.md)，以便具有相同的内部和外部 URL

[配置 SharePoint 2013 的备用访问映射](/SharePoint/administration/configure-alternate-access-mappings)