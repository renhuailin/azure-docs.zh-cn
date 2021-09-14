---
title: 安全建议
description: 实施安全建议，履行我们责任分担模型中所述的安全义务。 提高应用的安全性。
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 452c0479834ab7929696af2c95454662adfecfab
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538320"
---
# <a name="security-recommendations-for-app-service"></a>适用于应用服务的安全建议

本文包含适用于 Azure 应用服务的安全建议。 实施这些建议将有助于你履行我们的共享职责模型中描述的安全职责，并改进 Web 应用解决方案的总体安全性。 若要详细了解 Microsoft 采取哪些措施来履行服务提供商责任，请阅读 [Azure 基础结构安全性](../security/fundamentals/infrastructure.md)。

## <a name="general"></a>常规

| 建议 | 注释 |
|-|-|----|
| 保持最新状态 | 使用最新版的受支持平台、编程语言、协议和框架。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 建议 | 注释 |
|-|----|
| 禁用匿名访问 | 除非需要支持匿名请求，否则请禁用匿名访问。 有关 Azure 应用服务身份验证选项的详细信息，请参阅 [Azure 应用服务中的身份验证和授权](overview-authentication-authorization.md)。|
| 需要身份验证 | 在可能情况下，请使用应用服务身份验证模块，而不是编写代码来处理身份验证和授权。 请参阅 [Azure 应用服务中的身份验证和授权](overview-authentication-authorization.md)。 |
| 使用经身份验证的访问权限保护后端资源 | 可以使用用户标识或应用程序标识向后端资源进行身份验证。 选择使用应用程序标识时，请使用[托管标识](overview-managed-identity.md)。
| 需要客户端证书身份验证 | 客户端证书身份验证只允许从那些可以使用你提供的证书进行身份验证的客户端进行连接，因此可以改进安全性。 |

## <a name="data-protection"></a>数据保护

| 建议 | 注释 |
|-|-|
| 将 HTTP 重定向到 HTTPS | 默认情况下，客户端可以使用 HTTP 或 HTTPS 连接到 Web 应用。 建议将 HTTP 重定向到 HTTPS，因为 HTTPS 使用 SSL/TLS 协议来提供既加密又经过身份验证的安全连接。 |
| 加密与 Azure 资源的通信 | 当应用连接到 Azure 资源（例如 [SQL 数据库](https://azure.microsoft.com/services/sql-database/)或 [Azure 存储](../storage/index.yml)）时，连接一直保持在 Azure 中。 由于连接经过 Azure 中的共享网络，因此应始终加密所有通信。 |
| 需要尽可能新的 TLS 版本 | 从 2018 年开始，新的 Azure 应用服务应用使用 TLS 1.2。 更新版的 TLS 包含针对旧协议版本的安全改进。 |
| 使用 FTPS | 应用服务支持使用 FTP 和 FTPS 来部署文件。 尽可能使用 FTPS 而不是 FTP。 如果未使用这两种协议或其中一种协议，则应[将其禁用](deploy-ftp.md#enforce-ftps)。 |
| 保护应用程序数据 | 请勿将应用程序密钥（例如数据库凭据、API 令牌或私钥）存储在代码或配置文件中。 广为接受的方法是使用所选语言的标准模式将它们作为[环境变量](https://wikipedia.org/wiki/Environment_variable)进行访问。 在 Azure 应用服务中，可以通过[应用设置](./configure-common.md)和[连接字符串](./configure-common.md)定义环境变量。 应用设置和连接字符串以加密方式存储在 Azure 中。 只有在应用启动并将应用设置注入应用的进程内存中之前，才会对应用设置进行解密。 加密密钥会定期轮换。 或者，可以将 Azure 应用服务应用与 [Azure Key Vault](../key-vault/index.yml) 集成，以实现高级密钥管理。 通过[使用托管标识访问 Key Vault](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)，应用服务应用可以安全地访问所需的机密。 |

## <a name="networking"></a>网络

| 建议 | 注释 |
|-|-|
| 使用静态 IP 限制 | 使用 Windows 上的 Azure 应用服务，可定义允许访问应用的 IP 地址的列表。 允许列表可包括单个 IP 地址或由子网掩码定义的 IP 地址范围。 有关详细信息，请参阅 [Azure 应用服务静态 IP 限制](app-service-ip-restrictions.md)。  |
| 选择独立定价层 | 除了独立定价层，所有层都在 Azure 应用服务的共享网络基础结构上运行应用。 通过在专用的[应用服务环境](environment/intro.md)中运行应用，独立层可提供完整的网络隔离。 应用服务环境在你自己的 [Azure 虚拟网络](../virtual-network/index.yml)实例中运行。|
| 在访问本地资源时使用安全连接 | 可使用[混合连接](app-service-hybrid-connections.md)、[虚拟网络集成](web-sites-integrate-with-vnet.md)或[应用服务环境](environment/intro.md)连接到本地资源。 |
| 限制向入站网络流量公开 | 可以通过网络安全组限制网络访问并控制公开的终结点数。 有关详细信息，请参阅[如何控制应用服务环境的入站流量](environment/app-service-app-service-environment-control-inbound-traffic.md)。 |

## <a name="monitoring"></a>监视

| 建议 | 注释 |
|-|-|
|使用 Azure 安全中心的 Azure Defender for App Service | [Azure Defender for App Service](../security-center/defender-for-app-service-introduction.md) 与 Azure 应用服务实行本机集成。 安全中心会对应用服务计划涵盖的资源进行评估，并根据发现结果生成安全建议。 请使用[这些建议]() (../security-center/recommendations-reference.md#appservices-recommendations) 中的详细说明强化应用服务资源。 Azure Defender 还提供了威胁防护，能够检测到大量威胁，几乎涵盖 MITRE ATT&CK 战术的完整列表（从预攻击到命令和控制）。 若要获取 Azure 应用服务警报的完整列表，请参阅 [Azure Defender for App Service 警报](../security-center/alerts-reference.md#alerts-azureappserv)。|

## <a name="next-steps"></a>后续步骤

咨询你的应用程序提供商，以确定是否还要满足其他安全要求。 有关开发安全的应用程序的详细信息，请参阅[安全开发文档](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)。