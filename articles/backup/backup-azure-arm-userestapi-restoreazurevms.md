---
title: 使用 REST API 还原 Azure VM
description: 本文介绍如何使用 REST API 管理 Azure 虚拟机备份的还原操作。
ms.topic: conceptual
ms.date: 08/26/2021
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: f82adee9690c0114fef17640672c7326cffc8481
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966071"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>使用 REST API 还原 Azure 虚拟机

使用 Azure 备份完成 Azure 虚拟机的备份后，可以从同一个备份副本还原整个 Azure 虚拟机，或者还原磁盘或文件。 本文介绍如何使用 REST API 还原 Azure VM 或磁盘。

执行任何还原操作时，首先必须确定相关的恢复点。

## <a name="select-recovery-point"></a>选择恢复点

可以使用[列出恢复点 REST API](/rest/api/backup/recovery-points/list) 列出备份项的可用恢复点。 这是一个使用所有相关值执行的 GET 操作。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

`{containerName}` 和 `{protectedItemName}` 是按[此处](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation)所述构造的。 `{fabricName}` 是“Azure”。

*GET* URI 包含所有必需的参数。 无需额外提供请求正文。

### <a name="responses"></a>响应

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |   [RecoveryPointResourceList](/rest/api/backup/recovery-points/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>示例响应

提交 *GET* URI 后，将返回 200 (OK) 响应。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

上述响应中的 `{name}` 字段标识了恢复点。

## <a name="restore-operations"></a>还原操作

选择[相关还原点](#select-recovery-point)之后，请继续触发还原操作。

对备份项执行的所有还原操作都是通过相同 POST API 来执行。只有请求正文随还原方案而更改。

> [!IMPORTANT]
> [此处](./backup-azure-arm-restore-vms.md#restore-options)提到了有关各种还原选项及其依赖项的所有详细信息。 请在继续触发这些操作之前查看。

触发还原操作是 POST 请求。 若要详细了解 API，请参阅[“触发还原”REST API](/rest/api/backup/restores/trigger)。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

`{containerName}` 和 `{protectedItemName}` 是按[此处](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation)所述构造的。 `{fabricName}` 是“Azure”，`{recoveryPointId}` 是[上述](#example-response)恢复点的 `{name}` 字段。

获取恢复点后，需要为相关还原方案构造请求正文。 以下部分概述了每个方案的请求正文。

- [还原磁盘](#restore-disks)
- [替换磁盘](#replace-disks-in-a-backed-up-virtual-machine)
- [还原为新虚拟机](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>还原响应

触发任何还原操作都是[异步操作](../azure-resource-manager/management/async-operations.md)。 这意味着，此操作会创建另一个需要单独跟踪的操作。

它将返回两个响应：创建另一个操作时为 202（已接受），该操作完成时为 200（正常）。

|名称  |类型  |说明  |
|---------|---------|---------|
|202 已接受     |         |     已接受    |

#### <a name="example-responses"></a>示例响应

提交用于触发磁盘还原的 *POST* URI 后，初始响应为 202 (Accepted) 以及 location 标头或 Azure-async-header。

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

然后通过简单的 GET 命令并使用 location 标头或 Azure-AsyncOperation 标头跟踪生成的操作。

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

操作完成后，它将返回 200 (OK)，以及响应正文中生成的还原作业的 ID。

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

由于还原作业是长时间运行的操作，因此应该根据[使用 REST API 监视作业](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)文档中所述对其进行跟踪。

### <a name="restore-disks"></a>还原磁盘

如果需要自定义从备份数据创建 VM 的过程，只需将磁盘还原到所选的存储帐户，然后根据要求从这些磁盘创建 VM 即可。 存储帐户应与恢复服务保管库位于同一区域，并且不应采用区域冗余。 磁盘以及已备份 VM 的配置（“vmconfig.json”）将存储在给定的存储帐户中。 如[上文](#restore-operations)所述，下面提供适用于还原磁盘的相关请求正文。

#### <a name="create-request-body"></a>创建请求正文

若要触发从 Azure VM 备份还原磁盘的操作，需在请求正文中包含以下组成部分。

|名称  |类型  |说明  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

有关请求正文的完整定义列表和其他详细信息，请参阅[“触发还原”REST API 文档](/rest/api/backup/restores/trigger#request-body)。

##### <a name="example-request"></a>示例请求

以下请求正文定义触发磁盘还原所需的属性。

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>有选择地还原磁盘

如果[有选择地备份磁盘](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)，则会在[恢复点摘要](#select-recovery-point)和[详细响应](/rest/api/backup/recovery-points/get)中提供当前备份磁盘列表。 还可以有选择地还原磁盘，在[此处](selective-disk-backup-restore.md#selective-disk-restore)提供了更多详细信息。 若要有选择地还原备份磁盘列表中的磁盘，请从恢复点响应中找到磁盘的 LUN，并将 restoreDiskLunList 属性添加到[以上请求正文](#example-request)中，如下所示。

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

按[上文](#responses)所述跟踪响应，并且长时间运行的作业完成后，磁盘以及已备份虚拟机的配置（“VMConfig.json”）将出现在给定的存储帐户中。

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>替换已备份虚拟机中的磁盘

还原磁盘从恢复点创建磁盘时，替换磁盘会将备份的 VM 上的当前磁盘替换从恢复点创建的磁盘。 如[上文](#restore-operations)所述，下面提供适用于替换磁盘的相关请求正文。

#### <a name="create-request-body"></a>创建请求正文

若要触发从 Azure VM 备份替换磁盘的操作，需在请求正文中包含以下组成部分。

|名称  |类型  |说明  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

有关请求正文的完整定义列表和其他详细信息，请参阅[“触发还原”REST API 文档](/rest/api/backup/restores/trigger#request-body)。

#### <a name="example-request"></a>示例请求

以下请求正文定义触发磁盘还原所需的属性。

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>作为另一个虚拟机还原

如[上文](#restore-operations)所述，以下请求正文定义触发虚拟机还原所需的属性。

```json
{
  "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
     }
 }
```

应该根据[上文所述的还原磁盘](#responses)的相同方式处理响应。

## <a name="cross-region-restore"></a>跨区域还原

如果在用于保护 VM 的保管库上启用跨区域还原，则会将备份数据复制到次要区域。 可使用备份数据来执行还原操作。 若要使用 REST API 在次要区域触发还原操作，请执行以下步骤：

### <a name="select-recovery-point-in-secondary-region"></a>选择次要区域中的恢复点

可以使用“[列出用于 CRR 的恢复点 REST API](/rest/api/backup/recovery-points-crr/list)”列出次要区域中备份项目的可用恢复点。 这是一个使用所有相关值执行的 GET 操作。

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2018-12-20

```

`{containerName}` 和 `{protectedItemName}` 是按[此处](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation)所述构造的。 `{fabricName}` 是“Azure”。

*GET* URI 包含所有必需的参数。 不需要其他请求正文。

>[!NOTE]
>要获取次要区域中的恢复点，请使用 API 版本 2018-12-20，如上例所示。

#### <a name="responses"></a>响应

|名称  |类型  |说明  |
|---------|---------|---------|
|200 正常     |   [RecoveryPointResourceList](/rest/api/backup/recovery-points-crr/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>示例响应

提交 *GET* URI 后，将返回 200 (OK) 响应。

```http
Headers:
Pragma                        : no-cache
X-Content-Type-Options        : nosniff
x-ms-request-id               : bfc4a4e6-c585-46e0-8e38-f11a86093701
x-ms-client-request-id        : 4344a9c2-70d8-482d-b200-0ca9cc498812,4344a9c2-70d8-482d-b200-0ca9cc498812
Strict-Transport-Security     : max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-resource-requests: 149
x-ms-correlation-request-id   : bfc4a4e6-c585-46e0-8e38-f11a86093701
x-ms-routing-request-id       : SOUTHINDIA:20210731T112441Z:bfc4a4e6-c585-46e0-8e38-f11a86093701
Cache-Control                 : no-cache
Date                          : Sat, 31 Jul 2021 11:24:40 GMT
Server                        : Microsoft-IIS/10.0
X-Powered-By                  : ASP.NET

Body:
{
  "value": [
    {
      "id":
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/932895704780058094",
      "name": "932895704780058094",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2021-07-31T09:24:34.687561Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "PremiumVMOnPartialPremiumStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_D2s_v3",
        "originalStorageAccountOption": false,
        "osType": "Windows"
      }
    },
    {
      "id":
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/932891484644960954",
      "name": "932891484644960954",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2021-07-30T09:20:01.8355052Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "PremiumVMOnPartialPremiumStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_D2s_v3",
        "originalStorageAccountOption": false,
        "osType": "Windows"
      }
    },
.....
```

上述响应中的 `{name}` 字段标识了恢复点。

### <a name="get-access-token"></a>获取访问令牌

若要执行跨区域还原，需要访问令牌来为你的请求授权，以便能够访问次要区域中的复制还原点。 若要获取访问令牌，请执行以下步骤：

#### <a name="step-1"></a>步骤 1：

使用 [AAD 属性 API](/rest/api/backup/aad-properties/get) 获取次要区域（以下示例中的 westus）的 AAD 属性：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.RecoveryServices/locations/westus/backupAadProperties?api-version=2018-12-20
```

##### <a name="response-example"></a>响应示例

返回的响应格式如下：

```json
{
  "properties": {
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "audience": "https://RecoveryServices/IaasCoord/aadmgmt/wus",
    "servicePrincipalObjectId": "00000000-0000-0000-0000-000000000000"
  }
}
```

#### <a name="step-2"></a>步骤 2：

使用[获取访问令牌 API](/rest/api/backup/recovery-points-get-access-token-for-crr/get-access-token) 为你的请求授权，以便访问次要区域中的复制还原点：

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/accessToken?api-version=2018-12-20
```

对于请求正文，粘贴上一步中 AAD 属性 API 返回的响应内容。

```json
{
  "properties": {
    "tenantId": "00000000-0000-0000-0000-000000000000",
    "audience": "https://RecoveryServices/IaasCoord/aadmgmt/wus",
    "servicePrincipalObjectId": "00000000-0000-0000-0000-000000000000"
  }
}
```

##### <a name="response-example"></a>响应示例

返回的响应格式如下：

```json
{
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG1;testVM/protectedItems/VM;iaasvmcontainerv2;testRG1;testVM/recoveryPoints/26083826328862",
  "name": "932879774590051503",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
    "properties": {
        "objectType": "CrrAccessToken",
        "accessTokenString": "<access-token-string>",
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "resourceName": "testVault",
        "resourceId": "000000000000000000",
        "protectionContainerId": 000000,
        "recoveryPointId": "932879774590051503",
        "recoveryPointTime": "7/26/2021 3:35:36 PM",
        "containerName": "iaasvmcontainerv2;testRG1;testVM",
        "containerType": "IaasVMContainer",
        "backupManagementType": "AzureIaasVM",
        "datasourceType": "VM",
        "datasourceName": "testvm1234",
        "datasourceId": "000000000000000000",
        "datasourceContainerName": "iaasvmcontainerv2;testRG1;testVM",
        "coordinatorServiceStampUri": "https://pod01-coord1.eus.backup.windowsazure.com",
        "protectionServiceStampId": "00000000-0000-0000-0000-000000000000",
        "protectionServiceStampUri": "https://pod01-prot1h-int.eus.backup.windowsazure.com",
        "tokenExtendedInformation": "<IaaSVMRecoveryPointMetadataBase xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" i:type=\"IaaSVMRecoveryPointMetadata_V2015_09\" xmlns=\"http://windowscloudbackup.com/CloudCommon/V2011_09\"><MetadataVersion>V2015_09</MetadataVersion><ContainerType i:nil=\"true\" /><InstantRpGCId>ef4ab5a7-c2c0-4304-af80-af49f48af3d1;AzureBackup_testvm1234_932843259176972511;AzureBackup_20210726_033536;AzureBackupRG_eastus_1</InstantRpGCId><IsBlockBlobEnabled>true</IsBlockBlobEnabled><IsManagedVirtualMachine>true</IsManagedVirtualMachine><OriginalSAOption>false</OriginalSAOption><OsType>Windows</OsType><ReadMetadaFromConfigBlob i:nil=\"true\" /><RecoveryPointConsistencyType>CrashConsistent</RecoveryPointConsistencyType><RpDiskDetails i:nil=\"true\" /><SourceIaaSVMRPKeyAndSecret i:nil=\"true\" /><SourceIaaSVMStorageType>PremiumVMOnPartialPremiumStorage</SourceIaaSVMStorageType><VMSizeDescription>Standard_D2s_v3</VMSizeDescription><Zones xmlns:d2p1=\"http://schemas.microsoft.com/2003/10/Serialization/Arrays\" i:nil=\"true\" /></IaaSVMRecoveryPointMetadataBase>",
        "rpTierInformation": {
            "InstantRP": "Valid",
            "HardenedRP": "Valid"
        },
        "rpOriginalSAOption": false,
        "rpIsManagedVirtualMachine": true,
        "rpVMSizeDescription": "Standard_D2s_v3",
        "bMSActiveRegion": "EastUS"
    }
}
```

### <a name="restore-disks-to-the-secondary-region"></a>将磁盘还原到次要区域

使用[跨区域还原触发器 API](/rest/api/backup/cross-region-restore/trigger) 将某个项还原到次要区域。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.RecoveryServices/locations/{azureRegion}/backupCrossRegionRestore?api-version=2018-12-20
```

请求正文应包含两个部分：

1. **crossRegionRestoreAccessDetails**：粘贴上一步中执行的“获取访问令牌 API”请求的响应的 _properties* 块，以填充请求正文的这一部分*。

1. **restoreRequest**：要填充请求正文的 restoreRequest 段，需要传递之前获取的恢复点 ID、源 VM 的 Azure 资源管理器 (ARM) ID 以及要充当暂存位置的次要区域中的存储帐户详细信息*。 若要执行磁盘还原，请将 RestoreDisks 指定为还原类型。

下面是将 VM 的磁盘还原到次要区域的示例请求正文：

```json
 {
  "crossRegionRestoreAccessDetails": {
        "objectType": "CrrAccessToken",
        "accessTokenString": "<access-token-string>",
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "azurefiles",
        "resourceName": "azurefilesvault",
        "resourceId": "000000000000000000",
        "protectionContainerId": 000000,
        "recoveryPointId": "932879774590051503",
        "recoveryPointTime": "7/26/2021 3:35:36 PM",
        "containerName": "iaasvmcontainerv2;testRG1;testVM",
        "containerType": "IaasVMContainer",
        "backupManagementType": "AzureIaasVM",
        "datasourceType": "VM",
        "datasourceName": "testvm1234",
        "datasourceId": "000000000000000000",
        "datasourceContainerName": "iaasvmcontainerv2;testRG1;testVM",
        "coordinatorServiceStampUri": "https://pod01-coord1.eus.backup.windowsazure.com",
        "protectionServiceStampId": "00000000-0000-0000-0000-000000000000",
        "protectionServiceStampUri": "https://pod01-prot1h-int.eus.backup.windowsazure.com",
        "tokenExtendedInformation": "<IaaSVMRecoveryPointMetadataBase xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" i:type=\"IaaSVMRecoveryPointMetadata_V2015_09\" xmlns=\"http://windowscloudbackup.com/CloudCommon/V2011_09\"><MetadataVersion>V2015_09</MetadataVersion><ContainerType i:nil=\"true\" /><InstantRpGCId>ef4ab5a7-c2c0-4304-af80-af49f48af3d1;AzureBackup_testvm1234_932843259176972511;AzureBackup_20210726_033536;AzureBackupRG_eastus_1</InstantRpGCId><IsBlockBlobEnabled>true</IsBlockBlobEnabled><IsManagedVirtualMachine>true</IsManagedVirtualMachine><OriginalSAOption>false</OriginalSAOption><OsType>Windows</OsType><ReadMetadaFromConfigBlob i:nil=\"true\" /><RecoveryPointConsistencyType>CrashConsistent</RecoveryPointConsistencyType><RpDiskDetails i:nil=\"true\" /><SourceIaaSVMRPKeyAndSecret i:nil=\"true\" /><SourceIaaSVMStorageType>PremiumVMOnPartialPremiumStorage</SourceIaaSVMStorageType><VMSizeDescription>Standard_D2s_v3</VMSizeDescription><Zones xmlns:d2p1=\"http://schemas.microsoft.com/2003/10/Serialization/Arrays\" i:nil=\"true\" /></IaaSVMRecoveryPointMetadataBase>",
        "rpTierInformation": {
            "InstantRP": "Valid",
            "HardenedRP": "Valid"
        },
        "rpOriginalSAOption": false,
        "rpIsManagedVirtualMachine": true,
        "rpVMSizeDescription": "Standard_D2s_v3",
        "bMSActiveRegion": "EastUS"
    },
    "restoreRequest": {
        "affinityGroup": "",
        "createNewCloudService": false,
        "encryptionDetails": {
            "encryptionEnabled": false
        },
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "932879774590051503",
        "recoveryType": "RestoreDisks",
        "sourceResourceId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1/providers/Microsoft.Compute/virtualMachines/testVM",
        "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1",
        "storageAccountId":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG1/providers/Microsoft.Storage/storageAccounts/testStorageAccount",
        "region": "westus",
        "affinityGroup": "",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "restoreDiskLunList": []
    }
}
```

与主要区域还原操作类似，这是一个异步操作，需要[单独跟踪](/azure/backup/backup-azure-arm-userestapi-restoreazurevms#restore-response)。



## <a name="next-steps"></a>后续步骤

有关 Azure 备份 REST API 的详细信息，请参阅以下文档：

- [Azure 恢复服务提供程序 REST API](/rest/api/recoveryservices/)
- [Azure REST API 入门](/rest/api/azure/)
