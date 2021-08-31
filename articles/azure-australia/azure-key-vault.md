---
title: Azure 澳大利亚的 Azure Key Vault
description: 关于在澳大利亚区域根据澳大利亚政府政策、法规和法律的具体要求，配置和使用 Azure Key Vault 来进行密钥管理的指导。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: f14a2264769e1bb1fca8e4372ba1359e1b99e560
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727285"
---
# <a name="azure-key-vault-in-azure-australia"></a>Azure 澳大利亚的 Azure Key Vault

安全存储加密密钥和管理加密密钥生命周期是加密系统中至关重要的元素。  在 Azure 中提供此功能的服务是 Azure Key Vault。 Key Vault 已经为 PROTECTED 具备了 IRAP 安全访问权限并且获得了 ACSC 认证。  本文概述了在使用 Key Vault 遵循澳大利亚信号局 (ASD) 的[信息安全手册控制](https://acsc.gov.au/infosec/ism/) (ISM) 时的重要注意事项。

Azure Key Vault 是一项保护加密密钥和机密的云服务。 由于此数据是敏感数据和业务关键数据，因此，Key Vault 实现了对密钥保管库的安全访问，只允许授权用户和应用程序进行访问。 有三个由 Key Vault 管理和控制的主要项目：

- 密钥
- 机密
- certificates

本文将重点介绍使用 Key Vault 来管理密钥。

![Azure Key Vault](media/azure-key-vault-overview.png)

图 1 – Azure Key Vault

## <a name="key-design-considerations"></a>关键设计考虑因素

### <a name="deployment-options"></a>部署选项

有两个用于创建 Azure Key Vault 的选项。 这两个选项都使用硬件安全模块 (HSM) 的 nCipher nShield 系列，它们是经过美国联邦信息处理标准 (FIPS) 验证的，并且经批准用于在 PROTECTED 环境中存储密钥。 选项包括：

- 软件保护的保管库：通过了 FIPS 140-2 级别 1 验证。 存储在 HSM 上的密钥。 加密和解密操作在 Azure 上的计算资源中执行。
- HSM 保护的保管库：通过了 FIPS 140-2 级别 2 验证。 存储在 HSM 上的密钥。 加密和解密操作在 HSM 上执行。

Key Vault 支持 Rivest-Shamir-Adleman (RSA) 和椭圆曲线加密 (ECC) 密钥。 默认值为 RSA 2048 位密钥，但有 RSA 3072 位、RSA 4096 位和 ECC 密钥的高级选项。  所有密钥都符合 ISM 控制，但首选椭圆曲线密钥。

### <a name="resource-operations"></a>资源操作

Azure Key Vault 中涉及多个角色：

- Key Vault 管理员：管理保管库的生命周期
- 密钥管理员：管理保管库中密钥的生命周期
- 开发人员/操作员：将保管库中的密钥集成到应用程序和服务中
- 审核者：监视密钥使用情况和访问
- 应用程序：使用密钥来保护信息

Azure Key Vault 通过两个单独的接口进行保护：

- 管理平面：此平面负责管理保管库，并受 Azure RBAC 保护。
- 数据平面：此平面负责管理和访问保管库中的项目。  使用 Key Vault 访问策略保护安全。

根据 ISM 的要求，调用方（用户或应用程序）需要先经过适当的身份验证和授权，然后才能够通过任意一个平面来获取密钥保管库的访问权限。

Azure RBAC 有一个 Key Vault 的内置角色（[Key Vault 参与者](../role-based-access-control/built-in-roles.md#key-vault-contributor)），用于控制对 Key Vault 的管理。 建议创建与更精细的角色一致的自定义角色来管理 Key Vault。

>[!WARNING]
>在通过 Key Vault 访问策略启用了对密钥的访问权限时，用户或应用程序对该密钥保管库中的所有密钥就都会具有这种访问权限（例如，如果用户有“删除”访问权限，那么，他们就可以删除所有密钥）。  因此，应该部署多个密钥保管库，以与安全域/边界保持一致。

### <a name="networking"></a>网络

可以配置 Key Vault 防火墙和虚拟网络来控制对数据平面的访问。  可以允许对指定网络上用户或应用程序的访问，而同时拒绝对所有其他网络上用户或应用程序的访问。 如果启用了“允许受信任的服务”，则[受信任的服务](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)是此控制的一个例外情况。  虚拟网络控制不应用于管理平面。

应该将对 Key Vault 的访问显式限制到其用户或应用程序需要访问密钥的最小网络集。

### <a name="bring-your-own-key-byok"></a>创建自己的密钥 (BYOK)

Key Vault 支持 BYOK。  利用 BYOK，用户可以从其现有密钥基础结构导入密钥。 BYOK 工具集支持将密钥从外部 HSM（例如，脱机工作站生成的密钥）安全传输和导入到 Key Vault 中。

请转到 Microsoft 下载中心并下载适用于澳大利亚的 [Azure Key Vault BYOK 工具集](https://www.microsoft.com/download/details.aspx?id=45345)。 要下载的包名称及其对应的 SHA-256 包哈希为：

|包名称|SHA-256 哈希|
|---|---|
|KeyVault-BYOK-Tools-Australia.zip|CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A|
|

### <a name="key-vault-auditing-and-logging"></a>Key Vault 审核和记录

ACSC 要求英联邦实体使用相应的 Azure 服务来承担对其 Azure 工作负载的实时监视和报告。

记录是通过在密钥值上启用“AuditEvent”诊断设置来启用。  审核事件将会记录到指定的存储帐户。  应该根据数据保留策略来设置“RetentionInDays”期限。  管理平面和数据平面上的[操作](../key-vault/general/logging.md#interpret-your-key-vault-logs)都会被审核和记录。 [Azure Monitor 中的 Azure Key Vault 解决方案](../azure-monitor/insights/key-vault-insights-overview.md)可用于查看 Key Vault AuditEvent 日志。  许多其他 Azure 服务可用于处理和分发 Key Vault AuditEvent。

### <a name="key-rotation"></a>密钥轮换

将密钥存储在 Key Vault 中，这样就为维护应用程序外部的密钥提供了单一的点，使密钥能够更新，而不会影响应用程序的行为。 将密钥存储在 Azure Key Vault 中，可以实现支持密钥轮换的各种策略：

- 手动
- 通过 API 以编程方式
- 自动化脚本（例如，使用 PowerShell 和 Azure 自动化）

这些选项使密钥能够定期轮换，以满足合规性要求，或者如果担心密钥可能已经泄露，则临时轮换密钥。

#### <a name="key-rotation-strategies"></a>密钥轮换策略

为 KeyVault 中存储的密钥制定相应的密钥轮换策略至关重要。  使用错误的密钥将会导致信息被错误地解密，而丢失密钥则可能会导致完全失去信息访问权限。  不同方案的密钥轮换策略的示例包括：

- 即时数据：不稳定的信息在 2 方之间传输。  在轮换密钥时，双方都必须具有从密钥保管库中同步检索更新后的密钥的机制。
- 静态数据：一方存储加密数据，并在将来解密这些数据进行使用。  在密钥要轮换时，必须使用旧密钥解密数据，然后使用新的轮换密钥加密数据。  有方法可以最大程度地减少使用密钥加密密钥进行解密/加密的过程所产生的影响（请参阅示例）。  Microsoft 管理与 Azure 存储的密钥轮换相关的大多数过程（请参阅...）
- 访问密钥：许多 Azure 服务都具有可以存储在 Key Vault 中的访问密钥（例如 CosmosDB）。  这些 Azure 服务具有主访问密钥和辅助访问密钥。  这两种密钥不要同时轮换，这一点很重要。  因此，应该先轮换一个密钥，在一段时间之后，密钥操作经过了验证，然后就可以轮换第二个密钥。

### <a name="high-availability"></a>高可用性

ISM 具有多个与业务连续性相关的控制。
Azure Key Vault 具有多层冗余，其内容在区域内复制到辅助[配对区域](../best-practices-availability-paired-regions.md)。

在密钥保管库处于故障转移状态时，该保管库处于只读模式，并且将会在主服务将还原后返回到读写模式。

ISM 具有多个与备份相关的控制。  为保管库及其密钥制定并执行相应的备份/还原计划至关重要。

## <a name="key-lifecycle"></a>密钥生命周期

### <a name="key-operations"></a>密钥操作

Key Vault 支持对密钥执行以下操作：

- 创建：允许客户端在 Key Vault 中创建密钥。 密钥的值由 Key Vault 生成，存储但不发布到客户端。 可在 Key Vault 中创建非对称密钥。
- 导入：允许客户端将现有密钥导入到 Key Vault。 非对称密钥可以使用 JWK 构造中的多种不同的打包方法导入到 Key Vault。
- 更新：允许具有足够权限的客户端修改与以前存储在 Key Vault 中的密钥相关联的元数据（密钥属性）。
- 删除：允许具有足够权限的客户端删除 Key Vault 中的密钥。
- 列出：允许客户端列出给定 Key Vault 中的所有密钥。
- 列出版本：允许客户端列出给定 Key Vault 中的给定密钥的所有版本。
- 获取：允许客户端检索 Key Vault 中的给定密钥的公共部分。
- 备份：导出受保护窗体中的密钥。
- 还原：导入以前备份的密钥。

有一组相应的权限，可以将它们授予使用 Key Vault 访问控制条目的用户、服务主体或应用程序，以使他们能够执行密钥操作。

Key Vault 具有软删除功能，可用于恢复已删除的保管库和密钥。 在默认情况下，“软删除”处于未启用状态，但在启用后，对象就会保存 90 天（保持期），同时会显示为待删除状态。  如果禁用了“清除保护”选项，则另外一项权限“清除”就会允许永久删除密钥 。

创建或导入现有密钥会创建该密钥的新版本。

### <a name="cryptographic-operations"></a>加密操作

Key Vault 还支持使用密钥的加密操作：

- 签名和验证：此操作是“签名哈希”或“验证哈希”。 Key Vault 不支持内容的哈希作为签名创建过程的一部分。
- 密钥加密/包装：此操作用于保护另一个密钥。
- 加密和解密：存储的密钥用于加密或解密某一个数据块

有一组相应的权限，可以将它们授予使用 Key Vault 访问控制条目的用户、服务主体或应用程序，以使他们能够执行加密操作。

可以设置三个密钥属性来控制某个密钥是否已启用以及是否可用于加密操作：

- **enabled**
- nbf：在指定日期之前未启用
- exp：过期日期

## <a name="storage-and-keys"></a>存储和密钥

客户管理的密钥具有更大的灵活性，利用这种密钥，可以评估和管理要控制的密钥。 利用它们，还可以审核用于保护数据的加密密钥。
存储和 Key Vault 中存储的密钥有三个方面：

- Key Vault 管理的存储帐户密钥
- 静态数据的 Azure 存储服务加密 (SSE)
- 托管磁盘和 Azure 磁盘加密

Key Vault 的 Azure 存储帐户密钥管理是 Key Vault 密钥服务的扩展，它支持存储帐户密钥的同步和重新生成（轮换）。  在 [Azure 存储与 Azure Active Directory 集成](../storage/blobs/authorize-access-azure-active-directory.md)（预览版）发布后，建议使用它，因为它提供了出色的安全性和易用性。
SSE 使用两个密钥来管理静态数据的加密：

- 密钥加密密钥 (KEK)
- 数据加密密钥 (DEK)

虽然 Microsoft 管理 DEK，但 SSE 可选择使用可以存储在 Key Vault 中的客户管理的 KEK。 这样就能根据相应的合规性策略实现 Azure Key Vault 中密钥的轮换。 在轮换密钥时，Azure 存储会重新加密该存储帐户的帐户加密密钥。 这不会导致重新加密所有数据，并且无需执行其他操作。

SSE 用于托管磁盘，但不支持客户管理的密钥。  可以通过 Key Vault 中客户管理的 KEK 密钥使用 Azure 磁盘加密来完成托管磁盘的加密。

## <a name="next-steps"></a>后续步骤

查看有关[标识联合](identity-federation.md)的文章

查看[参考库](reference-library.md)中的其他 Azure Key Vault 文档和教程