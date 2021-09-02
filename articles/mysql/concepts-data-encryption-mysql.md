---
title: 使用客户托管密钥进行数据加密 - Azure Database for MySQL
description: 使用客户托管密钥进行 Azure Database for MySQL 数据加密，可创建自己的密钥 (BYOK) 来保护静态数据。 它还允许组织在管理密钥和数据时实现职责分离。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 6d2fca3ed64711133f1701446ebea61c28a3dcab
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674307"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>使用客户托管密钥进行 Azure Database for MySQL 数据加密

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

针对 Azure Database for MySQL 使用客户托管密钥进行数据加密，可创建自己的密钥 (BYOK) 来保护静态数据。 它还允许组织在管理密钥和数据时实现职责分离。 通过客户托管的加密，密钥的生命周期、密钥使用权限以及对密钥操作的审核都由你负责和完全控制。

在服务器级别使用客户托管密钥为 Azure Database for MySQL 进行数据加密。 对于给定的服务器，客户托管密钥（称为密钥加密密钥 (KEK)）用于加密服务使用的数据加密密钥 (DEK)。 KEK 是一种非对称密钥，它存储在客户自有和客户管理的 [Azure Key Vault](../key-vault/general/security-features.md) 实例中。 本文稍后将更详细地描述密钥加密密钥 (KEK) 和数据加密密钥 (DEK)。

Key Vault 是一种基于云的外部密钥管理系统。 它具有高可用性，并为 RSA 加密密钥提供可扩展的安全存储，根据需要由 FIPS 140-2 级别 2 验证的硬件安全模块 (HSM) 提供支持。 它不允许直接访问存储的密钥，但为授权实体提供加密和解密服务。 Key Vault 可以生成密钥，并将其导入，或者[从本地 HSM 设备传输密钥](../key-vault/keys/hsm-protected-keys.md)。

