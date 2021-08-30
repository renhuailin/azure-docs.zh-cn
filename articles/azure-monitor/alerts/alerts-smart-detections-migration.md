---
title: 将 Azure Monitor Application Insights 智能检测升级为警报（预览版）| Microsoft Docs
description: 了解将 Azure Monitor Application Insights 智能检测升级为警报规则需要执行的步骤
ms.topic: conceptual
ms.date: 05/30/2021
ms.openlocfilehash: d0995c05af707c5a4fb94305ca1211e38ca59e3a
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587474"
---
# <a name="migrate-azure-monitor-application-insights-smart-detection-to-alerts-preview"></a>将 Azure Monitor Application Insights 智能检测迁移到警报（预览版）

本文介绍将 Application Insights 智能检测迁移到警报的过程。 迁移时会为各种智能检测模块创建警报规则。 可以管理和配置这些规则，就像任何其他 Azure Monitor 警报规则一样。 还可以为这些规则配置操作组，从而通过多种方法对新的检测执行操作或触发通知。

## <a name="benefits-of-migration-to-alerts"></a>迁移到警报的好处

通过迁移，智能检测现在支持利用 Azure Monitor 警报的完整功能，其中包括：

- 适用于所有检测器的丰富通知选项 - 使用[操作组](../alerts/action-groups.md)可以配置触发警报时触发的多种类型的通知和操作。 可以通过电子邮件、短信、语音呼叫或推送通知以及调用安全 Webhook、逻辑应用、自动化 Runbook 等操作来配置通知。 操作组支持配置一次操作，然后跨多个警报规则使用它们，从而实现进一步的大规模管理。
- 使用 Azure Monitor 警报体验和 API 对智能检测警报进行大规模管理。
- 基于规则的通知抑制 - [操作规则](../alerts/alerts-action-rules.md)可帮助在任意 Azure 资源管理器范围（Azure 订阅、资源组或目标资源）内定义或禁止操作。 它们包含各种筛选器，可帮助你将应用范围缩减到要处理的特定警报实例子集。 

## <a name="migrated-smart-detection-capabilities"></a>迁移的智能检测功能

迁移 Application Insights 资源时，会创建一组新的警报规则。  系统会针对每个迁移的智能检测功能创建一个规则。 下表将迁移前的智能检测功能映射到迁移后的警报规则：

| 智能检测规则名称 <sup>(1)</sup>          | 警报规则名称 <sup>(2)</sup> |
| -------------------------                          | ------------------------------ |
| 服务器响应降级                | 响应延迟降低 - \<Application Insights resource name\>  |
| 依赖项持续时间减少                 | 依赖性延迟降低 - \<Application Insights resource name\>|
| 跟踪严重性比下降（预览）      | 跟踪严重性下降 - \<Application Insights resource name\>|
| 异常卷的异常增加（预览）        | 异常卷异常 - \<Application Insights resource name\>|
| 检测到潜在的内存泄漏（预览）           | 潜在的内存泄漏 - \<Application Insights resource name\>|
| 页面加载慢                                | 已中断 <sup>(3)</sup>  |
| 服务器响应慢                          | 已中断 <sup>(3)</sup>  |
| 依赖项持续时间长                           | 已中断 <sup>(3)</sup>  |
| 检测到潜在的安全问题（预览）        | 已中断 <sup>(3)</sup>  |
| 每日数据量中异常增加（预览）       | 已中断 <sup>(3)</sup>  |

<sup>(1)</sup>“智能检测设置”边栏选项卡中显示的规则名称  
<sup>(2)</sup> 迁移后新警报规则的名称  
<sup>(3)</sup> 这些智能检测功能不会转换为警报，因为使用率较低而且检测效果需要重新评估。 完成迁移后，此资源将不再支持这些检测器。 

迁移不会更改智能检测的算法设计和行为。 更改之前和之后预期具有相同的检测性能。

需要单独将迁移应用到每个 Application Insights 资源。 对于未显式迁移的资源，智能检测将继续像以前一样工作。

### <a name="action-group-configuration-for-the-new-smart-detection-alert-rules"></a>新智能检测警报规则的操作组配置

在迁移过程中，系统会为每个新警报规则自动配置一个操作组。 迁移可以为每个规则分配默认操作组。 默认操作组根据迁移前的规则通知进行配置：

