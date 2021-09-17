---
title: 教程 - Azure IoT 持续患者监视 | Microsoft Docs
description: 本教程介绍如何部署和使用 IoT Central 的持续患者监视应用程序模板。
author: philmea
ms.author: philmea
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: dc1cf6a9a250b64b84cacbcf300183b913144b45
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122180106"
---
# <a name="tutorial-deploy-and-walkthrough-the-continuous-patient-monitoring-app-template"></a>教程：部署和演练持续患者监视应用模板

:::image type="content" source="media/cpm-architecture.png" alt-text="持续患者监视体系结构":::

## <a name="bluetooth-low-energy-ble-medical-devices"></a>蓝牙低功耗 (BLE) 医疗设备

医疗保健 IoT 解决方案中使用的许多医疗可穿戴设备都是 BLE 设备。 这些设备无法直接与云通信，需要使用网关与云解决方案交换数据。 此体系结构使用手机应用程序作为网关。

## <a name="mobile-phone-gateway"></a>手机网关

该手机应用程序的主要功能是从医疗设备收集 BLE 数据，并将其传递给 IoT Central。 该应用还通过设备设置指导患者，让他们查看自己的个人健康数据。 其他解决方案可以使用病房中的平板电脑网关或静态网关。 开源示例移动应用程序可用于 Android 和 iOS，以用作应用程序开发的起点。 若要了解详细信息，请参阅 [IoT Central 持续患者监视移动应用](/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/)。

## <a name="export-to-azure-api-for-fhirreg"></a>导出到 Azure API for FHIR&reg;

Azure IoT Central 符合 HIPAA 并经过 HITRUST&reg; 认证。 还可以使用 [Azure API for FHIR](../../healthcare-apis/fhir/overview.md) 向其他服务发送患者健康状况数据。 Azure API for FHIR 是基于标准的临床健康状况数据 API。 通过[适用于 FHIR 的 Azure IoT 连接器](../../healthcare-apis/fhir/iot-fhir-portal-quickstart.md)，可以将 Azure API for FHIR 用作 IoT Central 的连续数据导出目标。

## <a name="machine-learning"></a>机器学习

将机器学习模型与 FHIR 数据一起使用，以生成见解并支持护理团队的决策制定。 若要了解详细信息，请参阅 [Azure 机器学习文档](../../machine-learning/index.yml)。

## <a name="provider-dashboard"></a>提供程序仪表板

使用 Azure API for FHIR 数据构建患者见解仪表板或将其直接集成到护理团队使用的电子病历中。 护理团队可以使用仪表板帮助患者并识别恶化的早期警告迹象。 若要了解详细信息，请参阅[构建 Power BI 提供程序仪表板](tutorial-health-data-triage.md)教程。

在本教程中，你将了解如何执行以下操作：

- 创建应用程序模板
- 演练应用程序模板

## <a name="prerequisites"></a>先决条件

- 无需满足特定的先决条件，就可以部署此应用。
- 你可以使用免费定价计划或使用 Azure 订阅。

## <a name="create-continuous-patient-monitoring-application"></a>创建持续患者监视应用程序

