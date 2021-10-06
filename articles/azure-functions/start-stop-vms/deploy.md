---
title: 部署启动/停止 VM v2（预览版）
description: 本文介绍如何为 Azure 订阅中的 Azure VM 部署启动/停止 VM v2（预览版）功能。
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: dc44fc58b8832c30ed1b740eb6637f1fc8ed1413
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129454955"
---
# <a name="deploy-startstop-vms-v2-preview"></a>部署启动/停止 VM v2（预览版）

请按顺序执行本主题中的步骤，以安装启动/停止 VM v2（预览版）功能。 完成设置过程后，请配置计划，以根据自己的要求对其进行自定义。

## <a name="permissions-considerations"></a>权限注意事项
在部署之前和部署期间，请牢记以下内容：
+   该解决方案允许对启动/停止 v2 部署具有适当的基于角色的访问控制 (RBAC) 权限的用户添加、删除和管理启动/停止 v2 范围下的虚拟机计划。 此行为是设计使然。 实际上，这意味着对虚拟机没有直接 RBAC 权限的用户在拥有 RBAC 权限时仍然可以在该虚拟机上创建启动、停止和自动停止操作，以修改管理它的启动/停止 v2 解决方案。
+ 任何有权访问启动/停止 v2 解决方案的用户都可以发现启动/停止 v2 应用程序所用的 Application Insights 实例中存储的成本、节省、操作历史记录和其他数据。
+ 管理启动/停止 v2 解决方案时，应考虑用户的启动/停止 v2 解决方案权限，特别是当用户无权直接修改目标虚拟机时。
## <a name="deploy-feature"></a>部署功能

可于[此处](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)的启动/停止 VM v2 GitHub 组织发起部署。 尽管此功能旨在跨订阅中单个部署的所有资源组管理订阅中的所有 VM，但你可以根据所在组织的操作模型或要求来安装其他实例。 还可以配置此功能，以跨多个订阅集中管理 VM。

为了简化管理和删除操作，我们建议你将启动/停止 VM v2（预览版）部署到专用资源组。

> [!NOTE]
> 此预览版目前不支持指定现有存储帐户或 Application Insights 资源。


> [!NOTE]
> 函数应用和存储帐户的命名格式已更改。 为保证全局唯一性，现在会将唯一的随机字符串追加到这些资源的名称之后。

