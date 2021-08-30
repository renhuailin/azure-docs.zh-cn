---
title: Blob 存储的不可变性存储概述
titleSuffix: Azure Storage
description: Azure 存储为 Blob 存储提供 WORM（一次写入，多次读取）支持，使用户能够以不可擦除、不可修改的状态存储数据。 基于时间的保留策略在指定的间隔内以 WORM 状态存储 Blob 数据，而法定保留在被显式清除之前会一直保持有效。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 1096b23305a4048f2e6ade860e322fed282711f7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778716"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>使用不可变的存储来存储业务关键型 Blob 数据

Azure Blob 存储的不可变存储可让用户以 WORM（一次写入，多次读取）状态存储业务关键型数据。 在 WORM 状态下，在用户指定的间隔内无法修改或删除数据。 为 Blob 数据配置不可变性策略可以防范数据被覆盖和删除。

Azure Blob 存储的不可变存储支持两种类型的不可变性策略：

- 基于时间的保留策略：使用基于时间的保留策略，用户可以设置策略以按指定的间隔持续时间存储数据。 设置基于时间的保留策略后，可以创建和读取对象，但不能修改或删除对象。 保留期过后，可以删除但不能覆盖对象。 若要详细了解基于时间的保留策略，请参阅[不可变 Blob 数据的基于时间的保留策略](immutable-time-based-retention-policy-overview.md)。

- 法定保留策略：在显式清除法定保留之前，该法定保留会一直存储不可变数据。 设置法定保留后，可以创建和读取对象，但不能修改或删除对象。 若要详细了解法定保留策略，请参阅[不可变 Blob 数据的法定保留](immutable-legal-hold-overview.md)。

下图显示了基于时间的保留策略和法定保留在有效时如何防止写入操作和删除操作。

:::image type="content" source="media/immutable-storage-overview/worm-diagram.png" alt-text="此图显示了保留策略和法定保留如何防止写入操作和删除操作":::

## <a name="about-immutable-storage-for-blobs"></a>关于 Blob 的不可变存储

不可变存储可以帮助医疗保健组织、金融机构和相关行业（&mdash;尤其是证券公司&mdash;）安全存储数据。 不可变存储可以在任何场景中防止关键数据遭到修改或删除。

典型的应用程序包含：

- **法规遵从**：Azure Blob 存储的不可变存储可帮助组织达到 SEC 17a-4(f)、CFTC 1.31(d)、FINRA 和其他法规要求。

- 安全的文档保留：Blob 的不可变存储确保任何用户（甚至包括拥有帐户管理特权的用户）都无法修改或删除数据。

- 法定保留：Blob 的不可变存储允许用户以防篡改状态存储对诉讼或业务用途至关重要的敏感信息，在删除法定保留之前，可将其持续存储所需的任意时间。 此功能并不局限于法律用例，而且还可将它视为基于事件的保留或企业锁定机制，满足企业根据事件触发器或政策保护数据的需求。

## <a name="regulatory-compliance"></a>法规符合性

Microsoft 一直在与一家领先的独立评估机构 Cohasset Associates 保持合作。该机构专业从事记录管理和信息治理，可以评估 Blob 不可变存储及其是否符合金融服务行业的相关要求。 经 Cohasset 验证，在用于以 WORM 状态保留 Blob 时，不可变存储符合 CFTC 规则 1.31(c)-(d)、FINRA 规则 4511 和 SEC 规则 17a-4(f) 的相关存储要求。 Microsoft 以这一套规则为目标，因为这些规则代表了全球最规范的金融机构记录保留准则。

