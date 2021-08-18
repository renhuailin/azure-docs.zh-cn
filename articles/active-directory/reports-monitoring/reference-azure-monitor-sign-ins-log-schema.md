---
title: Azure Monitor 中的登录日志架构 | Microsoft Docs
description: 介绍在 Azure Monitor 中使用的 Azure AD 登录日志架构
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12bb1d26c8c9abbf3d2312b1d0186b8b4303c746
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739956"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>解释 Azure Monitor 中的 Azure AD 登录日志架构

本文介绍 Azure Monitor 中的 Azure Active Directory (Azure AD) 登录日志架构。 与登录相关的大多数信息都在 `records` 对象的 Properties 属性下提供。


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013"
         }
}
```


## <a name="field-descriptions"></a>字段说明

| 字段名称 | 密钥 | 说明 |
| --- | --- | --- | 
| 时间 |  - | 日期和时间 (UTC)。 |
| ResourceId | - | 此值未映射，可以放心地忽略此字段。  |
| OperationName | - | 对于登录，此值始终为“登录活动”。 |
| OperationVersion | - | 客户端请求的 REST API 版本。 |
| Category | - | 对于登录，此值始终为“登录”。 | 
| TenantId | - | 与日志关联的租户 GUID。 |
| ResultType | - | 如果登录操作成功，则显示 `0`，如果登录操作失败则显示错误代码。 | 
| ResultSignature | - | 此值始终为 None。 |
| ResultDescription | 空值或空白 | 提供登录操作的错误说明。 |
| riskDetail | riskDetail | 提供特定风险用户、风险登录或风险检测状态背后的“原因”。 可能的值有：`none`、`adminGeneratedTemporaryPassword`、`userPerformedSecuredPasswordChange`、`userPerformedSecuredPasswordReset`、`adminConfirmedSigninSafe`、`aiConfirmedSigninSafe`、`userPassedMFADrivenByRiskBasedPolicy`、`adminDismissedAllRiskForUser`、`adminConfirmedSigninCompromised`、`unknownFutureValue`。 值 `none` 表示到目前为止尚未对用户或登录执行任何操作。 <br>**注意：** 此属性的详细信息需要 Azure AD Premium P2 许可证。 其他许可证返回值 `hidden`。 |
| riskEventTypes | riskEventTypes | 与登录相关的风险检测类型。 可能的值有：`unlikelyTravel`、`anonymizedIPAddress`、`maliciousIPAddress`、`unfamiliarFeatures`、`malwareInfectedIPAddress`、`suspiciousIPAddress`、`leakedCredentials`、`investigationsThreatIntelligence`、`generic` 和 `unknownFutureValue`。 |
| authProcessingDetails | Azure AD 应用身份验证库 | 包含系列、库和平台信息，格式为：“Family: ADAL Library: ADAL.JS 1.0.0 Platform: JS” |
| authProcessingDetails | IsCAEToken | 值为 True 或 False |
| riskLevelAggregated | riskLevel | 聚合风险级别。 可能的值有：`none`、`low`、`medium`、`high`、`hidden` 和 `unknownFutureValue`。 值 `hidden` 表示用户或登录未启用 Azure AD 标识保护。 **注意：** 此属性的详细信息仅适用于 Azure AD Premium P2 客户。 所有其他客户将返回 `hidden`。 |
| riskLevelDuringSignIn | riskLevel | 登录过程中的风险级别。 可能的值有：`none`、`low`、`medium`、`high`、`hidden` 和 `unknownFutureValue`。 值 `hidden` 表示用户或登录未启用 Azure AD 标识保护。 **注意：** 此属性的详细信息仅适用于 Azure AD Premium P2 客户。 所有其他客户将返回 `hidden`。 |
| riskState | riskState | 风险用户、风险登录或风险检测的报告状态。 可能的值有：`none`、`confirmedSafe`、`remediated`、`dismissed`、`atRisk`、`confirmedCompromised`、`unknownFutureValue`。 |
| DurationMs | - | 此值未映射，可以放心地忽略此字段。 |
| CallerIpAddress | - | 发出请求的客户端的 IP 地址。 | 
| CorrelationId | - | 客户端所传递的可选 GUID。 此值可帮助将客户端操作与服务器端操作关联，并且在跟踪跨服务的日志时非常有用。 |
| 标识 | - | 发出请求时提供的令牌中的标识。 可以是用户帐户、系统帐户或服务主体。 |
| Level | - | 提供消息的类型。 对于审核，它始终是“信息”。 |
| 位置 | - | 提供登录活动的位置。 |
| 属性 | - | 列出与登录关联的所有属性。|



## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的审核日志架构](./overview-reports.md)
* [详细了解 Azure 平台日志](../../azure-monitor/essentials/platform-logs-overview.md)