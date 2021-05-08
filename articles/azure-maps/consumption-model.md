---
title: 用于路线规划的车辆消耗模型 | Microsoft Azure Maps
description: 了解 Azure Maps 支持的消耗模型：“燃烧”和“电气”。 查看每个模型使用哪些参数，并查看参数约束。
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013133"
---
# <a name="consumption-model"></a>消耗模型

路线规划服务提供了一组参数，用于详细描述特定于车辆的消耗模型。
根据 vehicleEngineType 的值，支持以下两种主要消耗模型：燃烧和电气。 在同一请求中指定属于不同模型的参数是错误做法。 此外，消耗模型参数不能与以下 travelMode 值一起使用：bicycle 和 pedestrian。

## <a name="parameter-constraints-for-consumption-model"></a>消耗模型的参数约束

在这两种消耗模型中，指定参数时都存在一些依赖关系。 这意味着，显式指定某些参数可能需要指定某些其他参数。 以下是需要注意的依赖关系：

* 所有参数均要求用户指定 constantSpeedConsumption。 如果未指定“constantSpeedConsumption”，则指定任何其他消耗模型参数都是错误的。 “vehicleWeight”参数是此要求的一个例外。
* accelerationEfficiency 和 decelerationEfficiency  始终必须一起指定（即两者都指定或都不指定）。
* 如果指定了 accelerationEfficiency 和 decelerationEfficiency，两者值的乘积不得大于 1（防止出现永恒运动）。
* uphillEfficiency 和 downhillEfficiency  始终必须一起指定（即两者都指定或都不指定）。
* 如果指定了 uphillEfficiency 和 downhillEfficiency，两者值的乘积不得大于 1（防止出现永恒运动）。
* 如果用户指定了 \*Efficiency 参数，还必须指定“vehicleWeight”参数。 如果 vehicleEngineType 为燃料，则还必须指定 fuelEnergyDensityInMJoulesPerLiter。
* maxChargeInkWh 和 currentChargeInkWh  始终必须一起指定（即两者都指定或都不指定）。

> [!NOTE]
> 如果仅指定了 constantSpeedConsumption，消耗计算不会考虑其他任何消耗（如爬坡和车辆加速）。

## <a name="combustion-consumption-model"></a>燃料消耗模型

如果将 vehicleEngineType 设置为“燃烧”，则使用燃烧消耗模型。
下面是属于此模型的参数列表。 参阅“参数”部分，获取详细说明。

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>电力消耗模型

如果将 vehicleEngineType 设置为“电气”，则使用电气消耗模型。
下面是属于此模型的参数列表。 参阅“参数”部分，获取详细说明。

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>合理的消耗参数值

尽管一组特定消耗参数可能满足所有显式要求，但该组参数仍可能被拒绝。 当某个特定参数值或多个参数值组合被认为会导致不合理的消耗值量级时，会发生此情况。 如果出现此情况，则很可能是存在输入错误，应当注意调整所有合理的消耗参数值。 如果一组特定消耗参数遭到拒绝，随之而来的错误消息会解释其原因。
参数的详细说明中包含两种模型的合理值的示例。