- 如果智能检测规则配置了默认电子邮件或未配置通知，则新警报规则将配置名为“Application Insights 智能检测”的操作组。
    - 如果迁移工具找到使用该名称的现有操作组，它会将新的警报规则链接到该操作组。  
    - 否则，它会创建使用该名称的新操作组。 新的组是为“电子邮件 Azure 资源管理器角色”操作配置的，并会将通知发送到 Azure 资源管理器监视参与者和监视读取者用户。

- 如果在迁移之前更改了默认电子邮件通知，则会创建名一个为“Application Insights 智能检测 \<n\>”的操作组，并会创建一个向之前配置的电子邮件地址发送通知的电子邮件操作。

选择将为所有新警报规则配置的现有操作组，而不使用默认操作组。

## <a name="executing-smart-detection-migration-process"></a>执行智能检测迁移过程

### <a name="migrate-your-smart-detection-using-the-azure-portal"></a>使用 Azure 门户迁移智能检测

一次将迁移应用于一个特定的 Application Insights 资源。

若要迁移资源中的智能检测，请执行以下步骤：

1. 在 Application Insights 资源左侧菜单中的“调查”下，选择“智能检测” 。

2. 单击为名“将智能检测迁移到警报(预览版)”的横幅。 迁移对话框随即打开。

   ![智能检测源横幅](media/alerts-smart-detections-migration/smart-detection-feed-banner.png)

3. 选择要为新的警报规则配置的操作组。 可以选择使用默认操作组（如上所述）或使用现有操作组之一。

4. 选择“迁移”以启动迁移过程。

   ![“智能检测迁移”对话框](media/alerts-smart-detections-migration/smart-detection-migration-dialog.png)

迁移后，会为 Application Insight 资源创建新的警报规则，如上所述。

### <a name="migrate-your-smart-detection-using-azure-cli"></a>使用 Azure CLI 迁移智能检测

可以使用以下 Azure CLI 命令启动智能检测迁移。 如上文中所述，命令将触发预配置的迁移过程。

```azurecli
az rest --method POST --uri /subscriptions/{subscriptionId}/providers/Microsoft.AlertsManagement/migrateFromSmartDetection?api-version=2021-01-01-preview --body @body.txt
```

其中 body.txt 应包括：

```json
{
      "scope": [
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName} /providers/microsoft.insights/components/{resourceName} "
      ],
      "actionGroupCreationPolicy" : "{Auto/Custom}",
      "customActionGroupName" : "{actionGroupName}"           
}
```

ActionGroupCreationPolicy 选择用于将智能检测规则中的电子邮件设置迁移到操作组的策略。 允许值包括：

- “自动”，它使用本文档中所述的默认操作组
- “自定义”，它使用“customActionGroupName”中指定的操作组创建所有警报规则 。
- \<blank\> - 如果未指定 ActionGroupCreationPolicy，则使用“自动”策略。

### <a name="migrate-your-smart-detection-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板迁移智能检测

可以使用 Azure 资源管理器模板，为特定的 Application Insights 资源触发从智能检测到警报的迁移。 使用此方法需要完成以下操作：

- 为每个支持的检测器创建智能检测警报规则
- 修改 Application Insight 属性以指示迁移已完成

使用此方法可以控制要创建的警报规则、定义自己的警报规则名称和说明，以及为每个规则选择所需的任何操作组。

