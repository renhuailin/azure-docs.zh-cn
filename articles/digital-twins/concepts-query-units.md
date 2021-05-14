---
title: Azure 数字孪生中的查询单元
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生中查询单元的计费概念
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 710800bdb1dc4a9a2d0bc69493fe1d89d279ff78
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108734284"
---
# <a name="query-units-in-azure-digital-twins"></a>Azure 数字孪生中的查询单元 

Azure 数字孪生查询单元 (QU)是一个按需计算单元，用于通过[查询 API](/rest/api/digital-twins/dataplane/query) 执行 [Azure 数字孪生查询](how-to-query-graph.md)。 

它抽象掉执行 Azure 数字孪生支持的查询操作所需的 CPU、IOPS 和内存等系统资源，允许你改为在查询单元中跟踪使用情况。

执行查询所消耗的查询单元数量受以下因素影响：

* 查询的复杂度
* 结果集的大小（因此，一个返回 10 个结果的查询将比一个只返回一个结果的类似复杂度的查询消耗更多的 QU）

本文介绍如何了解查询单元以及如何跟踪查询单元消耗量。

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>在 Azure 数字孪生中查找查询单元消耗量

使用 Azure 数字孪生[查询 API](/rest/api/digital-twins/dataplane/query) 运行查询时，可以检查响应头以跟踪查询消耗的 QU 数量。 在从 Azure 数字孪生发送回的响应中查找“query-charge”。

Azure 数字孪生 [SDK](concepts-apis-sdks.md) 使你可以从可分页响应中提取 query-charge 标头。 本部分介绍如何查询数字孪生体，以及如何循环访问可分页响应来提取 query-charge 标头。 

以下代码片段演示了如何提取在调用查询 API 时产生的查询费用。 它首先循环访问响应页面以访问 query-charge 标头，然后循环访问每个页面中的数字孪生体结果。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>后续步骤

若要详细了解如何查询 Azure 数字孪生，请访问：

* [概念：查询语言](concepts-query-language.md)
* [操作指南：查询孪生图](how-to-query-graph.md)
* [查询 API 参考文档](/rest/api/digital-twins/dataplane/query/querytwins)

可以在 Azure 数字孪生服务限制中找到与 Azure 数字孪生查询相关的限制。