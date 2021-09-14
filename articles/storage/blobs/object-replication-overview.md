---
title: 对象复制概述
titleSuffix: Azure Storage
description: 对象复制在源存储帐户和目标帐户之间异步复制块 blob。 使用对象复制可最大程度地降低读取请求延迟，提高计算工作负载的效率，优化数据分发，并最大限度地降低成本。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5a0ca85ff28e56e7e9a47df1e56861d5c6552b97
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123468089"
---
# <a name="object-replication-for-block-blobs"></a>块 blob 的对象复制

对象复制在源存储帐户和目标帐户之间异步复制块 blob。 对象复制支持的部分方案包括：

- **最大程度地降低延迟。** 通过使客户端可以从物理位置更接近的区域使用数据，对象复制可以降低读取请求的延迟。
- **提高计算工作负载的效率。** 通过对象复制，计算工作负载可以在不同区域中处理相同的块 blob 集。
- **优化数据分发。** 您可以在单个位置处理或分析数据，然后仅将结果复制到其他区域。
- **优化成本。** 复制数据后，可以使用生命周期管理策略将数据转移到存档层，从而降低成本。

下图显示了对象复制如何将块 blob 从一个区域中的源存储帐户复制到两个不同区域中的目标帐户。

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="显示对象复制工作方式的图":::

若要了解如何配置对象复制，请参阅[配置对象复制](object-replication-configure.md)。

## <a name="prerequisites-for-object-replication"></a>对象复制的先决条件

对象复制还需要启用以下 Azure 存储功能：

