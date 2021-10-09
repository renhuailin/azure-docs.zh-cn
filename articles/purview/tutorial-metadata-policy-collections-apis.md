---
title: Azure Purview 集合元数据策略和角色 API 快速入门 – 对 Purview 集合管理基于角色的访问控制
description: 本教程讨论如何通过 Azure Purview API，对企业中的用户、组或服务主体管理对这些集合的基于角色的访问控制。
author: abandyop
ms.author: arindamba
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 09/24/2021
ms.openlocfilehash: 52c38e40d96b6d6f650a8b57fe2d5826a9a81842
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219679"
---
# <a name="tutorial-use-rest-apis-to-manage-role-based-access-control-rbac-on-azure-purview-collections"></a>教程：使用 REST API 对 Azure Purview 集合管理基于角色的访问控制 (RBAC) 

2021 年 8 月，Purview 中的访问控制从 Azure IAM（控制平面）转移到了 [Azure Purview 集合](how-to-create-and-manage-collections.md)（数据平面）。 此更改使企业数据管护者和管理员可以对 Purview 扫描的数据源进行更精确的粒度访问控制，并使组织能够审核对其数据的正确访问和正确使用。

本教程将指导你逐步使用 [Azure Purview 元数据策略 API](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml) 向集合添加用户、组或服务主体，以及在该集合中管理或删除其角色。 REST API 是使用 Azure 门户或 Purview Studio 实现相同基于角色的粒度访问控制的替代方法。

