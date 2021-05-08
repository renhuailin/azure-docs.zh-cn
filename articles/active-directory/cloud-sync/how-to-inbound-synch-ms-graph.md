---
title: 如何使用 MS Graph API 以编程方式配置云同步
description: 本主题介绍如何只使用 Graph API 启用入站同步
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c84636ea86b3b640aef365c1c5d8e634b9a1f48
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593154"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>如何使用 MS Graph API 以编程方式配置云同步

以下文档介绍如何只使用 MSGraph API 从头开始复制同步配置文件。  
如何执行此操作的结构包括以下步骤。  它们是：

- [基本设置](#basic-setup)
- [创建服务主体](#create-service-principals)
- [创建同步作业](#create-sync-job)
- [更新目标域](#update-targeted-domain)
- [启用同步密码哈希](#enable-sync-password-hashes-on-configuration-blade)
- [意外删除](#accidental-deletes)
- [启动同步作业](#start-sync-job)
- [查看状态](#review-status)

请使用这些[适用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块](/powershell/module/msonline/)命令来为生产租户启用同步，这是能够为该租户调用管理 Web 服务的先决条件。

## <a name="basic-setup"></a>基本设置

### <a name="enable-tenant-flags"></a>启用租户标志

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
这两个命令中的第一个需要 Azure Active Directory 凭据。 这些 commandlet 隐式标识租户并使其可进行同步。

## <a name="create-service-principals"></a>创建服务主体
接下来，我们需要创建 [AD2AAD 应用程序/服务主体](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

需要使用此应用程序 ID 1a4721b3-e57f-4451-ae87-ef078703ec94。 displayName 如果在门户（例如 contoso.com）中使用，则为 AD 域 url，但它可以被命名为其他内容。

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>创建同步作业
上述命令的输出将返回已创建的服务主体的 objectId。 对于本示例，该 objectId 为 614ac0e9-a59b-481f-bd8f-79a73d167e1c。  使用 Microsoft Graph 将 synchronizationJob 添加到该服务主体。  

可在[此处](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta)找到有关创建同步作业的文档。

如果未记录上述 ID，可通过运行以下 MS Graph 调用来查找该服务主体。 进行该调用需要有 Directory.Read.All 权限：
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

然后，在输出中查找你的应用名称。

运行以下两个命令来创建两个作业：一个用于用户/组预配，另一个用于密码哈希同步。 它是两次发出同一请求，但具有不同的模板 ID。


调用以下两个请求：

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

如果两个都要创建，则会需要两个调用。

示例返回值（用于预配）：

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>更新目标域
对于此租户，该服务主体的对象标识符和应用程序标识符如下所示：

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-c000-000000000000 DisplayName: testApp

我们将需要更新此配置所针对的域，因此，请更新此域的机密。

请确保你使用的域名与你为本地域控制器设置的 URL 相同

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 根据要尝试执行的操作，在下面的值数组中添加以下键/值对：
 - 将 PHS 和同步租户标志都启用 { key: "AppKey", value: "{"appKeyScenario":"AD2AADPasswordHash"}" }
 
 - 只启用同步租户标志（不打开 PHS）{ key: "AppKey", value: "{"appKeyScenario":"AD2AADProvisioning"}" }
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

正确的响应为 ... HTTP 204/无内容

在这里，突出显示的“域”值是要从中将条目预配到 Azure Active Directory 的本地 Active Directory 域的名称。

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>启用配置边栏选项卡上的同步密码哈希

 本部分将介绍如何为某个特定配置启用同步密码哈希。 它不同于启用租户级别功能标志的 AppKey 机密，它仅适用于单一域/配置。你需要将应用程序密钥设置为 PHS 密钥，以便让它以端到端方式工作。

1. 获取架构（警告：它会非常大） 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. 采用此 CredentialData attribute 映射：
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. 在架构中查找具有以下名称的对象映射
 - 预配 Active Directory 用户
 - 预配 Active Directory inetOrgPersons

 对象映射位于 schema.synchronizationRules[0].objectMappings 之内（目前你可以假定只有 1 个同步规则）

4. 采用步骤 (2) 中的 CredentialData 映射，并将其插入到步骤 (3) 中的对象映射

 你的对象映射会如下所示：
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 将上述 **Create AD2AADProvisioning and AD2AADPasswordHash jobs** 步骤中的映射复制/粘贴到 attributeMappings 数组。 

 此数组中的元素顺序并不重要（后端将会进行排序）。 如果该名称已存在于数组中（例如，如果 attributeMappings 中已经有具有 targetAttributeName CredentialData 的项），在添加此属性映射时要小心，你可能会遇到冲突错误，或者，预先存在的和新的映射可能会合并到一起（通常不是正确结果）。 后端不会为你消除重复数据。 

 请记得对用户和 inetOrgpersons 都执行此操作

5. 保存已创建的架构 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 在请求正文中添加该架构。 

## <a name="accidental-deletes"></a>意外删除
本部分将介绍如何以编程方式启用/禁用和使用[意外删除](how-to-accidental-deletes.md)。


### <a name="enabling-and-setting-the-threshold"></a>启用并设置阈值
每个作业设置都有两个你可以使用，它们分别是：

 - DeleteThresholdEnabled - 在设置为“true”时，为作业启用意外删除防护。 默认设置为“true”。
 - DeleteThresholdValue - 定义在启用了意外删除防护时每次执行作业所允许的最大删除次数。 在默认情况下，该值设置为 500。  因此，如果将该值设置为 500，则每次执行时允许的最大删除数将会是 499。

删除阈值设置是 `SyncNotificationSettings` 的一部分，可以通过图形进行修改。 

我们将需要更新此配置所针对的 SyncNotificationSettings，因此，请更新机密。

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 根据要尝试执行的操作，在下面的值数组中添加以下键/值对：

```
 Request body -
 {
   "value":[
             {
               "key":"SyncNotificationSettings",
               "value": "{\"Enabled\":true,\"Recipients\":\"foobar@xyz.com\",\"DeleteThresholdEnabled\":true,\"DeleteThresholdValue\":50}"
              }
            ]
  }


```

上述示例中的“已启用”设置用于在隔离作业时启用/禁用通知电子邮件。


目前，我们不支持机密的 PATCH 请求，因此，你将需要在 PUT 请求的正文中添加所有值（如以上示例中所示）以便保留其他值。

可以通过使用以下命令来检索所有机密的现有值 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>允许删除
若要在作业进入隔离后允许删除流过，则需要发出只以“ForceDeletes”作为范围的重启。 

```
Request:
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

```
Request Body:
{
  "criteria": {"resetScope": "ForceDeletes"}
}
```






## <a name="start-sync-job"></a>启动同步作业
可以通过以下命令再次检索该作业：

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

可在[此处](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta)找到有关检索作业的文档。 
 
若要启动该作业，请使用在第一步中创建的服务主体的 objectId 以及从创建该作业的请求返回的作业标识符来发出此请求。

可在[此处](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta)找到有关如何启动作业的文档。 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

正确的响应为 ... HTTP 204/无内容。

[此处](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)还介绍了其他用于控制作业的命令。
 
若要重启作业，用户可以使用 ...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>查看状态
请通过以下命令检索作业状态

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

请在返回对象的“状态”部分下查找相关详细信息

## <a name="next-steps"></a>后续步骤 

- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
- [转换](how-to-transformation.md)
- [Azure AD 同步 API](/graph/api/resources/synchronization-overview?view=graph-rest-beta)