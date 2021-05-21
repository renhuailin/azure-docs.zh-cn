---
title: Azure 证明审核日志
description: 描述为 Azure 证明收集的完整审核日志
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95758965"
---
# <a name="audit-logs-for-azure-attestation"></a>Azure 证明的审核日志

审核日志是对一段时间内发生的离散事件的记录，日志安全、不可改变而且有时间戳。 这些日志将捕获可能影响证明实例功能的重要事件。

Azure 证明管理证明实例及其关联的策略。 将审核并记录与实例管理和策略更改关联的操作。

本文包含有关记录的事件、收集的信息以及这些日志位置的信息。

## <a name="about-audit-logs"></a>关于审核日志

Azure 证明使用代码为影响执行证明的方式的事件生成审核日志。 这通常会归结为对证明实例以及某些管理操作进行策略更改的方式或时间。

### <a name="auditable-events"></a>可审核的事件
下面是我们收集的一些审核日志：

|     事件/API                              |     事件描述                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     创建实例                        |     创建证明服务的新实例。 |
|     销毁实例                       |     销毁证明服务的实例。 |
|     添加策略证书                 |     将证书添加到当前的策略管理证书集。 |
|     删除策略证书              |     从当前的策略管理证书集中删除证书。 |
|     设置当前策略                     |     为给定的 TEE 类型设置证明策略。 |
|     重置证明策略               |     为给定的 TEE 类型重置证明策略。 |
|     准备更新策略               |     为给定的 TEE 类型准备更新证明策略。 |
|     灾难后解除冻结租户       |     重新密封此证明服务实例上的所有证明租户。 此操作只能由证明服务管理员执行。 |

### <a name="collected--information"></a>收集的信息
对于每个事件，Azure 证明都会收集以下信息：

- 操作名称
- 操作成功
- 操作调用方，可以是以下任一项：
    - Azure AD UPN
    - 对象 ID
    - 证书
    - Azure AD 租户 ID
- 操作目标，可以是以下任一项：
    - 环境
    - 服务区域
    - 服务角色
    - 服务角色实例
    - 资源 ID
    - 资源区域

### <a name="sample-audit-log"></a>示例审核日志

审核日志以 JSON 格式提供。 审核日志如以下示例所示。

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>访问审核日志

这些日志存储在 Azure 中，不能直接访问。 如果需要访问这些日志，请提交支持票证。 有关详细信息，请参阅[联系 Microsoft 支持部门](https://azure.microsoft.com/support/options/)。 

提交支持票证后，Microsoft 将下载这些日志并为你提供这些日志的访问权限。
