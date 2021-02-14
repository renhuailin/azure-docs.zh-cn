---
title: Azure IoT Central 的解决方案构建 |Microsoft Docs
description: Azure IoT Central 是一种 IoT 应用程序平台，用于简化创建 IoT 解决方案。 本文概述如何使用 IoT Central 构建集成解决方案。
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417130"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central 解决方案生成器指南

本文适用于解决方案构建人员。

IoT Central 应用程序使你可以在设备整个生命周期内监视和管理数百万台设备。 本指南适用于使用 IoT Central 构建集成解决方案的解决方案构建者。 使用 IoT Central 应用程序，你可以管理设备、分析设备遥测并与其他后端服务集成。

解决方案生成器：

- 在 IoT Central web UI 中配置仪表板和视图。
- 使用内置规则和分析工具从连接的设备中派生业务见解。
- 使用数据导出和规则功能将 IoT Central 与其他后端服务集成。

## <a name="configure-dashboards-and-views"></a>配置仪表板和视图

一个 IoT Central 应用程序可以有一个或多个仪表板，操作员可以使用这些仪表板来查看应用程序并与之进行交互。 作为解决方案生成器，你可以自定义默认的仪表板并创建专用仪表板：

- 若要查看自定义仪表板的一些示例，请参阅 [行业专用模板](concepts-app-templates.md#industry-focused-templates)。
- 若要了解有关仪表板的详细信息，请参阅 [创建和管理多个仪表板](howto-create-personal-dashboards.md) 和 [配置应用程序仪表板](howto-add-tiles-to-your-dashboard.md)。

当设备连接到 IoT Central 时，设备会与设备类型的设备模板关联。 设备模板具有可自定义的视图，操作员可以使用这些视图来管理单个设备。 作为解决方案开发人员，您可以创建和自定义设备类型的可用视图。 若要了解详细信息，请参阅 [添加视图](howto-set-up-template.md#add-views)。

## <a name="use-built-in-rules-and-analytics"></a>使用内置规则和分析

解决方案开发人员可以向运行可自定义操作的 IoT Central 应用程序添加规则。 规则根据来自设备的数据评估条件，以确定何时运行操作。 若要了解有关规则的详细信息，请参阅：

- [教程：在 Azure IoT Central 应用程序中创建规则并设置通知](tutorial-create-telemetry-rules.md)
- [在 Azure IoT Central 中基于规则创建 Webhook 操作](howto-create-webhooks.md)
- [<c0 />将多个操作分组以从一个或多个规则运行<c1 />](howto-use-action-groups.md)

IoT Central 具有内置分析功能，操作员可以使用这些功能来分析从连接的设备流动的数据。 若要了解详细信息，请参阅 [如何使用分析来分析设备数据](howto-create-analytics.md)。

## <a name="integrate-with-other-services"></a>与其他服务集成

作为解决方案生成器，你可以使用 IoT Central 中的数据导出和规则功能与其他服务集成。 要了解详细信息，请参阅：

- [使用数据导出将 IoT 数据导出到云目标](howto-export-data.md)
- [使用工作流将 Azure IoT Central 应用程序与其他云服务相集成](howto-configure-rules-advanced.md)
- [使用流分析、Azure Functions 和 SendGrid 通过自定义规则扩展 Azure IoT Central](howto-create-custom-rules.md)
- [使用 Azure Databricks 通过自定义分析扩展 Azure IoT Central](howto-create-custom-analytics.md)
- [在 Power BI 仪表板中可视化和分析 Azure IoT Central 数据](howto-connect-powerbi.md)

## <a name="next-steps"></a>后续步骤

如果希望了解有关使用 IoT Central 的详细信息，建议的后续步骤是尝试快速入门，从[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)开始。
