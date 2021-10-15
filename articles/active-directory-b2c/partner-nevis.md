---
title: 为 Azure Active Directory B2C 配置 Nevis 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 身份验证与 Nevis 集成以进行无密码身份验证
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 0bbbe80a9f4c743fb658aa313641ab790e194509
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234331"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>为 Azure Active Directory B2C 配置 Nevis 以进行无密码身份验证的教程

在此示例教程中，了解如何使用 [Nevis](https://www.nevis.net/en/solution/authentication-cloud) 扩展 Azure AD B2C 以启用无密码身份验证。 Nevis 通过 Nevis Access 应用提供移动优先的完全品牌化的最终用户体验，以提供强大的客户身份验证并符合支付服务指令 2 (PSD2) 事务要求。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Nevis [试用帐户](https://www.nevis-security.com/aadb2c/)

- 一个 Azure AD 订阅。 如果没有，可获取[免费帐户](https://azure.microsoft.com/free/)。

- 已链接到 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。

- 已配置支持使用[自定义策略](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)的 Azure AD B2C 环境（如果要将 Nevis 集成到登录策略流中）。

## <a name="scenario-description"></a>方案描述

在此方案中，将完全品牌化的访问应用添加到后端应用程序以进行无密码身份验证。 以下组件构成了解决方案：

- 一个 Azure AD B2C 租户，其中登录和注册策略已合并到后端
- 用于增强 Azure AD B2C 的 Nevis 实例及其 REST API
- 你自己的品牌化访问应用

此图显示了实现流程。

![使用 Azure AD B2C 和 Nevis 的密码登录大致流程](./media/partner-nevis/nevis-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 一位用户尝试通过 Azure AD B2C 登录和注册策略登录或注册应用程序。
| 2. | 在注册过程中，会使用 QR 码将 Nevis Access 应用注册到用户设备。 随后会在用户设备上生成私钥，并将该私钥用于对用户请求进行签名。
| 3. |  Azure AD B2C 使用 RESTful 技术配置文件来启动使用 Nevis 解决方案的登录。
| 4. | 登录请求会以推送消息、QR 码或深层链接的形式发送到访问应用。
| 5. | 该用户同意通过其生物特征尝试登录。 然后，会向 Nevis 返回一条消息，该消息使用存储的公钥来验证登录。
| 6. | Azure AD B2C 向 Nevis 发送最后一个请求，以确认已成功完成登录。
| 7. |根据来自 Azure AD B2C 的成功/失败消息，将允许/拒绝该用户访问应用程序。

## <a name="integrate-your-azure-ad-b2c-tenant"></a>集成 Azure AD B2C 租户

### <a name="onboard-to-nevis"></a>加入 Nevis 

[注册 Nevis 帐户](https://www.nevis-security.com/aadb2c/)。
你会收到两封电子邮件：

1. 管理帐户通知

2. 移动应用邀请。

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>将 Azure AD B2C 租户添加到 Nevis 帐户

1. 从 Nevis 管理帐户试用电子邮件中，将管理密钥复制到剪贴板。

2. 在浏览器中打开 https://console.nevis.cloud/ 。

3. 使用密钥登录到管理控制台。

4. 选择“添加实例”

5. 选择新创建的实例以将其打开。

6. 在侧面导航栏中，选择“自定义集成”

7. 选择“添加自定义集成”。

8. 对于集成名称，输入你的 Azure AD B2C 租户名称。

9. 对于 URL/域，输入 `https://yourtenant.onmicrosoft.com`

10. 选择“下一步”

>[!NOTE]
>稍后需要使用 Nevis 访问令牌。

11. 选择“完成”。

### <a name="install-the-nevis-access-app-on-your-phone"></a>在手机上安装 Nevis Access 应用

1. 在 Nevis 移动应用试用电子邮件中，打开“测试外部测试版应用”邀请。

2. 安装应用。

3. 按照提供的说明安装 Nevis Access 应用。

### <a name="integrate-azure-ad-b2c-with-nevis"></a>将 Azure AD B2C 与 Nevis 集成

1. 打开 [Azure 门户](https://portal.azure.com/)。

2. 切换到 Azure AD B2C 租户。 请确保已选择正确的租户，因为 Azure AD B2C 租户通常是单独的租户。

3. 在菜单中，选择“Identity Experience Framework (IEF)”

4. 选择“策略密钥”

5. 选择“添加”，并使用以下设置创建新密钥：

      a. 在“选项”中选择“手动”

      b. 将“名称”设置为 AuthCloudAccessToken

      c. 将之前存储的“Nevis Access 令牌”粘贴在“机密”字段中

      d. 对于“密钥用法”，请选择“加密”

      e. 选择“创建”

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>配置 nevis.html 并将其上传到 Azure Blob 存储

1. 在标识环境中 (IDE)，转到[策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)文件夹。

2. 打开 [nevis.html](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) 文件。

3. 将“authentication_cloud_url”替换为 Nevis 管理控制台的 URL - `https://<instance_id>.mauth.nevis.cloud`。

4. 保存对文件所做的更改。

5. 按照[说明](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account)将 nevis.html 文件上传到 Azure Blob 存储。

6. 按照[说明](./customize-ui-with-html.md#3-configure-cors)为该文件启用跨域资源共享 (CORS)。

7. 上传完成并启用 CORS 后，选择列表中的 nevis.html 文件。

8. 在“概述”选项卡的“URL”旁边，选择“复制链接”图标  。

9. 在新的浏览器选项卡中打开该链接，确保它显示灰色框。

>[!NOTE]
>稍后将使用此 blob 链接。

### <a name="customize-your-trustframeworkbasexml"></a>自定义 TrustFrameworkBase.xml

1. 在 IDE 中，转到[策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)文件夹。

2. 打开 [TrustFrameworkBase.xml](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) 文件。

3. 将“yourtenant”替换为“TenantId”中的 Azure 租户帐户名 。

4. 将“yourtenant”替换为“PublicPolicyURI”中的 Azure 租户帐户名 。

5. 将所有“authentication_cloud_url”实例均替换为 Nevis 管理控制台的 URL

6. 保存对文件所做的更改。

### <a name="customize-your-trustframeworkextensionsxml"></a>自定义 TrustFrameworkExtensions.xml

1. 在 IDE 中，转到[策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)文件夹。

2. 打开 [TrustFrameworkExtensions.xml](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) 文件。

3. 将“yourtenant”替换为“TenantId”中的 Azure 租户帐户名 。

4. 将“yourtenant”替换为“PublicPolicyURI”中的 Azure 租户帐户名 。

5. 在“BasePolicy”下的“TenantId”中，同样将“yourtenant”替换为 Azure 租户帐户名 。

6. 在“BuildingBlocks”下，将“LoadUri”替换为 blob 存储帐户中“nevis.html”的 blob 链接 URL 。

7. 保存文件。

### <a name="customize-your-signuporsigninxml"></a>自定义 SignUpOrSignin.xml

1. 在 IDE 中，转到[策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy)文件夹。

2. 打开 [SignUpOrSignin.xml](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) 文件。

3. 将“yourtenant”替换为“TenantId”中的 Azure 租户帐户名 。

4. 将“yourtenant”替换为“PublicPolicyUri”中的 Azure 租户帐户名 。

5. 在“BasePolicy”下的“TenantId”中，同样将“yourtenant”替换为 Azure 租户帐户名  。

6. 保存文件。

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>将自定义策略上传到 Azure AD B2C

1. 打开 [Azure AD B2C 租户](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)主页。

2. 选择“标识体验框架”。

3. 选择“上传自定义策略”。

4. 选择已修改的“TrustFrameworkBase.xml”文件。

5. 选中“覆盖自定义策略(若已有)”复选框。
6. 选择“上传”。

7. 对 TrustFrameworkExtensions.xml 重复步骤 5 和 6。

8. 对 SignUpOrSignin.xml 重复步骤 5 和 6。

## <a name="test-the-user-flow"></a>测试用户流

### <a name="test-account-creation-and-nevis-access-app-setup"></a>测试帐户创建和 Nevis Access 应用设置

1. 打开 [Azure AD B2C 租户](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)主页。

2. 选择“标识体验框架”。

3. 向下滚动到“自定义策略”，然后选择“B2C_1A_signup_signin”。

4. 选择“立即运行”。

5. 在弹出窗口中，选择“立即注册”。

6. 添加电子邮件地址。

7. 选择“发送验证码”。

8. 复制电子邮件中的验证码。

9. 选择“验证”  。

10. 在表单中填入新密码和显示名称。

11. 选择“创建”  。

12. 你将转到 QR 码扫描页。

13. 在手机上，打开“Nevis Access 应用”。

14. 选择“Face ID”。

15. 当屏幕显示“Authenticator 注册已成功”时，选择“继续” 。

16. 在手机上，再次通过你的人脸进行身份验证。

17. 你将转到 [jwt.ms](https://jwt.ms) 登陆页面，其中显示已解码的令牌详细信息。

### <a name="test-the-pure-passwordless-sign-in"></a>测试单一的无密码登录

1. 在“Identity Experience Framework”下，选择“B2C_1A_signup_signin” 。

2. 选择“立即运行”。

3. 在弹出窗口中，选择“无密码身份验证”。

4. 输入电子邮件地址。

5. 选择“继续”。

6. 在手机上的“通知”中，选择“Nevis Access 应用通知”。

7. 通过人脸进行身份验证。

8. 你将自动转到显示令牌的 [jwt.ms](https://jwt.ms) 登陆页面。

## <a name="next-steps"></a>后续步骤

有关更多信息，请查看以下文章

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
