---
title: 调查 CIS 基准建议
titleSuffix: Azure Defender for IoT
description: 基于 OS 基准建议执行基本和高级调查。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2f68ebedb229f7295bc9c5dcc3b3349808970e8c
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809703"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>基于 CIS 基准 (调查 OS 基线) 建议 

基于 OS 基准建议执行基本和高级调查。

## <a name="basic-os-baseline-security-recommendation-investigation"></a>基本操作系统基准安全建议调查  

你可以通过导航到 **Iot 中心** 下的 Azure Defender for iot 门户来调查操作系统基准建议。 有关详细信息，请参阅如何 [调查安全建议](quickstart-investigate-security-recommendations.md)。

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>高级操作系统基准安全建议调查  

本部分介绍如何更好地了解 OS 基线测试结果，以及如何在 Azure Log Analytics 中查询事件。  

仅使用 log analytics 支持高级操作系统基准安全建议调查。 继续操作之前，请将 IoT 连接到 Log Analytics 工作区。 有关高级操作系统基准安全建议的详细信息，请参阅如何为 [基于 IoT 代理的解决方案配置 Azure Defender](how-to-configure-agent-based-solution.md)。

在 Log Analytics 中查询警报的 IoT 安全事件：

1. 导航到 " **警报** " 页。

1. **在 Log Analytics 工作区中选择调查建议**。

若要在 Log Analytics 中查询 IoT 安全事件，请执行以下操作：

1. 导航到 " **建议** " 页。

1. **在 Log Analytics 工作区中选择调查建议**。

1. 从 "**建议详细信息**" 快速视图页中选择 "**显示操作系统 (OS) 基线规则详细信息**，查看特定设备的详细信息。

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="查看特定设备的详细信息。"::: 

直接在 Log Analytics 工作区中查询 IoT 安全事件：

1. 导航到 " **日志** " 页。

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="从左侧窗格中选择 &quot;日志&quot;。":::

1. 选择 " **调查警报"** 或 "从任何安全建议中 **调查警报" Log Analytics** 选项，或单击 "警报"。   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>调查 OS 基线资源的有用查询： 

> [!Note]
> 请确保将替换为在 `<device-id>` 以下每个查询中为设备提供)  (的名称。 


### <a name="retrieve-the-latest-information"></a>检索最新信息

- **设备汽油失败**：运行以下查询以检索有关在设备上失败的检查的最新信息： 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **特定设备故障** -运行以下查询以检索有关特定设备上失败的检查的最新信息：  

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

- **特定设备错误** -运行此查询以检索有关特定设备上出错的检查的最新信息： 

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
 
- **更新未能通过特定检查的设备组的设备列表** -运行此查询，以检索设备汽油) 的、未通过特定检查的 (设备的更新列表：  
 
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
 