1. 导航到 [Azure IoT Central 生成](https://aka.ms/iotcentral)站点。 然后使用 Microsoft 个人、工作或学校帐户登录。 从左侧导航栏中选择“生成”，然后选择“医疗保健”选项卡：:::image type="content" source="media/app-manager-health.png" alt-text="应用程序模板"::: 

1. 选择“持续患者监视”下的“创建应用” 。

若要了解详细信息，请参阅[创建 IoT Central 应用程序](../core/howto-create-iot-central-application.md)。

## <a name="walk-through-the-application"></a>演练应用程序

以下各部分将介绍应用程序的主要功能：

### <a name="dashboards"></a>仪表板

部署应用模板之后，将首先登陆“Lamna 住院患者监视仪表板”  。 Lamna 医疗保健是一个虚构的医院系统，其中包含两个医院：Woodgrove 医院和 Burkville 医院。 在 Woodgrove 医院操作员仪表板上，你可以：

* 查看设备遥测数据和属性，例如设备的电池电量或其连接状态   。

* 查看 Smart Vitals Patch 设备的平面布置图和位置  。

* 为新患者重新预配 Smart Vitals Patch  。

* 请参阅医院护理团队为跟踪其患者而可能查看的提供程序仪表板的示例  。

* 更改设备的患者状态以指示该设备是用于住院患者方案还是远程方案  。

:::image type="content" source="media/lamna-in-patient.png" alt-text="住院患者状态":::

还可以选择“转到远程患者仪表板”，查看 Burkville 医院操作员仪表板。 此仪表板包含一组类似的操作、遥测和信息。 还可以查看正在使用的多个设备，并选择“更新每个设备上的固件”。

:::image type="content" source="media/lamna-remote.png" alt-text="远程操作员仪表板":::

### <a name="device-templates"></a>设备模板

如果选择“设备模板”，则会在模板中看到两个设备类型：

- **Smart Vitals Patch**：此设备表示测量各种生命体征的补丁。 它用于监视医院内外的患者。 如果选择模板，你会看到此补丁同时发送设备数据（如电池电量和设备温度）和患者健康状况数据（如呼吸频率和血压）。

- **Smart Knee Brace**：此设备表示患者在从膝关节置换手术中恢复过来时使用的膝盖支架。 如果选择此模板，则会看到设备数据、运动范围和加速等功能。

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="智能补丁模板":::

### <a name="device-groups"></a>设备组

使用设备组对一组设备进行逻辑分组，然后对其执行批量查询或操作。

如果选择“设备组”选项卡，则会在应用程序中看到每个设备模板的默认设备组。 还创建了另外两个示例设备组，名称分别是“预配设备”和“包含过期固件的设备” 。 可以将这些示例设备组用作输入来运行应用程序中的某些[作业](#jobs)。

### <a name="rules"></a>规则

如果选择“规则”，则会看到模板中的三个规则：

- **温度过高**：当 Smart Knee Brace 的设备温度超过 95&deg;F 且持续时间超过 5 分钟窗口期时，将触发此规则。 使用此规则来提醒患者和护理团队，并远程冷却设备。

- **检测到跌倒**：如果检测到患者跌倒，则触发此规则。 使用此规则来配置操作，以部署操作团队来帮助跌倒的患者。

- **Patch 电量不足**：当设备上的电池电量低于 10% 时将触发此规则。 使用此规则来触发通知，通知患者为其设备充电。

:::image type="content" source="media/brace-temp-rule.png" alt-text="规则":::

### <a name="jobs"></a>作业

通过作业，你可以将[设备组](#device-groups)用作输入来对一组设备运行批量操作。 应用程序模板包含操作员可运行的两个示例作业：

* 更新膝盖支架固件：此作业在设备组“包含过期固件的设备”中查找设备，并运行命令以将这些设备更新到最新固件版本。 此示例作业假设设备能够处理“update”命令，然后从云中提取固件文件。  

* 重新预配设备：你有一组最近返回到医院的设备。 此作业在设备组“预配设备”中查找设备，并运行命令以为下一组患者重新预配它们。

### <a name="devices"></a>设备

选择“设备”选项卡，然后选择“智能膝盖支架”的实例 。 有三个视图可用于浏览所选特定设备的相关信息。 构建设备的设备模板时，会创建并发布这些视图。 因此，这些视图在你连接或模拟的所有设备中保持一致。

“仪表板”视图提供了设备的面向操作员的遥测和属性的概述。

通过“属性”选项卡，可以编辑云属性以及读取/写入设备属性。

通过“命令”选项卡，可以在设备上运行命令。

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="膝盖支架仪表板":::


## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请依次访问“管理”>“应用程序设置”，然后单击“删除”来删除该应用程序 。

:::image type="content" source="media/admin-delete.png" alt-text="整理资源":::

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何创建连接到 IoT Central 应用程序的提供程序仪表板。

> [!div class="nextstepaction"]
> [生成提供程序仪表板](tutorial-health-data-triage.md)