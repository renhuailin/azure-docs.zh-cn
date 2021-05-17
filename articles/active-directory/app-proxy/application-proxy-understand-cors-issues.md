---
title: 了解并解决 Azure Active Directory 应用程序代理 CORS 问题
description: 了解 Azure Active Directory 应用程序代理 CORS，以及如何识别和解决 CORS 问题。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: c9331acc65b5a3e4b1f0acdda4ecccb5a6ca3044
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108229821"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>了解并解决 Azure Active Directory 应用程序代理 CORS 问题

[跨源资源共享 (CORS)](https://www.w3.org/TR/cors/) 有时会对通过 Azure Active Directory 应用程序代理发布的应用程序和 API 提出挑战。 本文讨论 Azure AD 应用程序代理 CORS 问题和解决方案。

浏览器安全性通常会阻止网页向另一个域发出 AJAX 请求。 此限制称为“同源策略”，可防止恶意站点从另一站点读取敏感数据。 但有时你可能想让其他站点调用你的 Web API。 CORS 是一项 W3C 标准，可让服务器放宽同源策略，在拒绝某些跨源请求的同时，允许接受另一些跨源请求。

## <a name="understand-and-identify-cors-issues"></a>了解并识别 CORS 问题

如果两个 URL 具有相同的方案、主机和端口 ([RFC 6454](https://tools.ietf.org/html/rfc6454))，则具有相同的源，例如：

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

与前两个 URL 相比，以下 URL 具有不同的源：

-   http:\//contoso.net - 不同域
-   http:\//contoso.com:9000/foo.html - 不同端口
-   https:\//contoso.com/foo.html - 不同方案
-   http:\//www.contoso.com/foo.html - 不同子域

如果未使用正确的访问控制标头，同源策略会阻止应用程序访问其他源中的资源。 如果 CORS 标头缺失或不正确，则跨源请求会失败。 

可以使用浏览器调试工具识别 CORS 问题：

1. 启动浏览器并浏览到 Web 应用。
1. 按 F12 以打开调试控制台。
1. 尝试重现事务，并查看控制台消息。 CORS 冲突会产生有关原点的控制台错误。

在下面的屏幕截图中，选择“试用”按钮导致出现 CORS 错误消息“在 Access-Control-Allow-Origin 标头中找不到 https:\//corswebclient-contoso.msappproxy.net”。

![CORS 问题](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>应用程序代理的 CORS 挑战

以下示例展示了典型的 Azure AD 应用程序代理 CORS 情形。 内部服务器托管 CORSWebService Web API 控制器，以及调用 CORSWebService 的 CORSWebClient。 CORSWebClient 向 CORSWebService 发出 AJAX 请求。

![本地同源请求](./media/application-proxy-understand-cors-issues/image1.png)

CORSWebClient 应用程序在本地进行托管时可正常工作，但如果通过 Azure AD 应用程序代理进行发布，则无法加载或者会出错。 如果通过应用程序代理分别将 CORSWebClient 和 CORSWebService 应用程序发布为不同的应用程序，则这两个应用程序会在不同的域中进行托管。 从 CORSWebClient 发送到 CORSWebService 的 AJAX 请求是跨源请求，因此会失败。

![应用程序代理 CORS 请求](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>应用程序代理 CORS 问题的解决方案

你可以采用以下多种方式之一来解决上述 CORS 问题。

### <a name="option-1-set-up-a-custom-domain"></a>选项 1：设置自定义域

使用 Azure AD 应用程序代理[自定义域](./application-proxy-configure-custom-domain.md)进行同源发布，不必对应用程序源、代码或标头进行任何更改。 

### <a name="option-2-publish-the-parent-directory"></a>选项 2：发布父目录

发布这两个应用程序的父目录。 如果 Web 服务器上只有两个应用程序，则此解决方案特别有效。 不是单独发布每个应用程序，而是发布公共父目录，这样可使其源相同。

以下示例显示 CORSWebClient 应用程序的门户 Azure AD 应用程序代理页。  当“内部 URL”设置为“contoso.com/CORSWebClient”时，应用程序无法向“contoso.com/CORSWebService”目录成功发出请求，因它们是跨源应用程序。 

![单独发布应用程序](./media/application-proxy-understand-cors-issues/image4.png)

可以设置“内部 URL”以发布父目录，其中包括 CORSWebClient 和 CORSWebService 目录：

![发布父目录](./media/application-proxy-understand-cors-issues/image5.png)

生成的应用程序 URL 可有效解决 CORS 问题：

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>选项 3：更新 HTTP 标头

添加 Web 服务的自定义 HTTP 响应头，以匹配源请求。 对于在 Internet Information Services (IIS) 中运行的网站，使用 IIS 管理器修改标头：

![在 IIS 管理器中添加自定义响应头](./media/application-proxy-understand-cors-issues/image6.png)

此修改不需要更改任何代码。 可在 Fiddler 跟踪中验证修改：

Post the Header Addition\
HTTP/1.1 200 OK\
Cache-Control: no-cache\
Pragma: no-cache\
Content-Type: text/plain; charset=utf-8\
Expires: -1\
Vary: Accept-Encoding\
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
Access-Control-Allow-Origin: https\://corswebclient-contoso.msappproxy.net\
X-AspNet-Version: 4.0.30319\
X-Powered-By: ASP.NET\
Content-Length: 17

### <a name="option-4-modify-the-app"></a>选项 4：修改应用程序

可以通过使用适当的值添加 Access-Control-Allow-Origin 标头来更改应用程序，以支持 CORS。 添加标头的方式取决于应用程序的代码语言。 更改代码是最不推荐的选项，因为所需工作量最大。

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>选项 5：延长访问令牌的生存期

某些 CORS 问题无法解决，例如应用程序重定向到 login.microsoftonline.com 进行身份验证而访问令牌过期这一情况。 否则，CORS 调用将失败。 这种场景的一种解决方法是延长访问令牌的生存期，防止其在用户会话期间过期。 有关如何执行此操作的详细信息，请参阅 [Azure AD 中的可配置令牌生存期](../develop/active-directory-configurable-token-lifetimes.md)。

## <a name="see-also"></a>另请参阅
- [教程：在 Azure Active Directory 中添加一个本地应用程序以通过应用程序代理进行远程访问](../app-proxy/application-proxy-add-on-premises-application.md) 
- [计划 Azure AD 应用程序代理部署](application-proxy-deployment-plan.md) 
- [通过 Azure Active Directory 应用程序代理远程访问本地应用程序](application-proxy.md)