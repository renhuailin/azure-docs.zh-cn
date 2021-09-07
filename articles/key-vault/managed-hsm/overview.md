---
title: Azure 托管 HSM 概述 - Azure 托管 HSM | Microsoft Docs
description: Azure 托管 HSM 是一项云服务，用于保护云应用程序的加密密钥。
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 06/21/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: 5cbb4496b9317e64c0cdec8e78bf825d19d8bd38
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258618"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>什么是 Azure Key Vault 托管的 HSM？

Azure Key Vault 托管 HSM 是一项云服务，它具备完全托管、高度可用、单租户和符合标准的特点。有了该服务，你就可以使用通过了 FIPS 140-2 级别 3 验证的 HSM（硬件安全模块）来保护云应用程序的加密密钥。 有关定价信息，请参阅 [Azure Key Vault 定价页](https://azure.microsoft.com/pricing/details/key-vault/)上的“托管的 HSM 池”部分。 

## <a name="why-use-managed-hsm"></a>为什么要使用托管 HSM？

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>完全托管、高度可用、单租户 HSM 即服务

- **完全托管**：HSM 预配、配置、修补和维护由服务处理。 
- **高度可用和区域复原能力**（支持可用性区域的位置）：每个 HSM 群集由跨至少两个可用性区域的多个 HSM 分区组成。 如果硬件出现故障，HSM 群集的成员分区将自动迁移到正常节点。
- **单租户**：每个托管 HSM 实例都专用于单个客户，并包含由多个 HSM 分区组成的群集。 每个 HSM 群集都使用特定于客户的单独安全域，该域以加密方式隔离每个客户的 HSM 群集。


### <a name="access-control-enhanced-data-protection--compliance"></a>访问控制、增强的数据保护与合规性

- **集中式密钥管理**：在一个位置管理整个组织内的重要高价值密钥。 通过精细控制每个密钥的权限，可根据“最低特权访问”原则控制每个密钥的访问权限。
- **独立的访问控制**：托管 HSM“本地 RBAC”访问控制模型允许指定的 HSM 群集管理员完全控制甚至连管理组、订阅或资源组管理员都无法重写的 HSM。
- 专用终结点：使用专用终结点从虚拟网络中运行的应用程序以私密方式安全地连接到托管 HSM。
- 通过 FIPS 140-2 级别 3 验证的 HSM：保护你的数据并满足通过 FIPS（联邦信息保护标准）140-2 级别 3 验证的 HSM 的合规性要求。 托管 HSM 使用 Marvell LiquidSecurity HSM 适配器。
- **监视和审核**：与 Azure Monitor 完全集成。 通过 Azure Monitor 获取所有活动的完整日志。 将 Azure Log Analytics 用于分析和警报。
- **数据驻留**：托管 HSM 不会在客户部署 HSM 实例的区域之外存储/处理客户数据。

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>与 Azure 和 Microsoft PaaS/SaaS 服务集成 

- 生成（或使用 [BYOK](hsm-protected-keys-byok.md) 导入）密钥，并使用它们来加密 Azure 服务中的静态数据，如 [Azure 存储](../../storage/common/customer-managed-keys-overview.md)、[Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md) 和 [Azure 信息保护](/azure/information-protection/byok-price-restrictions)。

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>使用与 Key Vault 相同的 API 和管理接口

- 轻松迁移使用保管库（多租户）的现有应用程序，以使用托管 HSM。
- 对所有应用程序使用相同的应用程序开发和部署模式，而无论正在使用何种密钥管理解决方案：多租户保管库还是单租户托管 HSM

### <a name="import-keys-from-your-on-premise-hsms"></a>从本地 HSM 导入密钥

- 在本地 HSM 中生成受 HSM 保护的密钥，并将其安全地导入托管 HSM

## <a name="next-steps"></a>后续步骤
- 请参阅[快速入门：使用 Azure CLI 预配和激活托管 HSM](quick-create-cli.md)，以创建和激活托管 HSM
- 请参阅[使用 Azure Key Vault 托管 HSM 的最佳做法](best-practices.md)
- [托管 HSM 状态](https://status.azure.com)
- [托管 HSM 服务级别协议](https://azure.microsoft.com/support/legal/sla/key-vault-managed-hsm/v1_0/)
- [托管 HSM 区域可用性](https://azure.microsoft.com/global-infrastructure/services/?products=key-vault)
