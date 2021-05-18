---
title: 在 Azure IoT Central 应用程序中管理帐单并从免费定价计划转换 | Microsoft Docs
description: 以管理员身份了解如何管理帐单并从免费定价计划移到 Azure IoT Central 应用程序中的标准定价计划
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 50d0119b08d2c76a5f6111e485408ebcdace83c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96549015"
---
# <a name="manage-your-bill-in-an-iot-central-application"></a>在 IoT Central 应用程序中管理帐单

本文介绍你如何以管理员身份来管理 Azure IoT Central 账单。 你可以将应用程序从免费定价计划转移到标准定价计划，还可以升级或降级定价计划。

要访问“管理”部分，必须具有管理员角色或具有允许查看账单的自定义用户角色。  如果你创建了 Azure IoT Central 应用程序，则会自动分配到“管理员”角色。

## <a name="move-from-free-to-standard-pricing-plan"></a>从免费定价计划转到标准定价计划

- 使用免费定价计划的应用程序在到期前可以免费使用 7 天。 为了避免丢失数据，可以随时将数据转移到标准定价计划中，以免数据过期。
- 使用标准定价计划的应用程序按设备收费，每个应用程序的前两个设备是免费的。

可在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上了解定价详细信息。

在定价部分中，你可以将应用程序从免费计划转移到标准定价计划。

若要完成此自助服务过程，请遵循以下步骤：

1. 在“管理”部分转到“定价”页。 

    :::image type="content" source="media/howto-view-bill/freetrialbilling.png" alt-text="试用状态":::

1. 选择“转换为付费计划”。

    :::image type="content" source="media/howto-view-bill/convert.png" alt-text="转换试用版":::

1. 选择相应的 Azure Active Directory，然后选择要对使用付费计划的应用程序使用的 Azure 订阅。

1. 选择“转换”后，你的应用程序现在使用付费计划，并且开始计费。

> [!Note]
> 默认将转换为“标准 2”定价计划。

## <a name="how-to-change-your-application-pricing-plan"></a>如何更改应用程序定价计划

使用标准定价计划的应用程序按设备收费，每个应用程序的前两个设备是免费的。

在定价部分中，可以随时升级或降级 Azure IoT 定价计划。

1. 在“管理”部分转到“定价”页。 

    :::image type="content" source="media/howto-view-bill/pricing.png" alt-text="升级定价计划":::

1. 选择“计划”，然后选择“保存”以升级或降级。 

## <a name="view-your-bill"></a>查看帐单

1. 选择相应的 Azure Active Directory，然后选择要对使用付费计划的应用程序使用的 Azure 订阅。

1. 选择“转换”后，你的应用程序现在使用付费计划，并且开始计费。

> [!Note]
> 默认将转换为“标准 2”定价计划。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中管理帐单，建议下一步是了解如何在 Azure IoT Central 中[自定义应用程序 UI](howto-customize-ui.md)。