---
title: 为 Azure Active Directory B2C 配置 Datawiza 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 身份验证与 Datawiza 集成，实现安全的混合访问
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 7/07/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: a007f567b6f137f1bff225f90c3f52dfaf71c1f2
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228577"
---
# <a name="tutorial-configure-azure-ad-b2c-with-datawiza-to-provide-secure-hybrid-access"></a>教程：为 Azure AD B2C 配置 Datawiza 以提供安全的混合访问权限

本示例教程介绍如何将 Azure Active Directory (AD) B2C 与 [Datawiza](https://www.datawiza.com/) 集成。
Datawiza 的 [Datawiza Access Broker (DAB)](https://www.datawiza.com/access-broker) 支持单一登录 (SSO) 和精细访问控制，扩展了 Azure AD B2C，可以保护本地旧的应用程序。 使用此解决方案，企业可以迅速从旧系统转换到 Azure AD B2C，无需重写应用程序。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 已链接到 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。

- [Docker](https://docs.docker.com/get-docker/) 是运行 DAB 的必备条件。 应用程序可以在任何平台上运行，例如虚拟机和裸机。

- 你将要从旧标识系统转换到 Azure AD B2C 的本地应用程序。 在本示例中，DAB 将部署在应用程序所在的同一服务器上。 应用程序将在 localhost: 3001 上运行，DAB 将通过 localhost: 9772 将流量代理到应用程序。 传送到应用程序的流量将首先到达 DAB，然后代理到应用程序。

## <a name="scenario-description"></a>方案描述

Datawiza 集成包括以下组件：

- Azure AD B2C：负责验证用户凭据的授权服务器。 经过身份验证的用户可以使用 Azure AD B2C 目录中存储的本地帐户来访问本地应用程序。

- **Datawiza Access Broker (BROKER)** ：服务用户登录，并通过 HTTP 标头以透明方式将标识传递给应用程序。

- **Datawiza Cloud Management Console (DCMC)** - 一个集中式管理控制台，用于管理 DAB。 DCMC 为管理员提供了 UI 和 Restful API，用于管理 DAB 配置及其访问控制策略。

以下体系结构图体现了实施详情。

![图中显示 Azure AD B2C 与 Datawiza 集成后的体系结构，此集成便于安全访问混合应用程序](./media/partner-datawiza/datawiza-architecture-diagram.png)

| 步骤 | 说明 |
|:-------|:---------------|
| 1. | 用户发出访问本地托管应用的请求。 DAB 将用户发出的请求代理到该应用程序。|
| 2. | DAB 检查用户的身份验证状态。 如果未收到会话令牌，或者提供的会话令牌无效，它会将该用户发送到 Azure AD B2C 进行身份验证。|
| 3. | Azure AD B2C 会将用户请求发送到 DAB 应用程序在 Azure AD B2C 租户中注册的过程中指定的终结点。|
| 4. | DAB 会评估访问策略，并计算要在转发到该应用程序的 HTTP 标头中包含的属性值。 在此步骤中，DAB 可能会调用 IdP，以检索正确设置标头值所需的信息。 DAB 会设置标头值并将请求发送到该应用程序。 |
|5.  | 现在，用户经过了身份验证，并且具有了该应用程序的访问权限。|

## <a name="onboard-with-datawiza"></a>通过 Datawiza 加入

若要将旧的本地应用与 Azure AD B2C 集成，请联系 [Datawiza](https://login.datawiza.com/df3f213b-68db-4966-bee4-c826eea4a310/b2c_1a_linkage/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile&client_id=4f011d0f-44d4-4c42-ad4c-88c7bbcd1ac8&redirect_uri=https%3A%2F%2Fconsole.datawiza.com%2Fhome&state=eyJpZCI6Ijk3ZjI5Y2VhLWQ3YzUtNGM5YS1hOWU2LTg1MDNjMmUzYWVlZCIsInRzIjoxNjIxMjg5ODc4LCJtZXRob2QiOiJyZWRpcmVjdEludGVyYWN0aW9uIn0%3D&nonce=08e1b701-6e42-427b-894b-c5d655a9a6b0&client_info=1&x-client-SKU=MSAL.JS&x-client-Ver=1.3.3&client-request-id=3ac285ba-2d4d-4ae5-8dc2-9295ff6047c6&response_mode=fragment)。

## <a name="configure-your-azure-ad-b2c-tenant"></a>配置 Azure AD B2C 租户

1. 在 Azure AD B2C 租户中[注册](https://docs.datawiza.com/idp/azureb2c.html#microsoft-azure-ad-b2c-configuration) Web 应用程序。

2. 在 Azure 管理门户中[配置注册和登录用户流](https://docs.datawiza.com/idp/azureb2c.html#configure-a-user-flow)。

  >[!Note]
  >稍后在 DCMC 中设置 DAB 时，需要使用租户名称、用户流名称、客户端 ID 和客户端密码。

## <a name="create-an-application-on-dcmc"></a>在 DCMC 上创建应用程序。

1. 在 DCMC 上[创建应用程序](https://docs.datawiza.com/step-by-step/step2.html)并为此应用程序生成密钥对 `PROVISIONING_KEY` 和 `PROVISIONING_SECRET`。

2. [配置 Azure AD B2C](https://docs.datawiza.com/tutorial/web-app-azure-b2c.html#part-i-azure-ad-b2c-configuration) 使其充当标识提供者 (IdP)

![图中显示用于配置 Idp 的值](./media/partner-datawiza/configure-idp.png)

## <a name="run-dab-with-a-header-based-application"></a>使用基于标头的应用程序运行 DAB

1. 可以使用 Docker 或 Kubernetes 来运行 DAB。 用户需要使用 docker 映像来创建基于标头的示例应用程序。 有关详细信息，请参阅如何[配置 DAB 与 SSO 的集成](https://docs.datawiza.com/step-by-step/step3.html)的说明，有关特定于 Kubernetes 的说明，请参阅如何[使用 Kubernetes 部署 DAB](https://docs.datawiza.com/tutorial/web-app-AKS.html)。 示例 docker 映像 `docker-compose.yml file` 可供下载和使用。 登录到容器注册表，并下载 DAB 映像和基于标头的应用程序。 按照[这些说明](https://docs.datawiza.com/step-by-step/step3.html#important-step)进行操作。
 
   ```YML
   version: '3'

    services:
    datawiza-access-broker:
    image: registry.gitlab.com/datawiza/access-broker
    container_name: datawiza-access-broker
    restart: always
    ports:
      - "9772:9772"
    environment:
      PROVISIONING_KEY: #############################
      PROVISIONING_SECRET: #############################

    header-based-app:
    image: registry.gitlab.com/datawiza/header-based-app
    container_name: ab-demo-header-app
    restart: always
    environment:
      CONNECTOR: B2C
    ports:
      - "3001:3001"
    ```

 2. 执行 `docker-compose -f docker-compose.yml up` 后，基于标头的应用程序应已通过 Azure AD B2C 启用了 SSO。 打开浏览器并键入 `http://localhost:9772/`。

3. 此时将显示 Azure AD B2C 登录页。

## <a name="pass-user-attributes-to-the-header-based-application"></a>将用户属性传递给基于标头的应用程序

1. DAB 从 IdP 获取用户属性，并可以通过标头或 Cookie 将用户属性传递给应用程序。 请参阅有关如何向基于标头的应用程序[传递用户属性](https://docs.datawiza.com/step-by-step/step4.html)（例如电子邮件地址、名字和姓氏）的说明。 

2. 成功配置用户属性后，应会看到每个用户属性都有一个绿色选中符号。

 ![图中显示传递的用户属性](./media/partner-datawiza/pass-user-attributes.png)

## <a name="test-the-flow"></a>测试流

1. 导航到本地应用程序 URL。

2. DAB 应该会重定向到在用户流中配置的页面。

3. 在该页面上从列表中选择 IdP。

4. 在重定向到该 IdP 后，请根据请求提供凭据，包括 Azure AD 多重身份验证 (MFA) 令牌（如果该 IdP 需要）。

5. 在成功进行身份验证后，你应该会重定向到 Azure AD B2C，这样就会将应用程序请求转发到 DAB 重定向 URI。

6. DAB 将评估策略，计算标头，并将用户发送到上游应用程序。  

7. 你应该会看到请求的应用程序。

## <a name="next-steps"></a>后续步骤

有关更多信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)