---
title: 通过自动管理数据生命周期优化成本
titleSuffix: Azure Storage
description: 使用 Azure 存储生命周期管理策略创建自动规则，以便在热层、冷层和存档层之间移动数据。
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 1f7b4152bee090e39c598b559ffa9d2e8aea8e88
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123477737"
---
# <a name="optimize-costs-by-automatically-managing-the-data-lifecycle"></a>通过自动管理数据生命周期优化成本

数据集具有独特的生命周期。 在生命周期的早期，人们经常访问某些数据。 但随着数据的老化，访问需求经常会急剧下降。 有些数据在云中持续处于空闲状态，并且在存储后很少被访问。 有些数据集在创建后的数日或者数月即会过期，还有一些数据集在其整个生存期会频繁受到读取和修改。 Azure 存储生命周期管理可提供基于规则的策略，用于将 blob 数据转移到最适合的访问层，或在数据生命周期结束时使数据过期。

利用生命周期管理策略，可以实现以下操作：

- 立即将所访问的 Blob 从冷层转移到热层，以便优化性能。
- 将一段时间内未访问或修改的 Blob、Blob 版本和 Blob 快照转移到较冷的存储层，以便优化成本。 在这种情况下，生命周期管理策略可以将对象从“热层”移动到“冷层”、从“热层”移动到“存档层”或从“冷层”移动到“存档层”。
- 在 Blob、Blob 版本和 Blob 快照的生命周期结束时将其删除。
- 在存储帐户级别定义每天运行一次的规则。
- 将规则应用于容器或 Blob 子集（使用名称前缀或 [Blob 索引标记](storage-manage-find-blobs.md)作为筛选器）。

假设某个数据集在生命周期的早期阶段频繁被访问，而两周后只是偶尔被访问。 一个月以后，该数据集很少被访问。 在这种场景下，早期阶段最适合使用热存储。 在偶尔访问阶段最适合使用冷存储。 在一个月后数据陈旧时，存档存储便是最佳的层选项。 借助生命周期管理策略规则，根据数据存在时间将其移动到适当的存储层，即可根据需要设计成本最低的解决方案。

生命周期管理策略支持块 Blob，并可在常规用途 v2、高级块 Blob 和 Blob 存储帐户中追加 Blob。 生命周期管理并不会影响诸如“$logs”和“$web”之类的系统容器。

> [!IMPORTANT]
> 如需将数据集设置为可读，则请不要设置将 Blob 移动到存档层的策略。 除非 Blob 是首个解除冻结对象，否则无法读取存档层中的 Blob，且这一过程可能既耗时又昂贵。 有关详细信息，请参阅[存档层中的 blob 解除冻结概述](archive-rehydrate-overview.md)。

## <a name="lifecycle-management-policy-definition"></a>生命周期管理策略定义

生命周期管理策略是 JSON 文档中的规则集合。 下方的 JSON 示例显示了完整的规则定义：

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

策略是规则的集合，如下表所述：

| 参数名称 | 参数类型 | 注释 |
|----------------|----------------|-------|
| `rules`        | 规则对象的数组 | 一个策略至少需要包含一个规则。 最多可在一个策略中定义 100 个规则。|

策略中的每个规则都拥有多个参数，如下表所述：

| 参数名称 | 参数类型 | 注释 | 必须 |
|--|--|--|--|
| `name` | String | 规则名称最多只能包含 256 个字母数字字符。 规则名称区分大小写。 该名称必须在策略中唯一。 | True |
| `enabled` | 布尔 | 一个允许暂时禁用规则的可选布尔值。 如果未设置，则默认值为 true。 | False |
| `type` | 枚举值 | 当前的有效类型为 `Lifecycle`。 | True |
| `definition` | 定义生命周期规则的对象 | 每个定义均由筛选器集和操作集组成。 | True |

## <a name="lifecycle-management-rule-definition"></a>生命周期管理规则定义

