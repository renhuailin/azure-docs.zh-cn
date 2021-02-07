---
title: 了解 Azure 数据工厂数据流的预订折扣 |Microsoft Docs
description: 了解如何将预订折扣应用于运行 ADF 数据流。 每小时将折扣应用于这些数据流。
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 3b97f09c2b6f4c5f5352bbaa081e0bcae900ebd5
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808134"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>如何将预订折扣应用于 Azure 数据工厂数据流

购买 ADF 数据流保留容量后，预订折扣将自动应用于使用与保留的计算类型和核心计数相匹配的 Azure 集成运行时的数据流。

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

预留折扣的性质是“不用就会失效”。 因此，如果没有用于任何小时的匹配 Azure 集成资源，则会丢失该小时的预订量。 不能结转未使用的预留小时数。

停止对数据流使用 integration runtime 时，预订折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失。

## <a name="discount-applied-to-adf-data-flows"></a>应用于 ADF 数据流的折扣

ADF 数据流保留容量折扣应用于每小时执行集成运行时。 您购买的预订与数据流的集成运行时所使用的计算类型匹配。 对于不运行完整小时的数据流，预订将自动应用于与预订属性匹配的其他数据流。 折扣还适用于同时运行的数据流。 如果没有与预订属性匹配的全小时运行的数据流，则不会获得该小时的预订折扣的全部权益。

下面的示例演示如何根据购买的核心数和运行时间来应用 ADF 数据流保留容量折扣。

- 方案1：通过输入80作为内存优化计算类型的数量，你可以购买一小时80内核的内存优化计算。 运行数据流时，Azure 集成运行时设置为144核心的内存，优化时间为一小时。 将按即用即付价格为数据流使用情况提供一小时的64核心。 对于内存优化使用量为80核心的一小时，可获得预订折扣。
- 方案2：通过输入32作为常规用途计算类型的数量，你购买了一小时32核心常规用途计算类型的 ADF 数据流预留。 使用 "常规计算" Azure 集成运行时的32个核心，将数据流调试1小时。 你可以获得这一小时使用的保留折扣。

若要了解 Azure 预留的应用情况并在计费使用情况报告中查看该信息，请参阅[了解 Azure 预留使用情况](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 保留，请参阅以下文章：

- [什么是 Azure 预订？](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
