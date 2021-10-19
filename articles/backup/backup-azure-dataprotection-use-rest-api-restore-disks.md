---
title: 使用 Azure 数据保护 REST API 还原 Azure 磁盘
description: 本文介绍如何使用 Azure 数据保护 REST API 还原 Azure 磁盘。
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: 30f4e7ff-2a55-4a85-be44-55feedc24607
ms.openlocfilehash: d8898b407fdcbea154d9282ff7964a3d0fa0264c
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620830"
---
# <a name="restore-azure-disks-using-azure-data-protection-rest-api"></a>使用 Azure 数据保护 REST API 还原 Azure 磁盘

本文介绍如何使用 Azure 备份还原[磁盘](disk-backup-overview.md)。

>[!Note]
>- 目前，不支持通过替换现有源磁盘（备份所在的位置）进行还原的“原始位置恢复(OLR)”选项。
>- 可从恢复点还原，以在源磁盘所属资源组或任何其他资源组中创建一个新磁盘。 它被称为“备用位置恢复 (ALR)”。

本文将指导如何进行以下操作：

- 还原并创建新磁盘

- 跟踪还原操作状态

## <a name="prerequisites"></a>先决条件

- [创建备份保管库](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [创建磁盘备份策略](backup-azure-dataprotection-use-rest-api-create-update-disk-policy.md)

- [配置磁盘备份](backup-azure-dataprotection-use-rest-api-backup-disks.md)

在本示例中，我们将引用资源组 testBkpVaultRG 下的现有备份保管库 TestBkpVault，其中 Azure 磁盘名为 msdiskbackup-2dc6eb5b-d008-4d68- 9e49-7132d99da0ed。

## <a name="restore-to-create-a-new-azure-disk"></a>还原以创建新的 Azure 磁盘

### <a name="set-up-permissions"></a>设置权限

备份保管库使用托管标识来访问其他 Azure 资源。 若要从备份中还原，备份保管库的托管标识需要对待还原磁盘所在的资源组拥有一组权限。

备份系统管库使用系统分配的托管标识（限制为每个资源一个），并绑定到此资源的生命周期。 若要向托管标识授予权限，请使用 Azure 基于角色的访问控制 (Azure RBAC)。 托管标识是一种只能用于 Azure 资源的特定服务主体。 详细了解[托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

为保管库的系统分配托管标识分配对磁盘还原/创建所在目标资源组的相关权限。 [了解详细信息](restore-managed-disks.md#restore-to-create-a-new-disk)。

### <a name="fetch-the-list-of-recovery-points"></a>提取恢复点列表

若要列出备份实例的所有可用恢复点，请使用[列出恢复点](/rest/api/dataprotection/recovery-points/list) API。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/recoveryPoints?api-version=2021-01-01
```

例如，此 API 转换为：

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints?api-version=2021-01-01
```

#### <a name="responses-for-list-of-recovery-points"></a>对恢复点列表的响应

提交 GET 请求后，该请求会返回响应“200 (正常)”、所有离散恢复点的列表以及所有相关详细信息。

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |    [AzureBackupRecoveryPointResourceList](/rest/api/dataprotection/recovery-points/list#azurebackuprecoverypointresourcelist)     |   确定      |
|其他状态代码     |    [CloudError](/rest/api/dataprotection/recovery-points/list#clouderror)     |     错误响应描述操作失败的原因。    |

##### <a name="example-response-for-list-of-recovery-points"></a>对恢复点列表的响应示例

```http
HTTP/1.1 200 OK
Content-Length: 7550
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: f01e2448-bdc5-4971-aee4-2edd1945c719
x-ms-routing-request-id: CENTRALUSEUAP:20210830T173435Z:0063423e-8b5e-493e-bb2e-74b7c7947c6c
Cache-Control: no-cache
Date: Mon, 30 Aug 2021 17:34:34 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints/a3d02fc3ab8a4c3a8cc26688c26d3356",
      "name": "a3d02fc3ab8a4c3a8cc26688c26d3356",
      "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints",
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
        "recoveryPointTime": "2021-08-30T10:02:11.6354913Z",
        "recoveryPointType": "Incremental",
        "friendlyName": "119ac243-a789-4a41-be24-0461bceb3888",
        "recoveryPointDataStoresDetails": [
          {
            "id": "13e7c1fe-005d-4b80-8532-c58d937132bb",
            "type": "OperationalStore",
            "creationTime": "2021-08-30T10:02:11.6354913Z",
            "expiryTime": "2021-09-06T10:02:11.6354913Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637607428336647408",
        "policyName": "DiskBackupPolicy-03",
        "policyVersion": null
      }
    },
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints/8f76ebc4c54847c09455d5785a150ce2",
      "name": "8f76ebc4c54847c09455d5785a150ce2",
      "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints",
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "8f76ebc4c54847c09455d5785a150ce2",
        "recoveryPointTime": "2021-08-29T10:01:50.7749008Z",
        "recoveryPointType": "Incremental",
        "friendlyName": "1ac5aa6b-3583-464f-9604-7490c04c2b22",
        "recoveryPointDataStoresDetails": [
          {
            "id": "13e7c1fe-005d-4b80-8532-c58d937132bb",
            "type": "OperationalStore",
            "creationTime": "2021-08-29T10:01:50.7749008Z",
            "expiryTime": "2021-09-05T10:01:50.7749008Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637607428336647408",
        "policyName": "DiskBackupPolicy-03",
        "policyVersion": null
      }
    },
    .
    .
    .
    .
}
```

从上述列表中选择相关恢复点，并继续准备还原请求。 我们将从上述列表中选择一个名为 a3d02fc3ab8a4c3a8cc26688c26d3356 的恢复点进行还原。

### <a name="prepare-the-restore-request"></a>准备还原请求

构造要在目标资源组（已按照[上文](#set-up-permissions)的详细说明向其分配权限）中创建的新磁盘的 Azure 资源管理器 (ARM) ID 以及所需磁盘名称。

例如，我们将使用资源组 targetrg 下名为 APITestDisk2 的磁盘，该磁盘与备份磁盘位于同一区域，但属于不同的订阅。

#### <a name="construct-the-request-body-for-restore-request"></a>构造还原请求的请求正文

以下请求正文包含恢复点 ID 和还原目标详细信息。

```json
{
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "OperationalStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "resourceName": "APITestDisk2",
        "resourceType": "Microsoft.Compute/disks",
        "resourceLocation": "westUS",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "datasourceType": "Microsoft.Compute/disks"
      },
      "restoreLocation": "westUS"
    }
  }
}
```

#### <a name="validate-restore-requests"></a>验证还原请求

准备好请求正文后，使用[验证还原 API](/rest/api/dataprotection/backup-instances/validate-for-restore) 对其进行验证。 与验证备份 API 一样，这是一个 POST 操作。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-01-01
```

例如，此 API 转换为：

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/validateRestore?api-version=2021-01-01"
```

[详细了解](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body)此 POST API 的请求正文。 

##### <a name="request-body-to-validate-restore-request"></a>用于验证还原请求的请求正文

我们在[上一节](#construct-the-request-body-for-restore-request)中构造了相同的节。 现在，我们将添加对象类型并使用它来触发验证操作。

```json

{
  "objectType": "ValidateRestoreRequestObject",
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "OperationalStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "resourceName": "APITestDisk2",
        "resourceType": "Microsoft.Compute/disks",
        "resourceLocation": "westUS",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "datasourceType": "Microsoft.Compute/disks"
      },
      "restoreLocation": "westUS"
    }
  }
}
```

##### <a name="response-to-validate-restore-requests"></a>针对验证还原请求的响应

验证还原请求是一个[异步操作](../azure-resource-manager/management/async-operations.md)。 因此，此操作会创建另一个需要单独跟踪的操作。

它将返回两个响应：创建另一个操作时为 202（已接受），该操作完成时为 200（正常）。

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |         |  验证请求的状态       |
|202 已接受     |         |     已接受    |

###### <a name="example-response-to-restore-validate-request"></a>针对还原验证请求的示例响应

提交 POST 操作后，该操作将返回带有 Azure-asyncOperation 标头的初始响应“202 (已接受)”。

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: bae60c92-669d-45a4-aed9-8392cca7cc8d
x-ms-routing-request-id: CENTRALUSEUAP:20210708T205935Z:f51db7a4-9826-4084-aa3b-ae640dc78af6
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:59:35 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

使用简单的 GET 请求来跟踪 Azure-AsyncOperation 标头。 请求成功后，它会返回“200 (正常)”，并返回成功状态响应。

```http
 GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:59:35.0060264Z",
  "endTime": "2021-07-08T20:59:57Z"
}
```

#### <a name="trigger-restore-requests"></a>触发还原请求

触发还原操作是一个 POST API。 [详细了解](/rest/api/dataprotection/backup-instances/trigger-restore)触发还原操作。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-01-01
```

例如，此 API 转换为：

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/restore?api-version=2021-01-01"
```

##### <a name="create-a-request-body-for-restore-operations"></a>创建还原操作的请求正文

验证请求后，使用相同的请求正文触发还原请求，只需进行细微的更改。

###### <a name="example-request-body-for-restore"></a>还原请求正文示例

对验证还原请求正文的唯一更改是删除开头的 restoreRequest 对象并更改对象类型。

```json
{
  "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "sourceDataStoreType": "OperationalStore",
  "restoreTargetInfo": {
    "datasourceInfo": {
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "datasourceType": "Microsoft.Compute/disks",
      "resourceName": "APITestDisk2",
      "resourceType": "Microsoft.Compute/disks",
      "resourceLocation": "westUS",
      "objectType": "Datasource"
    },
    "restoreLocation": "westUS",
    "recoveryOption": "FailIfExists",
    "objectType": "RestoreTargetInfo"
  }
}
```

#### <a name="response-to-trigger-restore-requests"></a>针对触发还原请求的响应

触发还原请求是一个[异步操作](../azure-resource-manager/management/async-operations.md)。 因此，此操作会创建另一个需要单独跟踪的操作。

它将返回两个响应：创建另一个操作时为 202（已接受），该操作完成时为 200（正常）。

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |         |  还原请求的状态       |
|202 已接受     |         |     已接受    |

##### <a name="example-response-to-trigger-restore-request"></a>针对触发还原请求的示例响应

提交 POST 操作后，该操作将返回带有 Azure-asyncOperation 标头的初始响应“202 (已接受)”。

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 8661209c-5b6a-44fe-b676-4e2b9c296593
x-ms-routing-request-id: CENTRALUSEUAP:20210708T204652Z:69e3fa4b-c5d9-4601-9410-598006ada187
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:46:52 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

使用简单的 GET 请求来跟踪 Azure-AsyncOperation 标头。 请求成功后，它将返回“200 (正常)”和一个作业 ID，应进一步跟踪该 ID 以完成还原请求。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:46:52.4110868Z",
  "endTime": "2021-07-08T20:46:56Z",
  "properties": {
    "jobId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
    "objectType": "OperationJobExtendedInfo"
  }
}
```

#### <a name="track-jobs"></a>跟踪作业

触发还原请求触发了还原作业。 若要跟踪生成的作业 ID，请使用 [GET Jobs API](/rest/api/dataprotection/jobs/get)。

使用简单的 GET 命令跟踪上述[触发还原响应](#example-response-to-trigger-restore-request)中提供的 JobId。

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-01-01

{
  "properties": {
    "activityID": "2881cc22-f527-4af4-9b34-46c6c7b72076-Ibz",
    "subscriptionId": "62b829ee-7936-40c9-a1c9-47a93f9f3965",
    "backupInstanceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
    "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
    "dataSourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
    "vaultName": "testBkpVault",
    "backupInstanceFriendlyName": "msdiskbackup",
    "policyName": "DiskBackup-Policy",
    "sourceResourceGroup": "RG-DiskBackup",
    "dataSourceSetName": null,
    "dataSourceName": "msdiskbackup",
    "sourceDataStoreName": null,
    "destinationDataStoreName": null,
    "progressEnabled": false,
    "etag": "W/\"datetime'2021-08-26T07%3A18%3A16.157629Z'\"",
    "sourceSubscriptionID": "62b829ee-7936-40c9-a1c9-47a93f9f3965",
    "dataSourceLocation": "westUS",
    "startTime": "2021-08-26T07:12:09.940517Z",
    "endTime": "2021-08-26T07:18:15.6815066Z",
    "dataSourceType": "Microsoft.Compute/disks",
    "operationCategory": "Restore",
    "operation": "Restore",
    "status": "Completed",
    "restoreType": null,
    "isUserTriggered": true,
    "rehydrationPriority": null,
    "supportedActions": [
      ""
    ],
    "duration": "PT6M5.7409896S",
    "progressUrl": null,
    "errorDetails": null,
    "extendedInfo": {
      "backupInstanceState": null,
      "dataTransferedInBytes": null,
      "targetRecoverPoint": null,
      "sourceRecoverPoint": {
        "recoveryPointID": "3a512290ec6b43d6b9a644869f4a3b38",
        "recoveryPointTime": "2021-08-25T09:03:11.6889015Z"
      },
      "recoveryDestination": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "subTasks": [
        {
          "taskId": 1,
          "taskName": "Trigger Restore",
          "taskStatus": "Completed",
          "taskProgress": null,
          "additionalDetails": null
        }
      ],
      "additionalDetails": null
    }
  },
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/3bc62c80-913f-47fa-b829-b7df476682be",
  "name": "3bc62c80-913f-47fa-b829-b7df476682be",
  "type": "Microsoft.DataProtection/BackupVaults/backupJobs"
}
```

上述作业状态指示还原作业已完成，且磁盘已恢复到指定的订阅和目标资源组。

## <a name="next-steps"></a>后续步骤

[Azure 磁盘备份概述](disk-backup-overview.md)

有关 Azure 备份 REST API 的详细信息，请参阅以下文章：

- [Azure 数据保护提供程序 REST API](/rest/api/dataprotection/)
- [Azure REST API 入门](/rest/api/azure/)