---
title: Azure Active Directory 应用程序代理和 Qlik Sense
description: Azure Active Directory 应用程序代理与 Qlik Sense 集成。
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
ms.openlocfilehash: 7d0218c06bfe7644fe376e812d14c67bfdf115bc
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229917"
---
# <a name="azure-active-directory-application-proxy-and-qlik-sense"></a>Azure Active Directory 应用程序代理和 Qlik Sense 
Azure Active Directory 应用程序代理和 Qlik Sense 已进行合作，确保可轻松使用应用程序代理来提供对 Qlik Sense 部署的远程访问。  

## <a name="prerequisites"></a>先决条件 
此方案的剩余部分假定你已完成以下操作：
 
- 配置 [Qlik Sense](https://community.qlik.com/docs/DOC-19822)。 
- [安装应用程序代理连接器](../app-proxy/application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>在 Azure 中发布应用程序 
若要发布 QlikSense，需要在 Azure 中发布两个应用程序。  

### <a name="application-1"></a>应用程序 1： 
按以下步骤发布应用。 有关步骤 1-8 更详细的演练，请参阅[使用 Azure AD 应用程序代理发布应用程序](../app-proxy/application-proxy-add-on-premises-application.md)。 


1. 以全局管理员身份登录到 Azure 门户。 
2. 选择“Azure Active Directory” > “企业应用程序”。  
3. 单击边栏选项卡顶部的“添加”。 
4. 选择“本地应用程序”。 
5. 在必填的字段中填写有关新应用的信息。 参考以下指导完成设置： 
   - **外部 URL**：此应用程序具有的内部 URL 本身应为 QlikSense URL。 例如，**https&#58;//demo.qlikemm.com:4244** 
   - **预身份验证方法**：Azure Active Directory（推荐使用但并非必需项） 
1. 选择边栏选项卡底部的“添加”。 添加应用程序后，将打开快速启动菜单。 
2. 在快速启动菜单中选择“分配用于测试的用户”，并将至少一个用户添加到应用程序。 确保此测试帐户有权访问本地应用程序。 
3. 选择“分配”，保存测试用户分配。 
4. （可选）在应用管理边栏选项卡中选择“单一登录”。 从下拉菜单中选择“Kerberos 约束委派”，然后根据 Qlik 配置填写必填字段。 选择“保存”。 

### <a name="application-2"></a>应用程序 2： 
按照应用程序 1 的相同步骤操作，但存在以下例外： 

**步骤 5**：外部 URL 现应为包含应用程序所用的身份验证端口的 QlikSense URL。 对于 2018 年 4 月之前的 QlikSense 版本，默认值为 4244（对于 HTTPS）和 4248（对于 HTTP） 。 对于 2018 年 4 月之后的 QlikSense 版本，默认值为 443（对于 HTTPS）和 80（对于 HTTP） 。  例如：**https&#58;//demo.qlik.com:4244**</br></br>
步骤 10：不设置 SSO，并禁用“单一登录”
 
 
## <a name="testing"></a>测试 
现在已准备好测试应用程序。 访问应用程序 1 中用来发布 QlikSense 的外部 URL，并以分配到两个应用程序的用户身份登录。  

## <a name="additional-references"></a>其他参考
有关使用应用程序代理发布 Qlik Sense 的更多信息，请参阅以下 Qlik 社区文章： 
- [通过 Kerberos 约束委派和 Qlik Sense，将 Azure AD 与集成 Windows 身份验证配合使用](https://community.qlik.com/docs/DOC-20183)
- [Qlik Sense 与 Azure AD 应用程序代理集成](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>后续步骤

- [使用应用程序代理发布应用程序](../app-proxy/application-proxy-add-on-premises-application.md)
- [使用应用程序代理连接器](../app-proxy/application-proxy-connector-groups.md)
