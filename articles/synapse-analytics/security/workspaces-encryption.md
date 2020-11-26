---
title: Azure Synapse Analytics 加密
description: 介绍 Azure Synapse Analytics 中的加密的文章
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 17dbdbbef45e0068601835197a1177ee20d98ca3
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296743"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces-preview"></a>Azure Synapse Analytics 加密 (工作区预览版) 

本文介绍：
* Azure Synapse Analytics 工作区中静态数据的加密。
* 配置 Synapse 工作区以便使用客户管理的密钥来启用加密。
* 管理用于对工作区中的数据进行加密的密钥。

## <a name="encryption-of-data-at-rest"></a>静态数据加密

完整的静态加密解决方案确保数据永远不会以未加密形式持久保存。 静态数据加密可以通过两个独立的加密层来防止出现威胁，防止任何单个层受到威胁。 Azure Synapse Analytics 使用客户管理的密钥为工作区中的数据提供了另一层的加密。 此密钥在你的 [Azure Key Vault](../../key-vault/general/overview.md)中受到保护，这允许你获取密钥管理和轮换的所有权。

Azure 服务的第一层加密是通过平台托管密钥启用的。 默认情况下，azure 存储帐户中的 Azure 磁盘和数据会自动静态加密。 若要详细了解如何在 [Azure 加密概述](../../security/fundamentals/encryption-overview.md)的 Microsoft Azure 中使用加密，请参阅。

## <a name="azure-synapse-encryption"></a>Azure Synapse 加密

本部分将帮助你更好地了解如何在 Synapse 工作区中启用和实施客户托管的密钥加密。 此加密使用 Azure Key Vault 中生成的现有密钥或新密钥。 单个密钥用于对工作区中的所有数据进行加密。 Synapse 工作区支持2048和3072字节大小的密钥的 RSA 密钥。

> [!NOTE]
> Synapse 工作区不支持使用 Elliptic-Curve 加密 (ECC) 密钥来进行加密。

以下 Synapse 组件中的数据是通过在工作区级别配置的客户托管密钥加密的：
* SQL 池
 * 专用 SQL 池
 * 无服务器 SQL 池
* Apache Spark 池
* Azure 数据工厂集成运行时、管道、数据集。

## <a name="workspace-encryption-configuration"></a>工作区加密配置

工作区可以配置为在创建工作区时使用客户托管的密钥启用双加密。 创建新的工作区时，请选择 "安全" 选项卡上的 "使用客户托管的密钥启用双加密" 选项。 你可以选择输入密钥标识符 URI，或从工作区 **所在的区域** 中的密钥保管库列表中进行选择。 Key Vault 本身需要 **启用清除保护**。

> [!IMPORTANT]
> 目前，在创建工作区后，不能更改双加密的配置设置。

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="此关系图显示了必须选择的选项，才能启用使用客户托管密钥进行双重加密的工作区。":::

### <a name="key-access-and-workspace-activation"></a>密钥访问和工作区激活

包含客户管理的密钥的 Azure Synapse 加密模型涉及访问 Azure Key Vault 中的密钥的工作区，可根据需要进行加密和解密。 工作区可以通过访问策略或 Azure Key Vault RBAC 访问 ([预览版](../../key-vault/general/rbac-guide.md)) 来访问这些密钥。 通过 Azure Key Vault 访问策略授予权限时，请在创建策略的过程中选择 "仅限应用程序" 选项。

 在激活工作区之前，必须向工作区托管标识授予对密钥保管库所需的权限。 这一分阶段工作区激活方法确保工作区中的数据是通过客户托管的密钥进行加密的。 请注意，可以为专用 SQL 池启用或禁用加密-默认情况下，每个池不启用加密。

#### <a name="permissions"></a>权限

要加密或解密静态数据，工作区托管标识必须具有以下权限：
* WrapKey (在创建新密钥) 时向 Key Vault 中插入密钥。
* UnwrapKey (获取解密) 密钥。
* 获取 (以读取密钥的公共部分) 

#### <a name="workspace-activation"></a>工作区激活

在工作区 (启用了双加密的情况下) 创建后，它将保持 "挂起" 状态，直到激活成功为止。 必须先激活工作区，然后才能完全使用所有功能。 例如，激活成功后，只能创建新的专用 SQL 池。 向工作区托管标识授予对密钥保管库的访问权限，并单击工作区中的 "激活" 链接 Azure 门户横幅。 激活成功完成后，你的工作区已准备好与你的客户托管的密钥一起使用，确保其中的所有数据都受到保护。 如前文所述，密钥保管库必须启用清除保护才能成功激活。

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="此关系图显示带有工作区激活链接的横幅。":::


### <a name="manage-the-workspace-customer-managed-key"></a>管理工作区客户管理的密钥 

你可以在 Azure 门户的 " **加密** " 页中更改用于加密数据的客户托管密钥。 在此，您可以使用密钥标识符来选择新密钥，也可以在工作区所在的区域中选择有权访问的密钥保管库。 如果在不同的密钥保管库中选择了与以前使用的密钥不同的密钥，请在新的密钥保管库中授予工作区托管标识 "获取"、"换行" 和 "解包" 权限。 工作区将验证其对新密钥保管库的访问权限，并使用新密钥重新加密工作区中的所有数据。

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="此图显示了 Azure 门户中的工作区加密部分。":::

Azure 密钥保管库策略自动、定期循环密钥或对密钥执行的操作可能会导致创建新的密钥版本。 你可以选择重新加密工作区中具有最新版本的活动密钥的所有数据。 若要重新加密，请将 Azure 门户中的密钥更改为临时密钥，然后切换回要用于加密的密钥。 例如，若要使用最新版本的 active key Key1 更新数据加密，请将工作区客户托管密钥更改为临时密钥，Key2。 等待带有 Key2 的加密完成。 然后将工作区客户托管的密钥切换回 Key1-工作区中的数据将用最新版本的 Key1 重新加密。

> [!NOTE]
> Azure Synapse Analytics 正在努力添加功能，以便在创建新的密钥版本时自动重新加密数据。 在此功能可用之前，若要确保工作区中的一致性，请使用上述过程强制对数据重新加密。

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>带有服务托管密钥的 SQL 透明数据加密

SQL 透明数据加密 (TDE) 适用于工作区中 *未* 启用双重加密的专用 SQL 池。 在此类型的工作区中，服务托管的密钥用于为专用 SQL 池中的数据提供双精度加密。 可以为单个专用 SQL 池启用或禁用具有服务托管密钥的 TDE。

## <a name="next-steps"></a>后续步骤

[使用内置的 Azure 策略实现 Synapse 工作区的加密保护](../policy-reference.md)

