---
title: 为 Microsoft Azure Maps 选择适当的定价层
description: 了解 Azure Maps 定价层。 了解哪些层提供哪些功能，并查看选择定价层的关键注意事项。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb57e424642141e73588a61d026d1ff2f1a8b096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905292"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>在 Azure Maps 中选择适当的定价层

Azure Maps 提供两个定价层：S0 和 S1。 本文旨在帮助你根据需要选择适当的定价层。 要选择适当的定价层，请考虑以下两个问题。

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>计划支持多少位并发用户？

S0 和 S1 定价层处理不同数量的数据吞吐量。 S0 定价层每秒最多可处理 50 次查询。 而 S1 层每秒可处理 50 多次查询。

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>计划使用哪些地理空间功能？

如果核心地理空间 API 满足服务需求，则选择 S0 定价层。 如果想为应用程序提供更高级的功能，请考虑选择 S1 定价层。 高级功能包括：区域加混合图像、获取路由范围和批量地理编码。 要选择最适合自己应用程序的定价层，请查看以下定价层功能表：

### <a name="pricing-tier-capabilities"></a>定价层功能

| 功能                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 地图呈现                              | ✓                   | ✓       |
| 卫星图像                       |                     | ✓        |
| 搜索                                  | ✓                    | ✓        |
| 批量搜索                            |                     | ✓        |
| 路由                                   | ✓                    |✓        |
| 批量路由                            |                    | ✓        |
| 矩阵路由                          |                     | ✓        |
| 路由范围（等时线）                |                     | ✓        |
| 交通                                |✓                    |✓        |
| 时区                               |✓                    |✓        |
| 地理位置（预览版）                    |✓                   |✓        |
| 空间操作                        |                    |✓        |
| 地理围栏                                |                    |✓        |
| Azure Maps 数据（预览版）                |                     | ✓        |
| 移动（预览版）                       |                     | ✓        |
| 天气（预览版）                        |✓                    |✓        |
|  Creator（预览版）                         |                   |✓        |
|  高程（预览版）                        |                   |✓        |

还需要考虑下列问题：

* 属于哪种企业类型？
* 应用程序的重要程度如何？

### <a name="pricing-tier-targeted-customers"></a>定价层目标客户

请参阅“定价层目标客户”表，以便更好地了解 S0 和 S1 定价层。 有关详细信息，请参阅 [Azure Maps 定价](https://azure.microsoft.com/pricing/details/azure-maps/)。 

| 定价层  |     目标客户                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    S0 定价层适合所有生产阶段的应用程序：从概念验证开发和早期阶段测试到应用程序生产和部署。 但是，此层专为小规模开发和/或并发用户较少的客户而设计。 
| S1            |    S1 定价层适合采用大规模企业应用程序、任务关键应用程序或具有大量并发用户的客户。 它也适用于那些需要高级地理空间服务的客户。

## <a name="next-steps"></a>后续步骤

了解有关如何查看和更改定价层的详细信息：

> [!div class="nextstepaction"]
> [管理定价层](how-to-manage-pricing-tier.md)
