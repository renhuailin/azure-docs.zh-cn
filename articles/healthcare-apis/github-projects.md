---
title: 与 Azure Healthcare APIs 相关的 GitHub 项目
description: 列出所有开源 (GitHub) 存储库
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: be855812af2e7721355c5cb7cfce59156ee5c346
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778101"
---
# <a name="github-projects"></a>GitHub 项目

我们已在 GitHub 上发布诸多开源项目，可提供源代码和说明，以便针对各种用途部署服务。 欢迎随时访问我们的 GitHub 存储库，了解并试验我们的功能和产品。 

## <a name="fhir-server"></a>FHIR 服务器
* [microsoft/fhir-server](https://github.com/microsoft/fhir-server/)：开源 FHIR 服务器，这是 FHIR 服务的基础
* 若要查看最新版本，请参阅[发行说明](https://github.com/microsoft/fhir-server/releases)
* [microsoft/fhir-server-samples](https://github.com/microsoft/fhir-server-samples)：示例环境

## <a name="data-conversion--anonymization"></a>数据转换与匿名化

#### <a name="fhir-converter"></a>FHIR 转换器
* [microsoft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter)：将旧数据格式转换为 FHIR 的转换实用工具
* 已通过 $convert-data 操作格式与 FHIR 服务及适用于 Azure 的 FHIR 服务器集成
* 在 OSS 方面持续取得改进，并实现与 FHIR 服务器的连续集成
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR 转换器 - VS Code 扩展
* [microsoft/FHIR-Tools-for-Anonymization](https://github.com/microsoft/FHIR-Tools-for-Anonymization)：一组有助实现数据（采用 FHIR 格式）匿名的工具
* 已通过“de-identified export”格式与 FHIR 服务及适用于 Azure 的 FHIR 服务器集成

#### <a name="fhir-tools-for-anonymization"></a>FHIR 匿名化工具
* [microsoft/vscode-azurehealthcareapis-tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools)：包含可使用 Azure Healthcare APIs 工具集合的 VS Code 扩展
* 已发布到 Visual Studio Marketplace
* 用于创作将在 FHIR 转换器中使用的 Liquid 模板

## <a name="iot-connector"></a>IoT 连接器

#### <a name="integration-with-iot-hub-and-iot-central"></a>与 IoT 中心和 IoT Central 集成
* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir)：借助数据规范化与规范化数据的 FHIR 转换，实现将 IoT 中心或 IoT Central 集成到 FHIR
* 规范化：已将设备数据信息提取为通用格式，以便作进一步处理
* FHIR 转换：规范化和分组数据已映射到 FHIR。 根据已配置模板创建或更新观察结果，并已链接到设备和患者。
* [有助于生成会话映射的工具](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)：可视化映射配置以规范设备输入数据，并将其转换为 FHIR 资源。 开发人员可以使用此工具来编辑和测试映射、设备映射和 FHIR 映射，并可将这些映射导出，上载到 Azure 门户中的 IoT 连接器。

#### <a name="healthkit-and-fhir-integration"></a>HealthKit 和 FHIR 集成
* [microsoft/healthkit-fhir](https://github.com/microsoft/healthkit-on-fhir)：可自动将 Apple HealthKit 数据导出到 FHIR 服务器的 Swift 库

 