[Purview 权限指南](catalog-permissions.md#roles)介绍有关 Azure Purview 中的每个内置角色的详细信息，并将角色映射到向用户授予的访问权限级别。


## <a name="metadata-policy-api-reference-summary"></a>元数据策略 API 参考摘要
此表提供 [Azure Purview 元数据策略 API 参考](/rest/api/purview/metadatapolicydataplane/Metadata-Policy.yml)的概述。 运行这些 API 之前，请将 {pv-acc-name} 替换为 Purview 帐户的名称。 例如，如果 Purview 帐户名称为“FabrikamPurviewAccount”，则 API 终结点会是“FabrikamPurviewAccount.purvi”

|API 函数|REST 方法|API 终结点|**说明**|
|:-|:-|:-|:-|
|读取所有元数据角色|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataroles?&api-version=2021-07-01| 从 Purview 帐户读取所有元数据角色|
|按集合名称读取元数据策略|GET|https://{pv-acc-name}.purview.azure.com /policystore/collections/{collectionName}/metadataPolicy?&api-version=2021-07-01| 按给定集合名称（在创建策略时由 Purview 生成的 6 字符随机名称）读取元数据策略|
|按 PolicyID 读取元数据策略|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| 按给定 PolicyID 读取元数据策略。 策略 ID 采用 GUID 格式。|
|读取所有元数据策略|GET|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies?&api-version=2021-07-01| 从 Purview 帐户读取所有元数据策略。 可以从此 API 发出的 JSON 输出列表中选择要使用的特定策略。|
|更新/放置元数据策略|PUT|https://{pv-acc-name}.purview.azure.com /policystore/metadataPolicies/{policyId}?&api-version=2021-07-01| 按给定 PolicyID 更新元数据策略。 策略 ID 采用 GUID 格式。|

## <a name="purview-catalog-collections-api-reference-summary"></a>Purview 目录集合 API 参考摘要
此表提供 Purview 集合 API 的概述。 单击下面的每个 API“操作”可获取有关每个 API 的完整文档。

| 操作 | **说明** |
|:-|:-|
| [创建或更新集合](/rest/api/purview/accountdataplane/collections/create-or-update-collection) | 创建或更新集合实体。 |
| [删除集合](/rest/api/purview/accountdataplane/collections/delete-collection) |删除集合实体。 |
| [获取集合](/rest/api/purview/accountdataplane/collections/get-collection) |获取集合|
| [获取集合路径](/rest/api/purview/accountdataplane/collections/get-collection-path) |获取表示集合路径的父名称和显示名称链。|
| [列出子集合名称](/rest/api/purview/accountdataplane/collections/list-child-collection-names) |列出集合中的子集合名称。|
| [列出集合](/rest/api/purview/accountdataplane/collections/list-collections) |列出帐户中的集合。|


- 如果使用 API，则执行 API 的服务主体 (SP)、用户或组应具有在 Purview 中分配的[集合管理员](how-to-create-and-manage-collections.md#check-permissions)角色，才能成功执行此 API。
- 对于所有需要 {collectionName} 的 Purview API，都需要“name”（而不是“friendlyName”）。 将 {collectionName} 替换为实际的六字符字母数字集合名称字符串。 请注意，这与创建集合时提供的友好显示名称不同。 如果你没有此 {collectionName}，请使用[列出集合](/rest/api/purview/accountdataplane/collections/list-collections) API 从 JSON 输出中选择六字符集合名称。
- 示例 JSON： 

```json
{
    "name": "74dhe7", 
    "friendlyName": "Friendly Name",
    "parentCollection": {
        "type": "CollectionReference",
        "referenceName": "{your_purview_account_name}"
    },
    "systemData": {
        "createdBy": "{guid}",
        "createdByType": "Application",
        "createdAt": "2021-08-26T21:21:51.2646627Z",
        "lastModifiedBy": "7f8d47e2-330c-42f0-8744-fcfb1ecb3ea0",
        "lastModifiedByType": "Application",
        "lastModifiedAt": "2021-08-26T21:21:51.2646628Z"
    },
    "collectionProvisioningState": "Succeeded"
}
```

### <a name="policy-json-description"></a>策略 JSON 说明
从集合 API 收到的 JSON 输出中一些重要标识符的详细信息。

Name：策略的名称。 

Id：策略的唯一标识符

Version：策略的最新版本号。 \*\*（每次调用 Update-Metadata-Policy API 时，版本号都会递增。 确保通过调用 Get-Policy-by-Policy-ID API 来提取策略的最新副本。 每次调用更新策略（放置）API 之前必须执行此刷新，以便始终具有最新版本的 JSON）

DecisionRules：列出此策略的规则和效果。 对于元数据策略，效果始终为“Permit”。

## <a name="use-purview-rest-apis-to-add-or-remove-usergroupserviceprincipal-to-a-collection-or-role"></a>使用 Purview REST API 对集合或角色添加或删除用户/组/服务主体
API 的详细用法随示例 JSON 输出一起提供。 强烈建议按顺序执行以下步骤，以便充分了解 Purview 元数据策略 API。

## <a name="get-all-metadata-roles"></a>获取所有元数据角色
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataroles?api-version=2021-07-01
```
列出所有可用的元数据访问权限角色。

输出 JSON 会采用此格式描述角色及其关联权限。

### <a name="default-metadata-roles"></a>默认元数据角色

|**角色 ID**|**权限**|角色说明|
|:-|:-|:-|
|purviewmetadatarole\_builtin\_data-source-administrator|Microsoft.Purview/accounts/scan/read Microsoft.Purview/accounts/scan/write Microsoft.Purview/accounts/collection/read|向其他人授予读取、写入集合、注册数据源和触发扫描的访问权限。|
|purviewmetadatarole\_builtin\_collection-administrator|Microsoft.Purview/accounts/collection/read Microsoft.Purview/accounts/collection/write|对整个集合的管理员级别完全访问权限，包括对集合添加、删除用户和 SPN、管理权限、授予和撤销访问权限。 在某些情况下，集合管理员可能与集合创建者不同。|
|purviewmetadatarole\_builtin\_purview-reader|Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/collection/read|仅授予对数据处理和所有元数据包括（集合中的分类、敏感度标签、见解、读取资产）的读取访问权限（扫描绑定除外）。|
|purviewmetadatarole\_builtin\_data-curator|Microsoft.Purview/accounts/data/read Microsoft.Purview/accounts/data/write Microsoft.Purview/accounts/collection/read|授予对数据处理和所有元数据包括（集合中的分类、敏感度标签、见解、读取资产）的完全访问权限（扫描绑定除外）。|
|purviewmetadatarole\_builtin\_data-share-contributor|Microsoft.Purview/accounts/share/read Microsoft.Purview/accounts/share/write|以参与者身份授予对数据共享的访问权限|

```json
{
  "values": [
    {
      "id": "purviewmetadatarole_builtin_data-curator",
      "name": "data-curator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Curator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/data/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-source-administrator",
      "name": "data-source-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data Source Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/scan/read",
                "Microsoft.Purview/accounts/scan/write",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_collection-administrator",
      "name": "collection-administrator",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Collection Administrator",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/collection/read",
                "Microsoft.Purview/accounts/collection/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_purview-reader",
      "name": "purview-reader",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Purview Reader",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/data/read",
                "Microsoft.Purview/accounts/collection/read"
              ]
            }
          ]
        ],
        "version": 1
      }
    },
    {
      "id": "purviewmetadatarole_builtin_data-share-contributor",
      "name": "data-share-contributor",
      "type": "Microsoft.Purview/role",
      "properties": {
        "provisioningState": "Provisioned",
        "roleType": "BuiltIn",
        "friendlyName": "Data share contributor",
        "cnfCondition": [
          [
            {
              "attributeName": "request.azure.dataAction",
              "attributeValueIncludedIn": [
                "Microsoft.Purview/accounts/share/read",
                "Microsoft.Purview/accounts/share/write"
              ]
            }
          ]
        ],
        "version": 1
      }
    }
  ]
}
```

## <a name="get-all-metadata-policies"></a>获取所有元数据策略
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies?api-version=2021-07-01
```
列出整个集合层次结构中所有可用的元数据策略（从根集合开始，其所有子策略都采用树格式）。 层次结构从顶部的根集合开始，后跟其子集合。 每个子集合封装其每个下一级子级。
示例：

