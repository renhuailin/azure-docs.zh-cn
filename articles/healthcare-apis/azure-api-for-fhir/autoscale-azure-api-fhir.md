---
title: Azure API for FHIR 的自动缩放
description: 本文介绍 Azure API for FHIR 的自动缩放功能。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: zxue
ms.openlocfilehash: cc5fd2dd91c1edc39886938d91ebb54bcbc3ef0d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778725"
---
# <a name="autoscale-for-azure-api-for-fhir"></a>Azure API for FHIR 的自动缩放 

Azure API for FHIR 作为一种托管服务，允许客户保留符合 FHIR 的医疗保健数据，并通过服务 API 安全地交换它。 为了适应不同的事务工作负载，客户可以使用手动缩放或自动缩放。

## <a name="what-is-autoscale"></a>自动缩放是什么？

默认情况下，Azure API for FHIR 设置为手动缩放。 当事务工作负载已知且一致时，此选项非常适合。 客户可以通过门户将吞吐量 `RU/s` 调整到10,000，并提交请求以增加限制。 

利用自动缩放，客户可以运行各种工作负载，并自动纵向扩展和缩减吞吐量 `RU/s`，而无需手动调整。

## <a name="how-to-enable-autoscale"></a>如何启用自动缩放？

若要启用自动缩放功能，你可以创建一次性支持票证来请求该功能。 Microsoft 支持团队将根据支持优先级启用自动缩放功能。

> [!NOTE]
> Azure 门户中无法使用自动缩放功能。

## <a name="how-to-adjust-the-maximum-throughput-rus"></a>如何调整最大吞吐量 RU/s？

启用自动缩放后，系统会计算并设置初始 `Tmax` 值。 可伸缩性由最大吞吐量 `RU/s` 值 (`Tmax`) 控制，且在 `0.1 *Tmax`（或 `Tmax` 的 10%）和 `Tmax RU/s` 之间运行。 

可以增加最大 `RU/s` 或 `Tmax` 值，直至服务支持的上限。 当服务繁忙时，吞吐量 `RU/s` 将纵向扩展到 `Tmax` 值。 当服务处于空闲状态时，吞吐量 `RU/s` 将纵向缩减到 `Tmax` 值的 10%。
 
您还可以减少最大 `RU/s` 或 `Tmax` 值。 减少最大 `RU/s` 时，可以设置的最小值为：`MAX (4000, highest max RU/s ever provisioned / 10, current storage in GB * 400)`，舍入到最接近的 1000 `RU/s`。

* 示例 1：有 1 GB 的数据，预配的 `RU/s` 最大值为 10,000。 最小值为 Max (4000, 10,000/10, 1x400) = 4000。 使用第一个数字 4000。
* 示例 2：有 20 GB 的数据，预配的 `RU/s` 最大值为 100,000。 最小值为 Max (4000, 100,000/10, 20x400) = 10,000。 使用第二个数字 100,000/10 =10,000。
* 示例 3：有 80 GB 的数据，预配的 RU/s 最大值为 300,000。 最小值为 Max (4000, 300,000/10, 80x400) = 32,000。 使用第三个数字 80x400=32,000。

如果是有效的数字，则可以通过门户调整最大 `RU/s` 或 `Tmax` 值，且其不超过 10,000 `RU/s`。 可以创建支持票证以请求大于 10,000 的 `Tmax` 值。

## <a name="what-is-the-cost-impact-of-autoscale"></a>自动缩放有哪些成本影响？

由于自动管理预配的吞吐量单位，因此自动缩放功能会产生成本。 此成本增加不适用于存储和运行时成本。 有关定价的详细信息，请参阅 [Azure API for FHIR 定价](https://azure.microsoft.com/pricing/details/azure-api-for-fhir/)。

>[!div class="nextstepaction"]
>[关于适用于 FHIR 的 Azure API](overview.md)