策略中的每个规则定义都包括筛选器集和操作集。 [筛选器集](#rule-filters)将规则操作限制为容器或对象名称中的某组对象。 [操作集](#rule-actions)对筛选的对象集应用分层或删除操作。

### <a name="sample-rule"></a>示例规则

以下示例规则将筛选帐户，以针对 `sample-container` 中存在的、以 `blob1` 开头的对象运行操作。

- 在上次修改后的 30 天后，将 Blob 分层到冷层
- 在上次修改后的 90 天后，将 Blob 分层到存档层
- 在上次修改后的 2,555 天（7 年）后，删除 Blob
- 在创建 90 天后删除以前的 Blob 版本

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "sample-rule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "sample-container/blob1"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>规则筛选器

筛选器将规则操作限制为存储帐户中的 Blob子集。 如果定义了多个筛选器，将对所有筛选器运行逻辑 `AND`。

筛选器包括：

| 筛选器名称 | 筛选器类型 | 注释 | 是否必需 |
|-------------|-------------|-------|-------------|
| blobTypes   | 预定义枚举值的数组。 | 当前版本支持 `blockBlob` 和 `appendBlob`。 `appendBlob` 仅支持删除，不支持设置层级。 | 是 |
| prefixMatch | 要匹配的前缀字符串数组。 每个规则最多可定义 10 个区分大小写的前缀。 前缀字符串必须以容器名称开头。 例如，如果要为某个规则匹配 `https://myaccount.blob.core.windows.net/sample-container/blob1/...` 下的所有 Blob，则 prefixMatch 为 `sample-container/blob1`。 | 如果未定义 prefixMatch，规则将应用到存储帐户中的所有 Blob。 | 否 |
| blobIndexMatch | 由要匹配的 Blob 索引标记键和值条件组成的字典值数组。 每个规则最多可以定义 10 个 Blob 索引标记条件。 例如，对于某个规则，如果要匹配 `https://myaccount.blob.core.windows.net/` 下 `Project = Contoso` 的所有 Blob，则 blobIndexMatch 为 `{"name": "Project","op": "==","value": "Contoso"}`。 | 如果未定义 blobIndexMatch，则规则将应用于存储帐户中的所有 Blob。 | 否 |

若要详细了解 Blob 索引功能以及已知问题和限制，请参阅[通过 Blob 索引管理和查找 Azure Blob 存储上的数据](storage-manage-find-blobs.md)。

### <a name="rule-actions"></a>规则操作

满足运行条件时，操作将应用到筛选的 Blob。

生命周期管理支持对 Blob 的分层和删除操作，并支持早期 Blob 版本和 Blob 快照。 请为基础 Blob、早期 Blob 版本或 Blob 快照的每个规则定义至少一个操作。

| 操作                      | 基本 Blob                                  | 快照      | 版本
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | 对 `blockBlob` 支持                  | 支持     | 支持     |
| enableAutoTierToHotFromCool | 对 `blockBlob` 支持                  | 不支持 | 不支持 |
| tierToArchive               | 对 `blockBlob` 支持                  | 支持     | 支持     |
| delete                      | 支持 `blockBlob` 和 `appendBlob` | 支持     | 支持     |

>[!NOTE]
>如果在同一 Blob 中定义了多个操作，生命周期管理将对该 Blob 应用开销最低的操作。 例如，操作 `delete` 的开销比 `tierToArchive` 更低。 操作 `tierToArchive` 的开销比 `tierToCool` 更低。

运行条件基于期限。 为了跟踪陈旧程度，基础 Blob 使用上次修改时间，Blob 版本使用版本创建时间，Blob 快照使用快照创建时间。

| 操作运行条件 | 条件值 | 说明 |
|--|--|--|
| daysAfterModificationGreaterThan | 指示陈旧程度（天）的整数值 | 基本 Blob 操作的条件 |
| daysAfterCreationGreaterThan | 指示陈旧程度（天）的整数值 | Blob 版本和 Blob 快照操作的条件 |
| daysAfterLastAccessTimeGreaterThan | 指示陈旧程度（天）的整数值 | 启用“访问跟踪”时的基本 Blob 操作条件 |

## <a name="examples-of-lifecycle-policies"></a>生命周期策略示例

以下示例演示如何使用生命周期策略规则满足常见场景。

### <a name="move-aging-data-to-a-cooler-tier"></a>将陈旧的数据移到冷层

此示例演示如何转移前缀为 `sample-container/blob1` 或 `container2/blob2` 的块 Blob。 该策略将 30 天以上未修改的 Blob 转移到冷存储，并将 90 天未修改的 Blob 转移到存档层：

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "sample-container/blob1", "container2/blob2" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-time"></a>基于上次访问时间移动数据

可以启用“上次访问时间跟踪”，以记录 Blob 上次读取或写入的时间，并将该功能用作筛选器，管理 Blob 数据的分层和保留。 若要了解如何启用“上次访问时间跟踪”，请参阅[启用访问时间跟踪（可选）](lifecycle-management-policy-configure.md#optionally-enable-access-time-tracking)。

启用上次访问时间跟踪后，在读取或写入 Blob 时会更新名为 `LastAccessTime` 的 Blob 属性。 [获取 Blob](/rest/api/storageservices/get-blob) 操作被视为访问操作。 [获取 Blob 属性](/rest/api/storageservices/get-blob-properties)、[获取 Blob 元数据](/rest/api/storageservices/get-blob-metadata)和[获取 Blob 标记](/rest/api/storageservices/get-blob-tags)不是访问操作，因此不会更新上次访问时间。

为了尽量减少对读取访问延迟的影响，只有最近 24 小时的第一次读取会更新最后访问时间。 同一个 24 小时期间内的后续读取不会更新上次访问时间。 如果 Blob 在两次读取之间被修改，则上次访问时间是两个值中的较新值。

在以下示例中，如果 Blob 在 30 天内未被访问，则会将其移到冷存储。 `enableAutoTierToHotFromCool` 属性是一个布尔值，用于指示当某个 Blob 在被分层到冷层后再次被访问时，是否应自动将其从冷层恢复到热层。

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

### <a name="archive-data-after-ingest"></a>引入数据后对其进行存档

某些数据在云中处于空闲状态，并且很少（如果有）被访问。 以下生命周期策略已配置为在引入数据后立即对其进行存档。 此示例将容器中名为 `archivecontainer` 的块 Blob 转换为存档层。 转移是通过在上次修改后的 0 天内处理 Blob 实现的：

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

> [!NOTE]
> Microsoft 建议直接将 Blob 上传到存档层，以提高效率。 可以在 [放置 Blob](/rest/api/storageservices/put-blob)或 [放置块列表](/rest/api/storageservices/put-block-list)操作内的“x-ms-access-tier”标头中指定存档层。 REST 版本 2018-11-09 及更高版本，或最新的 Blob 存储客户端库支持“x-ms-access-tier”标头。

### <a name="expire-data-based-on-age"></a>基于陈旧程度使数据过期

某些数据预期在创建后的数日或数月内过期。 可以将生命周期管理策略配置为：根据数据陈旧程度删除数据，以使数据过期。 以下示例中演示的策略删除超过 365 天的所有块 Blob。

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>删除带 Blob 索引标记的数据

某些数据只有在明确标记为删除时才会过期。 你可以配置生命周期管理策略，使标记有 Blob 索引键/值属性的数据过期。 以下示例中演示的策略会删除标有 `Project = Contoso` 的所有块 Blob。 若要详细了解 Blob 索引，请参阅[通过 Blob 索引（预览版）管理和查找 Azure Blob 存储上的数据](storage-manage-find-blobs.md)。

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>管理版本

对于在其整个生存期内定期修改和访问的数据，你可以启用 Blob 存储版本控制来自动维护对象的早期版本。 你可以创建策略，对早期版本进行分层或删除。 可通过评估版本创建时间来确定版本的陈旧程度。 此策略规则将容器 `activedata` 中版本创建时间至少为 90 天的早期版本分层到冷层，并删除版本创建时间至少为 365 天的早期版本。

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="feature-support"></a>功能支持

此表显示了你的帐户如何支持此功能，以及启用某些功能时对支持的影响。 

| 存储帐户类型                | Blob 存储（默认支持）   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>    
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 标准常规用途 v2 | ![是](../media/icons/yes-icon.png) |![是](../media/icons/yes-icon.png)              | ![是](../media/icons/yes-icon.png) | 
| 高级块 blob          | ![是](../media/icons/yes-icon.png)|![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

## <a name="regional-availability-and-pricing"></a>区域可用性和定价

生命周期管理功能在所有 Azure 区域中均可用。

生命周期管理策略不收取费用。 不过客户需要支付[设置 Blob 层](/rest/api/storageservices/set-blob-tier) API 调用的标准操作成本费用， 而删除操作亦不会收取费用。

需要在[其他操作](https://azure.microsoft.com/pricing/details/storage/blobs/)类别下支付每次 Blob 的“上次访问时间”更新所需的费用。

有关定价的详细信息，请参阅[块 Blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="faq"></a>常见问题

**我创建了一个新策略，但操作为什么没有立即运行？**

平台每天运行一次生命周期策略。 配置策略后，某些操作可能需要在长达 24 小时之后才能首次运行。

**如果更新现有策略，运行操作需要多长时间？**

已更新的策略最多需要 24 小时才能生效。 策略生效后，最多可能需要 24 小时才能执行操作。 因此，策略操作最多可能需要 48 小时才能完成。 如果更新是禁用或删除规则，并且使用了 enableAutoTierToHotFromCool，则仍将发生自动分层到热层的情况。 例如，根据上次访问设置一个包含 enableAutoTierToHotFromCool 的规则。 如果规则处于禁用/删除状态，并且 blob 当前处于冷状态并随后被访问，则该 blob 将会移回热层，因为该规则是对生命周期管理外部的访问而应用的。 由于生命周期管理规则为禁用/删除状态，该 blob 不会从热层移动到冷层。  阻止 autoTierToHotFromCool 的唯一方法就是关闭上次访问时间跟踪。

**我手动解冻了某个存档的 Blob，如何防止它暂时性地移回到存档层？**

将 Blob 从一个访问层移到另一个访问层后，其上次修改时间不会更改。 如果手动将存档的 Blob 解冻到热层，生命周期管理引擎会将它移回到存档层。 暂时禁用影响此 Blob 的规则可防止该 Blob 再次存档。 可以安全地将 Blob 移回到存档层时，重新启用该规则即可。 如果需要将 Blob 永久保留在热层或冷层，也可以将其复制到另一个位置。

## <a name="next-steps"></a>后续步骤

- [配置生命周期管理策略](lifecycle-management-policy-configure.md)
- [Azure Blob 存储的访问层 - 热、冷和存档](storage-blob-storage-tiers.md)
- [通过 Blob 索引管理和查找 Azure Blob 存储上的数据](storage-manage-find-blobs.md)