1. 打开浏览器并导航到启动/停止 VM v2 [GitHub 组织](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)。
1. 根据你在其中创建 Azure VM 的 Azure 云环境选择部署选项。 Azure 门户中随即将打开“自定义 Azure 资源管理器部署”页面。
1. 根据提示登录到 [Azure 门户](https://portal.azure.com)。
1. 输入以下值：

    |名称 |值 |
    |-----|------|
    |区域 |为新资源选择一个附近的区域。|
    |资源组名称 |为将要包含启动/停止 VM 的单项资源的资源组指定名称。 |
    |资源组区域 |指定资源组所在的区域。 例如“美国中部”。 |
    |Azure 函数应用名称 |键入在 URL 路径中有效的名称。 将对你键入的名称进行验证，以确保其在 Azure Functions 中是唯一的。 |
    |Application Insights 名称 |为将要包含启动/停止 VM 的分析数据的 Application Insights 实例指定名称。 |
    |Application Insights 区域 |为 Application Insights 实例指定区域。|
    |存储帐户名称 |为存储启动/停止 VM 执行遥测数据的 Azure 存储帐户指定名称。 |
    |电子邮件地址 |指定一个或多个电子邮件地址以接收状态通知，用逗号 (,) 分隔。|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="启动/停止 VM 遥测部署配置":::

1. 选择页面底部的“查看 + 创建”。
1. 选择“创建”以开始部署。
1. 选择屏幕顶部的铃铛图标（通知）可查看部署状态。 此时会看到“部署正在进行”。 等待部署完成。
1. 从通知窗格选择“转到资源组”。 将看到类似于以下的屏幕：

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="启动/停止 VM 遥测部署资源列表":::

> [!NOTE]
> 我们正在收集操作和检测信号遥测数据，以便在你联系支持团队进行任何故障排除时为你提供更好的帮助。 我们还正在收集虚拟机事件历史记录，用于验证服务在虚拟机上的运行时间，以及虚拟机的暂停时间，以确定服务效果。

## <a name="enable-multiple-subscriptions"></a>启用多个订阅

启动/停止部署完成后，请执行以下步骤，使启动/停止 VM v2（预览版）在多个订阅之间执行操作。

1. 复制在部署过程中指定的 Azure 函数应用名称的值。

1. 在门户中，导航到二级订阅。 选择订阅，然后选择“访问控制(IAM)”

1. 依次选择“添加”、“添加角色分配” 。

1. 从“角色”下拉列表中选择“参与者”角色 。

1. 在“选择”字段中输入 Azure 函数应用程序的名称。 在结果中选择函数名称。

1. 选择“保存”，以提交更改。

## <a name="configure-schedules-overview"></a>配置计划概述

若要管理自动化方法以控制 VM 的启动和停止，请根据需要配置一个或多个包含的逻辑应用。

- 计划 - 启动和停止操作基于你针对 Azure 资源管理器和经典 VM 指定的计划。ststv2_vms_Scheduled_start 和 ststv2_vms_Scheduled_stop 用于配置计划启动和停止 。

- 顺序 - 启动和停止操作基于针对具有预定义排序标记的 VM 的计划。 仅支持两个命名标记，即 sequencestart 和 sequencestop 。 ststv2_vms_Sequenced_start 和 ststv2_vms_Sequenced_stop 分别用于配置排序启动和排序停止 。

    > [!NOTE]
    > 此方案仅支持 Azure 资源管理器 VM。

- AutoStop - 此功能仅用于基于 Azure 资源管理器和经典 VM 的 CPU 使用率对它们执行停止操作。 该功能也可以是基于计划的执行操作，可在 VM 上创建警报，并根据条件触发警报，以执行停止操作。ststv2_vms_AutoStop 用于配置自动停止功能。

如果需要其他计划，可以使用 Azure 门户中的“克隆”选项来复制所提供的其中一个逻辑应用。

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="选择“克隆”选项以复制逻辑应用":::

## <a name="scheduled-start-and-stop-scenario"></a>计划启动和停止方案

执行以下步骤，为 Azure 资源管理器和经典 VM 配置已计划的启动和停止操作。 例如，可以配置 ststv2_vms_Scheduled_start 计划，在上午上班时启动 VM，并根据 ststv2_vms_Scheduled_stop 计划，在晚上下班时停止订阅中的所有 VM。

支持将逻辑应用配置为仅启动 VM。

对于每个方案，可以针对一个或多个订阅、单个或多个资源组执行操作，并在包含或排除列表中指定一个或多个 VM。 不能在同一逻辑应用中同时指定。

1. 登录 [Azure 门户](https://portal.azure.com)并导航到“逻辑应用”。

1. 在逻辑应用列表中，若要配置计划的启动，请选择 ststv2_vms_Scheduled_start。 若要配置计划的停止，请选择 ststv2_vms_Scheduled_stop。

1. 从左侧窗格中选择“逻辑应用设计器”。

1. “逻辑应用设计器”出现后，在“设计器”窗格中，选择“定期”以配置逻辑应用计划。 若要了解具体的定期选项，请参阅[计划定期任务](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)。

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="为逻辑应用配置定期频率":::

1. 在“设计器”窗格中，选择“函数-尝试”以配置目标设置。 在请求正文中，若要跨订阅中的所有资源组管理 VM，请修改请求正文，如以下示例中所示。

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    在 `subscriptions` 数组中指定多个订阅，每个值用逗号分隔，如以下示例中所示。

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    在请求正文中，若要管理特定资源组的 VM，请修改请求正文，如以下示例中所示。 指定的每个资源路径必须用逗号分隔。 如果需要，可以指定一个或多个资源组。

    此示例还演示了如何排除虚拟机。 可以通过指定 VM 资源路径或按通配符来排除 VM。

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    此处将对所有 VM 执行操作，但两个订阅中名称以 Az 和 Bz 开头的 VM 除外。

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    在请求正文中，若要管理订阅中一组特定的 VM，请修改请求正文，如以下示例中所示。 指定的每个资源路径必须用逗号分隔。 如果需要，可以指定一个 VM。

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
      }
    }
    ```

1. 在逻辑应用的“概述”窗格中，选择“启用”。  

## <a name="sequenced-start-and-stop-scenario"></a>顺序启动和停止方案

在分布式应用程序体系结构中，如果某环境在多个 Azure 资源管理器 VM 上包含两个或更多组件，则支持按顺序启动和停止组件的序列非常重要。

1. 在逻辑应用列表中，若要配置顺序启动，请选择 ststv2_vms_Sequenced_start。 若要配置顺序停止，请选择 ststv2_vms_Sequenced_stop。

1. 从左侧窗格中选择“逻辑应用设计器”。

1. “逻辑应用设计器”出现后，在“设计器”窗格中，选择“定期”以配置逻辑应用计划。 若要了解具体的定期选项，请参阅[计划定期任务](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)。

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="为逻辑应用配置定期频率":::

1. 在“设计器”窗格中，选择“函数-尝试”以配置目标设置。 在请求正文中，若要跨订阅中的所有资源组管理 VM，请修改请求正文，如以下示例中所示。

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    在 `subscriptions` 数组中指定多个订阅，每个值用逗号分隔，如以下示例中所示。

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    在请求正文中，若要管理特定资源组的 VM，请修改请求正文，如以下示例中所示。 指定的每个资源路径必须用逗号分隔。 如果需要，可以指定一个资源组。

    此示例还演示了如何根据使用通配符的计划启动/停止示例按资源路径排除虚拟机。

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    在请求正文中，若要管理订阅中一组特定的 VM，请修改请求正文，如以下示例中所示。 指定的每个资源路径必须用逗号分隔。 如果需要，可以指定一个 VM。

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>自动停止方案

启动/停止 VM v2（预览版）可评估在非高峰时段（如下班之后）未使用的计算机并在处理器使用率小于指定百分比时自动关闭它们，从而管理订阅中运行 Azure 资源管理器和经典 VM 的成本。

请求正文中的以下指标警报属性支持自定义：

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

若要详细了解 Azure Monitor 指标警报的工作方式以及配置方式，请参阅 [Azure Monitor 中的指标警报](../../azure-monitor/alerts/alerts-metric-overview.md)。

1. 在逻辑应用列表中，若要配置自动停止，请选择 ststv2_vms_AutoStop。

1. 从左侧窗格中选择“逻辑应用设计器”。

1. “逻辑应用设计器”出现后，在“设计器”窗格中，选择“定期”以配置逻辑应用计划。 若要了解具体的定期选项，请参阅[计划定期任务](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger)。

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="为逻辑应用配置定期频率":::

1. 在“设计器”窗格中，选择“函数-尝试”以配置目标设置。 在请求正文中，若要跨订阅中的所有资源组管理 VM，请修改请求正文，如以下示例中所示。

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    在请求正文中，若要管理特定资源组的 VM，请修改请求正文，如以下示例中所示。 指定的每个资源路径必须用逗号分隔。 如果需要，可以指定一个资源组。

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    在请求正文中，若要管理订阅中一组特定的 VM，请修改请求正文，如以下示例中所示。 指定的每个资源路径必须用逗号分隔。 如果需要，可以指定一个 VM。

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>后续步骤

若要了解如何监视由启动/停止 VM v2（预览版）功能管理的 Azure VM 的状态并执行其他管理任务，请参阅[管理启动/停止 VM](manage.md) 一文。
