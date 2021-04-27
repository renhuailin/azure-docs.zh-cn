---
title: 教程：将 Azure Active Directory 单一登录 (SSO) 与 Maverics Identity Orchestrator SAML Connector 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Maverics Identity Orchestrator SAML Connector 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 402f6cd6961108cdf1e9c94fb4f93309fbf15ead
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599020"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>将 Azure AD 单一登录与 Maverics Identity Orchestrator SAML Connector 集成

Strata 的 Maverics Identity Orchestrator 提供了一种简单方法，可以将本地应用程序与 Azure Active Directory (Azure AD) 集成，以进行身份验证和访问控制。 对于当前依赖于标头、cookie 和其他专有身份验证方法的应用，Maverics Orchestrator 可以实现身份验证和授权的现代化。 可将 Maverics Orchestrator 实例部署在本地或云中。 

本混合访问教程演示如何迁移当前受旧版 Web 访问管理产品保护的本地 Web 应用程序，从而使用 Azure AD 进行身份验证和访问控制。 下面是基本步骤：

1. 设置 Maverics Orchestrator
1. 代理应用程序
1. 在 Azure AD 中注册企业应用程序
1. 通过 Azure 进行身份验证并授予对应用程序的访问权限
1. 添加标头以实现无缝应用程序访问
1. 使用多个应用程序

## <a name="prerequisites"></a>先决条件

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Maverics Identity Orchestrator SAML Connector SSO 的订阅。 若要获取 Mavericks 软件，请联系 [Strata 销售部](mailto:sales@strata.io)。
* 至少一个使用基于标头的身份验证的应用程序。 这些示例不适用于名为 Connectulum 的应用程序（托管在 `https://app.connectulum.com` 上）。
* 用于托管 Maverics Orchestrator 的 Linux 计算机
  * OS：RHEL 7.7 或更高版本，CentOS 7 +
  * 磁盘：>= 10 GB
  * 内存：>= 4 GB
  * 端口：22 (SSH/SCP)、443、7474
  * 安装/管理任务的根访问
  * 托管 Maverics Identity Orchestrator 的服务器到受保护应用程序的网络流出量

## <a name="step-1-set-up-the-maverics-orchestrator"></a>步骤 1：设置 Maverics Orchestrator

### <a name="install-maverics"></a>安装 Maverics

1. 获取最新的 Maverics RPM。 将包复制到要在其中安装 Maverics 软件的系统中。

1. 安装 Maverics 包，并将文件名替换为 `maverics.rpm`。

   `sudo rpm -Uvf maverics.rpm`

   安装 Maverics 后，它将作为服务在 `systemd` 下运行。 若要验证服务是否正在运行，请执行以下命令：

   `sudo systemctl status maverics`

1. 若要重新启动 Orchestrator 并跟踪日志，可以运行以下命令：

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

安装 Mavericks 后，将在 `/etc/maverics` 目录中创建默认的 `maverics.yaml` 文件。 在编辑配置以添加 `appgateways` 和 `connectors` 前，配置文件如下所示：

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>配置 DNS

DNS 将非常有用，这样就不必记住 Orchestrator 服务器的 IP 了。

使用假设 Orchestrator IP 12.34.56.78，编辑浏览器计算机（笔记本电脑）的主机文件。 在基于 Linux 的操作系统上，此文件位于 `/etc/hosts`。 在 Windows 上，其位于 `C:\windows\system32\drivers\etc`。

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

若要确认 DNS 配置正确，可以向 Orchestrator 的状态终结点发出请求。 从浏览器中请求 http://sonar.maverics.com:7474/status 。

### <a name="configure-tls"></a>配置 TLS

通过安全通道与 Orchestrator 通信对于维护安全性至关重要。 可以在 `tls` 部分中添加证书/密钥对来实现此目的。

