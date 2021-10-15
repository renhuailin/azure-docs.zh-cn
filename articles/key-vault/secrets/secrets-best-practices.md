---
title: 机密管理的最佳做法 - Azure Key Vault | Microsoft Docs
description: 了解 Azure Key Vault 机密管理的最佳做法。
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 4735fcfd28830c8469ddfbed7b521a507b3ca521
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534679"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Key Vault 中机密管理的最佳做法

可以通过 Azure Key Vault 安全地将服务或应用程序凭据（例如密码、访问密钥）作为机密进行存储。 Key Vault 中的所有机密均已使用软件密钥进行加密。 使用 Key Vault 时，不再需要将安全信息存储在应用程序中。 无需将安全信息存储在应用程序中，因此也无需将此信息作为代码的一部分。

应存储在 Key Vault 中的机密的示例：

- 客户端应用程序机密
- 连接字符串
- 密码
- 访问密钥（Redis 缓存、Azure 事件中心、Azure Cosmos DB）
- SSH 密钥

任何其他敏感信息（如 IP 地址、服务名称和其他配置设置）和其他配置设置都应存储在 [Azure 应用程序配置](../../azure-app-configuration/overview.md)中，而不应存储在 Key Vault 中。

每个单独的 Key Vault 定义机密的安全边界。 建议为每个应用程序、每个区域、每个环境使用一个 Key Vault，以便为应用程序提供精细的机密隔离。

若要详细了解 Key Vault 的最佳做法，请参阅[使用 Key Vault 的最佳做法](../general/best-practices.md)。

## <a name="configuration-and-storing"></a>配置和存储 

建议以值的形式存储在轮换期间生成的凭据的动态部分。 示例包括客户端应用程序机密、密码和访问密钥。 任何相关的静态属性和标识符（如用户名、应用程序 ID、服务 URL）都应存储为机密标记，并在轮换期间复制到新版机密。

有关机密的详细信息，请参阅[关于 Azure Key Vault 机密](about-secrets.md)。

## <a name="secrets-rotation"></a>机密轮换
在整个应用程序生命周期中，机密通常作为环境变量或配置设置存储在应用程序内存中，这使得它们对不需要的公开操作非常敏感。 由于机密对泄露或公开非常敏感，因此经常轮换机密非常重要，至少每 60 天需轮换一次。

有关机密轮换过程详细信息，请参阅[自动轮换使用两组身份验证凭据的资源的机密](tutorial-rotation-dual.md)。 

## <a name="access-and-network-isolation"></a>访问和网络隔离

可以通过指定哪些 IP 地址有权访问来减少保管库的曝光。 将防火墙配置为仅允许应用程序和相关服务访问保管库中的机密，以削弱攻击者访问机密的能力。

有关网络安全的详细信息，请参阅[配置 Azure Key Vault 网络设置](../general/how-to-azure-key-vault-network-security.md)。

此外，应用程序应遵循最低特权访问原则，只能具有读取机密所需的访问权限。 可以使用访问策略或 Azure 基于角色的访问控制来控制对机密的访问。 

有关 Azure Key Vault 中的访问控制的详细信息，请参阅：
- [使用 Azure 基于角色的访问控制提供对 Key Vault 密钥、证书和机密的访问权限](../general/rbac-guide.md)
- [分配 Key Vault 访问策略](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>服务限制和缓存
Key Vault 最初是根据 [Azure Key Vault 服务限制](../general/service-limits.md)中指定的限制创建的。 若要最大化吞吐率，建议采用以下两种最佳做法：
- 在应用程序中缓存机密至少 8 小时。
- 实现指数回退重试逻辑，以处理超出服务限制时的情况。

有关限制指南的详细信息，请参阅 [Azure Key Vault 限制指南](../general/overview-throttling.md)。

## <a name="monitoring"></a>监视
若要监视对机密的访问以及机密的生命周期，请启用 Key Vault 日志记录。 使用 [Azure Monitor](../../azure-monitor/overview.md) 在一个位置监视所有保管库中的所有机密活动。 或使用 [Azure 事件网格](../../event-grid/overview.md)监视机密生命周期，因为它可以与 Azure 逻辑应用和 Azure Functions 轻松集成。

有关详细信息，请参阅：
- [充当事件网格源的 Azure Key Vault](../../event-grid/event-schema-key-vault.md?tabs=event-grid-event-schema.md)
- [Azure Key Vault 日志记录](../general/logging.md)
- [Azure Key Vault 的监视和警报](../general/alert.md)

## <a name="backup-and-purge-protection"></a>备份和清除保护
打开[清除保护](../general/soft-delete-overview.md#purge-protection)可防止强行删除机密。 在保管库中更新、删除或创建机密时定期执行保管库的备份。

- 若要了解备份 Azure PowerShell 备份命令，请参阅[备份机密](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)。
- 若要了解备份 Azure CLI 备份命令，请参阅[备份机密](/cli/azure/keyvault/secret#az_keyvault_secret_backup)。

## <a name="learn-more"></a>了解更多
- [关于 Azure Key Vault 机密](about-secrets.md)
- [使用密钥保管库的最佳做法](../general/best-practices.md)
