---
title: Azure IoT Connector for FHIR (预览) - 故障排除指南和操作方法
description: 如何排查常见 Azure IoT Connector for FHIR (预览) 错误消息和条件以及复制映射文件的问题
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 4e296f8d62afbebe49ebccc60654e26c46cbb2bf
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112991192"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>Azure IoT Connector for FHIR (预览版) 故障排除指南

本文提供排查常见 Azure IoT Connector for 快速医疗保健互操作性资源 (FHIR&#174;) 错误消息和条件的步骤。  

你还将了解如何创建 Azure IoT Connector for FHIR 转换映射 JSON 映射的副本 (例如：设备和 FHIR) 。  

可以使用转换映射 JSON 副本在数据库外部进行编辑和Azure 门户。  

> [!TIP]
> 如果要为 Azure IoT Connector for FHIR 打开 [Azure](https://azure.microsoft.com/support/create-ticket/) 技术支持票证，请确保包含转换映射 JSON 的副本，以帮助进行故障排除过程。

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>适用于 FHIR 的 Azure IoT Connector 的设备和 FHIR 转换映射 JSON 模板 (预览版) 
在本部分，你将了解 Azure IoT Connector for FHIR 执行验证过程，以验证设备和 FHIR 转换映射 JSON 模板，然后再保存它们供使用。  设备和 FHIR 转换映射 JSON 中需要这些元素。

**设备映射**

|元素|必须|
|:-------|:------|
|TypeName|正确|
|TypeMatchExpression|正确|
|DeviceIdExpression|正确|
|TimestampExpression|正确|
|Values[]。ValueName|正确|
|Values[]。ValueExpression|正确|

> [!NOTE]
> Values[]。ValueName 和 Values[]。ValueExpression
>
> 只有在数组中具有值条目时，才需要这些元素 - 没有映射任何值才有效。 当发送的遥测数据是事件时，会使用此功能。 例如：打开或删除可穿戴 IoMT 设备时。 除 (Connector for FHIR) 发出的名称之外，Azure IoT元素没有任何值。 在 FHIR 转换中，Azure IoT Connector for FHIR 映射到基于语义类型的代码支持概念 - 不填充任何实际值。

**FHIR 映射**

|元素|必须|
|:------|:-------|
|TypeName|正确|

> [!NOTE]
> 这是目前唯一需要验证的 FHIR Mapping 元素。

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>适用于 FHIR 的 Azure IoT 连接器预览版 (错误消息) 

|消息|显示|天气条件|Fix| 
|-------|---------|---------|---|
|映射名称无效，映射名称应为 device 或 FHIR。|API|提供的映射类型不是设备或 FHIR。|使用支持的两种映射类型之一 (例如：设备或 FHIR) 。|
|验证失败。 所需信息缺失或无效。|API 和Azure 门户|尝试保存缺少所需信息或元素的转换映射。|添加缺少的转换映射信息或元素，并尝试再次保存转换映射。|
|重新生成未定义的密钥参数。|API|重新生成密钥请求。|在重新生成密钥请求中包括参数。|
|已达到可在此IoT Connector预配的最大实例数。|API 和Azure 门户|Azure IoT FHIR 的连接器订阅配额达到 (默认值 (每个订阅) 2) 。|删除适用于 FHIR 的 Azure IoT实例之一。  使用尚未达到订阅配额的不同订阅。  请求增加订阅配额。|
|对于已启用移动资源IoT Connector移动Azure API for FHIR资源。|API 和Azure 门户|尝试对具有一个或多个 Azure API for FHIR Connector for FHIR 实例的 Azure IoT 资源执行移动操作。|删除适用于 FHIR () 连接器Azure IoT的现有实例，以执行移动操作。|
|IoT Connector未预配。|API|如果尚未预配父 (Connector for FHIR & (Azure IoT，) 尝试使用子服务) 连接和映射。|预配适用于 FHIR Azure IoT连接器。|
|不支持该请求。|API|不支持特定的 API 请求。|使用正确的 API 请求。|
|帐户不存在。|API|尝试添加 Azure IoT Connector for FHIR，Azure API for FHIR资源不存在。|创建Azure API for FHIR资源，然后重新尝试该操作。|
|Azure API for FHIR不支持资源 FHIR IoT Connector。|API|尝试将 Azure IoT Connector for FHIR 与不兼容版本的 Azure API for FHIR 资源。|在版本 R4 Azure API for FHIR创建新的 (资源) 或使用现有的 Azure API for FHIR 资源 (R4) 。

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>为什么我的 Azure IoT Connector for FHIR (预览) 数据未显示在Azure API for FHIR？

|潜在问题|修复项|
|----------------|-----|
|数据仍在处理中。|数据每隔大约 15 分钟Azure API for FHIR以批 (输出到) 。  可能仍在处理数据，并且需要额外时间才能将数据持久保存Azure API for FHIR。|
|尚未配置设备转换映射 JSON。|配置并保存一致的设备转换映射 JSON。|
|尚未配置 FHIR 转换映射 JSON。|配置并保存一致的 FHIR 转换映射 JSON。|
|设备消息不包含在设备映射中定义的预期表达式。|验证设备映射中定义的 JsonPath 表达式是否与设备消息中定义的令牌匹配。|
|"设备资源"尚未在"Azure API for FHIR (类型：仅查找) *" 。|在资源中心创建有效的设备Azure API for FHIR。 确保设备资源包含与传入消息中提供的设备标识符匹配的标识符。|
|"患者资源"尚未在"Azure API for FHIR (类型：仅查找) *" 。|在患者中心创建有效的患者Azure API for FHIR。|
|未设置 Device.patient 引用，或者引用在解析类型 (无效：仅查找) *。|确保设备资源包含对 [患者资源](https://www.hl7.org/fhir/device-definitions.html#Device.patient) 的有效引用。| 

*参考 [快速入门](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) ：使用 Azure 门户 部署 Azure IoT Connector (预览版) ，了解适用于 FHIR 的 Azure IoT 连接器解析类型的功能说明 (例如：查找或创建) 。

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>使用指标排查 Azure IoT Connector for FHIR 预览版 (中) 

Azure IoT Connector for FHIR 生成多个指标，以便深入了解数据流过程。 支持的指标之一称为"总错误数"，它为 FHIR 连接器实例Azure IoT错误计数。

每个错误都记录有一些关联的属性。 每个属性都提供有关错误的不同方面，这可以帮助你识别和排查问题。 本部分列出了为"总错误数"指标中每个错误捕获 *的不同属性，* 以及这些属性的可能值。

> [!NOTE]
> 可以导航到 Azure IoT  Connector for FHIR (预览版) 实例的"总错误数"指标，如[Azure IoT Connector for FHIR (](iot-metrics-display.md)预览版) 指标页中所述。

单击" *总错误数* "图， *然后单击"添加* 筛选器"按钮，使用下面提到的任何属性对错误指标进行切片和切分。

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR (预览版) 

此属性表示发生错误时由 IoT Connector执行的操作。 操作通常表示处理设备消息时数据流阶段。 下面是此属性的可能值列表。

> [!NOTE]
> 有关数据流的不同阶段的详细信息，请参阅此处的 Azure IoT Connector for FHIR (预览[版) 。](iot-data-flow.md)

|数据流阶段|说明|
|---------------|-----------|
|设置|特定于设置实例的操作IoT Connector|
|标准化|设备数据规范化的数据流阶段|
|分组|对规范化数据进行分组的数据流阶段|
|FHIRConversion|将分组规范化数据转换为 FHIR 资源的数据流阶段|
|未知|发生错误时操作类型未知|

### <a name="the-severity-of-the-error"></a>错误的严重性

此属性表示发生错误的严重性。 下面是此属性的可能值列表。

|严重性|说明|
|---------------|-----------|
|警告|数据流进程中存在一些细微问题，但设备消息的处理不会停止|
|错误|处理特定设备消息时遇到错误，其他消息可能会继续按预期执行|
|严重|某些系统级别问题与IoT Connector，预期不会处理任何消息|

### <a name="the-type-of-the-error"></a>错误的类型

此属性表示给定错误的类别，这基本上表示类似类型的错误的逻辑分组。 下面是此属性的可能值列表。

|错误类型|说明|
|----------|-----------|
|DeviceTemplateError|与设备映射模板相关的错误|
|DeviceMessageError|处理特定设备消息时出错|
|FHIRTemplateError|与 FHIR 映射模板相关的错误|
|FHIRConversionError|将消息转换为 FHIR 资源时出错|
|FHIRResourceError|与 FHIR 服务器中引用的 FHIR 服务器中的现有资源相关的错误IoT Connector|
|FHIRServerError|与 FHIR 服务器通信时发生的错误|
|GeneralError|所有其他类型的错误|

### <a name="the-name-of-the-error"></a>错误的名称

此属性提供特定错误的名称。 下面是所有错误名称的列表，以及其说明和关联的错误 () 、严重性和数据流阶段 () 。

|错误名称|说明|错误 () |错误严重性|数据流阶段 () |
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|在 FHIR 服务器中发现多个患者或设备资源时，设备消息中相应的标识符出错|FHIRResourceError|错误|FHIRConversion|
|TemplateNotFoundException|设备或 FHIR 映射模板未配置IoT Connector|DeviceTemplateError、FHIRTemplateError|严重|规范化、FHIRConversion|
|CorrelationIdNotDefinedException|设备映射模板中未指定相关 ID。 CorrelationIdNotDefinedException 是一个条件错误，仅在 FHIR 观察必须使用相关 ID 对设备度量进行分组，但配置不正确时发生|DeviceMessageError|错误|标准化|
|PatientDeviceMismatchException|当 FHIR 服务器上设备资源引用患者资源时，将发生此错误，该引用与消息中的患者标识符不匹配|FHIRResourceError|错误|FHIRConversionError|
|PatientNotFoundException|与设备消息中的设备标识符关联的设备 FHIR 资源不会引用任何患者 FHIR 资源。 请注意，只有在为实例配置IoT Connector查找解析类型时，才 *发生* 此错误|FHIRConversionError|错误|FHIRConversion|
|DeviceNotFoundException|FHIR 服务器上不存在与设备消息中的设备标识符关联的设备资源|DeviceMessageError|错误|标准化|
|PatientIdentityNotDefinedException|如果设备映射模板上未配置用于分析设备消息患者标识符的表达式，或者设备消息中不存在患者标识符，则会发生此错误。 请注意，只有在将 IoT Connector类型设置为 Create 时，才发生 *此错误*|DeviceTemplateError|严重|标准化|
|DeviceIdentityNotDefinedException|如果设备映射模板上未配置用于分析设备消息中的设备标识符的表达式，或者设备消息中不存在设备标识符，则会发生此错误|DeviceTemplateError|严重|标准化|
|NotSupportedException|收到格式不受支持的设备消息时出错|DeviceMessageError|错误|标准化|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>创建适用于 FHIR 的 Azure IoT 连接器 (预览) JSON

复制 Azure IoT Connector for FHIR 映射文件可用于在 Azure 门户 网站之外进行编辑和存档。

创建支持票证时，映射文件副本应提供给 Azure 技术支持部门，以帮助进行故障排除。

> [!NOTE]
> JSON 是设备和 FHIR 映射文件目前唯一支持的格式。

> [!TIP]
> 详细了解 FHIR Azure IoT连接器 [和 FHIR 转换映射 JSON](iot-mapping-templates.md)

1. 在 **"IoT Connector ("部分**) 资源仪表板的左下角选择"Azure API for FHIR预览 **版"。**

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT 连接器1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 选择要 **从中复制** 转换映射 JSON 的"连接器"。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT 连接器 2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> 此过程还可用于复制和保存"配置 **FHIR 映射** "JSON 的内容。

3. 选择 **"配置设备映射"。**

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT 连接器3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. 选择 JSON 的内容，然后执行复制 (例如：按 Ctrl + c) 。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT 连接器4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 执行粘贴操作 (例如：选择 Ctrl + v) 进入编辑器中的新文件 (例如：Visual Studio Code、记事本) ，然后使用 *.json 扩展名保存文件。

> [!TIP]
> 如果要为 Azure IoT Connector for FHIR 打开 [Azure](https://azure.microsoft.com/support/create-ticket/) 技术支持票证，请确保包含转换映射 JSON 的副本，以帮助进行故障排除过程。

## <a name="next-steps"></a>后续步骤

查看有关适用于 FHIR Azure IoT连接器的常见问题解答。

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 常见问题解答](fhir-faq.yml)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。