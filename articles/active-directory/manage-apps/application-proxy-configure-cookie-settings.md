---
title: 应用程序代理 cookie 设置 - Azure Active Directory | Microsoft 文档
description: Azure Active Directory (Azure AD) 具有访问和会话 cookie，可通过应用程序代理访问本地应用程序。 本文会介绍如何使用和配置 cookie 设置。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62afe97b44f45bc0b7aa12b33b6a65dd94ecf095
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108224455"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>用于在 Azure Active Directory 中访问本地应用程序的 Cookie 设置

Azure Active Directory (Azure AD) 具有访问和会话 cookie，可通过应用程序代理访问本地应用程序。 了解如何使用应用程序代理 cookie 设置。 

## <a name="what-are-the-cookie-settings"></a>Cookie 设置有哪些？

[应用程序代理](application-proxy.md)使用以下访问和会话 cookie 设置。

| Cookie 设置 | 默认 | 描述 | 建议 |
| -------------- | ------- | ----------- | --------------- |
| 使用仅限 HTTP 的 Cookie | **否** | **是** 允许应用程序代理在 HTTP 响应标头中包含 HTTPOnly 标志。 此标志提升安全性，例如，它会阻止客户端脚本 (CSS) 复制或修改 cookie。<br></br><br></br>在我们支持“仅限 HTTP”设置之前，应用程序代理为 Cookie 加密并通过安全 TLS 通道传输 Cookie，以防其被修改。 | 选择“是”，可提升安全性。<br></br><br></br>为那些需要访问会话 cookie 的客户端或用户代理使用“否”。 例如，为通过应用程序代理连接远程桌面网关服务器的 RDP 或 MTSC 客户端使用“否”。|
| 使用安全 Cookie | **否** | **是** 允许应用程序代理在 HTTP 响应标头中包含 Secure 标志。 Secure Cookie 通过经由 TLS 保护的通道（如 HTTPS）来传输 cookie，可增强安全性。 由于以明文形式传输 cookie，这样可防止 cookie 被未经授权的参与方观察到。 | 选择“是”，可提升安全性。|
| 使用永久性 Cookie | **否** | **是** 允许应用程序代理设置其访问 cookie，使其在 Web 浏览器关闭时不会过期。 其有效性一直持续到访问令牌过期为止，或持续到用户手动删除持久性 cookie 为止。 | 使用“否”是由于使用户一直处于通过验证的状态会产生安全风险。<br></br><br></br>建议仅为不能在进程间共享 cookie 的较旧应用程序使用“是”。 更好的做法是更新应用程序，在进程之间共享 cookie，而不是使用持久性 cookie。 例如，可能需要持久性 cookie 来允许用户从 SharePoint 站点的资源管理器视图中打开 Office 文档。 在没有持久性 cookie 的情况下，如果不在浏览器、资源管理器进程和 Office 进程之间共享访问 cookie，则此操作可能失败。 |

## <a name="samesite-cookies"></a>SameSite Cookie
从版本 Chrome 80 开始到最终使用 Chromium 的浏览器，未指定 [SameSite](https://web.dev/samesite-cookies-explained) 属性的 Cookie 将视为已设置为 SameSite=Lax。 SameSite 属性声明了应如何将 Cookie 限制为同一站点上下文。 设置为 Lax 时，Cookie 仅发送到同一站点请求或顶级导航。 但应用程序代理要求将这些 Cookie 保存在第三方上下文中，使用户能够在其会话期间正确登录。 因此，我们将更新应用程序代理访问和会话 Cookie，避免此更改带来的不利影响。 更新包括：

* 将 SameSite 属性设置为“无” 。 这允许在第三方上下文中正确发送应用程序代理访问和会话 Cookie。
* 将“使用安全 Cookie”设置的默认值设为“是” 。 Chrome 还要求 Cookie 指定安全标志，否则将被拒绝。 此更改将应用于通过应用程序代理发布的所有现有应用程序。 请注意，应用程序代理访问 Cookie 始终设置为安全，并且仅通过 HTTPS 传输。 此更改仅应用于会话 Cookie。

在发布 Chrome 80 之前的几周，这些对应用程序代理 Cookie 的更改将陆续推出。

此外，如果后端应用程序具有要在第三方上下文中使用的 Cookie，则必须通过将应用程序更改为对这些 Cookie 使用 SameSite=None 来显式选择加入。 应用程序代理会将 Set-Cookie 标头转换为其 URL，并遵循后端应用程序对于这些 Cookie 的设置。



## <a name="set-the-cookie-settings---azure-portal"></a>设置 cookie 设置 - Azure 门户
使用 Azure 门户设置 cookie 设置：

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 导航到“Azure Active Directory” “企业应用程序”> ****  >“所有应用程序” **** 。
3. 选择要为其启用 cookie 设置的应用程序。
4. 单击“应用程序代理”。
5. 在“其他设置”下，将 cookie 设置设置为“是”或“否”。
6. 单击“保存”应用所做的更改。 

## <a name="view-current-cookie-settings---powershell"></a>查看当前的 Cookie 设置 - PowerShell

要查看应用程序当前的 Cookie 设置，请使用此 PowerShell 命令：  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>设置 Cookie 设置 - PowerShell

在以下 PowerShell 命令中，```<ObjectId>``` 是应用程序的 ObjectId。 

**仅限 HTTP 的 Cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**安全 Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**持久性 Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