出于此目的，应使用以下模板（按需要进行编辑以提供订阅 ID 和 Application Insights 资源名称）

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "applicationInsightsResourceName": {
            "type": "string"
        },
        "actionGroupName": {
            "type": "string",
            "defaultValue": "Application Insights Smart Detection"
        },
        "actionGroupResourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().Name]"
        }
    },
    "variables": {
        "applicationInsightsResourceId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().Name,'/providers/microsoft.insights/components/',parameters('applicationInsightsResourceName'))]",
        "actionGroupId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',parameters('actionGroupResourceGroup'),'/providers/microsoft.insights/ActionGroups/',parameters('actionGroupName'))]",
        "requestPerformanceDegradationDetectorRuleName": "[concat('Response Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "dependencyPerformanceDegradationDetectorRuleName": "[concat('Dependency Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "traceSeverityDetectorRuleName": "[concat('Trace Severity Degradation - ', parameters('applicationInsightsResourceName'))]",
        "exceptionVolumeChangedDetectorRuleName": "[concat('Exception Anomalies - ', parameters('applicationInsightsResourceName'))]",
        "memoryLeakRuleName": "[concat('Potential Memory Leak - ', parameters('applicationInsightsResourceName'))]"
    },
    "resources": [
        {
            "name": "[variables('requestPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "RequestPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {   
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('dependencyPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Dependency Latency Degradation notifies you of an unusual increase in response by a dependency your app is calling (e.g. REST API or database)",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "DependencyPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('traceSeverityDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Trace Severity Degradation notifies you of an unusual increase in the severity of the traces generated by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "TraceSeverityDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('exceptionVolumeChangedDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Exception Anomalies notifies you of an unusual rise in the rate of exceptions thrown by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "ExceptionVolumeChangedDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('memoryLeakRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Potential Memory Leak notifies you of increased memory consumption pattern by your app which may indicate a potential memory leak.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "MemoryLeakDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[concat(parameters('applicationInsightsResourceName'),'/migrationToAlertRulesCompleted')]",
            "type": "Microsoft.Insights/components/ProactiveDetectionConfigs",
            "location": "[resourceGroup().location]",
            "apiVersion": "2018-05-01-preview",
            "properties": {
                "name": "migrationToAlertRulesCompleted",
                "sendEmailsToSubscriptionOwners": false,
                "customEmails": [],
                "enabled": true
            },
            "dependsOn": [
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('requestPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('dependencyPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('traceSeverityDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('exceptionVolumeChangedDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('memoryLeakRuleName'))]"
            ]
        }
    ]
}
```

## <a name="viewing-your-alerts-after-the-migration"></a>在迁移后查看警报

在迁移过程之后，可以通过在“Application Insights”资源左侧菜单中选择“警报”条目查看智能检测警报。 选择“智能检测器”作为“信号类型”，以仅筛选并显示智能检测警报 。 可以选择警报来查看其检测详细信息。

![智能检测警报](media/alerts-smart-detections-migration/smart-detector-alerts.png)

仍可以在 Application Insights 资源的智能检测源中查看可用的检测。

![智能检测源](media/alerts-smart-detections-migration/smart-detection-feed.png)

## <a name="managing-smart-detection-alert-rules-settings-after-the-migration"></a>在迁移后管理智能检测警报规则设置

### <a name="managing-alert-rules-settings-using-the-azure-portal"></a>使用 Azure 门户管理警报规则设置

迁移完成后，可以像访问为资源定义的其他警报规则一样访问新的智能检测警报规则：

1. 在 Application Insights 资源左侧菜单中的“监视”标题下，选择“警报” 。

   ![“警报”菜单](media/alerts-smart-detections-migration/application-insights-alerts.png)

2. 选择“管理警报规则”

   ![管理警报规则](media/alerts-smart-detections-migration/manage-alert-rules.png)

3. 选择“智能检测器”作为“信号类型”，以筛选并显示智能检测警报规则 。

   ![智能检测器规则](media/alerts-smart-detections-migration/smart-detector-rules.png)

### <a name="enabling--disabling-smart-detection-alert-rules"></a>启用/禁用智能检测警报规则 

智能检测警报规则可以通过门户 UI 启用或禁用，也可以像任何其他警报规则一样以编程方式启用或禁用。

如果在迁移之前禁用了特定的智能检测规则，则新警报规则也将被禁用。

### <a name="configuring-action-group-for-your-alert-rules"></a>为警报规则配置操作组

与任何其他 Azure Monitor 警报规则一样，你可以为新的智能检测警报规则创建和管理操作组。 

### <a name="managing-alert-rule-settings-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理警报规则设置

完成迁移后，可以使用 Azure 资源管理器模板为智能检测警报规则设置配置设置。

> [!NOTE]
> 完成迁移后，必须使用智能检测警报规则模板配置智能检测设置，并且无法再使用 [Application Insights 资源管理器模板](../app/proactive-arm-config.md#smart-detection-rule-configuration)进行配置。

此 Azure 资源管理器模板示例演示了如何在“已启用”（严重性为 2）的状态下配置“响应延迟降低”警报规则 。
* 智能检测是一项全局服务，因此规则位置是在全局位置中创建的。
* “id”属性应根据配置的特定检测器更改。 值必须是以下项之一：

  - FailureAnomaliesDetector
  - RequestPerformanceDegradationDetector
  - DependencyPerformanceDegradationDetector
  - ExceptionVolumeChangedDetector
  - TraceSeverityDetector  
  - MemoryLeakDetector
 
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Response Latency Degradation - my-app",
            "location": "global", 
            "properties": {
                  "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT24H",
                  "detector": {
                  "id": "RequestPerformanceDegradationDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```



## <a name="next-steps"></a>后续步骤

- [详细了解 Azure 中的警报](./alerts-overview.md)
- [详细了解 Application Insights 中的智能检测](../app/proactive-diagnostics.md)
