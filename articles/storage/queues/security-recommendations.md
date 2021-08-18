---
title: 适用于队列存储的安全建议
titleSuffix: Azure Storage
description: 了解适用于队列存储的安全建议。 实施此指南将有助于你履行我们的共享职责模型中描述的安全职责。
author: tamram
services: storage
ms.author: tamram
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: security-recommendations
ms.openlocfilehash: 260f8682766187ba9cedb3b5bf951e06548c0048
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727933"
---
# <a name="security-recommendations-for-queue-storage"></a>适用于队列存储的安全建议

本文包含适用于队列存储的安全建议。 实施执行建议将有助于你履行我们的共享职责模型中描述的安全职责。 若要详细了解 Microsoft 采取哪些措施来履行服务提供商责任，请参阅[云计算的分担责任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)。

包含在本文中的某些建议可能受 Azure 安全中心的自动监视。 在保护你在 Azure 中的资源方面，Azure 安全中心是第一道防线。 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../../security-center/security-center-introduction.md)。

Azure 安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向你提供有关如何解决这些安全漏洞的建议。 有关 Azure 安全中心建议的详细信息，请参阅 [Azure 安全中心的安全性建议](../../security-center/security-center-recommendations.md)。

## <a name="data-protection"></a>数据保护

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 使用 Azure 资源管理器部署模型 | 使用 Azure 资源管理器部署模型创建新的存储帐户，以用于重要的安全增强功能，包括高级的 Azure 基于角色的访问控制 (Azure RBAC) 和审核、基于资源管理器的部署和治理、托管标识访问权限、用于存储机密的 Azure Key Vault 访问权限、用于访问 Azure 存储数据和资源的基于 Azure AD 的身份验证和授权。 如果可能，请迁移使用经典部署模型的现有存储帐户以使用 Azure 资源管理器。 有关 Azure 资源管理器的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/management/overview.md)。 | - |
| 为所有存储帐户启用高级威胁防护 | 针对 Azure 存储的高级威胁防护提供额外的一层安全智能，用于检测是否存在访问或利用存储帐户的异常的和可能有害的企图。 当活动发生异常时，安全警报会在 Azure 安全中心触发，并会通过电子邮件发送给订阅管理员，内容包括可疑活动的详细信息以及如何调查和修正威胁的建议。 有关详细信息，请参阅[针对 Azure 存储的高级威胁防护](../common/azure-defender-storage-configure.md)。 | [是](../../security-center/security-center-remediate-recommendations.md) |
| 将共享访问签名 (SAS) 令牌限制为仅用于 HTTPS 连接 | 当客户端使用 SAS 令牌访问队列数据时要求使用 HTTPS 有助于最大程度地降低被窃听的风险。 有关详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。 | - |

## <a name="identity-and-access-management"></a>标识和访问管理

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 使用 Azure Active Directory (Azure AD) 授权对队列数据的访问 | 与用于对队列存储的请求授权的共享密钥授权相比，Azure AD 提供的安全性和易用性更高。 有关详细信息，请参阅[授权访问 Azure 存储中的数据](../common/authorize-data-access.md)。 | - |
| 通过 Azure RBAC 向 Azure AD 安全主体分配权限时，请记住“最低权限”原则 | 将角色分配给用户、组或应用程序时，只向该安全主体授予执行任务所需的权限。 限制对资源的访问有助于防止意外和恶意滥用数据。 | - |
| 使用 Azure Key Vault 保护帐户访问密钥 | Microsoft 建议使用 Azure AD 对 Azure 存储的请求进行授权。 但是，如果必须使用共享密钥授权，请使用 Azure Key Vault 保护帐户密钥。 可以在运行时从密钥保管库检索密钥，而不是将其与应用程序一起保存。 | - |
| 定期重新生成帐户密钥 | 定期轮换帐户密钥可以降低向恶意参与者公开数据的风险。 | - |
| 向 SAS 分配权限时，请记住最低权限原则 | 创建 SAS 时，请仅指定客户端执行其功能所需的权限。 限制对资源的访问有助于防止意外和恶意滥用数据。 | - |
| 为发布给客户端的任何 SAS 制定吊销计划 | 如果 SAS 遭到泄露，需要尽快撤销该 SAS。 要撤销用户委托 SAS，请撤销用户委托密钥，以使与该密钥关联的所有签名快速失效。 要撤销与存储的访问策略关联的服务 SAS，可以删除存储的访问策略，重命名策略或将其到期时间更改为过去的时间。 有关详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。  | - |
| 如果服务 SAS 与存储的访问策略没有关联，请将到期时间设置为一小时或更短 | 无法撤销与存储的访问策略没有关联的服务 SAS。 因此，建议限制到期时间，以使 SAS 的有效时间不超过一小时。 | - |

