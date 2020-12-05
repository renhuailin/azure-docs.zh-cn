---
title: 使用 MS 图形 API 进行云设置的入站同步
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
ms.openlocfilehash: b797bf5c7e69990a29f0d14c31586d0e85b42426
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2020
ms.locfileid: "96737959"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>使用 MS 图形 API 进行云设置的入站同步

以下文档介绍了如何使用 MSGraph Api 从头开始复制同步配置文件。  
如何执行此操作的结构包括以下步骤。  它们是：

- [基本设置](#basic-setup)
- [创建服务主体](#create-service-principals)
- [创建同步作业](#create-sync-job)
- [更新目标域](#update-targeted-domain)
- [启动同步作业](#start-sync-job)
- [查看状态](#review-status)

使用这些 [用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0) 命令可为生产租户启用同步，这是为该租户调用管理 Web 服务的先决条件。

## <a name="basic-setup"></a>基本设置

### <a name="enable-tenant-flags"></a>启用租户标志

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
这两个命令中的第一个命令需要 Azure Active Directory 凭据。 这些 commandlet 隐式标识租户并使其可用于同步。

## <a name="create-service-principals"></a>创建服务主体
接下来，我们需要创建 [AD2AAD 应用程序/服务主体](https://docs.microsoft.com/graph/apiapplicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

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
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

预期响应为 .。。 HTTP 204/无内容

此处，突出显示的 "域" 值是要将条目预配到 Azure Active Directory 的本地 Active Directory 域的名称。

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

- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)
- [转换](how-to-transformation.md)
- [Azure AD 同步 API](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
