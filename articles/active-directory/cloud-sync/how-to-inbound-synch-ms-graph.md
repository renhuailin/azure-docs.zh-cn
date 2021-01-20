---
title: 使用 MS 图形 API 进行云同步的入站同步
description: 本主题介绍如何只使用图形 API 启用入站同步
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
ms.openlocfilehash: e65569cadd8f778a94f93aa22dd3924c52ff12f8
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613173"
---
# <a name="inbound-synchronization-for-cloud-sync-using-ms-graph-api"></a>使用 MS 图形 API 进行云同步的入站同步

以下文档介绍了如何使用 MSGraph Api 从头开始复制同步配置文件。  
如何执行此操作的结构包括以下步骤。  它们是：

- [基本设置](#basic-setup)
- [创建服务主体](#create-service-principals)
- [创建同步作业](#create-sync-job)
- [更新目标域](#update-targeted-domain)
- [启用同步密码哈希](#enable-sync-password-hashes-on-configuration-blade)
- [启动同步作业](#start-sync-job)
- [查看状态](#review-status)

使用这些 [用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块](https://docs.microsoft.com/powershell/module/msonline/) 命令可为生产租户启用同步，这是为该租户调用管理 Web 服务的先决条件。

## <a name="basic-setup"></a>基本设置

### <a name="enable-tenant-flags"></a>启用租户标志

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
这两个命令中的第一个命令需要 Azure Active Directory 凭据。 这些 commandlet 隐式标识租户并使其可用于同步。

## <a name="create-service-principals"></a>创建服务主体
接下来，我们需要创建 [AD2AAD 应用程序/服务主体](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

需要使用此应用程序 ID 1a4721b3-e57f-4451-ae87-ef078703ec94。 DisplayName 是 AD 域 url，如果在门户中使用 (例如 contoso.com) ，但它可以命名为其他内容。

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>创建同步作业
上述命令的输出将返回已创建的服务主体的 objectId。 对于本示例，objectId 为614ac0e9-a59b-481f-bd8f-79a73d167e1c。  使用 Microsoft Graph 向该服务主体添加 synchronizationJob。  

可在 [此处](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http)找到有关创建同步作业的文档。

如果未记录上述 ID，可以通过运行以下 MS Graph 调用来查找服务主体。 你将需要读取目录。进行此调用的所有权限：
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

然后，在输出中查找应用名称。

运行以下两个命令来创建两个作业：一个用于用户/组预配，一个用于密码哈希同步。 它是同一请求两次，但具有不同的模板 Id。


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

如果要同时创建两个调用，则需要两个调用。

预配) 的示例返回值 (：

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
对于此租户，服务主体的对象标识符和应用程序标识符如下所示：

ObjectId： 8895955e-2e6c-4d79-8943-4d72ca36878f AppId： 00000014-0000-0000-c000-000000000000 DisplayName： testApp

我们将需要更新此配置所针对的域，因此请更新此域的机密。

请确保你使用的域名与你为本地域控制器设置的 url 相同

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 根据要尝试执行的操作，在下面的值数组中添加以下键/值对：
 - 同时启用 PHS 和同步租户标志 {key： "AppKey"，值： "{" appKeyScenario "：" AD2AADPasswordHash "}"}
 
 - 仅启用同步租户标志 (不要打开 PHS) {key： "AppKey"，值： "{" appKeyScenario "：" AD2AADProvisioning "}"}
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

预期响应为 .。。 HTTP 204/无内容

此处，突出显示的 "域" 值是要将条目预配到 Azure Active Directory 的本地 Active Directory 域的名称。

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>启用 "配置" 边栏选项卡上的同步密码哈希

 本部分将介绍如何为特定配置同步密码哈希。 这不同于启用租户级功能标志的 AppKey 机密-这仅适用于单一域/配置。你需要将应用程序密钥设置为 PHS，以使其端到端工作。

1. 获取架构 (警告，这是非常大的)  
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. 采用以下 CredentialData 属性映射：
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
3. 在架构中查找以下具有以下名称的对象映射
 - 预配 Active Directory 用户
 - 预配 Active Directory inetOrgPersons

 对象映射在 synchronizationRules [0] 中。 objectMappings (现在可以假设只有1个同步规则) 

4. 获取步骤 (2) 中的 CredentialData 映射，并将其插入到步骤 (3 中的对象映射) 

 对象映射如下所示：
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
 将上述 **Create AD2AADProvisioning And AD2AADPasswordHash job** 步骤中的映射复制/粘贴到 attributeMappings 数组。 

 此数组中的元素顺序并不重要 (后端将按) 进行排序。 如果该名称已存在于数组中，则请注意添加此属性映射 (例如，如果 attributeMappings 中已经有一个具有 targetAttributeName CredentialData) 的项，则可能会收到冲突错误，或者预先存在的和新的映射 (通常不是) 的结果。 后端不会重复。 

 对于用户和 inetOrgpersons，请务必执行此操作

5. 保存已创建的架构 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 在请求正文中添加架构。 

## <a name="start-sync-job"></a>启动同步作业
可以通过以下命令再次检索作业：

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

可在 [此处](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http)找到有关检索作业的文档。 
 
若要启动作业，请使用第一步中创建的服务主体的 objectId 和从创建作业的请求中返回的作业标识符发出此请求。

可在 [此处](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http)找到有关如何启动作业的文档。 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

预期响应为 .。。 HTTP 204/无内容。

[此处](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)还介绍了用于控制作业的其他命令。
 
若要重新启动作业，可以使用 .。。

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>查看状态
通过获取你的作业状态 .。。

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

查看返回对象的 "状态" 部分，了解相关详细信息

## <a name="next-steps"></a>后续步骤 

- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)
- [转换](how-to-transformation.md)
- [Azure AD 同步 API](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
