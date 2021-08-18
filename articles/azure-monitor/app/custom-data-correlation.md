---
title: Azure Application Insights | Microsoft Docs
description: 将 Application Insights 中的数据与其他数据集（如数据扩充或查找表、非 Application Insights 数据源和自定义数据）关联。
ms.topic: conceptual
author: eternovsky
ms.author: evternov
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.openlocfilehash: e45a9cfe8a372247c7ac3550c65a5a30e3fd7cb6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178213"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>将 Application Insights 数据与自定义数据源相关联

Application Insights 收集多种不同的数据类型：异常、跟踪、页面视图等。 虽然通常情况下这对调查应用程序的性能、可靠性和使用情况来说已足够，但有时候，将 Application Insights 中存储的数据与其他完全自定义的数据集相关联会很有用。

下述情况可能需要自定义数据：

- 数据扩充或查找表：例如，在提供服务器名称的同时，补充提供服务器的所有者以及服务器所在的实验室位置 
- 与非 Application Insights 数据源相关联：例如，将网店购买数据与购买履行服务的信息相关联，确定寄送时间估算的准确度如何 
- 完全自定义数据：我们的许多客户喜欢支持 Application Insights 的 Azure Monitor 日志平台的查询语言和性能，希望使用它来查询与 Application Insights 根本不相关的数据。 例如，在进行智能家居安装的过程中根据[此处](https://www.catapultsystems.com/blogs/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)所述跟踪太阳能板的性能。

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>如何将自定义数据与 Application Insights 数据相关联 

由于 Application Insights 由强大的 Azure Monitor 日志平台提供支持，因此我们可以充分利用 Azure Monitor 来引入数据。 然后，我们会使用“join”运算符来编写查询，将此自定义数据与 Azure Monitor 日志中提供的数据相关联。 

## <a name="ingesting-data"></a>引入数据

在此部分，我们会讨论如何将数据引入 Azure Monitor 日志中。

如果还没有 Log Analytics 工作区，请预配一个新的，只需按照[这些说明](../vm/monitor-virtual-machine.md)操作并包括“创建工作区”步骤即可。

开始将日志数据发送到 Azure Monitor。 存在多个选项：

- 对于同步机制，可以直接调用[数据收集器 API](../logs/data-collector-api.md)，也可以使用逻辑应用连接器 - 只需查找“Azure Log Analytics”并选取“发送数据”选项即可：

  ![“选择操作”的屏幕截图](./media/custom-data-correlation/01-logic-app-connector.png)  

- 对于异步选项，请使用数据收集器 API 来生成处理管道。 有关详细信息，请参阅[此文](../logs/create-pipeline-datacollector-api.md)。

## <a name="correlating-data"></a>关联数据

Application Insights 基于 Azure Monitor 日志平台。 因此，我们可以使用[跨资源联接](../logs/cross-workspace-query.md)将已引入到 Azure Monitor 中的任何数据与 Application Insights 数据相关联。

例如，可以将实验室清单和位置引入名为“myLA”的 Log Analytics 工作区的名为“LabLocations_CL”的表中。 然后，如果我们需要查看在名为“myAI”的 Application Insights 应用中跟踪的请求，并将处理请求的计算机名称与前述自定义表中存储的这些计算机位置相关联，则可在 Application Insights 或 Azure Monitor 上下文中运行以下查询：

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>后续步骤

- 查看[数据收集器 API](../logs/data-collector-api.md) 参考。
- 有关[跨资源联接](../logs/cross-workspace-query.md)的详细信息。