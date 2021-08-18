---
title: 了解 Azure 数据工厂数据流的预留折扣 | Microsoft Docs
description: 了解如何对运行中的 ADF 数据流应用预留折扣。 折扣按小时应用到这些数据流。
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 6796006bbac8987370bddab979356d5123fab204
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726873"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>如何对 Azure 数据工厂数据流应用预留折扣

购买 ADF 数据流预留容量后，预留折扣会自动应用到使用与预留的计算类型和核心计数相匹配的 Azure 集成运行时的数据流。

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

预留折扣的性质是“不用就会失效”。 因此，如果没有用于任何小时的匹配 Azure 集成资源，那么将丢失该小时的预留数量。 不能结转未使用的预留小时数。

停止对数据流使用集成运行后时，预留折扣将自动应用到指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失。

## <a name="discount-applied-to-adf-data-flows"></a>应用到 ADF 数据流的折扣

ADF 数据流预留容量折扣应用到按小时执行集成运行时。 您购买的预留与数据流的集成运行时所使用的计算类型匹配。 对于不是整小时运行的数据流，预留将自动应用到与预留属性匹配其他数据流。 折扣还可以应用到同时运行的数据流。 如果与预留属性匹配的数据流不是整小时运行，则无法获得该小时的完整预留折扣权益。

以下示例演示如何根据购买的核心数目以及 ADF 数据流的运行时间，来应用 ADF 数据流预留容量折扣。

- 方案 1：通过输入 80 作为内存优化计算类型的数量，可以购买 1 小时 80 个核心的内存优化计算的 ADF 数据流预留。 运行数据流时，Azure 集成运行时设置为 1 小时 144 个核心的内存优化。 将按 1 小时 64 个核心的数据流用量收取即用即付费用。 将获得 1 小时 80 个核心的内存优化用量的预留折扣。
- 方案 2：通过输入 32 作为常规用途计算类型的数量，可以购买 1 小时 32 个核心的常规用途计算的 ADF 数据流预留。 使用 32 个核心的常规计算 Azure 集成运行时，调试数据流 1 小时。 将获得整个小时用量的预留折扣。

若要了解 Azure 预留的应用情况并在计费使用情况报告中查看该信息，请参阅[了解 Azure 预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](../cost-management-billing/reservations/save-compute-costs-reservations.md)