[Microsoft 服务信任中心](https://aka.ms/AzureWormStorage)已提供 Cohasset 的报告。 [Azure 信任中心](https://www.microsoft.com/trustcenter/compliance/compliance-overview)包含有关 Microsoft 合规认证的详细信息。 若要向 Microsoft 请求有关 WORM 不可变性合规性的证明信，请联系 [Azure 支持部门](https://azure.microsoft.com/support/options/)。

## <a name="immutability-policy-scope"></a>不可变性策略范围

不可变性策略的范围可以限定为 Blob 版本（预览版）或容器。 对象在不可变性策略约束下的行为取决于该策略的范围。 有关每种类型的不可变性策略的范围详细信息，请参阅以下部分：

- [基于时间的保留策略范围](immutable-time-based-retention-policy-overview.md#time-based-retention-policy-scope) 
- [法定保留范围](immutable-legal-hold-overview.md#legal-hold-scope)

可以根据范围来为资源（容器或 Blob 版本）配置基于时间的保留策略和法定保留。 下表汇总了每个资源范围支持的不可变性策略：

| 范围 | 容器配置为支持版本级不可变性策略 | 容器未配置为支持版本级不可变性策略 |
|--|--|--|
| 容器 | 支持一个默认的版本级不可变性策略。 不支持法定保留。 | 支持一个容器级不可变性策略和一个法定保留。 |
| Blob 版本 | 支持一个版本级不可变性策略和一个法定保留。 | 不适用 |

### <a name="about-the-preview"></a>关于此预览版

可在以下区域中使用版本级不可变性策略预览版：

- 加拿大中部
- 加拿大东部
- 法国中部
- 法国南部

> [!IMPORTANT]
> 版本级不可变性策略目前为预览版。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="summary-of-immutability-scenarios"></a>不可变性方案摘要

不可变性策略提供的保护取决于不可变性策略的范围，使用基于时间的保留策略时，还取决于该策略是已锁定还是已解锁，以及它是处于有效状态还是已过期。

### <a name="scenarios-with-version-level-scope"></a>版本级范围的方案

下表提供了版本级不可变性策略提供的保护摘要。

| 场景 | 禁止的操作 | Blob 保护 | 容器保护 | 帐户保护 |
|--|--|--|--|--|
| Blob 版本受有效保留策略的保护，并且/或者某个法定保留会生效 | [删除 Blob](/rest/api/storageservices/delete-blob)、[设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)、[放置页面](/rest/api/storageservices/put-page)和[追加块](/rest/api/storageservices/append-block)<sup>1</sup> | 无法删除 Blob 版本。 无法写入用户元数据。 <br /><br /> 使用[放置 Blob](/rest/api/storageservices/put-blob)、[放置块列表](/rest/api/storageservices/put-block-list)或[复制 Blob](/rest/api/storageservices/copy-blob) 覆盖 Blob 会创建一个新版本。<sup>2</sup> | 如果容器中至少存在一个 Blob，无论策略是已锁定还是已解锁，容器删除操作都将失败。 | 如果至少为一个容器启用了版本级不可变存储，则存储帐户删除操作将会失败。 |
| Blob 版本受已过期保留策略的保护，并且不会有任何法定保留生效 | [设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)、[放置页面](/rest/api/storageservices/put-page)和[追加块](/rest/api/storageservices/append-block)<sup>1</sup> | 可以删除 Blob 版本。 无法写入用户元数据。 <br /><br /> 使用[放置 Blob](/rest/api/storageservices/put-blob)、[放置块列表](/rest/api/storageservices/put-block-list)或[复制 Blob](/rest/api/storageservices/copy-blob) 覆盖 Blob 会创建一个新版本<sup>2</sup>。 | 如果容器中至少存在一个 Blob，无论策略是已锁定还是已解锁，容器删除操作都将失败。 | 如果至少有一个包含 Blob 版本的容器具有已锁定的基于时间的保留策略，则存储帐户删除操作将会失败。<br /><br />已解锁的策略不提供删除保护。 |

<sup>1</sup> 只允许对启用了 allowProtectedAppendWrites 属性的基于时间的保留策略执行[追加块](/rest/api/storageservices/append-block)操作。 有关详细信息，请参阅[允许受保护的追加 Blob 写入](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)。
<sup>2</sup> Blob 版本的内容始终不可变。 如果为存储帐户启用了版本控制，则对块 Blob 执行写入操作会创建一个新版本，但[放置块](/rest/api/storageservices/put-block)操作除外。

### <a name="scenarios-with-container-level-scope"></a>容器级范围的方案

下表提供了容器级不可变性策略提供的保护摘要。

| 场景 | 禁止的操作 | Blob 保护 | 容器保护 | 帐户保护 |
|--|--|--|--|--|
| 容器受容器范围的有效基于时间的保留策略保护，并且/或者某个法定保留会生效 | [删除 Blob](/rest/api/storageservices/delete-blob)、[放置 Blob](/rest/api/storageservices/put-blob)<sup>1</sup>、[设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)、[放置页面](/rest/api/storageservices/put-page)、[设置 Blob 属性](/rest/api/storageservices/set-blob-properties)、[快照 Blob](/rest/api/storageservices/snapshot-blob)、[增量复制 Blob](/rest/api/storageservices/incremental-copy-blob)、[追加块](/rest/api/storageservices/append-block)<sup>2</sup> | 容器中所有 Blob 的内容和用户元数据不可变 | 如果容器级策略已生效，则容器删除操作将会失败。 | 如果某个容器中至少存在一个 Blob，则存储帐户删除操作将会失败。 |
| 容器受容器范围的、已过期的基于时间的保留策略保护，并且不会有任何法定保留生效 | [放置 Blob](/rest/api/storageservices/put-blob)<sup>1</sup>、[设置 Blob 元数据](/rest/api/storageservices/set-blob-metadata)、[放置页面](/rest/api/storageservices/put-page)、[设置 Blob 属性](/rest/api/storageservices/set-blob-properties)、[快照 Blob](/rest/api/storageservices/snapshot-blob)、[增量复制 Blob](/rest/api/storageservices/incremental-copy-blob)、[追加块](/rest/api/storageservices/append-block)<sup>2</sup> | 允许执行删除操作。 不允许执行覆盖操作。 | 如果容器中至少存在一个 Blob，无论策略是已锁定还是已解锁，容器删除操作都将失败。 | 如果至少有一个容器具有已锁定的基于时间的保留策略，则存储帐户删除操作将会失败。<br /><br />已解锁的策略不提供删除保护。 |

<sup>1</sup> Azure 存储允许使用[放置 Blob](/rest/api/storageservices/put-blob) 操作来创建新的 Blob。用。 不允许针对不可变容器中的现有 Blob 路径执行后续覆盖操作。

<sup>2</sup> 只允许对启用了 allowProtectedAppendWrites 属性的基于时间的保留策略执行[追加块](/rest/api/storageservices/append-block)操作。 有关详细信息，请参阅[允许受保护的追加 Blob 写入](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)。

> [!NOTE]
> 某些工作负载（如 [SQL Server 备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)）可创建 blob，然后将其添加到其中。 如果容器具有有效的基于时间的保留策略或法定保留，则此模式不会成功。

## <a name="supported-account-configurations"></a>支持的帐户配置

新的和现有的存储帐户都支持不可变性策略。 下表显示了每种策略类型支持的存储帐户类型：

| 不可变性策略的类型 | 策略范围 | 支持的存储帐户类型 | 支持分层命名空间（预览版） |
|--|--|--|--|
| 基于时间的保留策略 | 版本级范围（预览版） | 常规用途 v2<br />高级块 blob | 否 |
| 基于时间的保留策略 | 容器级范围 | 常规用途 v2<br />高级块 blob<br />常规用途 v1（旧版）<sup>1</sup><br> Blob 存储（旧版） | 是 |
| 法定保留 | 版本级范围（预览版） | 常规用途 v2<br />高级块 blob | 否 |
| 法定保留 | 容器级范围 | 常规用途 v2<br />高级块 blob<br />常规用途 v1（旧版）<sup>1</sup><br> Blob 存储（旧版） | 是 |

<sup>1</sup> Microsoft 建议将常规用途 v1 帐户升级为常规用途 v2，以便可以利用更多的功能。 有关升级现有常规用途 v1 存储帐户的信息，请参阅[升级存储帐户](../common/storage-account-upgrade.md)。

### <a name="access-tiers"></a>访问层级

所有 Blob 访问层都支持不可变存储。 可以使用“设置 Blob 层”操作更改 Blob 的访问层。 有关详细信息，请参阅 [Azure Blob 存储的访问层 - 热、冷和存档](storage-blob-storage-tiers.md)。

### <a name="redundancy-configurations"></a>冗余配置

所有冗余配置都支持不可变存储。 对于异地冗余配置，不支持客户管理的故障转移。 有关冗余配置的详细信息，请参阅 [Azure 存储冗余](../common/storage-redundancy.md)。

### <a name="hierarchical-namespace-support"></a>分层命名空间支持

对使用分层命名空间的帐户提供的不可变存储支持目前为预览版。 若要注册预览版，请参阅 [Azure Data Lake Storage 的预览版功能](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u)。

请记住，当 Blob 处于不可变状态并且为帐户启用了分层命名空间时，无法重命名或移动该 Blob。 Blob 名称和目录结构提供重要的容器级数据，在不可变策略生效后不可对其进行修改。

> [!IMPORTANT]
> 对于启用了分层命名空间功能的帐户中的 Azure Blob 存储，不可变存储目前为预览版。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="recommended-blob-types"></a>建议的 Blob 类型

Microsoft 建议配置主要用于块 Blob 和追加 Blob 的不可变性策略。 对于存储活动虚拟机的 VHD 磁盘的页 Blob，不建议配置不可变性策略，因为这会阻止对磁盘的写入。 Microsoft 建议在锁定任何基于时间的策略之前，全面查看文档并测试你的方案。

## <a name="immutable-storage-with-blob-soft-delete"></a>使用 Blob 软删除的不可变存储

为存储帐户配置 Blob 软删除后，软删除将应用于该帐户中的所有 Blob，而不管是否有法定保留或基于时间的保留策略生效。 Microsoft 建议在应用任何不可变性策略之前启用软删除，以提供额外的保护。

如果先启用 Blob 软删除，再配置不可变性策略，则在软删除保留策略过期后，已软删除的所有 Blob 将被永久删除。 软删除的 Blob 在软删除保留期内可还原。 尚未软删除的 Blob 或版本受不可变性策略的保护，只能在基于时间的保留策略已过期或法定保留被删除之后将其软删除。

## <a name="use-blob-inventory-to-track-immutability-policies"></a>使用 Blob 清单来跟踪不可变性策略

Azure 存储 Blob 清单提供存储帐户中的容器以及这些容器中的 Blob、快照和 Blob 版本的概述。 可以使用 Blob 清单报告来了解 Blob 和容器的属性，包括是否为资源配置了不可变性策略。

启用 Blob 清单后，Azure 存储每天会生成清单报告。 该报表概述了有关业务和符合性要求的数据。

有关 Blob 清单的详细信息，请参阅 [Azure 存储 Blob 清单（预览版）](blob-inventory.md)。

## <a name="pricing"></a>定价

使用不可变存储不会产生额外的容量费用。 不可变数据的定价方式与可变数据的定价方式相同。 有关 Azure Blob 存储中的定价详细信息，请参阅 [Azure 存储定价页](https://azure.microsoft.com/pricing/details/storage/blobs/)。

创建、修改或删除针对 Blob 版本的基于时间的保留策略或法定保留会产生写入事务费用。

如果你不支付帐单，而你的帐户中有一个基于时间的保留策略已生效，则会根据你与 Microsoft 签订的合同中条款和条件的规定应用一般的数据保留策略。 有关一般信息，请参阅 [Microsoft 数据管理](https://www.microsoft.com/trust-center/privacy/data-management)。

## <a name="next-steps"></a>后续步骤

- [不可变 Blob 数据的基于时间的保留策略](immutable-time-based-retention-policy-overview.md)
- [不可变 Blob 数据的法定保留](immutable-legal-hold-overview.md)
- [数据保护概述](data-protection-overview.md)
