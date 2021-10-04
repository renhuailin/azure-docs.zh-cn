---
title: 在 Azure Active Directory B2C 中配置 Saviynt 的教程
titleSuffix: Azure AD B2C
description: 本教程介绍如何为 Azure Active Directory B2C 配置 Saviynt 以实现跨应用程序集成，从而简化 IT 新式化，提高安全性、合规性并推进治理。 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2685570bb14cf177f0a658196aa1750f9d8e02a1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128558341"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置 Saviynt 的教程

在本示例教程中，我们提供了有关如何将 Azure Active Directory (AD) B2C 与 [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/)集成的指导。 Saviynt 的安全管理器平台在一个统一的平台上提供目前企业所需的可见性、安全性和治理。 Saviynt 包含应用程序风险和治理、基础结构管理、特权帐户管理和客户风险分析。

在本示例教程中，你将设置 Saviynt，以便为 Azure AD B2C 用户提供基于精细访问控制的委派管理。 Saviynt 执行以下检查来确定用户是否有权管理 Azure AD B2C 用户。

- 功能级别的安全性：用于确定用户是否可以执行特定操作。 例如，创建用户、更新用户、重置用户密码等。

- 字段级别安全性：用于确定用户是否可以在执行用户管理操作期间读取/写入另一个用户的特定属性。 例如，技术支持代理只能更新电话号码，所有其他属性均为只读。

- 数据级别安全性：用于确定用户是否可以对特定用户执行特定操作。 例如，UK 地区的技术支持管理员只能管理英国用户。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](./tutorial-create-tenant.md)。 租户已链接到 Azure 订阅。

- Saviynt [订阅](https://saviynt.com/contact-us/)

## <a name="scenario-description"></a>方案描述

Saviynt 集成包括以下组件：

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) – 作为服务的企业对客户标识，可对客户注册、登录和管理其配置文件的方式进行自定义控制。

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) – 标识治理平台，为用户生命周期管理和 Azure AD B2C 用户访问管理提供细粒度的委派管理。  

- [Microsoft Graph API](/graph/use-the-api) – 此 API 为 Saviynt 提供接口，用于管理 Azure AD B2C 用户及其在 Azure AD B2C 中的访问权限。

以下体系结构图体现了实施详情。

![图像显示 saviynt 体系结构关系图](./media/partner-saviynt/saviynt-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 委派管理员通过 Saviynt 启动管理 Azure AD B2C 用户操作。
| 2. | Saviynt 使用其授权引擎验证委派管理员是否可以执行特定操作。
| 3. | Saviynt 的授权引擎发送授权成功/失败响应。
| 4. | Saviynt 允许委派管理员执行所需的操作。
| 5. | Saviynt 调用 Microsoft Graph API 和用户属性来管理 Azure AD B2C 中的用户
| 6. | Microsoft Graph API 将依次在 Azure AD B2C 中创建/更新/删除用户。
| 7. | Azure AD B2C 将发送成功/失败响应。
| 8. | 然后，Microsoft Graph API 将响应返回给 Saviynt。

## <a name="onboard-with-saviynt"></a>加入 Saviynt

1. 若要创建 Saviynt 帐户，请联系 [Saviynt](https://saviynt.com/contact-us/)

2. 创建委派管理策略，并将用户分配为具有不同角色的委托管理员。

## <a name="configure-azure-ad-b2c-with-saviynt"></a>为 Azure AD B2C 配置 Saviynt

### <a name="create-an-azure-ad-application-for-saviynt"></a>创建适用于 Saviynt 的 Azure AD 应用程序

1. 登录 [Azure 门户](https://portal.azure.com/#home)。
1. 请确保使用的是包含 Azure AD B2C 租户的目录。 在门户工具栏中选择“目录 + 订阅”图标。
1. 在“门户设置 | 目录+订阅”页上的“目录名称”列表中找到你的 Azure AD B2C 目录，然后选择“切换”。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 选择“应用注册” > “新建注册”。
1. 输入应用程序的“名称”。 例如 Saviynt，然后选择“创建”。
1. 转到“API 权限”并选择“+ 添加权限”。
1. 会显示“请求获取 API 权限”页。 选择“Microsoft API”选项卡，并选择“Microsoft Graph”作为常用 Microsoft API。
1. 前往下一页，选择“应用程序权限”。
1. 选择“目录”，然后选中“Directory.Read.All”和“Directory.ReadWrite.All”复选框。
1. 选择“添加权限”。 检查添加的权限。
1. 选择“为默认目录授予管理员许可” > “保存”。
1. 转到“证书和密码”并选择“+ 添加客户端密码”。 输入客户端密码说明，选择有效期选项，然后选择“添加”。
1. 将生成密钥，并显示在“客户端密钥”部分。 稍后需要使用它。

1. 转到“概述”，获取”客户端 ID”和“租户 ID”。
1. 需要租户 ID、客户端 ID 和客户端密码才能完成 Saviynt 中的设置。

### <a name="enable-saviynt-to-delete-users"></a>启用 Saviynt 以删除用户

以下步骤说明如何启用 Saviynt 以在 Azure AD B2C 中执行用户删除操作。

>[!NOTE]
>[在授予管理角色访问服务主体之前评估风险。](../active-directory/develop/app-objects-and-service-principals.md)

1. 在 Windows 工作站/服务器上安装最新版本的 MSOnline PowerShell 模块。

2. 连接到 AzureAD PowerShell 模块并执行以下命令：

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>测试解决方案

浏览到 Saviynt 应用程序租户，并测试用户生命周期管理和访问治理用例。

## <a name="next-steps"></a>后续步骤

有关更多信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

- [创建 Web API 应用程序](./add-web-api-application.md)