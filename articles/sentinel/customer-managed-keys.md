---
title: 在 Azure Sentinel 中设置客户托管密钥 | Microsoft Docs
description: 了解如何在 Azure Sentinel 中设置客户托管密钥 (CMK)。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2021
ms.author: yelevin
ms.openlocfilehash: 66b9f5a2e4a0c62ab6847b9e851f8d04b1dbd0f8
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252882"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>设置 Azure Sentinel 客户托管密钥

本文介绍了为 Azure Sentinel 配置[客户管理的密钥 (CMK)](../azure-monitor/logs/customer-managed-keys.md) 的背景信息和步骤。 CMK 使你能够使用由你创建和拥有并存储在 [Azure Key Vault](../key-vault/general/overview.md) 中的加密密钥，为存储在 Azure Sentinel 中的所有数据（已由 Microsoft 在所有相关存储资源中加密）提供额外的一层保护。

## <a name="prerequisites"></a>先决条件

- CMK 功能需要具有至少 1 TB/天承诺层级的 Log Analytics 专用群集。 多个工作区可以链接到同一专用群集，它们共享同一个客户管理的密钥。

- 完成本指南中的步骤后，在使用工作区之前，请联系 [Azure Sentinel 产品组](mailto:azuresentinelCMK@microsoft.com)进行载入确认。

