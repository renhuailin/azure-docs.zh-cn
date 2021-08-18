---
title: 用 Rest API 管理 Azure 文件共享备份
description: 了解如何使用 REST API 管理和监视由 Azure 备份所备份的 Azure 文件共享。
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: cdd099cac609ef84a0ece9d0d116de5534d3e530
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445138"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>使用 REST API 管理 Azure 文件共享备份

本文介绍如何执行管理和监视由 [Azure 备份](./backup-overview.md)所备份的 Azure 文件共享的任务。

## <a name="monitor-jobs"></a>监视作业

Azure 备份服务会触发在后台运行的作业。 这包括触发备份、还原操作和禁用备份等方案。 可以使用作业的 ID 跟踪这些作业。

### <a name="fetch-job-information-from-operations"></a>从操作中获取作业信息

操作（例如触发备份）将始终返回 jobID 作为响应。

例如：[触发备份 REST API](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share)操作的最终响应如下所示：

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Azure 文件共享备份作业由“jobId”字段予以标识，并且可以如[此处](/rest/api/backup/job-details)所述使用简单的 GET 请求进行跟踪。

### <a name="tracking-the-job"></a>跟踪作业

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

{JobName} 是上面提到的“jobId”。 响应始终为“200 正常”其中“status”字段指示作业的状态。 当状态为“Completed”或“CompletedWithWarnings”之后，“extendedInfo”部分显示关于作业的更多详细信息。

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>响应

名称  | 类型  |  说明
--- | --- | ----
200 正常 |  JobResource  | 确定

#### <a name="response-example"></a>响应示例

提交“GET”URI 后，将返回 200 响应。

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>修改策略

若更改受保护文件共享的策略，可以使用与启用保护相同的格式。 只需在请求策略中提供新的策略 ID 并提交请求即可。

例如，若要将“testshare”的保护策略从“schedule1”更改为“schedule2”，请在请求正文中提供“schedule2”ID。   

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>停止保护，但保留现有数据

可以删除受保护文件共享的保护，但保留已备份的数据。 为此，请在用于[启用备份](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share)和提交请求的请求正文中删除策略。 删除与策略的关联后，将不再触发备份，也不会创建新的恢复点。

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "" ,
"protectionState":"ProtectionStopped"
  }
}
```

### <a name="sample-response"></a>示例响应

停止对文件共享的保护是一个异步操作。 此操作会创建另一个需要跟踪的操作。 它将返回两个响应：创建另一个操作时为 202（已接受），该操作完成时为 200。

成功接受操作时的响应标头：

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

然后通过 GET 命令并使用位置标头或 Azure-AsyncOperation 标头跟踪生成的操作：

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>响应正文

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>停止保护并删除数据

若要删除对受保护文件共享的保护并删除备份数据，请执行[此处](/rest/api/backup/protected-items/delete)详述的删除操作。

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

参数 {containerName} 和 {protectedItemName} 如[此处](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname)所设置。

下面的示例触发操作以停止保护受“azurefilesvault”保护的“testshare”文件共享。 

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>响应

删除保护是一种异步操作。 此操作会创建另一个需要单独跟踪的操作。
它将返回两个响应：创建另一个操作时为 202（已接受），然后在该操作完成时为 204（无内容）。

## <a name="next-steps"></a>后续步骤

* 了解如何[排查为 Azure 文件共享配置备份时遇到的问题](troubleshoot-azure-files.md)。
