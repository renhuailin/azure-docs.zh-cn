---
title: IoT 连接器相关的 GitHub 项目-Azure 医疗保健 api
description: 列出用于 IoT 连接器的所有开源 (GitHub) 存储库
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/18/2021
ms.author: jasteppe
ms.openlocfilehash: 0092482af2f9f4f6f9a090a00cc387ae901e5b68
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2021
ms.locfileid: "130187405"
---
# <a name="open-source-projects"></a>开放源代码项目

请查看 GitHub 上的开放源代码项目，该项目提供了针对各种使用 IoT 连接器的服务部署服务的源代码和说明。 

## <a name="iot-connector-github-projects"></a>IoT 连接器 GitHub 项目

#### <a name="integration-with-iot-hub-and-iot-central"></a>与 IoT 中心和 IoT Central 集成

* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir)：与 IoT 中心或 IoT Central 集成到快速医疗保健互操作性资源 (fhir&#174;) 与标准化数据的数据标准化和 fhir 转换
* 规范化：已将设备数据信息提取为通用格式，以便作进一步处理
* FHIR 转换：规范化和分组数据已映射到 FHIR。 根据已配置模板创建或更新观察结果，并已链接到设备和患者。

#### <a name="device-and-fhir-destination-mappings-authoring-and-troubleshooting"></a>设备和 FHIR 目标映射的创作和疑难解答

* [有助于构建设备和 FHIR 目标映射的工具](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)：可视化用于规范化设备输入数据并将其转换为 FHIR 资源的映射配置。 开发人员可以使用此工具来编辑和测试设备映射以及 FHIR 目标映射。 导出它们以上载到 Azure 门户中的 IoT 连接器。

#### <a name="healthkit-and-fhir-integration"></a>HealthKit 和 FHIR 集成

* [microsoft/healthkit-fhir](https://github.com/microsoft/healthkit-on-fhir)：一个 Swift 库，可自动将 Apple healthkit 数据导出到 fhir 服务。

## <a name="next-steps"></a>后续步骤
了解如何在 Azure 门户中部署 IoT 连接器

>[!div class="nextstepaction"]
>[部署 IoT 连接器托管服务](deploy-iot-connector-in-azure.md)

 (FHIR&#174;) 是 [HL7](https://hl7.org/fhir/) 的注册商标，并与 HL7 的权限一起使用。