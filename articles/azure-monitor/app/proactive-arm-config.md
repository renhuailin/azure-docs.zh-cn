---
title: 智能检测规则设置 - Azure Application Insights
description: 使用 Azure 资源管理器模板自动完成 Azure Application Insights 智能检测规则的管理和配置
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 02/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 6f13bc07ce5ae6a11b59b6d18a609ca2ee259964
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111949402"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Application Insights 智能检测规则

>[!NOTE]
>你可以将 Application Insights 资源迁移到警报库智能检测（预览版）。 迁移时会为各种智能检测模块创建警报规则。 创建后就可以管理和配置这些规则，就像任何其他 Azure Monitor 警报规则一样。 你还可以为这些规则配置操作组，从而通过多种方法针对新的检测执行操作或触发通知。
>
> 有关迁移过程和迁移后智能检测行为的更多详细信息，请参阅[智能检测警报迁移](../alerts/alerts-smart-detections-migration.md)。
> 

可以使用 [Azure 资源管理器模板](../../azure-resource-manager/templates/syntax.md)来管理和配置 Application Insights 中的智能检测规则。
使用 Azure 资源管理器自动化部署新的 Application Insights 资源或修改现有资源的设置时，可以使用此方法。

## <a name="smart-detection-rule-configuration"></a>智能检测规则配置

可以配置智能检测规则的以下设置：
- 是否已启用该规则（默认值为 **true**。）
- 发现检测时是否应向与订阅的 [监视读者](../../role-based-access-control/built-in-roles.md#monitoring-reader)和 [监视参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)角色关联的用户发送电子邮件（默认值为 **true**）。
- 找到检测项时，应收到通知的其他任何电子邮件收件人。
    -  电子邮件配置不适用于标记为“预览”的智能检测规则  。

为了让用户通过 Azure 资源管理器配置规则设置，智能检测规则配置现已在 Application Insights 资源中提供一个名为 **ProactiveDetectionConfigs** 的内部资源。
为了提供最大的灵活性，可为每个智能检测规则配置独特的通知设置。

## <a name="examples"></a>示例

以下几个示例演示如何使用 Azure 资源管理器模板配置智能检测规则的设置。
在所有示例中，Application Insights 资源名为 _myApplication_，“‘依赖项持续时间长’智能检测规则”的内部名称为 _longdependencyduration_。
请务必替换 Application Insights 资源名称，并指定相关的智能检测规则内部名称。 在下表中查看每个智能检测规则的对应内部 Azure 资源管理器名称列表。

### <a name="disable-a-smart-detection-rule"></a>禁用智能检测规则

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>禁用发送有关某个智能检测规则的电子邮件通知

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>为智能检测规则添加更多的电子邮件收件人

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>智能检测规则名称

下表列出了门户中显示的智能检测规则名称，以及应在 Azure 资源管理器模板中为这些规则使用的内部名称。

> [!NOTE]
> 标记为“预览”的智能检测规则不支持电子邮件通知。 因此，只能为这些规则设置“已启用”属性。 

| Azure 门户规则名称 | 内部名称
|:---|:---|
| 页面加载慢 | slowpageloadtime |
| 服务器响应慢 | slowserverresponsetime |
| 依赖项持续时间长 | longdependencyduration |
| 服务器响应降级 | degradationinserverresponsetime |
| 依赖项持续时间减少 | degradationindependencyduration |
| 跟踪严重性比下降（预览） | extension_traceseveritydetector |
| 异常卷的异常增加（预览） | extension_exceptionchangeextension |
| 检测到潜在的内存泄漏（预览） | extension_memoryleakextension |
| 检测到潜在的安全问题（预览） | extension_securityextensionspackage |
| 每日数据量中异常增加（预览） | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>失败异常警报规则

此 Azure 资源管理器模板演示如何配置严重性为 2 的故障异常警报规则。

> [!NOTE]
> 失败异常是一项全局服务，因此在全局位置上创建规则位置。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
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

> [!NOTE]
> 此 Azure 资源管理器模板对于失败异常警报规则是唯一的，并且不同于本文中所述的其他经典智能检测规则。 如果要手动管理故障异常，请在 Azure Monitor 警报中完成，而所有其他智能检测规则均在 UI 的“智能检测”窗格中进行管理。

## <a name="next-steps"></a>后续步骤

了解有关自动检测的详细信息：

- [失败异常](./proactive-failure-diagnostics.md)
- [内存泄漏](./proactive-potential-memory-leak.md)
- [性能异常](./proactive-performance-diagnostics.md)