```json
{
  "values": [
    {
      "name": "policy_FabrikamPurview",
      "id": "9b2f1cb9-584c-4a16-811e-9232884b5cac",
      "version": 30,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "fabrikampurview"
                },
                {
                  "fromRule": "permission:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "8988fe5c-5736-4179-9435-0a64c273b90b",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9",
                    "26f98046-5b02-4fa9-b709-e0519c658891",
                    "73fc02dc-becd-468b-a2a3-82238e722dae"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "ffd851fa-86ec-431b-95ea-8b84d5012383",
                    "cf84b126-4384-4952-91f1-7f705b25e569",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b",
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "649f56ab-2dd2-40de-a731-3d3f28e7af92",
                    "c29a5809-f9ec-49fd-b762-2d4d64abb93e",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "73fc02dc-becd-468b-a2a3-82238e722dae",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "name": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0",
                    "04314867-60a4-4e5a-ae16-8e5856f415d9",
                    "517a27d2-39ba-4c91-a032-dd9ecf8ad6f1",
                    "6d563253-1d5b-48f2-baaa-5489f22ddce9"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator"
                },
                {
                  "attributeName": "principal.microsoft.groups",
                  "attributeValueIncludedIn": [
                    "b055a5c6-a04e-4d1a-8524-001ad81bfb28",
                    "cc194892-92fa-4ce3-96ae-1f98bef8211c",
                    "d34eb741-be5e-4098-90d7-eca8d4a5153f",
                    "664ec992-9af0-4773-88f2-dc39edc46f6f",
                    "5046aba1-5b81-411c-8fec-b84600f3f08b"
                  ]
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:fabrikampurview",
            "name": "permission:fabrikampurview",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_purview-reader:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_purview-reader:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-curator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-curator:fabrikampurview"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_data-source-administrator:fabrikampurview"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "fabrikampurview"
        }
      }
    },
    {
      "name": "policy_b2zpf1",
      "id": "12b0bb28-2acc-413e-8fe1-179ff9cc54c3",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "b2zpf1"
                },
                {
                  "fromRule": "permission:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:b2zpf1"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "name": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:b2zpf1",
            "name": "permission:b2zpf1",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:b2zpf1",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:b2zpf1"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "b2zpf1"
        },
        "parentCollectionName": "ukx7pq"
      }
    },
    {
      "name": "policy_7wte2n",
      "id": "a72084e4-ccab-4aec-a364-08ab001e4999",
      "version": 0,
      "properties": {
        "description": "",
        "decisionRules": [
          {
            "kind": "decisionrule",
            "effect": "Permit",
            "dnfCondition": [
              [
                {
                  "attributeName": "resource.purview.collection",
                  "attributeValueIncludes": "7wte2n"
                },
                {
                  "fromRule": "permission:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:7wte2n"
                }
              ]
            ]
          }
        ],
        "attributeRules": [
          {
            "kind": "attributerule",
            "id": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "name": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
            "dnfCondition": [
              [
                {
                  "attributeName": "principal.microsoft.id",
                  "attributeValueIncludedIn": [
                    "2f656762-e440-4b62-9eb6-a991d17d64b0"
                  ]
                },
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator",
                  "attributeName": "derived.purview.role",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
                }
              ],
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:ukx7pq"
                }
              ]
            ]
          },
          {
            "kind": "attributerule",
            "id": "permission:7wte2n",
            "name": "permission:7wte2n",
            "dnfCondition": [
              [
                {
                  "fromRule": "purviewmetadatarole_builtin_collection-administrator:7wte2n",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:7wte2n"
                }
              ],
              [
                {
                  "fromRule": "permission:ukx7pq",
                  "attributeName": "derived.purview.permission",
                  "attributeValueIncludes": "permission:ukx7pq"
                }
              ]
            ]
          }
        ],
        "collection": {
          "type": "CollectionReference",
          "referenceName": "7wte2n"
        },
        "parentCollectionName": "ukx7pq"
      }
    }
  ]
}
```