## <a name="networking"></a>网络

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 为存储帐户配置所需的传输层安全性 (TLS) 最低版本。  | 要求客户端使用更安全的 TLS 版本来对 Azure 存储帐户发出请求，具体方式是为该帐户配置最低的 TLS 版本。 有关详细信息，请参阅[为存储帐户配置所需的传输层安全性 (TLS) 最低版本](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)| - |
| 在所有存储帐户中启用“需要安全传输”选项 | 启用“需要安全传输”选项时，对存储帐户发出的所有请求都必须通过安全连接进行。 通过 HTTP 发出的任何请求都将失败。 有关详细信息，请参阅[在 Azure 存储中要求安全传输](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。 | [是](../../security-center/security-center-remediate-recommendations.md) |
| 启用防火墙规则 | 配置防火墙规则，将对存储帐户的访问权限限制给源自指定 IP 地址或范围的请求，或源自 Azure 虚拟网络 (VNet) 中一系列子网的请求。 有关配置防火墙规则的详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。 | - |
| 允许受信任的 Microsoft 服务访问此存储帐户 | 默认情况下，除非请求源自在 Azure VNet 中运行的服务或者源自允许的公共 IP 地址，否则为存储帐户启用防火墙规则会阻止数据传入请求。 被阻止的请求包括来自其他 Azure 服务、来自 Azure 门户、来自日志记录和指标服务等的请求。 可以通过添加例外，允许受信任的 Microsoft 服务访问此存储帐户，从而允许来自其他 Azure 服务的请求。 若要详细了解如何为受信任的 Microsoft 服务添加例外，请参阅[配置 Azure 存储防火墙和虚拟网络](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)。| - |
| 使用专用终结点 | 专用终结点会将 Azure VNet 中的专用 IP 地址分配给存储帐户。 它保护 VNet 和存储帐户之间通过专用链接传送的所有流量。 有关专用终结点的详细信息，请参阅[使用 Azure 专用终结点以私密方式连接到存储帐户](../../private-link/tutorial-private-endpoint-storage-portal.md)。 | - |
| 使用 VNet 服务标记 | 服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。 有关 Azure 存储支持的服务标记的详细信息，请参阅 [Azure 服务标记概述](../../virtual-network/service-tags-overview.md)。 有关演示如何使用服务标记创建出站网络规则的教程，请参阅[限制对 PaaS 资源的访问](../../virtual-network/tutorial-restrict-network-access-to-resources.md)。 | - |
| 限制对特定网络的网络访问 | 将网络访问限制为托管需要访问的客户端的网络可减少你的资源受到网络攻击的风险。 | [是](../../security-center/security-center-remediate-recommendations.md) |

## <a name="logging-and-monitoring"></a>日志记录和监视

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 跟踪请求的授权方式 | 启用 Azure 存储日志记录以跟踪对 Azure 存储发出的每个请求的授权方式。 日志可指示请求是匿名提出的，还是使用 OAuth 2.0 令牌、共享密钥或共享访问签名 (SAS) 提出的。 有关详细信息，请参阅 [Azure 存储分析日志记录](../common/storage-analytics-logging.md)。 | - |

## <a name="next-steps"></a>后续步骤

- [Azure 安全文档](../../security/index.yml)
- [安全开发文档](../../security/develop/index.yml)。
