---
title: 适用于 Blob 存储的安全建议
titleSuffix: Azure Storage
description: 了解适用于 Blob 存储的安全建议。 实施此指南将有助于你履行我们的共享职责模型中描述的安全职责。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 7b60913753580027d050770bc9d4909b5686bb46
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111888439"
---
# <a name="security-recommendations-for-blob-storage"></a>适用于 Blob 存储的安全建议

本文包含适用于 Blob 存储的安全建议。 实施执行建议将有助于你履行我们的共享职责模型中描述的安全职责。 有关 Microsoft 如何履行服务提供商责任的详细信息，请参阅[云端的责任共担](../../security/fundamentals/shared-responsibility.md)。

包含在本文中的某些建议可能受 Azure 安全中心的自动监视。 在保护你在 Azure 中的资源方面，Azure 安全中心是第一道防线。 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../../security-center/security-center-introduction.md)

Azure 安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向你提供有关如何解决这些安全漏洞的建议。 有关 Azure 安全中心建议的详细信息，请参阅 [Azure 安全中心的安全性建议](../../security-center/security-center-recommendations.md)。

## <a name="data-protection"></a>数据保护

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 使用 Azure 资源管理器部署模型 | 使用 Azure 资源管理器部署模型创建新的存储帐户，以用于重要的安全增强功能，包括高级的 Azure 基于角色的访问控制 (Azure RBAC) 和审核、基于资源管理器的部署和治理、托管标识访问权限、用于存储机密的 Azure Key Vault 访问权限、用于访问 Azure 存储数据和资源的基于 Azure AD 的身份验证和授权。 如果可能，请迁移使用经典部署模型的现有存储帐户以使用 Azure 资源管理器。 有关 Azure 资源管理器的详细信息，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/management/overview.md)。 | - |
| 为所有存储帐户启用 Azure Defender | 适用于 Azure 存储的 Azure Defender 提供额外的安全智能层，可检测是否存在访问或攻击存储帐户的异常和潜在有害尝试行为。 当活动发生异常时 Azure 安全中心中将触发这些安全警报，还会通过电子邮件发送给订阅管理员，内容包括可疑活动的详细信息以及如何调查和修正威胁的建议。 有关详细信息，请参阅[配置适用于 Azure 存储的 Azure Defender](../common/azure-defender-storage-configure.md)。 | [是](../../security-center/security-center-remediate-recommendations.md) |
| 为 Blob 启用软删除 | Blob 的软删除允许在删除 Blob 数据后将其恢复。 若要详细了解 Blob 的软删除，请参阅 [Azure 存储 Blob 的软删除](./soft-delete-blob-overview.md)。 | - |
| 为容器启用软删除 | 容器软删除允许在删除容器后将其恢复。 有关容器软删除的详细信息，请参阅[容器软删除（预览版）](./soft-delete-container-overview.md)。 | - |
| 锁定存储帐户以防止意外或恶意删除或配置更改 | 将 Azure 资源管理器锁定应用于你的存储帐户，以防止帐户发生意外或恶意删除或配置更改。 锁定存储帐户并不会阻止删除该帐户中的数据。 此操作只阻止删除帐户本身。 有关详细信息，请参阅[将 Azure 资源管理器锁定应用于存储帐户](../common/lock-account-resource.md)。
| 在不可变 Blob 中存储业务关键数据 | 配置法定保留和基于时间的保留策略，以 WORM（一次写入，多次读取）状态存储 Blob 数据。 在保留时间间隔期间内，可以读取即时存储的 Blob，但不能对其进行修改或删除。 有关详细信息，请参阅[使用不可变的存储来存储业务关键型 Blob 数据](storage-blob-immutable-storage.md)。 | - |
| 需要安全传输 (HTTPS) 到存储帐户 | 在需要针对存储帐户进行安全传输时，必须通过 HTTPS 向存储帐户发出所有请求。 任何通过 HTTP 发出的请求都会被拒绝。 Microsoft 建议始终对所有存储帐户都要求采用安全传输。 有关详细信息，请参阅[要求采用安全传输以确保安全连接](../common/storage-require-secure-transfer.md)。 | - |
| 将共享访问签名 (SAS) 令牌限制为仅用于 HTTPS 连接 | 当客户端使用 SAS 令牌访问 Blob 数据时要求使用 HTTPS 有助于最大程度地降低被窃听的风险。 有关详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。 | - |

