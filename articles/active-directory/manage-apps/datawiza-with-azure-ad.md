---
title: 使用 Azure AD 和 Datawiza 实现安全的混合访问
description: 本教程介绍如何将 Datawiza 与 Azure AD 集成以实现安全的混合访问
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/27/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf8b1d7a3f45cd49bdb2ea420084a242e3e2c959
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214230"
---
# <a name="tutorial-configure-datawiza-with-azure-active-directory-for-secure-hybrid-access"></a>教程：为 Datawiza 配置 Azure Active Directory 以实现安全的混合访问

本示例教程介绍如何将 Azure Active Directory (Azure AD) 与 [Datawiza](https://www.datawiza.com/) 集成，以实现安全的混合访问。

Datawiza 的 [Datawiza Access Broker (DAB) ](https://www.datawiza.com/access-broker) 可以扩展 Azure AD，以启用单一登录 (SSO) 和精细访问控制来保护本地和云托管的应用程序，例如 Oracle E-Business Suite、Microsoft IIS 和 SAP。

企业可以使用此解决方案快速从 Symantec SiteMinder、NetIQ、Oracle 和 IBM 等旧式 Web 访问管理器 (WAM) 过渡到 Azure AD，而无需重新编写应用程序。 企业还可以使用 Datawiza 作为无代码或少代码解决方案，将新应用程序集成到 Azure AD。 这可以节省工程时间，显著降低成本，并以受保护方式交付项目。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- Azure 订阅。 如果你没有订阅，可以获取一个[试用帐户](https://azure.microsoft.com/free/)。

- 一个已关联到你的 Azure 订阅的 [Azure AD 租户](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)。

- 需要有 [Docker](https://docs.docker.com/get-docker/) 和 [docker-compose](https://docs.docker.com/compose/install/) 才能运行 DAB。 应用程序可以在任何平台（例如虚拟机和裸机）上运行。

- 一个要从旧式标识系统过渡到 Azure AD 的应用程序。 在本示例中，DAB 将部署在该应用程序所在的同一服务器上。 该应用程序将在 localhost: 3001 上运行，DAB 通过 localhost: 9772 将流量代理到该应用程序。 传送到应用程序的流量将首先到达 DAB，然后代理到应用程序。

## <a name="scenario-description"></a>方案描述

Datawiza 集成包括以下组件：

- [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) - Microsoft 的基于云的标识和访问管理服务，可帮助用户登录以及访问内部和外部资源。

- Datawiza Access Broker (DAB) - 用户将登录到该服务，并通过 HTTP 头以透明方式将标识传递给应用程序。

- Datawiza Cloud Management Console (DCMC) - 一个集中式管理控制台，用于管理 DAB。 DCMC 为管理员提供了 UI 和 Restful API，用于管理 DAB 配置及其访问控制策略。

以下体系结构图体现了实施详情。

![该插图显示体系结构示意图](./media/datawiza-with-azure-active-directory/datawiza-architecture-diagram.png)

|步骤| 说明|
|:----------|:-----------|
|  1. | 用户发出访问本地或云托管应用程序的请求。 DAB 将用户发出的请求代理到该应用程序。|
| 2. |DAB 检查用户的身份验证状态。 如果未收到会话令牌，或者提供的会话令牌无效，它会将该用户定向到 Azure AD 进行身份验证。|
| 3. | Azure AD 会将用户请求发送到 DAB 应用程序在 Azure AD 租户中注册的过程中指定的终结点。|
| 4. | DAB 会评估访问策略，并计算要在转发到该应用程序的 HTTP 标头中包含的属性值。 在此步骤中，DAB 可能会调用标识提供者，以检索正确设置头值所需的信息。 DAB 会设置标头值并将请求发送到该应用程序。 |
| 5. |  现在，用户经过了身份验证，并且具有了该应用程序的访问权限。|

## <a name="onboard-with-datawiza"></a>通过 Datawiza 加入

若要将本地或云托管的应用程序与 Azure AD 集成，请登录到 [Datawiza Cloud Management Console](https://console.datawiza.com/) (DCMC)。

## <a name="create-an-application-on-dcmc"></a>在 DCMC 上创建应用程序。

在 DCMC 上[创建一个应用程序](https://docs.datawiza.com/step-by-step/step2.html)，并为此应用程序生成 `PROVISIONING_KEY` 和 `PROVISIONING_SECRET` 密钥对。

对于 Azure AD，Datawiza 提供了便利的[一键式集成](https://docs.datawiza.com/tutorial/web-app-azure-one-click.html)。 这种将 Azure AD 与 DCMC 集成的方法可以在你的 Azure AD 租户中代表你创建应用程序注册。

![该插图显示如何配置 idp](./media/datawiza-with-azure-active-directory/configure-idp.png)

不过，如果你想要在 Azure AD 租户中使用现有的 Web 应用程序，可以禁用该选项并填充窗体字段。 需要提供租户 ID、客户端 ID 和客户端机密。 [创建 Web 应用程序并在租户中获取这些值](https://docs.datawiza.com/idp/azure.html)。

![该插图显示如何使用窗体配置 idp](./media/datawiza-with-azure-active-directory/use-form.png)

## <a name="run-dab-with-a-header-based-application"></a>使用基于标头的应用程序运行 DAB

1. 可以使用 Docker 或 Kubernetes 来运行 DAB。 用户需要使用 docker 映像来创建基于标头的示例应用程序。 [配置 DAB 和 SSO 集成](https://docs.datawiza.com/step-by-step/step3.html)。 [使用 Kubernetes 部署 DAB](https://docs.datawiza.com/tutorial/web-app-AKS.html)。 已提供一个示例 Docker 映像 `docker-compose.yml` 文件供你下载和使用。 [登录到容器注册表](https://docs.datawiza.com/step-by-step/step3.html#important-step)，以便下载 DAB 映像和基于头的应用程序。

    ```YML
    services:
      datawiza-access-broker:
      image: registry.gitlab.com/datawiza/access-broker
      container_name: datawiza-access-broker
      restart: always
      ports:
      - "9772:9772"
      environment:
      PROVISIONING_KEY: #############################################
      PROVISIONING_SECRET: ##############################################
      
      header-based-app:
      image: registry.gitlab.com/datawiza/header-based-app
      restart: always
     ports:
     - "3001:3001"
   ```

2. 执行 `docker-compose -f docker-compose.yml up` 后，基于头的应用程序应已启用 Azure AD 的 SSO。 打开浏览器并键入 `http://localhost:9772/`。

3. 此时将显示 Azure AD 登录页。

## <a name="pass-user-attributes-to-the-header-based-application"></a>将用户属性传递给基于标头的应用程序

1. DAB 从 IdP 获取用户属性，并可以通过标头或 Cookie 将用户属性传递给应用程序。 请参阅有关如何向基于标头的应用程序[传递用户属性](https://docs.datawiza.com/step-by-step/step4.html)（例如电子邮件地址、名字和姓氏）的说明。

2. 成功配置用户属性后，应会看到每个用户属性都有一个绿色选中符号。

   ![该插图显示 datawiza 应用程序主页](./media/datawiza-with-azure-active-directory/datawiza-application-home-page.png)

## <a name="test-the-flow"></a>测试流

1. 导航到应用程序 URL。

2. DAB 应会重定向到 Azure AD 登录页。

3. 成功完成身份验证后，你应会重定向到 DAB。

4. DAB 将评估策略，计算标头，并将用户发送到上游应用程序。

5. 此时应会显示请求的应用程序。

## <a name="next-steps"></a>后续步骤

- [为 Datawiza 配置 Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/partner-datawiza)

- [Datawiza 文档](https://docs.datawiza.com)
