---
title: 自动缩放Azure API for FHIR
description: 本文介绍自动缩放功能Azure API for FHIR。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: zxue
ms.openlocfilehash: cc5fd2dd91c1edc39886938d91ebb54bcbc3ef0d
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713371"
---
# <a name="autoscale-for-azure-api-for-fhir"></a>自动缩放Azure API for FHIR 

作为Azure API for FHIR服务，客户可以保留符合 FHIR 的医疗保健数据，并通过服务 API 安全地交换这些数据。 为了适应不同的事务工作负荷，客户可以使用手动缩放或自动缩放。

## <a name="what-is-autoscale"></a>自动缩放是什么？

默认情况下，Azure API for FHIR设置为手动缩放。 当事务工作负荷已知且一致时，此选项非常有效。 客户可以通过门户调整吞吐量 `RU/s` ，最大为 10，000，并提交请求以提高限制。 

借助自动缩放，客户可以运行各种工作负载，并且无需手动调整即可 `RU/s` 自动增加和缩减吞吐量。

## <a name="how-to-enable-autoscale"></a>如何启用自动缩放？

若要启用自动缩放功能，可以创建一次支持票证来请求它。 Microsoft 支持团队将基于支持优先级启用自动缩放功能。

> [!NOTE]
> 自动缩放功能不可用于Azure 门户。

## <a name="how-to-adjust-the-maximum-throughput-rus"></a>如何调整最大吞吐量 RU/s？

启用自动缩放后，系统将计算并设置初始 `Tmax` 值。 可伸缩性由最大吞吐量值 或 控制，在 和 `RU/s` `Tmax` (`0.1 *Tmax` 10%) `Tmax` 运行 `Tmax RU/s` 。 

可以增加最大值 `RU/s` `Tmax` 或值，并达到服务支持的值。 当服务繁忙时，吞吐量 `RU/s` 将扩展为 `Tmax` 值。 当服务处于空闲状态时， `RU/s` 吞吐量将缩减到 10% `Tmax` 的值。
 
还可以减小最大值 `RU/s` 或 `Tmax` 值。 当降低最大值 `RU/s` 时，可设置为的最小值为：，舍入到最接近 `MAX (4000, highest max RU/s ever provisioned / 10, current storage in GB * 400)` 的 1000。 `RU/s`

* **示例 1：** 你有 1 GB 的数据，预配的最高 `RU/s` 数据为 10，000。 最小值为 Max (4000、10，000/10、1x400) = 4000。  使用第一个数字 **4000。**
* **示例 2：** 你有 20 GB 的数据，预配的最高 `RU/s` 数据为 100，000。 最小值为 Max (**4000、100，000/10、20x400**) = 10，000。 使用第二个数字 **100，000/10 =10，000。**
* **示例 3：** 你有 80 GB 的数据，预配的最高 RU/s 为 300，000。 最小值为 Max (4000、300，000/10、80x400) = 32，000。  使用第三个数字 **80x400=32，000。**

如果最大值或值是有效的数字，并且该值不大于 `RU/s` `Tmax` 10，000，则可以通过门户调整最大值或值 `RU/s` 。 可以创建支持票证来请求大于 `Tmax` 10，000 的值。

## <a name="what-is-the-cost-impact-of-autoscale"></a>自动缩放对成本有什么影响？

由于自动管理预配的吞吐量单位，自动缩放功能会产生成本。 这种成本增加不适用于存储和运行时成本。 有关定价的信息，请参阅Azure API for FHIR [定价](https://azure.microsoft.com/pricing/details/azure-api-for-fhir/)。

>[!div class="nextstepaction"]
>[关于适用于 FHIR 的 Azure API](overview.md)
