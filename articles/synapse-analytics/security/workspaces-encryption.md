---
title: Azure Synapse Analytics 加密
description: 一篇介绍 Azure Synapse Analytics 中的加密的文章
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 07/20/2021
ms.author: mesrivas
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: 55585013df7c0fde6cb2eea1a9598d494cb4b2cb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128583630"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics 工作区的加密

本文介绍：
* Synapse Analytics 工作区中静态数据的加密。
* 配置 Synapse 工作区以便启用使用客户管理的密钥进行加密。
* 管理用于对工作区中的数据进行加密的密钥。

## <a name="encryption-of-data-at-rest"></a>静态数据加密

完整的静态加密解决方案可确保数据从不以未加密形式持久保存。 对静态数据进行双重加密可以减轻威胁，这种加密采用两个独立的加密层，防止任何一个加密层遭受威胁。 Azure Synapse Analytics 使用客户管理的密钥为工作区中的数据提供了另一层加密。 此密钥在 [Azure Key Vault](../../key-vault/general/overview.md) 中受到保护，这样你就能掌握密钥的管理和轮换。

Azure 服务的第一层加密是通过平台管理的密钥实现的。 默认情况下，Azure 存储帐户中的 Azure 磁盘和数据会自动完成静态加密。 请参阅 [Azure 加密概述](../../security/fundamentals/encryption-overview.md)，了解加密在 Microsoft Azure 中的运用方式。

## <a name="azure-synapse-encryption"></a>Azure Synapse 加密

本部分将帮助你更好地了解如何在 Synapse 工作区中启用并强制实施客户管理的密钥加密。 此加密使用 Azure Key Vault 中生成的现有密钥或新密钥。 使用单个密钥对工作区中的所有数据进行加密。 Synapse 工作区支持 RSA 2048 和3072 字节大小的密钥，以及 RSA-HSM 密钥。

> [!NOTE]
> Synapse 工作区不支持使用 EC、EC-HSM 和 oct-HSM 密钥进行加密。 

以下 Synapse 组件中的数据是通过在工作区级别配置的客户管理的密钥加密的：
* SQL 池
 * 专用 SQL 池
 * 无服务器 SQL 池
* Apache Spark 池
* Azure 数据工厂集成运行时、管道、数据集。

## <a name="workspace-encryption-configuration"></a>工作区加密配置

可将工作区配置为在创建工作区时启用使用客户管理的密钥的双重加密。 创建新工作区时，请在“安全性”选项卡上启用使用客户管理的密钥进行双重加密的功能。 你可以选择输入密钥标识符 URI，或从工作区所在的区域中的密钥保管库列表中进行选择。 Key Vault 本身需要启用清除保护。

> [!IMPORTANT]
> 创建工作区后，无法更改双重加密的配置设置。

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="此示意图展示的是要为工作区启用使用客户管理的密钥进行双重加密所必须选择的选项。" lightbox="./media/workspaces-encryption/workspaces-encryption.png":::

### <a name="key-access-and-workspace-activation"></a>密钥访问和工作区激活

