---
title: 将 Azure 安全资源从 Azure 德国迁移到全球 Azure
description: 本文介绍如何将 Azure 安全资源从 Azure 德国迁移到全球 Azure。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: db8c13e214145df91f4b385f95c54e8084f3206c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "117029188"
---
# <a name="migrate-security-resources-to-global-azure"></a>将安全资源迁移到全球 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

本文中的信息可帮助你将 Azure 安全资源从 Azure 德国迁移到全球 Azure。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-active-directory"></a>Azure Active Directory

有关迁移 Azure Active Directory 的信息，请参阅[迁移标识](./germany-migration-identity.md#azure-active-directory)。

## <a name="key-vault"></a>Key Vault

Azure Key Vault 的某些功能无法从 Azure 德国迁移到全球 Azure。

### <a name="encryption-keys"></a>加密密钥

无法迁移加密密钥。 在目标区域中创建新密钥，然后使用这些密钥保护目标资源（例如，Azure 存储或 Azure SQL 数据库）。 安全地将数据从旧区域迁移到新的区域。

### <a name="application-secrets"></a>应用程序密钥

应用程序机密是证书、存储帐户密钥和其他与应用程序相关的机密。 在迁移过程中，请先在全球 Azure 中创建一个新的密钥保管库。 然后，完成以下任一操作：

- 创建新的应用程序机密。
- 阅读 Azure 德国中的当前机密，然后在新的保管库中输入值。

```powershell
Get-AzKeyVaultSecret -vaultname mysecrets -name Deploydefaultpw
```

参考信息：

- 通过完成 [Key Vault 教程](../key-vault/index.yml)，学习更多新知识。
- 查看 [Key Vault 概述](../key-vault/general/overview.md)。
- 查看 [Key Vault PowerShell cmdlet](/powershell/module/az.keyvault/)。

## <a name="vpn-gateway"></a>VPN 网关

目前不支持将 Azure VPN 网关实例从 Azure 德国迁移到全球 Azure。 建议在全球 Azure 中创建和配置 VPN 网关的新实例。

可以使用门户或 PowerShell 收集有关当前 VPN 网关配置的信息。 在 PowerShell 中，使用一组以 `Get-AzVirtualNetworkGateway*` 开头的 cmdlet。

确保更新本地配置。 此外，在更新 Azure 网络环境后，删除旧 IP 地址范围的所有现有规则。

参考信息：

- 通过完成 [VPN 网关教程](../vpn-gateway/index.yml)，学习更多新知识。
- 了解如何[创建站点到站点连接](../vpn-gateway/tutorial-site-to-site-portal.md)。
- 查看 [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) PowerShell cmdlet。
- 阅读博客文章[创建站点到站点连接](/archive/blogs/ralfwi/connecting-clouds)。
  
## <a name="application-gateway"></a>应用程序网关

目前不支持将 Azure 应用程序网关实例从 Azure 德国迁移到全球 Azure。 建议在全球 Azure 中创建和配置新网关。

可以使用门户或 PowerShell 收集有关当前网关配置的信息。 在 PowerShell 中，使用一组以 `Get-AzApplicationGateway*` 开头的 cmdlet。

参考信息：

- 通过完成[应用程序网关教程](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)，学习更多新知识。
- 了解如何[创建应用程序网关](../application-gateway/quick-create-portal.md)。
- 查看 [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) PowerShell cmdlet。

## <a name="next-steps"></a>后续步骤

了解用于在以下服务类别中迁移资源的工具、方法和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)