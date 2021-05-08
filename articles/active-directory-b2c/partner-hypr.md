---
title: 为 Azure Active Directory B2C 配置 HYPR 的教程
titleSuffix: Azure AD B2C
description: 为 Azure Active Directory B2C 配置 HYPR 以实现真正无密码强客户身份验证的教程
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953757"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>为 Azure Active Directory B2C 配置 HYPR 的教程

在本示例教程中，我们提供了有关如何将为 Azure AD B2C 配置 [HYPR](https://get.hypr.com)。 使用 Azure AD B2C 作为标识提供者，你可以将 HYPR 与任何客户应用程序集成，为用户提供真正的无密码身份验证。 HYPR 将密码替换为公钥加密，消除了欺诈、仿冒和凭据重用。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](./tutorial-create-tenant.md)。 租户已链接到 Azure 订阅。

- HYPR 云租户，获取免费[试用帐户](https://get.hypr.com/free-trial)。

- 使用 HYPR Rest API 或 HYPR 租户中的 HYPR 设备管理器注册的用户的移动设备。 例如，你可以使用 [HYPR JAVA SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) 来完成此任务。

## <a name="scenario-description"></a>方案描述

HYRP 集成包括以下组件：

- Azure AD B2C – 授权服务器，负责验证用户的凭据，也称为标识提供者

- Web 和移动应用程序 - 你选择用 HYPR 和 Azure AD B2C 保护的移动或 Web 应用程序。 HYPR 提供了一个强大的移动 SDK，还提供了一个可在 iOS 和 Android 平台上使用的移动应用，用于执行真正的无密码身份验证。

- HYPR 移动应用 - 如果不想在自己的移动应用程序中使用移动 Sdk，则可以使用 HYPR 移动应用来执行本示例。

- HYPR REST Api - 可以使用 HYPR API 来执行用户设备注册和身份验证。 可在[此处](https://apidocs.hypr.com)找到这些 API。

以下体系结构图显示了实现方式。

![HYPR 体系结构图的屏幕截图](media/partner-hypr/hypr-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户到达登录页。 用户选择“登录/注册”，然后在页面上输入用户名。
| 2. | 应用程序将用户属性发送到 Azure AD B2C，以便进行身份验证。
| 3. | Azure AD B2C 收集用户属性并将属性发送到 HYPR，以通过 HYPR 移动应用对用户进行身份验证。
| 4. | HYPR 将推送通知发送到已注册用户的移动设备，以进行由 Fast Identity Online (FIDO) 认证的身份验证。 这可以是用户指纹、生物识别或去中心化的 pin。  
| 5. | 用户确认推送通知后，将根据验证结果向用户授予或拒绝对客户应用程序的访问权限。

## <a name="configure-the-azure-ad-b2c-policy"></a>配置 Azure AD B2C 策略

1. 请在策略文件夹中转到 [AZURE AD B2C HYPR 策略](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy)。

2. 按照该[文档](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)的说明来下载 [LocalAccounts 入门包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. 为 Azure AD B2C 租户配置策略。

>[!NOTE]
>更新提供的策略，使其与你的特定租户相关。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，在“策略”下选择“Identity Experience Framework”。

2. 选择前面创建的“SignUpSignIn”。

3. 选择“运行用户流”，然后选择设置：

   a. 应用程序：选择已注册的应用（示例为 JWT）

   b. 回复 URL：选择“重定向 URL”

   c. 选择“运行用户流”。

4. 浏览注册流程并创建帐户

5. 创建用户属性后，将在流中调用 HYPR。 如果流未完成，请检查该用户是否未保存在目录中。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参阅以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./custom-policy-get-started.md?tabs=applications)