- 了解 [Log Analytics 专用群集定价](../azure-monitor/logs/logs-dedicated-clusters.md#cluster-pricing-model)。

## <a name="considerations"></a>注意事项

- 仅支持通过 REST API（而非 Azure 门户）将 CMK 工作区载入 Sentinel。 当前不支持 Azure 资源管理器模板（ARM 模板）进行 CMK 载入。

- Azure Sentinel CMK 功能仅提供给尚未载入到 Azure Sentinel 的 Log Analytics 专用群集的工作区 。

- 不支持以下 CMK 相关的更改，因为这些更改将不起作用（Azure Sentinel 数据仍仅由 Microsoft 管理的密钥加密，而不由 CMK 加密）：

  - 在已载入到 Azure Sentinel 的工作区上启用 CMK。
  - 在包含已载入 Sentinel 的工作区的群集上启用 CMK。
  - 将已载入 Sentinel 的非 CMK 工作区链接到启用了 CMK 的群集。

- 不支持以下 CMK 相关的更改，因为这些更改可能导致未定义和有问题的行为：

  - 在已载入 Azure Sentinel 的工作区上禁用 CMK。
  - 通过将已载入 Sentinel 并已启用 CMK 的工作区从其已启用 CMK 的专用群集中取消链接，来将其设置为非 CMK 工作区。
  - 在启用了 CMK 的 Log Analytics 专用群集上禁用 CMK。

- Azure Sentinel 支持 CMK 配置中的系统分配的标识。 因此，专用 Log Analytics 群集的标识应为系统分配的类型。 建议使用创建 Log Analytics 群集时自动分配给该群集的标识。

- 当前不支持将客户管理的密钥更改为其他密钥（具有另一个 URI）。 应通过[轮换密钥](../azure-monitor/logs/customer-managed-keys.md#key-rotation)来更改密钥。

- 对生产工作区或 Log Analytics 群集做出任何 CMK 更改之前，请联系 [Azure Sentinel 产品组](mailto:azuresentinelCMK@microsoft.com)。

## <a name="how-cmk-works"></a>CMK 的工作原理 

Azure Sentinel 解决方案将多个存储资源用于日志收集和功能，包括 Log Analytics 专用群集。 在执行 Azure Sentinel CMK 配置的过程中，必须在相关 Log Analytics 专用群集上配置 CMK 设置。 Azure Sentinel 在 Log Analytics 以外的存储资源中保存的数据也将使用为专用 Log Analytics 群集配置的客户管理的密钥进行加密。

请参阅以下其他相关文档：
- [Azure Monitor 客户管理的密钥 (CMK)](../azure-monitor/logs/customer-managed-keys.md)。
- [Azure Key Vault](../key-vault/general/overview.md)。
- [Log Analytics 专用群集](../azure-monitor/logs/logs-dedicated-clusters.md)。

> [!NOTE]
> 如果在 Azure Sentinel 上启用了 CMK，则不会启用任何不支持 CMK 的公共预览功能。

## <a name="enable-cmk"></a>启用 CMK 

若要预配 CMK，请执行以下步骤： 

1.  创建 Azure Key Vault 并生成或导入密钥。

2.  在 Log Analytics 工作区上启用 CMK。

3.  注册到 Cosmos DB 资源提供程序。

4.  将访问策略添加到 Azure Key Vault 实例。

5.  通过[载入 API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx) 将工作区载入 Azure Sentinel。

### <a name="step-1-create-an-azure-key-vault-and-generate-or-import-a-key"></a>步骤 1：创建 Azure Key Vault 并生成或导入密钥

1.  [创建 Azure Key Vault 资源](/azure-stack/user/azure-stack-key-vault-manage-portal)，然后生成或导入用于数据加密的密钥。
    > [!NOTE]
    >  必须将 Azure Key Vault 配置为可恢复，以保护密钥和访问。

1.  [打开恢复选项：](../key-vault/general/key-vault-recovery.md)

    -   请确保[软删除](../key-vault/general/soft-delete-overview.md)已打开。

    -   打开[清除保护](../key-vault/general/soft-delete-overview.md#purge-protection)可防止在软删除后强行删除机密/保管库。

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>步骤 2：在 Log Analytics 工作区上启用 CMK

按照 [Azure Monitor 客户托管的密钥配置](../azure-monitor/logs/customer-managed-keys.md)中的说明创建 CMK 工作区，以下步骤中将使用该工作区作为 Azure Sentinel 工作区。

### <a name="step-3-register-to-the-cosmos-db-resource-provider"></a>步骤 3：注册到 Cosmos DB 资源提供程序

Azure Sentinel 使用 Cosmos DB 作为附加存储资源。 请确保注册到 Cosmos DB 资源提供程序。

按照 Cosmos DB 说明，为你的 Azure 订阅[注册 Azure Cosmos DB 资源提供程序](../cosmos-db/how-to-setup-cmk.md#register-resource-provider)资源提供程序。

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>第 4 步：将访问策略添加到 Azure Key Vault 实例

请确保添加 Cosmos DB 到 Azure Key Vault 实例的访问权限。 按照 Cosmos DB 说明，使用 Azure Cosmos DB 主体[将访问策略添加到 Azure Key Vault 实例](../cosmos-db/how-to-setup-cmk.md#add-access-policy)。

### <a name="step-5-onboard-the-workspace-to-azure-sentinel-via-the-onboarding-api"></a>步骤 5：通过载入 API 将工作区载入 Azure Sentinel

通过[载入 API](https://github.com/Azure/Azure-Sentinel/raw/master/docs/Azure%20Sentinel%20management.docx) 将工作区载入 Azure Sentinel。

## <a name="key-encryption-key-revocation-or-deletion"></a>密钥加密密钥吊销或删除

如果用户通过删除密钥加密密钥 (CMK) 或删除专用群集和 Cosmos DB 资源提供程序的访问权限来撤销密钥加密密钥，在一小时内，Azure Sentinel 将执行此更改，并表现为数据不再可用。 此时，将阻止任何使用持久性存储资源（如数据引入、持久配置更改和事件创建）的操作。 以前存储的数据不会删除，但仍无法访问。 不可访问的数据由数据保留策略管理，并根据该策略进行清除。

撤消或删除加密密钥后，唯一可行的操作是删除帐户。

如果在吊销后恢复访问操作，Azure Sentinel 会在一小时内恢复对数据的访问权限。

对于专用 Log Analytics 群集和 Cosmos DB，可通过在密钥保管库中禁用客户管理的密钥，或删除密钥的访问策略来撤销对数据的访问权限。 不支持通过从专用 Log Analytics 群集中删除密钥或删除与专用 Log Analytics 群集关联的标识来撤销访问权限。

若要详细了解此功能在 Azure Monitor 中的工作原理，请参阅 [Azure Monitor CMK 吊销](../azure-monitor/logs/customer-managed-keys.md#key-revocation)。

## <a name="customer-managed-key-rotation"></a>客户管理的密钥轮换

Azure Sentinel 和 Log Analytics 支持密钥轮换。 当用户在 Key Vault 执行密钥轮换时，Azure Sentinel 会在一小时内开始支持新密钥。

在 Key Vault 中，可通过创建新的密钥版本来执行密钥轮换：

![密钥轮换](./media/customer-managed-keys/key-rotation.png)

可以在 24 小时后，或者在 Azure Key Vault 审核日志不再显示任何使用以前版本的活动后，禁用密钥的以前版本。

轮换密钥后，必须使用新 Azure Key Vault 密钥版本显式更新 Log Analytics 中的专用 Log Analytics 群集资源。 有关详细信息，请参阅 [Azure Monitor CMK 轮换](../azure-monitor/logs/customer-managed-keys.md#key-rotation)。

## <a name="replacing-a-customer-managed-key"></a>替换客户管理的密钥

Azure Sentinel 不支持替换客户管理的密钥。 你应转而使用[密钥轮换功能](#customer-managed-key-rotation)。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何在 Azure Sentinel 中设置客户托管密钥。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。