## <a name="identity-and-access-management"></a>标识和访问管理

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 使用 Azure Active Directory (Azure AD) 授权对 Blob 数据的访问 | 与用于授权对 Blob 存储的请求的共享密钥相比，Azure AD 提供了卓越的安全性和易用性。 有关详细信息，请参阅[使用 Azure Active Directory 授予对 Azure Blob 和队列的访问权限](../common/storage-auth-aad.md)。 | - |
| 通过 Azure RBAC 向 Azure AD 安全主体分配权限时，请记住“最低权限”原则 | 将角色分配给用户、组或应用程序时，只向该安全主体授予执行任务所需的权限。 限制对资源的访问有助于防止意外和恶意滥用数据。 | - |
| 使用用户委托 SAS 授予客户端对 Blob 数据的有限访问权限 | 用户委托 SAS 使用 Azure Active Directory (Azure AD) 凭据以及为 SAS 指定的权限进行保护。 用户委托 SAS 在其作用域和功能方面类似于服务 SAS，但相对于服务 SAS 具有安全优势。 有关详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 | - |
| 使用 Azure Key Vault 保护帐户访问密钥 | Microsoft 建议使用 Azure AD 对 Azure 存储的请求进行授权。 但是，如果必须使用共享密钥授权，请使用 Azure Key Vault 保护帐户密钥。 可以在运行时从密钥保管库检索密钥，而不是将其与应用程序一起保存。 有关 Azure Key Vault 的详细信息，请参阅 [Azure Key Vault 概述](../../key-vault/general/overview.md)。 | - |
| 定期重新生成帐户密钥 | 定期轮换帐户密钥可以降低向恶意参与者公开数据的风险。 | - |
| 禁止共享密钥授权 | 当禁止对某个存储帐户进行共享密钥授权时，Azure 存储将拒绝向该帐户发出的所有使用帐户访问密钥进行授权的后续请求。 只有通过 Azure AD 进行授权的安全请求才会成功。 有关详细信息，请参阅[阻止对 Azure 存储帐户进行共享密钥授权](../common/shared-key-authorization-prevent.md)。 | - |
| 向 SAS 分配权限时，请记住最低权限原则 | 创建 SAS 时，请仅指定客户端执行其功能所需的权限。 限制对资源的访问有助于防止意外和恶意滥用数据。 | - |
| 为发布给客户端的任何 SAS 制定吊销计划 | 如果 SAS 遭到泄露，需要尽快撤销该 SAS。 要撤销用户委托 SAS，请撤销用户委托密钥，以使与该密钥关联的所有签名快速失效。 要撤销与存储的访问策略关联的服务 SAS，可以删除存储的访问策略，重命名策略或将其到期时间更改为过去的时间。 有关详细信息，请参阅[使用共享访问签名 (SAS) 授予对 Azure 存储资源的有限访问权限](../common/storage-sas-overview.md)。  | - |
| 如果服务 SAS 与存储的访问策略没有关联，请将到期时间设置为一小时或更短 | 无法撤销与存储的访问策略没有关联的服务 SAS。 因此，建议限制到期时间，以使 SAS 的有效时间不超过一小时。 | - |
| 禁止对容器和 Blob 的匿名公共读取访问 | 对容器及其 Blob 的匿名公共读取访问权限向任何客户端授予对这些资源的只读访问权限。 除非方案需要，否则请避免启用公共读取访问权限。 若要了解如何禁止对存储帐户进行匿名公共访问，请参阅[配置对容器和 blob 的匿名公共读取访问](anonymous-read-access-configure.md)。  | - |

