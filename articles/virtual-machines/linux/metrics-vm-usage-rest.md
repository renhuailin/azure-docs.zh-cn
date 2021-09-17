---
title: 使用 REST API 获取 Azure 虚拟机使用情况数据
description: 使用 Azure REST API 收集虚拟机的使用情况指标。
author: rloutlaw
ms.service: virtual-machines
ms.subservice: monitoring
ms.custom: REST
ms.topic: how-to
ms.date: 06/13/2018
ms.author: routlaw
ms.openlocfilehash: 536a3040bda2fec6aa5139949d31ef47bd2bbd9e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694918"
---
# <a name="get-virtual-machine-usage-metrics-using-the-rest-api"></a>使用 REST API 获取虚拟机使用情况指标

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

此示例演示如何使用 [Azure REST API](/rest/api/azure/) 检索 Linux 虚拟机的 CPU 使用情况。

[Azure Monitor REST 参考](/rest/api/monitor)中提供了完整的参考文档和 REST API 的其他示例。 

## <a name="build-the-request"></a>生成请求

使用以下 GET 请求从虚拟机中收集 [CPU 百分比指标](../../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
```

### <a name="request-headers"></a>请求标头

以下标头是必需的： 

|请求标头|说明|  
|--------------------|-----------------|  
|Content-Type： |必需。 设置为 `application/json`。|  
|Authorization： |必需。 设置为有效的`Bearer` [访问令牌](/rest/api/azure/#authorization-code-grant-interactive-clients)。 |  

### <a name="uri-parameters"></a>URI 参数

| 名称 | 说明 |
| :--- | :---------- |
| subscriptionId | 用于标识 Azure 订阅的订阅 ID。 如果拥有多个订阅，请参阅[使用多个订阅](/cli/azure/manage-azure-subscriptions-azure-cli)。 |
| resourceGroupName | 与资源相关的 Azure 资源组的名称。 可以从 Azure 资源管理器 API、CLI 或门户获取此值。 |
| vmname | Azure 虚拟机的名称。 |
| metricnames | 以逗号分隔的有效[负载均衡器指标](../../load-balancer/load-balancer-standard-diagnostics.md)列表。 |
| api-version | 要用于请求的 API 版本。<br /><br /> 本文档涵盖 API 版本 `2018-01-01`，包含于上述 URL 中。  |
| timespan | 带有以下格式 `startDateTime_ISO/endDateTime_ISO` 的字符串，用于定义返回的指标的时间范围。 此可选参数设置为在示例中返回一天的数据。 |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>请求正文

此操作无需任何请求正文。

## <a name="handle-the-response"></a>处理响应

成功返回指标值列表时，返回状态代码 200。 [参考文档](/rest/api/monitor/metrics/list#errorresponse)中提供了错误代码的完整列表。

## <a name="example-response"></a>示例响应 

```json
{
    "cost": 0,
    "timespan": "2018-06-08T23:48:10Z/2018-06-09T00:48:10Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmname}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=Percentage%20CPU",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "Percentage CPU",
                "localizedValue": "Percentage CPU"
            },
            "unit": "Percent",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-08T23:48:00Z",
                            "average": 0.44
                        },
                        {
                            "timeStamp": "2018-06-08T23:49:00Z",
                            "average": 0.31
                        },
                        {
                            "timeStamp": "2018-06-08T23:50:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:51:00Z",
                            "average": 0.29
                        },
                        {
                            "timeStamp": "2018-06-08T23:52:00Z",
                            "average": 0.285
                        } ]
                } ]
        } ]
}
```
