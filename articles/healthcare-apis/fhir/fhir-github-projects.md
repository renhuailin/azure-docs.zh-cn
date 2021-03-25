---
title: 适用于 FHIR 的 Azure API 的相关 GitHub 项目
description: 列出适用于 FHIR 的 Azure API 的所有开源 (GitHub) 存储库。
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "105097626"
---
# <a name="related-github-projects"></a>相关 GitHub 项目

GitHub 上有许多开源项目，它们为你提供了针对各种用途部署服务的源代码和说明。 你始终欢迎访问我们的 GitHub 存储库，了解并试用我们的功能和产品。 

## <a name="fhir-server"></a>FHIR 服务器
* [microsoft/fhir](https://github.com/microsoft/fhir-server/)：开源 fhir 服务器，这是用于 Fhir 的 Azure API 的基础
* 若要查看最新版本，请参阅 [发行说明](https://github.com/microsoft/fhir-server/releases)
* [microsoft/fhir](https://github.com/microsoft/fhir-server-samples)：示例环境

## <a name="data-conversion--anonymization"></a>数据转换 & 匿名

#### <a name="fhir-converter"></a>FHIR 转换器
* [microsoft/FHIR](https://github.com/microsoft/FHIR-Converter)：用于将旧数据格式转换为 FHIR 的转换实用工具
* 与用于 FHIR 的 Azure API 以及适用于 Azure 的 FHIR 服务器（采用 $convert 数据操作的形式）集成
* OSS 中的持续改进，并持续集成到 FHIR 服务器
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR 转换器-VS Code 扩展
* [microsoft/FHIR-匿名](https://github.com/microsoft/FHIR-Tools-for-Anonymization)：用于帮助 (以 FHIR 格式) 匿名的一组工具
* 与用于 FHIR 的 Azure API 以及 "取消识别的导出" 形式的 Azure FHIR 服务器集成

#### <a name="fhir-tools-for-anonymization"></a>用于匿名的 FHIR 工具
* [microsoft/vscode-azurehealthcareapis](https://github.com/microsoft/vscode-azurehealthcareapis-tools)：一个 VS Code 扩展，其中包含用于 Azure 医疗保健 api 的工具集合
* 已释放到 Visual Studio Marketplace
* 用于创作要在 FHIR 转换器中使用的液体模板

## <a name="iot-connector"></a>IoT 连接器

#### <a name="integration-with-iot-hub-and-iot-central"></a>与 IoT 中心和 IoT Central 集成
* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir)：与 IoT 中心或 IoT Central 集成，与规范化数据的数据标准化和 fhir 转换
* 规范化：将设备数据信息提取为通用格式，以便进一步处理
* FHIR 转换：标准化和分组的数据映射到 FHIR。 根据配置的模板创建或更新观察，并链接到设备和患者。
* [有助于构建会话映射的工具](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)：可视化映射配置以规范化设备输入数据并将其转换为 FHIR 资源。 开发人员可以使用此工具来编辑和测试映射、设备映射和 FHIR 映射，并将其导出以上载到 Azure 门户中的 IoT 连接器。

#### <a name="healthkit-and-fhir-integration"></a>HealthKit 和 FHIR 集成
* [microsoft/healthkit-fhir](https://github.com/microsoft/healthkit-on-fhir)：一个 Swift 库，可自动将 Apple healthkit 数据导出到 fhir 服务器

 