在使用客户管理的密钥的 Azure Synapse 加密模型中，工作区会根据需要访问 Azure Key Vault 中的密钥以完成加密和解密。 要让工作区能访问这些密钥，可以采用访问策略或 [Azure Key Vault RBAC 访问](../../key-vault/general/rbac-guide.md)。 通过 Azure Key Vault 访问策略授予权限时，请在创建策略时选择[“仅限应用程序”](../../key-vault/general/security-features.md#key-vault-authentication-options)选项（选择工作区的托管标识，且不要将其添加为已授权的应用程序）。

 在激活工作区之前，必须向工作区托管标识授予访问密钥保管库所需的权限。 这种分阶段的工作区激活方法可以确保工作区中的数据是通过客户管理的密钥进行加密的。 请注意，可以为专用 SQL 池启用或禁用加密（默认情况下每个池都不启用加密）。

#### <a name="using-a-user-assigned-managed-identity"></a>使用用户分配的托管标识
工作区可配置为使用[用户分配的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)访问存储在 Azure Key Vault 中的客户管理的密钥。 请配置用户分配的托管标识，以避免在使用通过客户管理的密钥进行的双重加密时分阶段激活 Azure Synapse 工作区。 若要将用户分配的托管标识分配给 Azure Synapse 工作区，则需要“托管标识参与者”内置角色。
> [!NOTE]
> 当 Azure Key Vault 位于防火墙后面时，无法将用户分配的托管标识配置为访问客户管理的密钥。

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption-uami.png" alt-text="此图显示了为了使工作区能够将用户分配的托管标识用于通过客户管理的密钥进行的双重加密而必须选择的选项。" lightbox="./media/workspaces-encryption/workspaces-encryption-uami.png":::


#### <a name="permissions"></a>权限

若要加密或解密静态数据，托管标识必须具有以下权限：
* 包装密钥（用于在新建密钥时将密钥插入 Key Vault）。
* 解包密钥（用于获取解密所需的密钥）。
* 获取（用于读取密钥的公共部分）

#### <a name="workspace-activation"></a>工作区激活

如果在创建工作区期间未将用户分配的托管标识配置为访问客户管理的密钥，则工作区会保持“挂起”状态，直到激活成功为止。 必须先激活工作区，然后才能使用完整的功能。 例如，激活成功后，只能创建新的专用 SQL 池。 向工作区托管标识授予对密钥保管库的访问权限，并单击工作区 Azure 门户横幅中的激活链接。 激活成功完成后，工作区便可供使用并保证其中的所有数据都使用客户管理的密钥进行保护。 如前文所述，密钥保管库必须启用清除保护才能激活成功。

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="此示意图展示的是带有工作区激活链接的横幅。" lightbox="./media/workspaces-encryption/workspace-activation.png":::


### <a name="manage-the-workspace-customer-managed-key"></a>管理工作区客户管理的密钥 

你可以在 Azure 门户的“加密”页中更改用于加密数据的客户管理的密钥。 你还可以在此处使用密钥标识符来选择新密钥，或者从工作区所在的区域里自己有权访问的密钥保管库中进行选择。 如果没有从之前使用的那些密钥保管库中选择密钥，请授予工作区托管标识对新的密钥保管库中的密钥进行“获取”、“包装”和“解包”操作的权限。 工作区将验证其对新密钥保管库的访问权限，并使用新密钥重新加密工作区中的所有数据。

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="此示意图展示的是 Azure 门户中的工作区加密部分。" lightbox="./media/workspaces-encryption/workspace-encryption-management.png":::

>[!IMPORTANT]
>更改工作区的加密密钥时，请保持使用密钥，直到将其替换为新密钥。 这是为了在用新密钥重新加密数据之前允许使用旧密钥解密数据。

用于自动化、定期轮换密钥或对密钥执行操作的 Azure 密钥保管库策略可能会产生新的密钥版本。 你可以选择使用最新版本的有效密钥对工作区中的所有数据进行重新加密。 若要重新加密，请将 Azure 门户中的密钥更改为临时密钥，然后切换回要用于加密的密钥。 例如，若要使用最新版本的有效密钥 Key1 对数据加密进行更新，请将工作区客户管理的密钥更改为临时密钥 Key2。 等待直到使用 Key2 的加密操作完成。 然后将工作区客户管理的密钥切换回 Key1，系统将使用最新版本的 Key1 重新加密工作区中的数据。

> [!NOTE]
> Azure Synapse Analytics 正在积极开发添加功能，目的是能在创建新的密钥版本时自动重新加密数据。 在此功能可用之前，为了确保工作区中的一致性，请使用上述过程强制对数据进行重新加密。

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>使用服务管理的密钥的 SQL 透明数据加密

SQL 透明数据加密 (TDE) 可用于工作区中未启用双重加密的专用 SQL 池。 在此类型的工作区中，服务管理的密钥用于为专用 SQL 池中的数据提供双重加密。 可以为单个专用 SQL 池启用或禁用使用服务管理的密钥的 TDE。

## <a name="next-steps"></a>后续步骤

[使用内置的 Azure 策略实现 Synapse 工作区的加密保护](../policy-reference.md)

[使用 ARM 模板创建 Azure 密钥保管库和密钥](../../key-vault/keys/quick-create-template.md)