## <a name="networking"></a>网络

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 为存储帐户配置所需的传输层安全性 (TLS) 最低版本。  | 要求客户端使用更安全的 TLS 版本来对 Azure 存储帐户发出请求，具体方式是为该帐户配置最低的 TLS 版本。 有关详细信息，请参阅[为存储帐户配置所需的传输层安全性 (TLS) 最低版本](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)| - |
| 在所有存储帐户中启用“需要安全传输”选项 | 启用“需要安全传输”选项时，对存储帐户发出的所有请求都必须通过安全连接进行。 通过 HTTP 发出的任何请求都将失败。 有关详细信息，请参阅[在 Azure 存储中要求安全传输](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 | [是](../../security-center/security-center-remediate-recommendations.md) |
| 启用防火墙规则 | 配置防火墙规则以将存储帐户的访问权限限制于源自指定的 IP 地址或范围，或源自 Azure 虚拟网络 (VNet) 中一系列子网的请求。 有关配置防火墙规则的详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 | - |
| 允许受信任的 Microsoft 服务访问此存储帐户 | 默认情况下，除非请求源自在 Azure 虚拟网络 (VNet) 中运行的服务或者源自允许的公共 IP 地址，否则启用存储帐户的防火墙规则会阻止数据传入请求。 被阻止的请求包括来自其他 Azure 服务、来自 Azure 门户、来自日志记录和指标服务等的请求。 可以通过添加例外，允许受信任的 Microsoft 服务访问此存储帐户，从而允许来自其他 Azure 服务的请求。 若要详细了解如何为受信任的 Microsoft 服务添加例外，请参阅[配置 Azure 存储防火墙和虚拟网络](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。| - |
| 使用专用终结点 | 专用终结点将 Azure 虚拟网络 (VNet) 中的专用 IP 地址分配给存储帐户。 它通过专用链接保护 VNet 和存储帐户之间的所有流量。 有关专用终结点的详细信息，请参阅[使用 Azure 专用终结点以私密方式连接到存储帐户](../../private-link/tutorial-private-endpoint-storage-portal.md)。 | - |
| 使用 VNet 服务标记 | 服务标记代表给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。 有关 Azure 存储支持的服务标记的详细信息，请参阅 [Azure 服务标记概述](../../virtual-network/service-tags-overview.md)。 有关演示如何使用服务标记创建出站网络规则的教程，请参阅[限制对 PaaS 资源的访问](../../virtual-network/tutorial-restrict-network-access-to-resources.md)。 | - |
| 限制对特定网络的网络访问 | 将网络访问限制为托管需要访问的客户端的网络可减少你的资源受到网络攻击的风险。 | [是](../../security-center/security-center-remediate-recommendations.md) |
| 配置网络路由首选项 | 可以为 Azure 存储帐户配置网络路由首选项，以指定网络流量如何使用 Microsoft 全球网络或 Internet 路由通过 Internet 从客户端路由到你的帐户。 有关详细信息，请参阅[为 Azure 存储配置网络路由首选项](../common/network-routing-preference.md)。 | - |

## <a name="loggingmonitoring"></a>日志记录/监视

| 建议 | 注释 | 安全中心 |
|-|----|--|
| 跟踪请求的授权方式 | 启用 Azure 存储日志记录以跟踪对 Azure 存储发出的每个请求的授权方式。 日志可指示请求是匿名提出的，还是使用 OAuth 2.0 令牌、共享密钥或共享访问签名 (SAS) 提出的。 有关详细信息，请参阅[通过 Azure Monitor 监视 Azure Blob 存储](monitor-blob-storage.md)或[采用经典监视的 Azure 存储分析日志记录](../common/storage-analytics-logging.md)。 | - |
| 在 Azure Monitor 中设置警报 | 配置日志警报，以便按设置的频率评估资源日志，并根据结果触发警报。 有关详细信息，请参阅 [Azure Monitor 中的日志警报](../../azure-monitor/alerts/alerts-unified-log.md)。 | - |

## <a name="next-steps"></a>后续步骤

- [Azure 安全文档](../../security/index.yml)
- [安全开发文档](../../security/develop/index.yml)。
