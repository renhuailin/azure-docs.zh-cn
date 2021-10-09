---
title: Azure Active Directory 报告 API 的先决条件 | Microsoft Docs
description: 了解有关访问 Azure AD 报告 API 的先决条件
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/21/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08ad43beae80d48224d7b0eb960c1e5a0d09f112
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607852"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>访问 Azure Active Directory 报告 API 的先决条件

[Azure Active Directory (Azure AD) 报告 API](./concept-reporting-api.md) 通过一组基于 REST 的 API，可让你以编程方式访问数据。 可从许多编程语言和工具中调用这些 API。

报告 API 使用 [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) 授权访问 Web API。

若要准备访问报告 API，需要执行以下操作：

1. [分配角色](#assign-roles)
2. [许可证要求](#license-requirements)
3. [注册应用程序](#register-an-application)
4. [授予权限](#grant-permissions)
5. [收集配置设置](#gather-configuration-settings)

## <a name="assign-roles"></a>分配角色

若要通过 API 访问报告数据，需分配以下角色之一：

- 安全读取者

- 安全管理员

- 全局管理员角色

## <a name="license-requirements"></a>许可证要求

Azure AD 租户只有在关联了 Azure AD Premium 许可证的情况下才能访问租户的登录报告。 若要访问任何 Azure AD 租户的登录报告，需要 Azure AD Premium P1（或更高版本）许可证。 或者，如果目录类型为 Azure AD B2C，则无需满足任何其他许可证要求即可通过 API 访问登录报告。 


## <a name="register-an-application"></a>注册应用程序

即使是在使用脚本来访问报告 API，也需要注册。 注册会提供一个 **应用程序 ID**，该 ID 是进行授权调用所必需的，代码也通过该 ID 来接收令牌。

若要配置目录以访问 Azure AD 报告 API，必须使用同为 Azure AD 租户中 **全局管理员** 目录角色成员的 Azure 管理员帐户登录到 [Azure 门户](https://portal.azure.com)。

> [!IMPORTANT]
> 在具有管理员权限的凭据下运行的应用程序可能具有非常强大的功能，因此请务必将应用程序的 ID 和机密凭据放置在一个安全的位置。
> 

**若要注册 Azure AD 应用程序，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)中，从左侧导航窗格中选择“Azure Active Directory”。 
   
    ![屏幕截图显示了从 Azure 门户菜单中选择的“Azure Active Directory”。](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在“Azure Active Directory”  页面中，选择“应用注册”  。

    ![屏幕截图显示了从“管理”菜单中选择的“应用注册”。](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. 在“应用注册”页面中，选择“新建注册”   。

    ![屏幕截图显示了已选定的“新建注册”。](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. “注册应用程序”页： 

    ![屏幕截图显示了可在其中输入此步骤的值的“注册应用程序”页。](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. 在“名称”  文本框中，键入 `Reporting API application`。

    b. 对于“支持的帐户类型”设置，请选择“仅限此组织中的帐户”。  

    c. 在“重定向 URL”  中，选择“Web”文本框  ，然后键入 `https://localhost`。

    d. 选择“注册”  。 


## <a name="grant-permissions"></a>授予权限 

若要访问 Azure AD 报告 API，必须授予应用以下两个权限：  

| API | 权限 |
| --- | --- |
| Microsoft Graph | 读取目录数据 |
| Microsoft Graph | 读取所有审核日志数据 |

![屏幕截图显示了可在“API 权限”窗格中选择“添加权限”的位置。](./media/howto-configure-prerequisites-for-reporting-api/api-permissions.png)

以下部分列出了适用于 API 设置的步骤。

**若要授予应用程序使用 API 的权限，请执行以下操作：**


1. 选择“API 权限”，然后选择“添加权限”。

    ![屏幕截图显示了可在其中选择“添加权限”的“API 权限”页。](./media/howto-configure-prerequisites-for-reporting-api/add-api-permission.png)

2. 在“请求 API 权限”页上，找到“Microsoft Graph”。

    ![屏幕截图显示了可在其中选择“Azure Active Directory Graph”的“请求 API 权限”页。](./media/howto-configure-prerequisites-for-reporting-api/select-microsoft-graph-api.png)

3. 在“所需权限”页上，选择“应用程序权限”。 选择“目录”复选框，然后选择“Directory.ReadAll” 。 选择“AuditLog”复选框，然后选择“AuditLog.Read.All” 。 选择“添加权限”  。

    ![屏幕截图显示了可在其中选择“应用程序权限”的“请求 API 权限”页。](./media/howto-configure-prerequisites-for-reporting-api/select-permissions.png)

4. 在“报表 API 应用程序 - API 权限”页上，选择“授予管理员许可”。  

    ![屏幕截图显示了可在其中选择“授予管理员同意”的“报表 API 应用程序 - API 权限”页。](./media/howto-configure-prerequisites-for-reporting-api/grant-admin-consent.png)


## <a name="gather-configuration-settings"></a>收集配置设置 

本部分介绍了如何从目录中获取以下设置：

- 域名
- 客户端 ID
- 客户端机密或证书

在配置对报告 API 的调用时需要这些值。 建议使用证书，因为它更安全。

### <a name="get-your-domain-name"></a>获取域名

**若要获取域名，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)中，在左侧导航窗格中，选择“Azure Active Directory”  。
   
    ![屏幕截图显示了从 Azure 门户菜单中选择的“Azure Active Directory”，用于获取域名。](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在“Azure Active Directory”  页上，选择“自定义域名”  。

    ![屏幕截图显示了从“Azure Active Directory”中选择的“自定义域名”。](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. 从域列表中复制域名。


### <a name="get-your-applications-client-id"></a>获取应用程序的客户端 ID

**若要获取应用程序的客户端 ID，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Azure Active Directory”  。
   
    ![屏幕截图显示了从 Azure 门户菜单中选择的“Azure Active Directory”，用于获取应用程序的客户端 ID。](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 从“应用注册”  页面中选择你的应用程序。

3. 从应用程序页面中，导航到“应用程序 ID”  并选择“单击以复制”。 

    ![屏幕截图显示了可以在其中复制应用程序 ID 的“报表 API 应用程序”页。](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>获取应用程序的客户端机密

**若要获取应用程序的客户端机密，请执行以下操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航窗格中，单击“Azure Active Directory”  。
   
    ![屏幕截图显示了从 Azure 门户菜单中选择的“Azure Active Directory”，用于获取应用程序的客户端机密。](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  从“应用注册”  页面中选择你的应用程序。

3.  在“API 应用程序”页上选择“证书和机密”，然后在“客户端机密”部分单击“+ 新建客户端机密”。     

    ![屏幕截图显示了可以在其中添加客户端密码的“证书和机密”页。](./media/howto-configure-prerequisites-for-reporting-api/12.png)

4. 在“添加客户端机密”页上，添加以下内容： 

    a. 在“说明”  文本框中，键入 `Reporting API`。

    b. 选择“2 年内”作为“过期时间”。  

    c. 单击“保存”  。

    d. 复制密钥值。

### <a name="upload-the-certificate-of-your-application"></a>上传应用程序的证书

若要上传证书，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，在左侧导航窗格中，选择“Azure Active Directory”  。
   
    ![屏幕截图显示了从 Azure 门户菜单中选择的“Azure Active Directory”，用于上传证书。](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. 在“Azure Active Directory”页上，选择“应用注册”。
3. 从应用程序页选择应用程序。
4. 选择“证书和机密”。
5. 选择“上传证书”。 
6. 选择文件图标，转到证书，然后选择“添加”。

    ![屏幕截图，显示如何上传证书。](./media/howto-configure-prerequisites-for-reporting-api/upload-certificate.png)

## <a name="troubleshoot-errors-in-the-reporting-api"></a>排查报告 API 中的错误

本部分列出了使用 Microsoft Graph API 访问活动报告时可能遇到的常见错误消息及其解决步骤。

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>错误：无法从 Microsoft Graph 获取用户角色

 使用 Graph 浏览器 UI 中的两个登录按钮登录到你的帐户，以避免在尝试使用 Graph 浏览器登录时出现错误。 

![Graph 浏览器](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>错误：无法从 Microsoft Graph 执行 Premium 许可证检查 

如果尝试使用 Graph 浏览器访问登录时收到此错误消息，请在左侧导航栏中选择帐户下方的“修改权限”，然后选择“Tasks.ReadWrite”和“Directory.Read.All”。 

![修改权限 UI](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>错误：租户不是 B2C，或者租户没有 Premium 许可证

访问登录报告需要 Azure Active Directory Premium 1 (P1) 许可证。 如果访问登录时看到此错误消息，请确保你的租户已获得 Azure AD P1 许可证。

### <a name="error-the-allowed-roles-does-not-include-user"></a>错误：允许的角色中不包括“用户”。 

 避免尝试访问审核日志或使用 API 登录时出现的错误。 确保帐户属于 Azure Active Directory 租户中的“安全读取者”或“报表读取者”角色   。

### <a name="error-application-missing-aad-read-directory-data-permission"></a>错误：应用程序缺少 AAD“读取目录数据”权限 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>错误：应用程序缺少 Microsoft Graph API“读取所有审核日志数据”权限

按照[访问 Azure Active Directory 报告 API 的先决条件](howto-configure-prerequisites-for-reporting-api.md)中的步骤操作，确保应用程序使用正确的权限集运行。 

## <a name="next-steps"></a>后续步骤

* [使用具有证书的 Azure Active Directory 报告 API 获取数据](tutorial-access-api-with-certificates.md)
* [审核 API 参考](/graph/api/resources/directoryaudit) 
* [登录活动报告 API 参考](/graph/api/resources/signin)
