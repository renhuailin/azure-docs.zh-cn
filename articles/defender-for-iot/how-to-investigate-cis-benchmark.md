---
title: 调查 CIS 基准建议
description: 基于 OS 基线建议执行基本和高级调查。
ms.date: 1/21/2021
ms.topic: how-to
ms.openlocfilehash: 01ca6e1fecddff9800872a3e5495a5cac578a74f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782021"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>调查 OS 基线（基于 CIS 基准）建议 

基于 OS 基线建议执行基本和高级调查。

## <a name="basic-os-baseline-security-recommendation-investigation"></a>基本 OS 基线安全建议调查  

可以通过导航到 IoT 中心下的 Azure Defender for IoT 门户来调查 OS 基线建议。 有关详细信息，请参阅如何[调查安全建议](quickstart-investigate-security-recommendations.md)。

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>高级 OS 基线安全建议调查  

本部分介绍如何更好地了解 OS 基线测试结果，以及如何在 Azure Log Analytics 中查询事件。  

仅可使用日志分析来支持高级 OS 基线安全建议调查。 继续操作之前，请将 Defender for IoT 连接到 Log Analytics 工作区。 有关高级 OS 基线安全建议的详细信息，请参阅如何[配置 Azure Defender for IoT 基于代理的解决方案](how-to-configure-agent-based-solution.md)。

针对警报，在 Log Analytics 中查询 IoT 安全事件：

1. 导航到“警报”页面。

1. 选择“调查 Log Analytics 工作区中的建议”。

针对建议，在 Log Analytics 中查询 IoT 安全事件：

1. 导航到“建议”页面。

1. 选择“调查 Log Analytics 工作区中的建议”。

1. 从“建议详细信息”快速视图页中选择“显示操作系统 (OS) 基线规则详细信息”，查看特定设备的详细信息 。

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="查看特定设备的详细信息。"::: 

要在 Log Analytics 工作区中直接查询 IoT 安全事件：

1. 导航到“日志”页。

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="从左侧窗格中选择日志。":::

1. 选择“调查警报”或从任何安全建议或警报中选择“在 Log Analytics 中调查警报”选项 。   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>调查 OS 基线资源的有用查询： 

> [!Note]
> 确保在以下查询之一中将 `<device-id>` 替换为你的设备名称。 


### <a name="retrieve-the-latest-information"></a>检索最新信息

- **设备组故障**：运行以下查询以检索有关在设备组上失败的检查的最新信息： 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **特定设备故障**：运行以下查询以检索有关在特定设备上失败的检查的最新信息：  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **特定设备错误**：运行以下查询以检索有关特定设备上出现错误的检查的最新信息： 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **更新未能通过特定检查的设备组的设备列表** - 运行此查询，以检索未通过特定检查的设备（在设备组中）的已更新列表：  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>后续步骤

[调查安全建议](quickstart-investigate-security-recommendations.md)。
 