若要为 Orchestrator 服务器生成自签名证书和密钥，请从 `/etc/maverics` 目录中运行以下命令：

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> 对于生产环境，可能希望使用由已知 CA 签名的证书，以避免浏览器中出现警告。 如果要寻找受信任的 CA，[Let's Encrypt](https://letsencrypt.org/) 是一个不错的免费选项。

现在，将新生成的证书和密钥用于 Orchestrator。 配置文件现在应包含以下代码：

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

若要确认 TLS 配置正确，请重新启动 Maverics 服务，并向状态终结点发出请求。

## <a name="step-2-proxy-an-application"></a>步骤 2：代理应用程序

接下来，使用 `appgateways` 在 Orchestrator 中配置基本代理。 此步骤帮助验证 Orchestrator 是否有与受保护应用程序的必要连接。

配置文件现在应包含以下代码：

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

若要确认代理运行正确，请重新启动 Maverics 服务，并通过 Maverics 代理向应用程序发出请求。 可以选择性地向特定应用程序资源发出请求。

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>步骤 3：在 Azure AD 中注册企业应用程序

现在，在 Azure AD 中创建一个新的企业应用程序，用于对最终用户进行身份验证。

> [!NOTE]
> 使用条件访问等 Azure AD 功能时，请务必为每个本地应用程序创建企业应用程序。 这可允许按应用进行条件访问、按应用进行风险评估、按应用分配权限等。 通常，Azure AD 中的企业应用程序映射到 Maverics 中的 Azure 连接器。

要在 Azure AD 中注册企业应用程序：

1. 在 Azure AD 租户中，转到“企业应用程序”，然后选择“新建应用程序” 。 在 Azure AD 库中，搜索“Maverics Identity Orchestrator SAML Connector”，然后选择它。

1. 在 Maverics Identity Orchestrator SAML Connector 的“属性”窗格上，将“需要进行用户分配?”设置为“否”，使该应用程序可用于目录中的所有用户。  

1. 在 Maverics Identity Orchestrator SAML Connector 的“概述”窗格上，选择“设置单一登录”，然后选择“SAML”。

1. 在 Maverics Identity Orchestrator SAML Connector 的“基于 SAML 的登录”窗格上，通过选择“编辑”（铅笔图标）按钮来编辑“基本 SAML 配置”。

   ![“基本 SAML 配置”“编辑”按钮的屏幕截图。](common/edit-urls.png)

1. 输入 `https://sonar.maverics.com` 的“实体 ID”。 实体 ID 在租户的所有应用中必须唯一，它可以是任意值。 在下一部分中为 Azure 连接器定义 `samlEntityID` 字段时，将使用此值。

1. 输入 `https://sonar.maverics.com/acs` 的“回复 URL”。 在下一部分中为 Azure 连接器定义 `samlConsumerServiceURL` 字段时，将使用此值。

1. 输入 `https://sonar.maverics.com/` 的“登录 URL”。 Maverics 不会使用此字段，但在 Azure AD 中是必填的，以使用户能够通过 Azure AD“我的应用”门户来访问应用程序。

1. 选择“保存”。

1. 在“SAML 签名证书”部分中，选择“复制”按钮以复制“应用联合元数据 URL”值，然后将其保存到计算机中。  

   ![“SAML 签名证书”“复制”按钮的屏幕截图。](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>步骤 4：通过 Azure 进行身份验证并授予对应用程序的访问权限

接下来，通过在 Maverics 中配置 Azure 连接器来使用刚创建的企业应用程序。 此 `connectors` 配置与 `idps` 块配对，使 Orchestrator 能够对用户进行身份验证。

配置文件现在应包含以下代码。 请确保将 `METADATA_URL` 替换为上一步中的应用联合元数据 URL 值。

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

若要确认身份验证运行正确，请重新启动 Maverics 服务，并通过 Maverics 代理向应用程序资源发出请求。 在访问资源之前，应重定向到 Azure 以进行身份验证。

## <a name="step-5-add-headers-for-seamless-application-access"></a>步骤 5：添加标头以实现无缝应用程序访问

尚未向上游应用程序发送标头。 在请求通过 Maverics 代理时将 `headers` 添加到请求，以便上游应用程序可以识别用户。

配置文件现在应包含以下代码：

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

若要确认身份验证运行正确，请通过 Maverics 代理向应用程序资源发出请求。 受保护应用程序现在应正在接收请求标头。 

如果应用程序需要不同的标头，则可以随意编辑标头键。 作为 SAML 流的一部分从 Azure AD 返回的所有声明都可以在标头中使用。 例如，可以包含另一个标头 `secondary_email: azureSonarApp.email`，其中 `azureSonarApp` 是连接器名称，`email` 是从 Azure AD 返回的声明。 

## <a name="step-6-work-with-multiple-applications"></a>步骤 6：使用多个应用程序

现在来看一下代理到不同主机上的多个应用程序需要什么条件。 若要完成此步骤，请配置另一个应用程序网关、Azure AD 中的另一个企业应用程序和另一个连接器。

配置文件现在应包含以下代码：

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

可能已注意到，代码在应用程序网关定义中添加了 `host` 字段。 通过 `host` 字段，Maverics Orchestrator 可以区分要将流量代理到哪个上游主机。

若要确认新添加的应用程序网关运行正确，请向 `https://connectulum.maverics.com` 发出请求。

## <a name="advanced-scenarios"></a>高级方案

### <a name="identity-migration"></a>标识迁移

容忍不了陈旧的 web 访问管理工具，但又无法在不进行大规模密码重置的情况下迁移用户？ Maverics Orchestrator 通过使用 `migrationgateways` 支持标识迁移。

### <a name="web-server-gateways"></a>Web 服务器网关

不想通过 Maverics Orchestrator 修改网络和代理流量？ 没关系。 可以将 Maverics Orchestrator 与 web 服务器网关（模块）配对，以提供相同的解决方案，无需代理。

## <a name="wrap-up"></a>总结

此时已经安装了 Maverics Orchestrator，在 Azure AD 中创建并配置了企业应用程序，并配置了 Orchestrator 以在需要身份验证和强制实施策略的同时代理到受保护应用程序。 若要了解有关如何将 Maverics Orchestrator 用于分布式标识管理用例的详细信息，请[联系 Strata](mailto:sales@strata.io)。

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
- [什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)
