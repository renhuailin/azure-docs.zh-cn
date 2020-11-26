---
title: 用尼维斯配置 Azure Active Directory B2C 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C authentication 与尼维斯集成以进行无密码 authentication
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 470dc146d1658b97a707be8cb7ad868d943a8e60
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170884"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>用于配置无密码 authentication Azure Active Directory B2C 的尼维斯的教程

在此示例教程中，了解如何使用  [尼维斯](https://www.nevis.net/solution/authentication-cloud) 扩展 Azure AD B2C 来启用无密码 authentication。 尼维斯提供与客户一起使用的、完全署名的最终用户体验和尼维斯访问应用，可提供强大的客户身份验证并符合支付服务指令 2 (PSD2) 事务要求。

## <a name="prerequisites"></a>先决条件

若要开始，你将需要：

- 尼维斯 [试用帐户](https://www.nevis-security.com/aadb2c/)

- 一个 Azure AD 订阅。 如果没有，请获取一个 [免费帐户](https://azure.microsoft.com/free/)。

- 链接到 Azure 订阅的 [Azure AD B2C 租户](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) 。

- 配置了使用 [自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)Azure AD B2C 环境，如果你希望将尼维斯集成到注册策略流中。

## <a name="scenario-description"></a>方案描述

在此方案中，将完全署名的访问应用添加到后端应用程序进行无密码 authentication。 以下组件组成解决方案：

- 一个 Azure AD B2C 租户，将登录和注册策略组合到后端
- 尼维斯实例及其 REST API 提高 Azure AD B2C
- 你自己的品牌访问应用

此图显示了实现。

![具有 Azure AD B2C 和尼维斯的高级密码登录流](./media/partner-nevis/nevis-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户尝试通过 Azure AD B2C 登录和注册策略登录或注册到应用程序。
| 2. | 注册过程中，会使用 QR 码将尼维斯访问应用注册到用户设备。 在用户设备上生成私钥，并使用该密钥对用户请求进行签名。
| 3. |  Azure AD B2C 使用 RESTful 技术配置文件来启动使用尼维斯解决方案的登录。
| 4. | 登录请求会作为推送消息、QR 码或深层链接发送到访问应用。
| 5. | 用户通过其生物识别批准登录尝试。 然后，会向尼维斯返回一条消息，其中使用存储的公钥来验证登录名。
| 6. | Azure AD B2C 将最后一个请求发送到尼维斯，以确认登录名已成功完成。
| 7. |根据来自 Azure AD B2C 的成功/失败消息，将授予/拒绝对应用程序的访问权限。

## <a name="integrate-your-azure-ad-b2c-tenant"></a>集成 Azure AD B2C 租户

### <a name="onboard-to-nevis"></a>加入尼维斯 

[注册尼维斯帐户](https://www.nevis-security.com/aadb2c/)。
你会收到两封电子邮件：

1. 管理帐户通知

2. 移动应用邀请。

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>将 Azure AD B2C 租户添加到尼维斯帐户

1. 从 "尼维斯管理帐户试用" 电子邮件中，将管理密钥复制到剪贴板。

2. https://console.nevis.cloud/在浏览器中打开。

3. 用你的密钥登录到管理控制台。

4. 选择 **添加实例**

5. 选择新创建的实例以将其打开。

6. 在侧面导航栏中，选择 "**自定义集成**"

7. 选择 " **添加自定义集成**"。

8. 对于 "集成名称"，请输入 Azure AD B2C 租户名称。

9. 对于 URL/域，输入 `https://yourtenant.onmicrosoft.com`

10. 选择“下一步”

>[!NOTE]
>稍后需要使用尼维斯访问令牌。

11. 选择“完成”  。

### <a name="install-the-nevis-access-app-on-your-phone"></a>在手机上安装尼维斯访问应用

1. 在尼维斯移动应用试用电子邮件中，打开 **测试航班应用** 邀请。

2. 安装应用程序。

3. 按照提供的说明安装尼维斯访问应用。

### <a name="integrate-azure-ad-b2c-with-nevis"></a>将 Azure AD B2C 与尼维斯集成

1. 打开 [Azure 门户](https://portal.azure.com/)。

2. 切换到 Azure AD B2C 租户。 请确保已选择正确的租户，因为 Azure AD B2C 租户通常在单独的租户中。

3. 在菜单中，选择 " **标识体验框架" (IEF ")**

4. 选择“策略密钥”

5. 选择 " **添加** "，并使用以下设置创建新密钥：

      a. 选择 **手动** 选项

      b. 将名称设置为 **AuthCloudAccessToken**

      c. 将以前存储的 **尼维斯访问令牌** 粘贴到 "密钥" 字段中

      d. 对于密钥用法，选择 "**加密**"

      e. 选择“创建”

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>配置 nevis.html 并将其上传到 Azure blob 存储

1. 在标识环境中 (IDE) ，请参阅 [**策略**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) 文件夹。

2. 打开  [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) 文件。

3. 将  **authentication_cloud_url** 替换为尼维斯管理控制台的 url- `https://<instance_id>.mauth.nevis.cloud` 。

4. **保存** 对文件所做的更改。

5. 按照 [说明](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#2-create-an-azure-blob-storage-account) 将 **nevis.html** 文件上传到 Azure blob 存储。

6. 按照 [说明进行操作](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#3-configure-cors) ，并为此文件启用跨域资源共享 (CORS) 。

7. 上传完成并且 CORS 启用后，选择列表中的 **nevis.html** 文件。

8. 在 " **概述** " 选项卡上的 **URL** 旁边，选择 " **复制链接** " 图标。

9. 在新的浏览器选项卡中打开链接，确保它显示灰色框。

>[!NOTE]
>稍后将需要 blob 链接。

### <a name="customize-your-trustframeworkbasexml"></a>自定义 TrustFrameworkBase.xml

1. 在 IDE 中，切换到 " [**策略**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) " 文件夹。

2. 打开 [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) 文件。

3. 将 **yourtenant** 替换为 **TenantId** 中的 Azure 租户帐户名称。

4. 将 **yourtenant** 替换为 **PublicPolicyURI** 中的 Azure 租户帐户名称。

5. 将所有 **authentication_cloud_url** 实例替换为尼维斯管理控制台的 url

6. **保存** 对文件所做的更改。

### <a name="customize-your-trustframeworkextensionsxml"></a>自定义 TrustFrameworkExtensions.xml

1. 在 IDE 中，切换到 " [**策略**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) " 文件夹。

2. 打开 [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) 文件。

3. 将 **yourtenant** 替换为 **TenantId** 中的 Azure 租户帐户名称。

4. 将 **yourtenant** 替换为 **PublicPolicyURI** 中的 Azure 租户帐户名称。

5. 在 **TenantId** 下的 **BasePolicy** 中，还将 _Yourtenant_ 替换为你的 Azure 租户帐户名称。

6. 在 **BuildingBlocks** 下，将 **loaduri 来** 替换为 blob 存储帐户中 _nevis.html_ 的 blob 链接 URL。

7. 保存文件。

### <a name="customize-your-signuporsigninxml"></a>自定义 SignUpOrSignin.xml

1. 在 IDE 中，切换到 " [**策略**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) " 文件夹。

2. 打开 [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) 文件。

3. 将 **yourtenant** 替换为 **TenantId** 中的 Azure 租户帐户名称。

4. 将 **yourtenant** 替换为 **PublicPolicyUri** 中的 Azure 租户帐户名称。

5. 在 **BasePolicy** 下的 **TenantId** 中，还将 **Yourtenant** 替换为你的 Azure 租户帐户名称。

6. 保存文件。

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>将自定义策略上传到 Azure AD B2C

1. 打开 [Azure AD B2C 租户](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) 主页。

2. 选择“标识体验框架”。

3. 选择“上传自定义策略”。

4. 选择你修改的 **TrustFrameworkBase.xml** 文件。

5. 选择 " **如果已存在，则覆盖自定义策略"** 复选框。
6. 选择“上传”。

7. 为 **TrustFrameworkExtensions.xml** 重复步骤5和6。

8. 为 **SignUpOrSignin.xml** 重复步骤5和6。

## <a name="test-the-user-flow"></a>测试用户流

### <a name="test-account-creation-and-nevis-access-app-setup"></a>测试帐户创建和尼维斯访问应用安装

1. 打开 [Azure AD B2C 租户](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) 主页。

2. 选择“标识体验框架”。

3. 向下滚动到 "自定义策略"，然后选择 **B2C_1A_signup_signin**。

4. 选择“立即运行”。

5. 在弹出窗口中，选择 " **立即注册**"。

6. 添加电子邮件地址。

7. 选择 " **发送验证码**"。

8. 从电子邮件复制验证代码。

9. 选择“验证”  。

10. 在表单中填写新密码和显示名称。

11. 选择“创建”  。

12. 你将转到 QR 代码扫描页面。

13. 在手机上，打开 " **尼维斯访问应用**"。

14. 选择 **面部 ID**。

15. 当屏幕显示 " **验证器注册成功**" 时，选择 " **继续**"。

16. 在您的手机上，再次向您的人脸进行身份验证。

17. 将转到 [jwt.ms](https://jwt.ms) 登陆页面，其中显示了解码令牌的详细信息。

### <a name="test-the-pure-passwordless-sign-in"></a>测试纯无密码登录

1. 在 " **标识体验框架**" 下，选择 **B2C_1A_signup_signin**。

2. 选择“立即运行”。

3. 在弹出窗口中，选择 " **无密码 Authentication**"。

4. 输入电子邮件地址。

5. 选择“继续”。

6. 在你的手机上，在 "通知" 中，选择 " **尼维斯访问应用通知**"。

7. 用人脸进行身份验证。

8. 将自动转到显示令牌的 [jwt.ms](https://jwt.ms) 登陆页面。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章

- [Azure AD B2C 中的自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Azure AD B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
