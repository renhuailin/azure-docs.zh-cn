---
title: Azure IoT Central 的解决方案生成 | Microsoft Docs
description: Azure IoT Central 是一种 IoT 应用程序平台，用于简化创建 IoT 解决方案。 本文概述如何使用 IoT Central 生成集成解决方案。
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 5c8ef9fae7e0fa26de6a0d568e88a80a9a8cf989
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459137"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central 解决方案构建者指南

IoT Central 应用程序使你可以在设备整个生命周期内监视和管理数百万台设备。 本指南适用于使用 IoT Central 来构建集成解决方案的解决方案构建者。 使用 IoT Central 应用程序，你可以管理设备、分析设备遥测以及与其他后端服务集成。

解决方案构建者：

- 在 IoT Central Web UI 中配置仪表板和视图。
- 使用内置规则和分析工具从连接的设备中获取业务见解。
- 使用数据导出和规则功能将 IoT Central 与其他后端服务集成。

## <a name="configure-dashboards-and-views"></a>配置仪表板和视图

一个 IoT Central 应用程序可以有一个或多个仪表板，操作员可以使用这个仪表板或这些仪表板来查看应用程序并与之进行交互。 作为解决方案构建者，你可以自定义默认仪表板并创建专用仪表板：

- 若要查看自定义仪表板的一些示例，请参阅[行业专用模板](concepts-app-templates.md#industry-focused-templates)。
- 若要详细了解仪表板，请参阅[创建和管理多个仪表板](howto-manage-dashboards.md)和[配置应用程序仪表板](howto-manage-dashboards.md)。

当设备连接到 IoT Central 时，设备会与该设备类型的设备模板相关联。 设备模板具有可自定义的视图，操作员可以使用这些视图来管理各个设备。 作为解决方案开发人员，你可以针对设备类型创建和自定义可用视图。 若要了解详细信息，请参阅[添加视图](howto-set-up-template.md#views)。

## <a name="use-built-in-rules-and-analytics"></a>使用内置规则和分析

解决方案开发人员可以向运行可自定义操作的 IoT Central 应用程序添加规则。 规则会根据来自设备的数据评估条件，以确定何时运行操作。 若要详细了解规则，请参阅：

- [教程：在 Azure IoT Central 应用程序中创建规则并设置通知](tutorial-create-telemetry-rules.md)
- [配置规则](howto-configure-rules.md)

IoT Central 具有内置的分析功能，操作员可以使用这些功能来分析从连接的设备流出的数据。 若要了解详细信息，请参阅[如何使用分析功能来分析设备数据](howto-create-analytics.md)。

## <a name="integrate-with-other-services"></a>与其他服务集成

作为解决方案构建者，你可以使用 IoT Central 中的数据导出和规则功能与其他服务集成。 若要了解更多信息，请参阅以下文章：

- [使用数据导出功能将 IoT 数据导出到云目标](howto-export-data.md)
- [转换 IoT Central 的数据](howto-transform-data.md)
- [使用工作流将 Azure IoT Central 应用程序与其他云服务相集成](howto-configure-rules-advanced.md)
- [使用流分析、Azure Functions 和 SendGrid 通过自定义规则扩展 Azure IoT Central](howto-create-custom-rules.md)
- [使用 Azure Databricks 通过自定义分析扩展 Azure IoT Central](howto-create-custom-analytics.md)
- [在 Power BI 仪表板中可视化和分析 Azure IoT Central 数据](howto-connect-powerbi.md)

## <a name="apis"></a>API

IoT Central API 可让你与 IoT 解决方案中的其他服务建立深度集成。 可用 API 分类为数据平面 API 或控制平面 API。

可以使用数据平面 API 访问 IoT Central 应用程序中的实体和功能。 例如，管理设备、设备模板、用户和角色。 IoT Central REST API 操作是数据平面操作。 若要了解详细信息，请参阅[如何使用 IoT Central REST API 管理用户和角色](howto-manage-users-roles-with-rest-api.md)。

可以使用控制平面管理 Azure 订阅中与 IoT Central 相关的资源。 可以使用 Azure CLI 和资源管理器模板执行控制平面操作。 例如，可以使用 Azure CLI 创建 IoT Central 应用程序。 若要了解详细信息，请参阅[从 Azure CLI 管理 IoT Central](howto-manage-iot-central-from-cli.md)。

## <a name="next-steps"></a>后续步骤

如果希望了解有关使用 IoT Central 的详细信息，建议的后续步骤是尝试快速入门，从[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)开始。