> [!NOTE]
> 仅在“通用”和“内存优化”定价层中可用的“通用存储 v2（支持高达 16TB）”存储上支持此功能。 有关更多详细信息，请参阅[存储概念](concepts-pricing-tiers.md#storage)。 有关其他限制，请参阅[限制](concepts-data-encryption-mysql.md#limitations)部分。

## <a name="benefits"></a>优点

使用客户管理的密钥对 Azure Database for MySQL 进行数据加密提供以下优势：

* 数据访问完全由你控制，你可删除密钥并使数据库无法访问 
* 可完全控制密钥生命周期，包括根据公司策略轮替密钥
* 在 Azure Key Vault 中集中管理和整理密钥
* 可实现安全专员与 DBA 和系统管理员之间的职责分离


## <a name="terminology-and-description"></a>术语和说明

**数据加密密钥 (DEK)** ：对称 AES256 密钥，用于加密数据分区或数据块。 使用不同的密钥加密每个数据块可以增加加密分析攻击的难度。 资源提供程序或应用程序实例需要 DEK 访问权限才能加密和解密特定的块。 将 DEK 替换为新密钥时，只需使用新密钥对其关联的块中的数据重新加密。

**密钥加密密钥 (KEK)** ：用于加密 DEK 的加密密钥。 KEK 始终在 Key Vault 中，这使得 DEK 本身能得到加密和控制。 具有 KEK 访问权限的实体可能不是需要 DEK 的实体。 由于解密 DEK 需要 KEK，因此 KEK 实际上构成了一个单点机制：删除 KEK 即可删除 DEK。

DEK 使用 KEK 加密且单独存储。 只有有权访问 KEK 的实体才能解密这些 DEK。 有关详细信息，请参阅[静态加密中的安全性](../security/fundamentals/encryption-atrest.md)。

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>使用客户管理的密钥进行数据加密的工作原理

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/mysqloverview.png" alt-text="显示“创建自己的密钥”概述的关系图":::

若要使 MySQL 服务器使用存储在 Key Vault 中的客户托管密钥对 DEK 进行加密，Key Vault 管理员将授予服务器以下访问权限：

* **get**：用于检索密钥保管库中密钥的公共部分和属性。
* **wrapKey**：可加密 DEK。 加密的 DEK 存储在 Azure Database for MySQL 中。
* **unwrapKey**：可解密 DEK。 Azure Database for MySQL 需要解密的 DEK 对数据进行加密/解密

Key Vault 管理员还可[启用 Key Vault 审核事件的日志记录](../azure-monitor/insights/key-vault-insights-overview.md)，便于稍后对其进行审核。

当服务器配置为使用存储在 Key Vault 中的客户管理的密钥时，该服务器会将 DEK 发送到 Key Vault 进行加密。 Key Vault 返回存储在用户数据库中已加密的 DEK。 同样在必要时，服务器会将受保护的 DEK 发送到 Key Vault 进行解密。 如果启用了日志记录，则审核员可以使用 Azure Monitor 查看 Key Vault 审核事件日志。

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>为 Azure Database for MySQL 配置数据加密的要求

下面是配置 Key Vault 的要求：

* Key Vault 和 Azure Database for MySQL 必须属于同一个 Azure Active Directory (Azure AD) 租户。 不支持跨租户的 Key Vault 和服务器交互。 之后若要移动 Key Vault 资源，需要重新配置数据加密。
* 启用密钥保管库上的[软删除](../key-vault/general/soft-delete-overview.md)功能，将保留期设置为“90 天”，防止在意外删除密钥（或 Key Vault）时丢失数据。 默认情况下，被软删除的资源将保留 90 天，除非保留期显式设置为 <=90 天。 “恢复”和“清除”操作均自带与 Key Vault 访问策略关联的权限。 软删除功能默认关闭，但你可通过 PowerShell 或 Azure CLI 启用它（请注意，无法通过 Azure 门户启用）。
* 启用密钥保管库上的[清除保护](../key-vault/general/soft-delete-overview.md#purge-protection)功能，将保留期设置为“90 天”。 只有启用软删除后才能启用清除保护。 可以通过 Azure CLI 或 PowerShell 来启用它。 启用清除保护后，在保留期结束之前，无法清除处于已删除状态的保管库或对象。 软删除的保管库和对象仍可恢复，这可以确保遵循保留策略。 
* 通过使用其唯一的托管标识授予具有 get、wrapKey 和 unwrapKey 权限的密钥保管库的 Azure Database for MySQL 访问权限。 在 Azure 门户中，在 MySQL 上启用数据加密时，将自动创建唯一“服务”标识。 有关使用 Azure 门户时的详细分步说明，请参阅[为 MySQL 配置数据加密](howto-data-encryption-portal.md)。

下面是配置客户托管密钥的要求：

* 用于加密 DEK 的客户管理的密钥只能是非对称的 RSA 2048。
* 密钥激活日期（如果已设置）必须是过去的日期和时间。 未设置到期日期。
* 密钥必须处于“已启用”状态。
* 密钥必须具有[软删除](../key-vault/general/soft-delete-overview.md)功能并将保持期设置为“90 天”。这会隐式设置所需的键属性 recoveryLevel: “Recoverable”。 如果保留期设置为 < 90 天，则 recoveryLevel：“CustomizedRecoverable”不是必需的，因此请确保将保留期设置为“90 天”。
* 密钥必须[启用清除保护](../key-vault/general/soft-delete-overview.md#purge-protection)。
* 若要[导入现有密钥](/rest/api/keyvault/ImportKey/ImportKey)到密钥保管库，请确保以受支持的文件格式（`.pfx`、`.byok`、`.backup`）提供该密钥。

## <a name="recommendations"></a>建议

通过客户管理的密钥使用数据加密时，请查看下列 Key Vault 配置建议：

* 在 Key Vault 中设置资源锁可控制谁能删除该关键资源，并防止意外或未经授权的删除。
* 对所有加密密钥启用审核和报告功能。 Key Vault 提供可轻松注入到其他安全信息和事件管理工具的日志。 Azure Monitor Log Analytics 是已集成的服务的一个示例。
* 确保 Key Vault 和 Azure Database for MySQL 位于同一区域，以确保对 DEK 包装和解包操作的更快访问。
* 将 Azure KeyVault 锁定为仅“专用终结点和所选网络”，并仅允许“受信任的 Microsoft”服务保护资源。

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

下面是客户管理的密钥的配置建议：

* 将客户管理的密钥副本保存在安全的位置，或将其托管到托管服务。

* 如果 Key Vault 生成密钥，请在首次使用该密钥之前创建密钥备份。 只能将备份还原到 Key Vault。 要详细了解备份命令，请参阅 [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey)。

## <a name="inaccessible-customer-managed-key-condition"></a>无法访问客户管理的密钥的情形

在 Key Vault 中使用客户管理的密钥配置数据加密时，服务器必须保持联机状态才能持续访问该密钥。 如果服务器无法再访问 Key Vault 中客户管理的密钥，它将在 10 分钟内开始拒绝所有连接。 服务器会发出相应的错误消息，并将服务器状态更改为“无法访问”。 服务器达到此状态的一些原因包括：

* 如果我们为 Azure Database for MySQL 创建已启用数据加密的时间点还原服务器，则新创建的服务器将处于“无法访问”状态。 可以通过 [Azure 门户](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers)或 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) 修复此问题。
* 如果我们为 Azure Database for MySQL 创建已启用数据加密的只读副本，则副本服务器将处于“无法访问”状态。 可以通过 [Azure 门户](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers)或 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers) 修复此问题。
* 如果删除 KeyVault，Azure Database for MySQL 将无法访问密钥，并将转到“无法访问”状态。 恢复 [Key Vault](../key-vault/general/key-vault-recovery.md) 并重新验证数据加密，以使服务器“可用”。
* 如果我们从 KeyVault 中删除密钥，Azure Database for MySQL 将无法访问密钥，并将转到“无法访问”状态。 恢复[密钥](../key-vault/general/key-vault-recovery.md)并重新验证数据加密，以使服务器“可用”。
* 如果存储在 Azure KeyVault 中的密钥过期，则该密钥将变为无效，并且 Azure Database for MySQL 将转换为“无法访问”状态。 使用 [CLI](/cli/azure/keyvault/key#az_keyvault_key_set-attributes) 延长密钥到期日期，然后重新验证数据加密以使服务器“可用”。

### <a name="accidental-key-access-revocation-from-key-vault"></a>从 Key Vault 意外撤消密钥访问

可能会发生这样的情况：对 Key Vault 具有足够访问权限的人员通过下列方式意外禁用了服务器对密钥的访问：

* 从服务器撤消密钥保管库的 `get`、`wrapKey` 和 `unwrapKey` 权限。
* 删除密钥。
* 删除 Key Vault。
* 更改 Key Vault 的防火墙规则。
* 删除 Azure AD 中服务器的托管标识。

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>在 Key Vault 中监视客户管理的密钥

若要监视数据库状态并在透明数据加密保护程序访问权限丢失时发出警报，请配置以下 Azure 功能：

* [Azure 资源运行状况](../service-health/resource-health-overview.md)：在与数据库的第一次连接遭到拒绝后，已失去客户密钥访问权限的无法访问的数据库将显示为“无法访问”。
* [活动日志](../service-health/alerts-activity-log-service-notifications-portal.md)：对 Key Vault 中客户管理的密钥访问失败时，活动日志中会添加相应条目。 如果为这些事件创建警报，就可尽快恢复访问。

* [操作组](../azure-monitor/alerts/action-groups.md)：定义这些组，使其根据首选项向你发送通知和警报。

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>在 Key Vault 中使用客户托管密钥进行还原和复制

在使用客户存储在 Key Vault 中的托管密钥对 Azure Database for MySQL 进行加密后，还将对服务器的任何新创建的副本进行加密。 可以通过本地或异地还原操作，或通过只读副本创建此新副本。 可更改该副本，使其反映出用于加密的客户管理的新密钥。 当客户管理的密钥更改时，服务器的旧备份将开始使用最新的密钥。

为避免在还原或只读副本创建期间设置客户管理的数据加密时出现问题，有必要在源服务器和还原/副本服务器上执行以下步骤：

* 从源 Azure Database for MySQL 启动还原或只读副本创建过程。
* 使新创建的服务器（还原/副本）保持无法访问状态，因为其唯一标识尚未授予 Key Vault 的权限。
* 在还原/副本服务器上，重新验证数据加密设置中客户托管密钥，以确保为新创建的服务器提供对存储在 Key Vault 中的密钥的包装和解包权限。

## <a name="limitations"></a>限制

对于 Azure Database for MySQL，对使用客户管理的密钥 (CMK) 加密静态数据的支持有少数限制 -

* 对此功能的支持仅限“常规用途”和“内存优化”定价层。
* 此功能仅在支持通用存储 v2（最高 16 TB）的区域和服务器上受支持。 有关支持存储最多 16 TB 的 Azure 区域的列表，请参阅[此处](concepts-pricing-tiers.md#storage)文档中的“存储”部分

    > [!NOTE]
    > - [Azure 区域](concepts-pricing-tiers.md#storage)中创建的所有新 MySQL 服务器都支持通用存储 v2，因此也支持使用客户管理器密钥的加密。 时间点还原 (PITR) 服务器或只读副本不符合条件，尽管其在理论上是“新的”。
    > - 要验证预配的服务器是否支持通用存储 v2，可以在门户中访问“定价层”边栏选项卡，并查看预配服务器支持的最大存储大小。 如果可以将滑块向上移动到 4TB，则服务器是通用存储 v1，不支持通过客户管理的密钥进行加密。 但是，始终使用服务托管密钥对数据进行加密。 如果有任何疑问，请联系 AskAzureDBforMySQL@service.microsoft.com。

* 仅支持使用 RSA 2048 加密密钥进行加密。

## <a name="next-steps"></a>后续步骤

* 了解如何通过 [Azure 门户](howto-data-encryption-portal.md)和 [Azure CLI](howto-data-encryption-cli.md) 使用客户管理的密钥为 Azure Database for MySQL 设置数据加密。
* 了解 [Azure Database for MySQL - 单服务器](concepts-pricing-tiers.md#storage)的存储类型支持
