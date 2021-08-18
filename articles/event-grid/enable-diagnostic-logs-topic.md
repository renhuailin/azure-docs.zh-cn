---
title: Azure 事件网格 - 为主题或域启用诊断日志
description: 本文提供了有关如何为 Azure 事件网格主题启用诊断日志的分步说明。
ms.topic: how-to
ms.date: 06/25/2021
ms.openlocfilehash: 7ae1900c08b78ac5d84f4d36ef48319cd42ebce2
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112969844"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>为 Azure 事件网格主题或域启用诊断日志
本文提供了有关如何为事件网格主题或域启用诊断设置的分步说明。  这些设置使你可以捕获和查看“发布和传递失败”日志。 

> [!IMPORTANT]
> 如需查看诊断日志的架构，请参阅[诊断日志](diagnostic-logs.md)。 


## <a name="prerequisites"></a>先决条件

- 预配的事件网格主题
- 用于捕获诊断日志的预配目标。 它可以是事件网格主题所在位置中的以下目标之一：
    - Azure 存储帐户
    - 事件中心
    - Log Analytics 工作区

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>为自定义主题启用诊断日志

> [!NOTE]
> 以下过程提供了有关如何为主题启用诊断日志的分步说明。 为域启用诊断日志的步骤非常相似。 在步骤 2 中，导航到 Azure 门户中的事件网格域。  

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到要为其启用诊断日志设置的事件网格主题。 
    1. 在顶部的搜索栏中，搜索“事件网格主题”。 
    
        ![搜索自定义主题](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. 从列表中选择要为其配置诊断设置的 **主题**。 
1. 在左侧菜单中的“监视”下，选择“诊断设置” 。
1. 在“诊断设置”页面上，选择“添加新的诊断设置”。  
    
    ![“添加诊断设置”按钮](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 为诊断设置指定一个名称。 
6. 在“日志”部分中选择 **DeliveryFailures** 和 **PublishFailures** 选项。 
    ![选择具体的失败](./media/enable-diagnostic-logs-topic/log-failures.png)
7. 为日志启用一个或多个捕获目标，然后通过选择以前创建的捕获资源对其进行配置。 
    - 如果选择“存档到存储帐户”，请选择“存储帐户 - 配置”，然后选择你的 Azure 订阅中的存储帐户。 

        ![此屏幕截图显示了“诊断设置”页，其中已选中“存档到 Azure 存储帐户”并已选择存储帐户。](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - 如果选择“流式传输到事件中心”，请选择“事件中心 - 配置”，然后选择事件中心命名空间、事件中心和访问策略。 
        ![此屏幕截图显示了“诊断设置”页，其中已选中“流式传输到事件中心”。](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - 如果选择“发送到 Log Analytics”，请选择 Log Analytics 工作区。
        ![此屏幕截图显示了“诊断设置”页，其中已选中“发送到 Log Analytics”。](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. 选择“保存” 。 然后，选择右上角的 X 以关闭页面。 
9. 现在，返回“诊断设置”页面，确认在“诊断设置”表中看到了一个新条目。 
    ![此屏幕截图显示了“诊断设置”页，在“诊断设置”表中突出显示了一个新条目。](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     你还可以为主题启用所有指标的收集。 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>为系统主题启用诊断日志

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到要为其启用诊断日志设置的事件网格主题。 
    1. 在顶部的搜索栏中，搜索“事件网格系统主题”。 
    
        ![搜索系统主题](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. 选择要为其配置诊断设置的 **系统主题**。 
    
        ![选择系统主题](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. 在左侧菜单中的“监视”下选择“诊断设置”，然后选择“添加诊断设置”。 

    ![添加诊断设置 - 按钮](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. 为诊断设置指定一个名称。 
7. 在“日志”部分中选择“DeliveryFailures”。 
    ![选择“传递失败”](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. 为日志启用一个或多个捕获目标，然后通过选择以前创建的捕获资源对其进行配置。 
    - 如果选择“发送到 Log Analytics”，请选择 Log Analytics 工作区。
        ![发送到 Log Analytics](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - 如果选择“存档到存储帐户”，请选择“存储帐户 - 配置”，然后选择你的 Azure 订阅中的存储帐户。 

        ![存档到 Azure 存储帐户](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - 如果选择“流式传输到事件中心”，请选择“事件中心 - 配置”，然后选择事件中心命名空间、事件中心和访问策略。 
        ![流式传输到事件中心](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. 选择“保存” 。 然后，选择右上角的 X 以关闭页面。 
9. 现在，返回“诊断设置”页面，确认在“诊断设置”表中看到了一个新条目。 
    ![列表中的诊断设置](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     还可以为系统主题启用收集所有指标的功能。

    ![系统主题 - 启用所有指标](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>查看 Azure 存储中的诊断日志 

1. 启用存储帐户作为捕获目标后，事件网格会开始发出诊断日志。 应在存储帐户中看到名为“insights-logs-deliveryfailures”和“insights-logs-publishfailures”的新容器。 

    ![存储 - 诊断日志的容器](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. 当你在其中一个容器中导航时，最终将停在一个 JSON 格式的 blob 中。 该文件包含传递失败或发布失败的日志条目。 导航路径表示事件网格主题的 ResourceId 以及有关何时发出日志条目的时间戳（分钟级别）。 该 blob/JSON 文件（可下载）在结尾处遵循下一节中所述的架构。 

    [![存储中的 JSON 文件](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. 你应该在 JSON 文件中看到类似于以下示例的内容： 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板
下面是一个示例 Azure 资源管理器模板，用于为事件网格主题启用诊断设置。 部署此示例模板时，将创建以下资源。

- 事件网格主题
- Log Analytics 工作区

然后，它会针对该主题创建诊断设置，以将诊断信息发送到 Log Analytics 工作区。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "topic_name": {
            "defaultValue": "spegrid0917topic",
            "type": "String"
        },
        "log_analytics_workspace_name": {
            "defaultValue": "splogaw0625",
            "type": "String"
        },
        "location": {
            "defaultValue": "eastus",
            "type": "String"
        },
        "sku": {
            "defaultValue": "Free",
            "type": "String"
        }               
    },  
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/topics",
            "apiVersion": "2020-10-15-preview",
            "name": "[parameters('topic_name')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Basic"
            },
            "kind": "Azure",
            "identity": {
                "type": "None"
            },
            "properties": {
                "inputSchema": "EventGridSchema",
                "publicNetworkAccess": "Enabled"
            }
        },
        {
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('log_analytics_workspace_name')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.OperationalInsights/workspaces",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
                }
            }
        },
        {
            "type": "Microsoft.EventGrid/topics/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('topic_name'), '/', 'Microsoft.Insights/', parameters('log_analytics_workspace_name'))]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.EventGrid/topics', parameters('topic_name'))]",
                "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('log_analytics_workspace_name'))]"          
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('log_analytics_workspace_name'))]",
                "metrics": [
                    {
                        "category": "AllMetrics",
                        "enabled": true
                    }
                ],
                "logs": [
                    {
                        "category": "DeliveryFailures",
                        "enabled": true
                    },
                    {
                        "category": "PublishFailures",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤
有关主题或域的诊断日志的日志架构和其他概念性信息，请参阅[诊断日志](diagnostic-logs.md)。
