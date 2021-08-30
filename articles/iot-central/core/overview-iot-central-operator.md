---
title: Azure IoT Central 操作员指南
description: Azure IoT Central 是一种 IoT 应用程序平台，用于简化创建 IoT 解决方案。 本文概述了 IoT Central 中的操作员角色。
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 86a5d327e00ee7c9595cc586aa7e63023a386bba
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586538"
---
# <a name="iot-central-operator-guide"></a>IoT Central 操作员指南

IoT Central 应用程序使你可以在设备整个生命周期内监视和管理数百万台设备。 本指南面向使用 IoT Central 应用程序管理 IoT 设备的操作员。

操作员：

- 监视和管理连接到应用程序的设备。
- 排查和修正设备问题。
- 预配新设备。

## <a name="monitor-and-manage-devices"></a>监视和管理设备

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="显示设备视图的屏幕截图":::

若要监视设备，操作员可以使用解决方案生成器定义的设备视图作为设备模板的一部分。 这些视图可以显示设备遥测数据和属性值。 示例为前面的屏幕截图中所显示的 **概述** 视图。

有关更多详细信息，操作员可以使用设备组和内置分析功能。 若要了解详细信息，请参阅“[如何使用分析功能来分析设备数据](howto-create-analytics.md)”。

若要管理单个设备，操作员可以使用设备视图来设置设备和云属性，并调用设备命令。 例如，在前面的屏幕截图中添加“**管理设备**”和“**命令**”视图。

若要批量管理设备，操作员可以创建和计划作业。 作业可以在多个设备上更新属性并运行命令。 若要了解详细信息，请参阅“[在 Azure IoT Central 应用程序中创建并运行作业](howto-manage-devices-in-bulk.md)”。

## <a name="troubleshoot-and-remediate-issues"></a>排查和修正问题

操作员应对应用程序及其设备的健康状况负责。 [故障排除指南](troubleshoot-connection.md)可帮助操作员诊断和修正常见问题。 在问题解决之前，操作员可以使用 **设备** 页来阻止出现故障的设备。

## <a name="add-and-remove-devices"></a>添加和删除设备

操作员可以单独或批量向 IoT Central 应用程序添加或删除设备。 要了解详细信息，请参阅“[在 Azure IoT Central 应用程序中管理设备](howto-manage-devices-individually.md)”。

## <a name="personalize"></a>个性化设置

操作员可以在 IoT Central 应用程序中创建个性化的仪表板，其中包含指向他们常用资源的链接。 若要了解详细信息，请参阅“[管理仪表板](howto-manage-dashboards.md)”。

## <a name="next-steps"></a>后续步骤

如果希望了解有关使用 IoT Central 的详细信息，建议的后续步骤是尝试快速入门，从[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)开始。