## <a name="get-selected-metadata-policy"></a>获取所选元数据策略 
有两个 API 可用于提取特定集合的元数据策略的 JSON 结构 - 通过提供 {collectionName} 或 {PolicyID}。
这两个 API（在下面两个部分中介绍）具有相同用途，并且两者的 JSON 输出完全相同。

### <a name="get-metadatapolicy-of-the-collection-by-collectionname"></a>按 collectionName 获取集合的 metadataPolicy
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/collections/{collectionName}/metadataPolicy?api-version=2021-07-01
```
1. Purview 帐户名称为 {your_purview_account_name}。 将其替换为 Purview 帐户名称。
1. 在前面 API“获取所有元数据策略”的 JSON 输出中，查找以下部分：{ "type": "CollectionReference", "referenceName": "7xkdg2"}
1. 将 API URL 中的“{collectionName}”替换为“referenceName”的值：“{6 字符集合名称}”。 因此，如果 6 字符集合名称为“7xkdg2”，则 API URL 类似于 https://{your_purview_account_name}.purview.azure.com/policystore/collections/7xkdg2/metadataPolicy?api-version=2021-07-01
1. 现在执行此 API。 

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```

### <a name="get-metadatapolicy-of-the-collection-by-policyid"></a>按 policyID 获取集合的 metadataPolicy
```ruby
GET https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```
1. Purview 帐户名称为 {your_purview_account_name}。 替换为 Purview 帐户名称。
1. 在前面 API“获取所有元数据策略”的 JSON 输出中，查找以下部分：{.... "name": "policy_qu45fs", "id": "{policy-guid}", "version": N ....}
1. 将 API URL 中的“{policyId}”替换为“id”的值。 因此，如果“{policy-guid}”为“c6639bb2-9c41-4be0-912b-775750e725de”，则 API URL 会类似于 https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/c6639bb2-9c41-4be0-912b-775750e725de?api-version=2021-07-01
1. 现在执行此 API。 请注意，此 API 调用的输出与前面 API 调用的输出相同。 如本教程前面所述，可以选择其中任一个。

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```


## <a name="update-policy-addremove-usergroup-from-collection"></a>更新策略：对集合添加/删除用户/组
```ruby
PUT https://{your_purview_account_name}.purview.azure.com/policystore/metadataPolicies/{policyId}?api-version=2021-07-01
```

此步骤会更新在上一步获取的策略 JSON，然后使用 PUT REST 方法将它推送到 Purview 服务。
无论是要添加还是删除用户/组/SP（服务主体），都会遵循相同的 API 过程 。

1. 在 JSON 的“attributeValueIncludedIn”数组中提供用户/组/服务主体对象 ID {guid}。
1. 在上一步的 Get-Policy-by-ID API JSON 输出搜索“attributeValueIncludedIn”数组，然后在该数组中添加或删除用户/组/服务主体对象 ID 。 如果不确定如何提取用户或组对象 ID，请阅读本教程 [Get-AzureADUser](/powershell/module/azuread/get-azureaduser)
1. 请注意，JSON 中会有多个部分映射到 4 个角色中的每个角色。 对于集合管理员权限角色，请使用包含名为“purviewmetadatarole_builtin_collection-administrator”的“ID”的部分。 同样，为其他角色使用对应部分。
1. 为了更好地了解添加/删除操作，请仔细检查前面 API 的 JSON 输出与下面输出的区别。 你会注意到，在下面的 JSON 输出中，我们添加了用户 ID：“3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f”作为集合管理员。

```json
{
  "name": "policy_qu45fs",
  "id": "c6639bb2-9c41-4be0-912b-775750e725de",
  "version": 0,
  "properties": {
    "description": "",
    "decisionRules": [
      {
        "kind": "decisionrule",
        "effect": "Permit",
        "dnfCondition": [
          [
            {
              "attributeName": "resource.purview.collection",
              "attributeValueIncludes": "qu45fs"
            },
            {
              "fromRule": "permission:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:qu45fs"
            }
          ]
        ]
      }
    ],
    "attributeRules": [
      {
        "kind": "attributerule",
        "id": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "name": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
        "dnfCondition": [
          [
            {
              "attributeName": "principal.microsoft.id",
              "attributeValueIncludedIn": [
                "2f656762-e440-4b62-9eb6-a991d17d64b0",
                "3a3a3a3a-2c2c-4b4b-1c1c-2a3b4c5d6e7f"
              ]
            },
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator",
              "attributeName": "derived.purview.role",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator"
            }
          ],
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:fabrikampurview"
            }
          ]
        ]
      },
      {
        "kind": "attributerule",
        "id": "permission:qu45fs",
        "name": "permission:qu45fs",
        "dnfCondition": [
          [
            {
              "fromRule": "purviewmetadatarole_builtin_collection-administrator:qu45fs",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "purviewmetadatarole_builtin_collection-administrator:qu45fs"
            }
          ],
          [
            {
              "fromRule": "permission:fabrikampurview",
              "attributeName": "derived.purview.permission",
              "attributeValueIncludes": "permission:fabrikampurview"
            }
          ]
        ]
      }
    ],
    "collection": {
      "type": "CollectionReference",
      "referenceName": "qu45fs"
    },
    "parentCollectionName": "fabrikampurview"
  }
}
```
## <a name="add-root-collection-admin"></a>添加根集合管理员
默认情况下，创建 Purview 帐户的用户是根集合管理员（集合层次结构最上层的管理员）。 但是在某些情况下，组织需要使用 API 更改根集合管理员。 例如，当前根集合管理员可能不再存在于组织中。 在这些情况下，可能组织中的任何人都问啊访问 Azure 门户，因此，使用 API 分配新集合管理员和管理集合权限便不可避免，这是重新获得对 Purview 帐户的访问权限的唯一方法。

```ruby
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Purview/accounts/{accountName}/addRootCollectionAdmin?api-version=2021-07-01
```
只需传递新的根集合管理员的对象 ID。 如前所述，该对象 ID 可以是任何用户、组或服务主体的对象 ID。
```json
{"objectId": "{guid}"}
```
成功后，所有 REST API 都会返回 HTTP 响应 200 正常。

> [!NOTE]
> 调用此 API 的用户必须具有 Purview 帐户的所有者或 UAA 权限，才能对帐户执行写入操作。


##  <a name="purview-rest-api-combined-archive"></a>Purview REST API 合并存档
为了帮助你快速开始使用 Purview API - [下载](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/azure-purview-rest-api-specs.zip)在一个位置收集到一起的所有 Purview REST API 规范的存档：[azure-purview-rest-api-specs.zip](https://github.com/Azure/Azure-Purview-API-PowerShell/blob/main/azure-purview-rest-api-specs.zip) 可以使用这些 API 模板来了解和构建自己的 Purview API 代码、脚本、自动化、[AutoRest](https://github.com/Azure/autorest) 或 Postman 集合。

## <a name="powershell-utility-to-run-purview-apis"></a>用于运行 Purview API 的 PowerShell 实用程序
可以选择使用 PowerShell 实用程序 [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 执行 Purview REST API。 它可以从 PowerShell 库随时安装。 它会执行所有相同的命令（不过是从Windows PowerShell 执行）。

> [!div class="nextstepaction"] 
> [Purview-API-PowerShell](https://aka.ms/purview-api-ps) 


