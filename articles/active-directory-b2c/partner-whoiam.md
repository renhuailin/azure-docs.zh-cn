---
title: 将 Azure Active Directory B2C 与 WholAM 配置的教程
titleSuffix: Azure AD B2C
description: 在本教程中，了解如何将 Azure AD B2C 身份验证与 WhoIAM 集成以进行用户验证。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 32c288476c8491747cc80f0115daa72919aa05a8
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257732"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>将 WhoIAM 与 Azure Active Directory B2C 配置的教程

在此示例教程中，我们提供了有关如何在你的环境中配置 [WhoIAM](https://www.whoiam.ai/brims/) 品牌标识管理系统 (BRIMS) 的指导，以及如何将其与 Active Directory B2C (Azure AD B2C) 集成。

BRIMS 是一组在你的环境中部署的应用和服务。 它为用户群提供语音、短信和电子邮件验证。 BRIMS 与现有标识和访问管理解决方案结合使用，并且它与平台无关。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。

- 一个 WhoIAM [试用帐户](https://www.whoiam.ai/contact-us/)。

## <a name="scenario-description"></a>方案描述

WhoIAM 集成包括以下组件：

- Azure AD B2C 租户。 这是授权服务器，基于其中定义的自定义策略来验证用户的凭据。 也被称作标识提供者。

- 用于管理客户端及其配置的管理门户。

- 通过终结点公开各种功能的 API 服务。  

- Azure Cosmos DB，充当 BRIMS 管理门户和 API 服务的后端。

以下体系结构图体现了实施详情。

![Azure AD B2C 的体系结构与 WhoIAM 集成的示意图。](media/partner-whoiam/whoiam-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户会进入一个页面，开始向使用 Azure AD B2C 作为标识提供者的应用提出注册或登录请求。
| 2. | 作为身份验证的一部分，用户请求验证其电子邮件或电话的所有权，或者使用其语音作为生物识别验证因素。  
| 3. | Azure AD B2C 调用 BRIMS API 服务，并传送用户的电子邮件地址、电话号码和录制语音。
| 4. | BRIMS 使用预定义的配置（如完全可定制的电子邮件和短信模板），通过与应用风格一致的方式，用用户使用的语言与用户交互。
| 5. | 用户的标识验证完成后，BRIMS 将返回 Azure AD B2C 的令牌，以指示验证结果。 然后根据结果，Azure AD B2C 向用户授予对应用程序的访问权限，或者提示用户的身份验证失败。  

## <a name="sign-up-with-whoiam"></a>注册 WhoIAM

1. 请联系 [WhoIAM](https://www.whoiam.ai/contact-us/) 并创建一个 BRIMS 帐户。

2. 根据可用的注册指南进行注册，并配置以下 Azure 服务：

    - [Azure 密钥保管库](https://azure.microsoft.com/services/key-vault/)：用于安全存储密码，如邮件服务密码。

    - [Azure 应用服务](https://azure.microsoft.com/services/app-service/)：用于托管 BRIMS API 和管理门户服务。

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)：用于对管理门户的管理用户进行身份验证。

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)：用于存储和检索设置。

    - [Application Insights](../azure-monitor/app/app-insights-overview.md)（可选）：用于登录 API 和管理门户。

3. 在你的 Azure 环境中部署 BRIMS API 和 BRIMS 管理门户。

4. BRIMS 注册文档中提供了 Azure AD B2C 自定义策略示例。 按照文档配置应用，并将 BRIMS 平台用于用户身份验证。  

有关 WhoIAM's BRIMS 的详细信息，请参阅[产品文档](https://www.whoiam.ai/brims/)。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户。 在“策略”下，选择“Identity Experience Framework”。 

2. 选择前面创建的“SignUpSignIn”。

3. 选择“运行用户流”，然后：

   a. 对于应用程序，选择已注册的应用（示例为 JWT）。

   b. 对于“答复 URL” ，选择“重定向 URL”。

   c. 选择“运行用户流”。

4. 浏览注册流并创建帐户。

5. 创建用户属性后，将在流中调用 BRIMS 服务。 如果流未完成，请检查该用户是否未保存在目录中。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参阅以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)