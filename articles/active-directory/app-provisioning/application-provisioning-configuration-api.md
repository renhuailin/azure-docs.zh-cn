---
title: 使用 Microsoft Graph API 配置预配
description: 了解如何通过使用 Microsoft Graph API 自动配置自动预配，从而节省时间。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/03/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 4bede3a7f5c39f8665d47984fb91cf2503842cae
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741732"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>使用 Microsoft Graph API 配置预配

Azure 门户是一次为一个应用配置预配的一种便捷方法。 但是如果要创建多个（甚至数百个）应用程序实例，则使用 Microsoft Graph API 可以更轻松地自动创建和配置应用。 本文概述如何通过 API 自动配置预配。 此方法通常用于 [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso) 等应用程序。

使用 Microsoft Graph API 自动配置预配的步骤概述


|步骤  |详细信息  |
|---------|---------|
|[步骤 1。创建库应用程序](#step-1-create-the-gallery-application)     |登录 API 客户端 <br> 检索库应用程序模板 <br> 创建库应用程序         |
|[步骤 2。基于模板创建预配作业](#step-2-create-the-provisioning-job-based-on-the-template)     |检索预配连接器的模板 <br> 创建预配作业         |
|[步骤 3。授予访问权限](#step-3-authorize-access)     |测试与应用程序的连接 <br> 保存凭据         |
|[步骤 4。开始预配作业](#step-4-start-the-provisioning-job)     |启动作业         |
|[步骤 5。监视预配](#step-5-monitor-provisioning)     |检查预配作业的状态 <br> 检索预配日志         |

## <a name="step-1-create-the-gallery-application"></a>步骤 1：创建库应用程序

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>登录 Microsoft Graph 浏览器（推荐）、Postman 或你使用的其他任意 API 客户端

1. 启动 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)。
1. 选择“使用 Microsoft 登录”按钮，然后使用 Azure AD 全局管理员或应用管理员凭据登录。
1. 成功登录后，你将在左侧窗格中看到用户帐户详细信息。

### <a name="retrieve-the-gallery-application-template-identifier"></a>检索库应用程序模板标识符
Azure AD 应用程序库中的每个应用程序都有一个[应用程序模板](/graph/api/applicationtemplate-list?tabs=http&view=graph-rest-beta&preserve-view=true)，它描述了该应用程序的元数据。 通过此模板，可在租户中创建应用程序和服务主体的实例以进行管理。 检索 AWS Single-Account Access 的应用程序模板的标识符，然后从响应中记录 id 属性的值，供本教程稍后使用 。

#### <a name="request"></a>请求

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates?$filter=displayName eq 'AWS Single-Account Access'
```
#### <a name="response"></a>响应

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Single-Account Access",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": "Federate to a single AWS account and use SAML claims to authorize access to AWS IAM roles. If you have many AWS accounts, consider using the AWS Single Sign-On gallery application instead."    
  
}
```

### <a name="create-the-gallery-application"></a>创建库应用程序

使用在上一步上为应用程序检索得到的模板 ID 在租户中创建应用程序和服务主体的[实例](/graph/api/applicationtemplate-instantiate?tabs=http&view=graph-rest-beta&preserve-view=true)。

#### <a name="request"></a>请求


```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>响应

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>步骤 2：基于模板创建预配作业

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>检索预配连接器的模板

库中支持预配的应用程序具有简化配置的模板。 使用以下请求[检索预配配置的模板](/graph/api/synchronization-synchronizationtemplate-list?tabs=http&view=graph-rest-beta&preserve-view=true)。 请注意，你将需要提供 ID。 ID 指的是上述资源，在本例中是 servicePrincipal 资源。 

#### <a name="request"></a>请求

```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```

#### <a name="response"></a>响应
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>创建预配作业
若要启用预配，首先需要[创建作业](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true)。 使用以下请求创建预配作业。 指定要用于作业的模板时，请使用上一步骤中的 templateId。

#### <a name="request"></a>请求

```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>响应
```http
HTTP/1.1 201 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>步骤 3：授予访问权限

### <a name="test-the-connection-to-the-application"></a>测试与应用程序的连接

测试与第三方应用程序的连接。 以下示例适用于需要客户端机密和机密令牌的应用程序。 每个应用程序都有自己的要求。 应用程序通常使用基址代替客户端机密。 若要确定应用需要哪些凭据，请转到应用程序的“预配配置”页，在开发人员模式下，单击“测试连接”。 网络流量将显示用于凭据的参数。 有关凭据的完整列表，请参阅 [synchronizationJob: validateCredentials](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta&preserve-view=true)。 大多数应用程序（例如 Azure Databricks）都依赖 BaseAddress 和 SecretToken。 BaseAddress 在 Azure 门户中称为租户 URL。 

#### <a name="request"></a>请求
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials

{ 
    "credentials": [ 
        { 
            "key": "ClientSecret", "value": "xxxxxxxxxxxxxxxxxxxxx" 
        },
        {
            "key": "SecretToken", "value": "xxxxxxxxxxxxxxxxxxxxx"
        }
    ]
}
```
#### <a name="response"></a>响应
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>保存凭据

配置预配需要在 Azure AD 和应用程序之间建立信任关系。 授予访问第三方应用程序的权限。 以下示例适用于需要客户端机密和机密令牌的应用程序。 每个应用程序都有自己的要求。 查看 [API 文档](/graph/api/synchronization-synchronizationjob-validatecredentials?tabs=http&view=graph-rest-beta&preserve-view=true)以查看可用选项。 

#### <a name="request"></a>请求
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    "value": [ 
        { 
            "key": "ClientSecret", "value": "xxxxxxxxxxxxxxxxxxxxx"
        },
        {
            "key": "SecretToken", "value": "xxxxxxxxxxxxxxxxxxxxx"
        }
    ]
}
```

#### <a name="response"></a>响应
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>步骤 4：启动预配作业
配置预配作业后，使用以下命令[启动作业](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true)。 


#### <a name="request"></a>请求

```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```


#### <a name="response"></a>响应
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>步骤 5：监视预配

### <a name="monitor-the-provisioning-job-status"></a>监视预配作业状态

现在预配作业正在运行，请使用以下命令跟踪当前预配周期的进度以及到目前为止的统计信息，例如已在目标系统中创建的用户和组数。 

#### <a name="request"></a>请求
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>响应
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>使用预配日志监视预配事件
除了监视预配作业的状态之外，还可以使用[预配日志](/graph/api/provisioningobjectsummary-list?tabs=http&view=graph-rest-beta&preserve-view=true)查询正在发生的所有事件。 例如，查询特定用户并确定他们是否已成功预配。

#### <a name="request"></a>请求
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>响应
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "AWS Contoso",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "AWS Contoso"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            }
            ]
       }
    ]
}
```
## <a name="see-also"></a>另请参阅

- [查看同步 Microsoft Graph 文档](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)
- [将自定义 SCIM 应用与 Azure AD 集成](./use-scim-to-provision-users-and-groups.md)
