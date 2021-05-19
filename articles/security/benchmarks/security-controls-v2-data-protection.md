---
title: Azure 安全基准 V2 - 数据保护
description: Azure 安全基准 V2 数据保护
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 50358eed580bbd83f25386feb0068a252060672b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037109"
---
# <a name="security-control-v2-data-protection"></a>安全控制 V2：数据保护

数据保护包括对静态数据保护、传输中数据保护以及通过授权访问机制进行的数据保护进行控制。 这包括使用 Azure 中的访问控制、加密和日志记录对敏感数据资产进行发现、分类、保护和监视操作。

若要查看适用的内置 Azure Policy，请参阅 [Azure 安全基准管理法规合规性内置计划的详细信息：数据保护](../../governance/policy/samples/azure-security-benchmark.md#data-protection)

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1：对敏感数据进行发现、分类和标记

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| DP-1 | 13.1、14.5、14.7 | SC-28 |

对敏感数据进行发现、分类和标记，以便设计合适的控件，确保组织的技术系统能够安全地存储、处理和传输敏感信息。

对于 Azure 中的、本地的、Office 365 中的和其他位置中的 Office 文档内的敏感信息，请使用 Azure 信息保护（及其关联的扫描工具）。

使用 Azure SQL 信息保护有助于对 Azure SQL 数据库中存储的信息进行分类和标记。

- [使用 Azure 信息服务标记敏感信息](/azure/information-protection/what-is-information-protection) 

- [如何实现 Azure SQL 数据发现](../../azure-sql/database/data-discovery-and-classification-overview.md)

**责任**：共享

**客户安全利益干系人**（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [数据安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| DP-2 | 13.2、2.10 | SC-7、AC-4 |

使用 Azure 基于角色的访问控制 (Azure RBAC)、基于网络的访问控制以及 Azure 服务中的特定控制（例如 SQL 和其他数据库中的加密）来限制访问，从而保护敏感数据。 

为了确保一致的访问控制，所有类型的访问控制都应符合企业分段策略。 企业分段策略还应根据敏感的或业务关键型的数据和系统的位置来确定。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据始终安全，Microsoft 实施了一些默认的数据保护控制机制和功能。

- [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)

- [了解 Azure 中的客户数据保护](../fundamentals/protection-customer-data.md)

**责任**：共享

**客户安全利益干系人**（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [数据安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3：监视未经授权的敏感数据传输

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| DP-3 | 13.3 | AC-4、SI-4 |

监视是否存在未经授权将数据传输到企业不可见且无法控制的位置的行为。 这通常涉及监视那些可能意味着未经授权的数据外泄的异常活动（大型或异常传输）。 

Azure 存储高级威胁防护 (ATP) 和 Azure SQL ATP 可以对可能意味着未经授权传输敏感信息的异常传输行为发出警报。 

Azure 信息保护 (AIP) 提供的监视功能针对已分类并标记的信息。 

如果要求满足数据丢失防护 (DLP) 规范，可以使用基于主机的 DLP 解决方案来强制实施检测性的和/或预防性的控制，以防止数据外泄。

- [Azure Defender for SQL](../../azure-sql/database/azure-defender-for-sql.md)

- [适用于存储的 Azure Defender](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**责任**：共享

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security) 

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密传输中的敏感信息

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| DP-4 | 14.4 | SC-8 |

为了对访问控制进行补充，应使用加密保护传输中的数据，以免遭受“带外”攻击（例如流量捕获），确保攻击者无法轻松读取或修改数据。

虽然这对于专用网络上的流量来说是可选的，但对于外部和公共网络上的流量来说，这是至关重要的。 对于 HTTP 流量，请确保连接到 Azure 资源的任何客户端能够协商 TLS v1.2 或更高版本。 对于远程管理，请使用 SSH（适用于 Linux）或 RDP/TLS（适用于 Windows），而不是使用未加密的协议。 应当禁用已过时的 SSL、TLS 和 SSH 版本和协议，以及弱密码。

默认情况下，Azure 为在 Azure 数据中心之间传输的数据提供加密。

- [了解 Azure 传输中的加密](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [有关 TLS 安全性的信息](/security/engineering/solving-tls1-problem)

- [传输中的 Azure 数据的双重加密](../fundamentals/double-encryption.md#data-in-transit)

**责任**：共享

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [数据安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5：加密静态敏感数据

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| DP-5 | 14.8 | SC-28、SC-12 |

为了对访问控制进行补充，应使用加密保护静态数据，以免遭受“带外”攻击（例如访问底层存储）。 这有助于确保攻击者无法轻松读取或修改数据。 

默认情况下，Azure 为静态数据提供加密。 对于高度敏感的数据，你可以选择在所有可用的 Azure 资源上进行额外的静态加密。 默认情况下，Azure 管理你的加密密钥，但是 Azure 为某些 Azure 服务提供了管理你自己的密钥（客户管理的密钥）的选项。

- [了解 Azure 中的静态加密](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [如何配置客户管理的加密密钥](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [加密模型和密钥管理表](../fundamentals/encryption-models.md)

- [Azure 中的静态数据双重加密](../fundamentals/double-encryption.md#data-at-rest)

**责任**：共享

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [数据安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)