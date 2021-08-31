---
title: 使用 REST API 创建 blob 的 Azure 备份策略。
description: 本文介绍如何使用 REST API 创建用于在存储帐户中备份 blob的策略。
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 93861379-5bec-4ed5-95d2-46f534a115fd
ms.openlocfilehash: 9b32e69bcd96d48dcd1321a69132790a93b1220b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598579"
---
# <a name="create-azure-backup-vault-using-rest-api"></a>使用 REST API 创建 Azure 备份保管库

Azure 备份的新数据保护平台为较新的工作负荷（例如存储帐户中的 blob、托管磁盘和 PostGre SQL 服务器的 PaaS 平台）提供了增强的备份和还原功能。 它旨在最大程度地降低管理开销，同时简化备份的组织。 “备份保管库”是数据保护平台的基础，这不同于“恢复服务”保管库。

[创建保管库 REST API](/rest/api/dataprotection/backup-vaults/create-or-update) 文档中概述了使用 REST API 创建 Azure 备份保管库的步骤。 让我们使用本文档作为参考在“美国西部”区域中“TestBkpVaultRG”资源组下面创建一个名为“testBkpVault”的保管库。

若要创建或更新 Azure 备份保管库，请使用以下 *PUT* 操作。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/testBkpVault?api-version=2021-01-01
```

## <a name="create-a-request"></a>创建请求

若要创建 *PUT* 请求，必须使用 `{subscription-id}` 参数。 如果拥有多个订阅，请参阅[使用多个订阅](/cli/azure/manage-azure-subscriptions-azure-cli)。 你将定义资源的 `{resourceGroupName}` 和 `{vaultName}` 以及 `api-version` 参数。 本文使用的是 `api-version=2021-01-01`。

以下标头是必需的：

| 请求标头   | 说明 |
|------------------|-----------------|
| Content-Type：   | 必需。 设置为 `application/json`。 |
| Authorization：  | 必需。 设置为有效的`Bearer` [访问令牌](/rest/api/azure/#authorization-code-grant-interactive-clients)。 |

有关如何创建请求的详细信息，请参阅 [REST API 请求/响应的组件](/rest/api/azure/#components-of-a-rest-api-requestresponse)。

## <a name="create-the-request-body"></a>创建请求正文

下面的通用定义用来构建请求正文：

|名称  |必须  |类型  |说明  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  可选的 eTag       |
|location     |  是       |String         |   资源位置      |
|properties     |   是      | [BackupVault](/rest/api/dataprotection/backup-vaults/create-or-update#backupvault)        |  保管库的属性       |
|标识     |         |  [DPPIdentityDetails](/rest/api/dataprotection/backup-vaults/create-or-update#dppidentitydetails)       |    指明每个 Azure 资源的唯一系统标识符     |
|标记     |         | Object        |     资源标记    |

请注意，保管库名称和资源组名称是在 PUT URI 中提供的。 请求正文定义位置。

## <a name="example-request-body"></a>示例请求正文

以下示例正文用来在“美国西部”区域中创建一个保管库。 指定位置。

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "None"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

如果要创建备份保管库并同时生成系统分配的标识，则应提供以下请求正文。

```json
{
  "location": "WestUS",
  "tags": {
    "key1": "val1"
  },
  "identity": {
    "type": "systemAssigned"
  },
  "properties": {
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  }
}
```

## <a name="responses"></a>响应

创建备份保管库的是一种[异步操作](../azure-resource-manager/management/async-operations.md)。 这意味着，此操作会创建另一个需要单独跟踪的操作。
对于要创建或更新备份保管库的操作，有两个成功响应：

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |   [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)      | OK        |
|201 Created     | [BackupVaultResource](/rest/api/dataprotection/backup-vaults/create-or-update#backupvaultresource)        |   创建      |
| 其他状态代码  |  [CloudError](/rest/api/dataprotection/backup-vaults/create-or-update#clouderror)

有关 REST API 响应的详细信息，请参阅[处理响应消息](/rest/api/azure/#process-the-response-message)。

### <a name="example-response"></a>示例响应

下面是前面的示例请求正文的精简后 *201 Created* 响应，该响应表明已分配了一个 *id* 并且 *provisioningState* 为 *Succeeded*：

```json
{
    "eTag": null,
    "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/TestBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/testBkpVault",
    "identity": {
      "principalId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenantId": "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "SystemAssigned"
    },
    "location": "westUS",
    "name": "testBkpVault",
    "properties": {
      "provisioningState": "Succeeded",
      "storageSettings": [
        {
          "datastoreType": "VaultStore",
          "type": "GeoRedundant"
        }
      ]
    },
    "resourceGroup": "TestBkpVaultRG",
    "systemData": null,
    "tags": {},
    "type": "Microsoft.DataProtection/backupVaults"
  }
```

## <a name="next-steps"></a>后续步骤

[创建用于在此保管库中备份 blob 的备份策略](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md)。

有关 Azure REST API 的详细信息，请参阅以下文档：

- [Azure 数据保护提供程序 REST API](/rest/api/dataprotection/)
- [Azure REST API 入门](/rest/api/azure/)
