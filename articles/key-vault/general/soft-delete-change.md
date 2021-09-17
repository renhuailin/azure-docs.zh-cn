---
title: 对所有密钥保管库对象启用软删除 - Azure Key Vault | Microsoft Docs
description: 使用本文档对所有密钥保管库采用软删除，并进行应用程序和管理更改以避免冲突错误。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 45eea8f8809e97f7e424a988140bcf76b47d5947
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122609282"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>对所有 Key Vault 启用软删除

> [!WARNING]
> 中断性变更：即将弃用选择退出软删除的功能。 Azure Key Vault 用户和管理员应立即对其 Key Vault 启用软删除。
>
> 对于 Azure Key Vault 托管 HSM，默认情况下启用软删除，并且无法禁用。

如果在没有软删除保护的情况下从 Key Vault 中删除机密，则该机密将被永久删除。 在创建密钥保管库期间，用户当前可选择退出软删除。 但是，Microsoft 即将对所有密钥保管库启用软删除保护，以防止用户意外或恶意删除机密。 用户将无法再选择退出或禁用软删除。

:::image type="content" source="../media/softdeletediagram.png" alt-text="示意图显示了在具有软删除保护与没有软删除保护的情况下如何删除密钥保管库。":::

有关软删除功能的完整详细信息，请参阅 [Azure Key Vault 软删除概述](soft-delete-overview.md)。

## <a name="can-my-application-work-with-soft-delete-enabled"></a>启用软删除后，我的应用程序是否可以正常工作？

> [!Important] 
> 对密钥保管库启用软删除前，仔细阅读以下信息。

密钥保管库名称具备全局唯一性。 密钥保管库中存储的机密名称也是唯一的。 你将无法重用处于“已软删除”状态的密钥保管库或密钥保管库对象的名称。 

例如，如果应用程序以编程方式创建了一个名为“保管库 A”的密钥保管库，然后删除了“保管库 A”，则该密钥保管库将进入“已软删除”状态。 在从“已软删除”状态清除该密钥保管库之前，应用程序将无法重新创建另一个名为“保管库 A”的密钥保管库。 

同样，如果应用程序在“保管库 A”中创建了一个名为 `test key` 的密钥，然后删除了该密钥，则在从“已软删除”状态清除 `test key` 对象之前，应用程序将无法在“保管库 A”中创建名为 `test key` 的新密钥。 

如果你尝试删除一个密钥保管库对象并使用相同的名称重新创建它，而不先将其从“已软删除”状态中清除，这可能会导致冲突错误。 这些错误可能会导致应用程序或自动化失败。 在进行以下必要的应用程序和管理更改之前，请咨询你的开发团队。 

### <a name="application-changes"></a>应用程序更改

如果应用程序假设未启用软删除，并且希望已删除的机密或密钥保管库名称可立即重用，你需要对应用程序逻辑进行以下更改。

1. 删除原始的密钥保管库或机密。
1. 清除“已软删除”状态下的 Key Vault 或机密。
1. 等待清除完成。 立即进行重新创建可能会导致冲突。
1. 重新创建具有相同名称的 Key Vault。
1. 如果创建操作仍然导致名称冲突错误，请再次尝试重新创建密钥保管库。 在最糟糕的情况下，Azure DNS 记录可能需要长达 10 分钟的时间进行更新。

### <a name="administration-changes"></a>管理更改

需要访问永久删除机密的安全主体必须获授更多的访问策略权限才能清除这些机密和密钥保管库。

禁用对密钥保管库实施的任何要求关闭软删除的 Azure Policy 分配。 你可能需要将此问题上报给控制应用于环境的 Azure Policy 分配的管理员。 如果未禁用此策略分配，你可能无法在所应用策略分配范围内创建新的密钥保管库。

如果你的组织受到法律合规性要求的约束，并且不允许已删除的密钥保管库和机密长时间保持可恢复状态，则你必须调整软删除的保持期，以满足你组织的标准。 可将保持期配置为持续 7 至 90 天。

## <a name="procedures"></a>过程

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>审核 Key Vault，检查是否启用了软删除

1. 登录到 Azure 门户。
1. 搜索“Azure Policy”。
1. 选择“定义”。
1. 在“类别”下，选择筛选器中的“密钥保管库” 。
1. 选择“密钥保管库应启用软删除”策略。
1. 选择“分配”。 
1. 将范围设置为你的订阅。
1. 确保策略的效果设置为“审核”。
1. 选择“查看 + 创建”  。 最多需要 24 小时才能完成环境的完整扫描。
1. 在“Azure Policy”窗格中，选择“合规性” 。
1. 选择应用的策略。

现在，你可筛选并查看启用了软删除的密钥保管库（合规资源）以及未启用软删除的密钥保管库（不合规资源）。

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>对现有的密钥保管库启用软删除

1. 登录到 Azure 门户。
1. 搜索你的密钥保管库。
1. 选择“设置”下的“属性” 。
1. 在“软删除”下，选择“启用对此保管库及其对象的恢复”选项 。
1. 设置软删除的保持期。
1. 选择“保存”。 

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>向安全主体授予清除访问策略权限

1. 登录到 Azure 门户。
1. 搜索你的密钥保管库。
1. 选择“设置”下的“访问策略” 。
1. 选择要授予访问权限的服务主体。
1. 浏览“密钥”、“机密”和“证书权限”下的每个下拉菜单，直到看到“特权操作”为止   。 选择“清除”权限。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-this-change-affect-me"></a>此更改是否会对我产生影响？

如果你已启用软删除，或者没有删除并重新创建同名的密钥保管库对象，则可能不会注意到密钥保管库的行为发生任何更改。

如果你的应用程序经常删除和重新创建具有相同命名约定的密钥保管库对象，则你必须在应用程序逻辑中进行更改，以保持预期的行为。 请参阅本文中的[应用程序更改](#application-changes)部分。

### <a name="how-do-i-benefit-from-this-change"></a>我如何从这一更改中获益？

软删除保护为你的组织提供了另一层保护，防止意外或恶意删除。 Key Vault 管理员可以限制对恢复权限和清除权限的访问。

如果用户意外地删除了密钥保管库或机密，你可以授予他们访问权限以自行恢复机密，而不会造成他们永久删除机密或密钥保管库的风险。 此自助式过程将最大程度地减少环境中的停机时间，并保证机密的可用性。

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>如何确定我是否需要采取措施？

按照本文中[审核密钥保管库，检查是否启用了软删除](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled)部分的步骤进行操作。 此更改将影响任何未启用软删除的密钥保管库。

### <a name="what-action-do-i-need-to-take"></a>我需要采取哪些措施？

确认不需要对应用程序逻辑进行更改后，对所有密钥保管库启用软删除。

### <a name="when-do-i-need-to-take-action"></a>什么时候需要措施？

为确保你的应用程序不受影响，请尽快对密钥保管库启用软删除。

## <a name="next-steps"></a>后续步骤

- 如有任何关于此更改的问题，请通过 [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) 与我们联系。
- 阅读[软删除概述](soft-delete-overview.md)。