- [更改源](storage-blob-change-feed.md)：必须在源帐户上启用。 若要了解如何启用更改源，请参阅[启用和禁用更改源](storage-blob-change-feed.md#enable-and-disable-the-change-feed)。
- [Blob 版本控制](versioning-overview.md)：必须同时在源帐户和目标帐户上启用。 若要了解如何启用版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。

启用更改源和 blob 版本控制可能会产生额外的成本。 有关更多详细信息，请参阅 [Azure 存储定价页](https://azure.microsoft.com/pricing/details/storage/)。

在预览版中，通用 v2 存储帐户和高级块 blob 帐户支持对象复制。 源帐户和目标帐户都必须是通用 v2 或高级块 blob 帐户。 对象复制仅支持块 blob；不支持追加 blob 和页 blob。

> [!IMPORTANT]
> 针对高级块 blob 帐户的对象复制目前处于预览阶段。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-object-replication-works"></a>对象复制的工作原理

对象复制根据你配置的规则异步复制容器中的块 blob。 blob 的内容、与 blob 关联的任何版本以及 blob 的元数据和属性都从源容器复制到目标容器。

> [!IMPORTANT]
> 因为块 blob 数据是以异步方式复制的，所以源帐户和目标帐户不会立即同步。目前没有关于将数据复制到目标帐户所需时间的 SLA。 你可以在源 blob 上检查复制状态，确定复制是否已完成。 有关详细信息，请参阅[查看 blob 的复制状态](object-replication-configure.md#check-the-replication-status-of-a-blob)。

### <a name="blob-versioning"></a>Blob 版本控制

对象复制要求同时在源帐户和目标帐户上启用 blob 版本控制。 当源帐户中复制的 blob 被修改时，源帐户中将创建一个新的 blob 版本来反映修改前 blob 以前的状态。 源帐户中的当前版本反映了最近的更新。 当前版本和任何之前的版本都将复制到目标帐户。 要详细了解写入操作如何影响 blob 版本，请参阅[对写入操作进行版本控制](versioning-overview.md#versioning-on-write-operations)。

删除源帐户中的 blob 后，该 blob 的当前版本将变为以前的版本，以前的版本不再存在。 将保留该 blob 的所有现有先前版本。 此状态将被复制到目标帐户。 要详细了解删除操作如何影响 blob 版本，请参阅[对删除操作进行版本控制](versioning-overview.md#versioning-on-delete-operations)。

### <a name="snapshots"></a>快照

对象复制不支持 blob 快照。 源帐户中 blob 上的任何快照都不会复制到目标帐户。

### <a name="blob-tiering"></a>Blob 分层

如果源帐户和目标帐户位于热层或冷层，则支持对象复制。 源帐户和目标帐户可能位于不同的层级。 但如果源帐户或目标帐户中的 blob 已移至存档层，则对象复制将失败。 有关 blob 层的详细信息，请参阅 [Azure Blob 存储的访问层 - 热、冷和存档](storage-blob-storage-tiers.md)。

### <a name="immutable-blobs"></a>不可变 blob

Azure Blob 存储的不可变性策略包括基于时间的保留策略和法定保留。 当不可变性策略对目标帐户生效时，对象复制可能会受到影响。 有关不可变性策略的详细信息，请参阅[使用不可变存储来存储业务关键型 blob 数据](immutable-storage-overview.md)。

如果容器级不可变性策略对目标帐户中的容器有效，并且源容器中的对象被更新或删除，则对源容器执行的操作可能会成功，但对目标容器执行该操作将会失败。 若要详细了解范围设为容器的不可变性策略禁止哪些操作，请参阅[使用容器级范围的方案](immutable-storage-overview.md#scenarios-with-container-level-scope)。

如果版本级不可变性策略对目标帐户中的 blob 版本有效，并且源容器中的 blob 版本被删除或更新，则对源对象执行的操作可能会成功，但对目标对象执行该操作将会失败。 若要详细了解范围设为容器的不可变性策略禁止哪些操作，请参阅[使用版本级范围的方案](immutable-storage-overview.md#scenarios-with-version-level-scope)。

## <a name="object-replication-policies-and-rules"></a>对象复制策略和规则

配置对象复制时，需要创建复制策略，以指定源存储帐户和目标帐户。 复制策略包括一个或多个规则，用于指定源容器和目标容器，并指明复制源容器中的哪些块 blob。

配置对象复制以后，Azure 存储会定期检查源帐户的更改源，并将所有写入或删除操作以异步方式复制到目标帐户。 复制延迟取决于要复制的块 blob 的大小。

### <a name="replication-policies"></a>复制策略

配置对象复制时，需要通过 Azure 存储资源提供程序在目标帐户上创建复制策略。 创建复制策略后，Azure 存储将为其分配策略 ID。 然后，必须使用该策略 ID 将该复制策略与源帐户进行关联。 源帐户和目标帐户上的策略 ID 必须相同，才能进行复制。

一个源帐户最多可以复制到两个目标帐户，每个目标帐户使用一个策略。 类似地，一个帐户可以作为不超过两个复制策略的目标帐户。

源帐户和目标帐户可以位于相同区域，也可以位于不同区域。 此外，它们可以位于同一订阅，也可以位于不同订阅。 （可选）源帐户和目标帐户可能位于不同的 Azure Active Directory (Azure AD) 租户中。 只能为每个源帐户/目标帐户对创建一个复制策略。

### <a name="replication-rules"></a>复制规则

复制规则指定 Azure 存储如何将 blob 从源容器复制到目标容器。 可以为每个复制策略最多指定 10 个复制规则。 每个复制规则定义一个源和目标容器，每个源和目标容器只能在一个规则中使用，这意味着最多可以有 10 个源容器和 10 个目标容器参与一个复制策略。

创建复制规则时，默认情况下仅复制随后添加到源容器的新块 blob。 你可以指定复制新的和现有的块 blob，也可以定义自定义复制范围，以复制从指定的时间开始创建的块 blob。

也可以将一个或多个筛选器指定为复制规则的一部分，以按前缀筛选块 blob。 指定前缀时，只会将源容器中与该前缀匹配的 blob 复制到目标容器。

源和目标容器必须都存在，然后才能在规则中指定它们。 创建复制策略后，不允许将操作写入目标容器。 任何尝试写入目标容器的操作都会失败，错误代码为 409（冲突）。 若要写入为其配置了复制规则的目标容器，则必须删除为该容器配置的规则，或者删除复制策略。 当复制策略处于活动状态时，允许对目标容器执行读取和删除操作。

你可以针对目标容器中的 blob 调用[设置 Blob 层](/rest/api/storageservices/set-blob-tier)操作，将其移动到存档层。 有关存档层的详细信息，请参阅 [Azure Blob 存储：热、冷和存档访问层](storage-blob-storage-tiers.md#archive-access-tier)。

## <a name="policy-definition-file"></a>策略定义文件

对象复制策略由 JSON 文件定义。 可以从现有对象复制策略中获取策略定义文件。 还可以通过上传策略定义文件来创建对象复制策略。

### <a name="sample-policy-definition-file"></a>策略定义文件示例

下面的示例在目标帐户上定义了复制策略，该策略包含一个与前缀 b 匹配的规则，并为要复制的 blob 设置了最小创建时间。 请注意将尖括号中的值替换为你自己的值：

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
    "destinationAccount": "/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
    "rules": [
      {
        "ruleId": "",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2021-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

### <a name="specify-full-resource-ids-for-the-source-and-destination-accounts"></a>指定源帐户和目标帐户的完整资源 ID

创建策略定义文件时，请为 sourceAccount 和 destinationAccount 条目指定完整的 Azure 资源管理器资源 ID，如上一节中的示例所示。  若要了解如何查找存储帐户的资源 ID，请参阅[获取存储帐户的资源 ID](../common/storage-account-get-info.md#get-the-resource-id-for-a-storage-account)。

完整资源 ID 采用以下格式：

```http
/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

策略定义文件以前只需要帐户名称，而不是存储帐户的完整资源 ID。 由于 Azure 存储资源提供程序 REST API 版本 2021-02-01 中引入了 AllowCrossTenantReplication 安全属性，现在，如果对参与复制策略的存储帐户禁止跨租户复制，必须为创建的任何对象复制策略提供完整的资源 ID。 Azure 存储使用完整的资源 ID 来验证源帐户和目标帐户是否位于同一租户中。 若要详细了解如何禁止跨租户复制策略，请参阅[防止跨 Azure AD 租户复制](#prevent-replication-across-azure-ad-tenants)。

虽然在对存储帐户允许跨租户复制时仍支持仅提供帐户名称，但作为最佳实践，Microsoft 建议始终提供完整资源 ID。 Azure 存储资源提供程序 REST API 的所有先前版本都支持在对象复制策略中使用完整的资源 ID 路径。

下表描述了在对存储帐户允许或禁止跨租户复制的情况下，如果在创建复制策略时指定完整资源 ID 而非帐户名称，会发生什么情况。

| 策略定义中的存储帐户标识符 | 允许跨租户复制 | 不允许跨租户复制 |
|--|--|--|
| 完整资源 ID | 可以创建同租户策略。<br /><br /> 可以创建跨租户策略。 | 可以创建同租户策略。<br /><br /> 无法创建跨租户策略。 |
| 仅帐户名称 | 可以创建同租户策略。<br /><br /> 可以创建跨租户策略。 | 同租户策略和跨租户策略都无法创建。 发生错误，因为 Azure 存储无法验证源帐户和目标帐户是否在同一租户中。 此错误指示必须在策略定义文件中指定 sourceAccount 和 destinationAccount 条目的完整资源 ID。  |

### <a name="specify-the-policy-and-rule-ids"></a>指定策略和规则 ID

下表汇总了每种情况下，要对策略定义文件中 policyId 和 ruleId 条目使用的值。 

| 为此帐户创建策略定义文件时... | 将策略 ID 设置为此值 | 将规则 ID 设置为此值 |
|-|-|-|
| 目标帐户 | 字符串值“默认”。 Azure 存储会为你创建策略 ID 值。 | 空字符串。 Azure 存储会为你创建规则 ID 值。 |
| 源帐户 | 下载目标帐户的策略定义文件时返回的策略 ID 的值。 | 下载目标帐户的策略定义文件时返回的规则 ID 的值。 |

## <a name="prevent-replication-across-azure-ad-tenants"></a>防止跨 Azure AD 租户复制

Azure Active Directory (Azure AD) 租户是 Azure AD 的专用实例，代表一个组织，专门用于标识和访问管理。 每个 Azure 订阅都与单个 Azure AD 租户具有信任关系。 订阅中的所有资源（包括存储帐户）都与同一个 Azure AD 租户相关联。 有关详细信息，请参阅[什么是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

默认情况下，具有适当权限的用户为对象复制配置的源存储帐户和目标帐户可以位于不同的 Azure AD 租户中。 如果安全策略要求将对象复制限制为仅位于同一租户中的存储帐户，则可以通过设置安全属性 AllowCrossTenantReplication（预览版）禁止跨租户复制。 对某个存储帐户禁止跨租户对象复制时，对于将该存储帐户配置为源或目标帐户的任何对象复制策略，Azure 存储要求源帐户和目标帐户位于同一个 Azure AD 租户中。  有关禁止跨租户对象复制的详细信息，请参阅[防止跨 Azure Active Directory 租户进行对象复制](object-replication-prevent-cross-tenant-policies.md)。

若要对某个存储帐户禁止跨租户对象复制，则将 AllowCrossTenantReplication 属性设置为 false。 如果存储帐户当前未参与任何跨租户对象复制策略，则将 AllowCrossTenantReplication 属性设置为 false 可防止将来配置跨租户对象复制策略时将该存储帐户作为源或目标。

如果存储帐户当前已参与一个或多个跨租户对象复制策略，则不允许将 AllowCrossTenantReplication 属性设置为 false。 必须先删除现有的跨租户策略，然后才能禁止跨租户复制。

默认情况下，不会为存储帐户设置 AllowCrossTenantReplication 属性，并且其值为 null，这等效于 true。  当存储帐户的 AllowCrossTenantReplication 属性的值为 null 或 true 时，授权用户可以配置跨租户对象复制策略，并以该帐户作为源或目标。  有关如何配置跨租户策略的详细信息，请参阅[为块 blob 配置对象复制](object-replication-configure.md)。

可以使用 Azure Policy 审核一组存储帐户，以确保将 AllowCrossTenantReplication 属性设置为防止跨租户对象复制。 还可以使用 Azure Policy 对一组存储帐户实施治理。 例如，可以创建具有拒绝效果的策略，以在 AllowCrossTenantReplication 属性设置为 true 的情况下防止用户创建存储帐户，或者防止用户通过修改现有存储帐户将属性值更改为 true。 

## <a name="replication-status"></a>复制状态

你可以在源帐户中查看 blob 的复制状态。 有关详细信息，请参阅[查看 blob 的复制状态](object-replication-configure.md#check-the-replication-status-of-a-blob)。

如果源帐户中 blob 的复制状态指示失败，则请调查以下可能的原因：

- 请确保已在目标帐户上配置对象复制策略。
- 请验证目标容器是否仍然存在。
- 如果在写入操作中已使用客户提供的密钥对源 blob 进行了加密，则对象复制将失败。 有关客户提供的密钥的详细信息，请参阅[在对 Blob 存储的请求中提供加密密钥](encryption-customer-provided-keys.md)。

## <a name="feature-support"></a>功能支持

此表显示了你的帐户如何支持此功能，以及当你启用某些功能时对支持的影响。 

| 存储帐户类型                | Blob 存储（默认支持）   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>    
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 标准常规用途 v2 | ![是](../media/icons/yes-icon.png) |![否](../media/icons/no-icon.png)              | ![否](../media/icons/no-icon.png) | 
| 高级块 blob          | ![是](../media/icons/yes-icon.png) |![否](../media/icons/no-icon.png)              | ![否](../media/icons/no-icon.png) | 

<sup>1</sup>    Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

## <a name="billing"></a>计费

对于源帐户和目标帐户的读写事务，对象复制会产生额外的成本，在将数据从源帐户复制到目标帐户时，会产生出站费用，处理更改源时，还会产生读取费用。

## <a name="next-steps"></a>后续步骤

- [配置对象复制](object-replication-configure.md)
- [防止跨 Azure Active Directory 租户进行对象复制](object-replication-prevent-cross-tenant-policies.md)
- [Blob 版本控制](versioning-overview.md)
- [Azure Blob 存储中的更改源支持](storage-blob-change-feed.md)
