---
title: 使用 Azure 数据保护 REST API 备份存储帐户中的 blob。
description: 本文介绍如何使用 REST API 配置、启动和管理 blob 的备份操作。
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 7c244b94-d736-40a8-b94d-c72077080bbe
ms.openlocfilehash: 709579f814dde3e1972888b0edea18069334b6dd
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598580"
---
# <a name="back-up-blobs-in-a-storage-account-using-azure-data-protection-via-rest-api"></a>通过 REST API 使用 Azure 数据保护备份存储帐户中的 blob

本文介绍如何通过 REST API 管理存储帐户中的 blob 的备份 Blob 的备份在存储帐户级别进行配置。 因此，存储帐户中的所有 blob 都受操作备份保护。

有关 Azure Blob 区域可用性、支持的方案和限制，请参阅[支持矩阵](blob-backup-support-matrix.md)。

## <a name="prerequisites"></a>先决条件

- [创建备份保管库](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [创建 blob 备份策略](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md)

## <a name="configure-backup"></a>配置备份

创建保管库和策略后，用户需要考虑 2 个关键点来保护存储帐户中的所有 Azure Blob。

### <a name="key-entities-involved"></a>所涉关键实体

#### <a name="storage-account-which-contains-the-blobs-to-be-protected"></a>含有要保护的 Blob 的存储帐户

提取含有要保护的 Blob 的存储帐户的 Azure 资源管理器 ID。 用作存储帐户的标识符。 我们将在不同的订阅中使用名为 msblobbackup 的示例存储帐户（资源组 RG-BlobBackup 下），且区域为美国西部 。

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup"
```

#### <a name="backup-vault"></a>备份保管库

备份保管库需要存储帐户的权限才能对存储帐户中的 Blob 启用备份。 保管库的系统分配托管标识用于分配此类权限。 我们将使用名为“testBkpVault”的备份保管库示例，它位于资源组“TestBkpVaultRG”下，区域为“美国西部”。

### <a name="assign-permissions"></a>分配权限

你需要通过 RBAC 向保管库（以保管库 MSI 表示）和相关存储帐户分配一些权限。 可以通过门户或 PowerShell 或 REST API 执行上述操作。 详细了解所有[相关权限](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts)。

### <a name="prepare-the-request-to-configure-backup"></a>准备配置备份的请求

为保管库和存储帐户设置相关权限并配置保管库和策略后，即可准备请求配置备份。 下面是为存储帐户内的所有 Blob 配置备份的请求正文。 在“datasourceinfo”部分中提到了存储帐户的 Azure 资源管理器 ID (ARM ID) 及其详细信息，策略信息则出现在“policyinfo”部分中。

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westUS",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    },
    "objectType": "BackupInstance"
  }
}
```

### <a name="validate-the-request-to-configure-backup"></a>验证配置备份的请求

我们可以使用[验证备份 API](/rest/api/dataprotection/backup-instances/validate-for-backup) 验证配置备份请求是否能成功。 客户可以参考响应来执行所有必需的先决条件，然后提交备份请求的配置。

验证备份请求是 POST 操作，并且 URI 具有 `{subscriptionId}`、`{vaultName}` 和 `{vaultresourceGroupName}` 参数。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.DataProtection/backupVaults/{backupVaultName}/validateForBackup?api-version=2021-01-01
```

例如，这转换为

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/validateForBackup?api-version=2021-01-01
```

我们之前准备的[请求正文](#prepare-the-request-to-configure-backup)将用于提供要保护的存储帐户的详细信息。

#### <a name="example-request-body"></a>示例请求正文

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westUS",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-for-validate-backup-request"></a>验证备份请求的响应

验证备份请求是一个[异步操作](../azure-resource-manager/management/async-operations.md)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它将返回两个响应：创建另一个操作时为 202（已接受），该操作完成时为 200（正常）。

|名称  |类型  |说明  |
|---------|---------|---------|
|202 已接受     |         |  该操作将异步完成      |
|200 正常     |   [OperationJobExtendedInfo](/rest/api/dataprotection/backup-instances/validate-for-backup#operationjobextendedinfo)      |     已接受    |
| 其他状态代码 |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    描述操作失败原因的错误响应    |

##### <a name="example-responses-for-validate-backup-request"></a>验证备份请求的响应示例

###### <a name="error-response"></a>错误响应

如果给定的存储帐户已受到保护，则响应为 HTTP 400（错误请求），并清楚地指出给定存储帐户受备份保管库的保护以及详细信息。

```http
HTTP/1.1 400 BadRequest
Content-Length: 999
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: f36eb67a-8932-42a8-8aba-c5ee2443aa2e
x-ms-routing-request-id: WESTUS:20210707T124745Z:bcd23af5-fa17-4cd0-9929-a55f141e33ce
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:47:45 GMT
X-Powered-By: ASP.NET

{
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
          "recommendedAction": [
            "Delete the backup instance msblobbackuptemp from the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault to re-protect the datasource in any other vault."
          ],
          "details": null,
          "code": "UserErrorDppDatasourceAlreadyProtected",
          "target": "",
          "innerError": null,
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "f36eb67a-8932-42a8-8aba-c5ee2443aa2e"
          }
        }
      }
    ],
    "code": "UserErrorDppDatasourceAlreadyProtected",
    "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
    "target": null
  }
}
```

###### <a name="tracking-response"></a>跟踪响应

如果数据源未受保护，API 将继续进一步验证并创建跟踪操作。

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 3e7cacb3-65cd-4b3c-8145-71fe90d57327
x-ms-routing-request-id: CENTRALUSEUAP:20210707T124850Z:105f2105-6db1-44bf-8a34-45972a8ba861
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:48:50 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

使用带有简单 GET 命令的“Azure-AsyncOperation”标头跟踪生成的操作

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Inprogress",
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "0001-01-01T00:00:00"
}
```

完成后，它会返回 200（正常），响应正文会列出需要满足的更多要求，例如权限。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Failed",
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Appropriate permissions to perform the operation is missing.",
          "recommendedAction": [
            "Grant appropriate permissions to perform this operation as mentioned at https://aka.ms/UserErrorMissingRequiredPermissions and retry the operation."
          ],
          "code": "UserErrorMissingRequiredPermissions",
          "target": "",
          "innerError": {
            "code": "UserErrorMissingRequiredPermissions",
            "additionalInfo": {
              "DetailedNonLocalisedMessage": "Validate for Protection failed. Exception Message: The client 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' with object id 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup/providers/Microsoft.Authorization' or the scope is invalid. If access was recently granted, please refresh your credentials."
            }
          },
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "3e7cacb3-65cd-4b3c-8145-71fe90d57327"
          }
        }
      }
    ],
    "code": "UserErrorMissingRequiredPermissions",
    "message": "Appropriate permissions to perform the operation is missing."
  },
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "2021-07-07T12:49:22Z"
}
```

授予所有权限后，重新提交验证请求，跟踪生成的操作，如果满足所有条件，则会返回 200（正常）表示成功。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "status": "Succeeded",
  "startTime": "2021-07-07T13:03:54.8627251Z",
  "endTime": "2021-07-07T13:04:06Z"
}
```

### <a name="configure-backup-request"></a>配置备份请求

验证请求后，可以将相同内容提交到[创建备份实例 API](/rest/api/dataprotection/backup-instances/create-or-update)。 备份实例表示一个项，该项受备份保管库中 Azure 备份的数据保护服务保护。 在这种情况下，存储帐户是备份实例，可以使用前面经过验证的同一请求正文，只需添加一点内容。

必须为备份实例确定一个唯一名称，因此建议使用资源名称和唯一标识符的组合。 我们此处将使用示例“msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d”，并将该示例标记为备份实例名称。

若要创建或更新备份实例，请使用以下 PUT 操作。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/{BkpvaultName}/backupInstances/{UniqueBackupInstanceName}?api-version=2021-01-01
```

例如，这转换为

```http
 PUT https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d?api-version=2021-01-01
```

#### <a name="create-the-request-for-configure-backup"></a>创建配置备份的请求

要创建备份实例，下面是请求正文的组成部分

|名称  |类型  |说明  |
|---------|---------|---------|
|properties     |  [BackupInstance](/rest/api/dataprotection/backup-instances/create-or-update#backupinstance)       |     BackupInstanceResource 属性    |

##### <a name="example-request-for-configure-backup"></a>配置备份请求示例

我们将使用用于验证备份请求的请求正文，并采用唯一名称，[如上](#configure-backup)所述。

```json
{
  "name": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupvaults/backupInstances",
  "properties": {
    "objectType": "BackupInstance",
    "datasourceinfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    }
  }
}
```

#### <a name="responses-to-configure-backup-request"></a>配置备份请求的响应

创建备份实例请求是一个[异步操作](../azure-resource-manager/management/async-operations.md)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它返回两种响应：创建备份实例并配置保护时返回 201（已创建），配置完成时返回 200（正常）。

|名称  |类型  |说明  |
|---------|---------|---------|
|201 Created   |   [备份实例](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)      |  创建备份实例并配置保护      |
|200 正常     |    [备份实例](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)     |     已配置保护    |
| 其他状态代码 |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    描述操作失败原因的错误响应    |

##### <a name="example-responses-to-configure-backup-request"></a>配置备份请求的响应示例

提交 PUT 请求以创建备份实例后，初始响应是带有 Azure-asyncOperation 标头的 201（已创建）。 请注意，请求正文包含所有备份实例属性。

```http
HTTP/1.1 201 Created
Content-Length: 1149
Content-Type: application/json
Expires: -1
Pragma: no-cache
Retry-After: 15
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 5d9ccf1b-7ac1-456d-8ae3-36c93c0d2427
x-ms-routing-request-id: CENTRALUSEUAP:20210707T170219Z:9e897266-5d86-4d13-b298-6561c60cf043
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 17:02:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "name": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances",
  "properties": {
    "friendlyName": "msblobbackup",
    "dataSourceInfo": {
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceUri": "",
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceLocation": "westus",
      "objectType": "Datasource"
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy"
    },
    "protectionStatus": {
      "status": "ConfiguringProtection"
    },
    "currentProtectionState": "ConfiguringProtection",
    "provisioningState": "Provisioning",
    "objectType": "BackupInstance"
  }
}
```

然后通过简单的 GET 命令使用“Azure-AsyncOperation”标头跟踪生成的操作。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
```

操作完成后，它将在响应正文中返回 200（正常）和成功消息。

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "status": "Succeeded",
  "startTime": "2021-07-07T17:02:19.0611871Z",
  "endTime": "2021-07-07T17:02:20Z"
}
```

> [!IMPORTANT]
> 为 Blob 备份配置存储帐户后，一些功能会受到影响，例如更改源和删除锁。 [了解详细信息](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts)。

### <a name="stop-protection-and-delete-data"></a>停止保护并删除数据

若要删除对存储帐户的保护并删除备份数据，请执行[此处](/rest/api/dataprotection/backup-instances/delete)详述的删除操作。

停止保护和删除数据是一种 DELETE 操作。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}?api-version=2021-01-01
```

对于我们的示例，这已转换为：

```http
DELETE "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d?api-version=2021-01-01"
```

#### <a name="responses-for-delete-protection"></a>针对删除保护的响应

DELETE 操作是一种[异步操作](../azure-resource-manager/management/async-operations.md)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它将返回两个响应：创建另一个操作时为 202（已接受），该操作完成时为 200（正常）。

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |         |  删除请求的状态       |
|202 已接受     |         |     已接受    |

##### <a name="example-responses-for-delete-protection"></a>删除保护的响应示例

提交 DELETE 请求后，初始响应将为带有 Azure-asyncOperation 标头的 202（已接受）。

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-deletes: 14999
x-ms-correlation-request-id: fee7a361-b1b3-496d-b398-60fed030d5a7
x-ms-routing-request-id: CENTRALUSEUAP:20210708T071330Z:5c3a9f3e-53aa-4d5d-bf9a-20de5601b090
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 07:13:29 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

使用简单的 GET 请求来跟踪 Azure-AsyncOperation 标头。 请求成功后，它将返回 200（正常），并返回成功状态响应。

```http
GET "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01"

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "status": "Succeeded",
  "startTime": "2021-07-08T07:13:30.23815Z",
  "endTime": "2021-07-08T07:13:46Z"
}
```

## <a name="next-steps"></a>后续步骤

[从 Azure Blob 备份还原数据](backup-azure-arm-userestapi-restoreazurevms.md)。

有关 Azure 备份 REST API 的详细信息，请参阅以下文档：

- [Azure 数据保护提供程序 REST API](/rest/api/dataprotection/)
- [Azure REST API 入门](/rest/api/azure/)
