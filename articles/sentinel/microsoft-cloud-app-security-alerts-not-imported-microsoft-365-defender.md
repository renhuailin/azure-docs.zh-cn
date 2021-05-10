---
title: 未通过 Microsoft 365 Defender 集成将 Microsoft Cloud App Security 警报导入 Azure Sentinel | Microsoft Docs
description: 本文显示了来自 Microsoft Cloud App Security 的警报，这些警报必须直接引入到 Azure Sentinel 中，因为 Microsoft 365 Defender 不会收集它们。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 0232cbde5aaddad268aa0e4725ee3587a2461800
ms.sourcegitcommit: 18cd3c1c8cc47258c6a1a04e0e03d6248c52ef24
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107992722"
---
# <a name="microsoft-cloud-app-security-alerts-not-imported-into-azure-sentinel-through-microsoft-365-defender-integration"></a>未通过 Microsoft 365 Defender 集成将 Microsoft Cloud App Security 警报导入 Azure Sentinel

与其他 Microsoft Defender 组件（Defender for Endpoint、Defender for Identity 和 Defender for Office 365）一样，Microsoft Cloud App Security 也会生成由 Microsoft 365 Defender 收集的警报。 启用 Microsoft 365 Defender 连接器后，Microsoft 365 Defender 会反过来生成[由 Azure Sentine 引入并与之同步](microsoft-365-defender-sentinel-integration.md#microsoft-365-defender-incidents-and-microsoft-incident-creation-rules)的事件。

与其他三个组件不同的是，并非所有类型的 Cloud App Security 警报都已加入到 Microsoft 365 Defender，因此，如果需要 Azure Sentinel 中所有 Cloud App Security 警报的事件，则必须相应地调整 Microsoft 事件创建分析规则，以便直接引入到 Sentinel 的警报将继续生成事件，而加入到 Microsoft 365 Defender 的警报则不会生成事件（因此不会出现重复）。

## <a name="cloud-app-security-alerts-not-onboarded-to-microsoft-365-defender"></a>Cloud App Security 警报未加入到 Microsoft 365 Defender

以下警报未加入到 Microsoft 365 Defender，应继续配置生成这些警报的 Microsoft 事件创建规则，以生成事件。

| Cloud App Security 警报显示名称 | Cloud App Security 警报名称 |
|-|-|
| **访问策略警报** | `ALERT_CABINET_INLINE_EVENT_MATCH` |
| **通过“发现的流量”日志创建的活动超出了每日限制** | `ALERT_DISCOVERY_TRAFFIC_LOG_EXCEEDED_LIMIT` |
| **活动策略警报** | `ALERT_CABINET_EVENT_MATCH_AUDIT` |
| **异常泄露警报** | `ALERT_EXFILTRATION_DISCOVERY_ANOMALY_DETECTION` |
| **遭到入侵的帐户** | `ALERT_COMPROMISED_ACCOUNT` |
| **发现的应用安全漏洞警报** | `ALERT_MANAGEMENT_DISCOVERY_BREACHED_APP` |
| **非活动帐户** | `ALERT_ZOMBIE_USER` |
| **已提高调查优先级分数** | `ALERT_UEBA_INVESTIGATION_PRIORITY_INCREASE` |
| **恶意 OAuth 应用许可** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MALICIOUS_OAUTH_APP_CONSENT` |
| **误导性 OAuth 应用名称** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MISLEADING_APP_NAME` |
| **OAuth 应用的误导性发布者名称** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_MISLEADING_PUBLISHER_NAME` |
| **发现了新应用** | `ALERT_CABINET_DISCOVERY_NEW_SERVICE` |
| **OAuth 应用使用不安全的重定向 URL** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_NON_SECURE_REDIRECT_URL` |
| **OAuth 应用策略警报** | `ALERT_CABINET_APP_PERMISSION` |
| **可疑的活动警报** | `ALERT_SUSPICIOUS_ACTIVITY` |
| **可疑的云使用警报** | `ALERT_DISCOVERY_ANOMALY_DETECTION` |
| **可疑的 OAuth 应用名称** | `ALERT_CABINET_APP_PERMISSION_ANOMALY_SUSPICIOUS_APP_NAME` |
| **系统警报应用连接器错误** | `ALERT_MANAGEMENT_DISCONNECTED_API` |
| **系统警报: Cloud Discovery 自动日志上传错误** | `ALERT_MANAGEMENT_LOG_COLLECTOR_LOW_RATE` |
| **系统警报: Cloud Discovery 日志处理错误** | `ALERT_MANAGEMENT_LOG_COLLECTOR_CONSTANTLY_FAILED_PARSING` |
| **系统警报: ICAP 连接器错误** | `ALERT_MANAGEMENT_DLP_CONNECTOR_ERROR` |
| **系统警报: SIEM 代理错误** | `ALERT_MANAGEMENT_DISCONNECTED_SIEM` |
| **系统警报: SIEM 代理通知** | `ALERT_MANAGEMENT_NOTIFICATIONS_SIEM` |
| **云资源的异常区域** | `MCAS_ALERT_ANUBIS_DETECTION_UNCOMMON_CLOUD_REGION` |
|

## <a name="next-steps"></a>后续步骤

- [将 Microsoft 365 Defender 连接](connect-microsoft-365-defender.md)到 Azure Sentinel。
- 详细了解 [Azure Sentinel](overview.md)、[Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender) 和 [Cloud App Security](/cloud-app-security/what-is-cloud-app-security)。
