---
title: Azure Monitor Application Insights 依赖关系数据模型
description: 适用于依赖项遥测的 Application Insights 数据模型
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: 06e22c69e8d2d5187b2f78022302d10d12fe6a6c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110098949"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>依赖项遥测：Application Insights 数据模型

[Application Insights](./app-insights-overview.md) 中的依赖项遥测表示受监视组件与远程组件（如 SQL 或 HTTP 终结点）的交互。

## <a name="name"></a>名称

通过此依赖项调用启动的命令的名称。 低基数值。 示例包括存储过程名称和 URL 路径模板。

## <a name="id"></a>ID

依赖项调用实例的标识符。 用于与此依赖项调用所对应的请求遥测项关联。 有关详细信息，请参阅[关联](./correlation.md)页。

## <a name="data"></a>数据

此依赖项调用启动的命令。 示例包括使用所有查询参数的 SQL 语句和 HTTP URL。

## <a name="type"></a>类型

依赖项类型名称。 低基数值，用于对依赖项进行逻辑分组和解释 commandName 和 resultCode 等其他字段。 示例包括 SQL、Azure 表和 HTTP。

## <a name="target"></a>目标

依赖项调用的目标站点。 示例包括服务器名称、主机地址。 有关详细信息，请参阅[关联](./correlation.md)页。

## <a name="duration"></a>持续时间

请求持续时间，格式为：`DD.HH:MM:SS.MMMMMM`。 必须小于 `1000` 天。

## <a name="result-code"></a>结果代码

依赖项调用的结果代码。 示例包括 SQL 错误代码和 HTTP 状态代码。

## <a name="success"></a>成功

指示调用成功还是失败。

## <a name="custom-properties"></a>自定义属性

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>自定义度量值

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>后续步骤

- 为 [.NET](./asp-net-dependencies.md) 设置依赖项跟踪。
- 为 [Java](java-2x-agent.md) 设置依赖项跟踪。
- [编写自定义依赖项遥测](./api-custom-events-metrics.md#trackdependency)
- 有关 Application Insights 的类型和数据模型，请参阅[数据模型](data-model.md)。
- 查看 Application Insights 支持的[平台](./platforms.md)。

