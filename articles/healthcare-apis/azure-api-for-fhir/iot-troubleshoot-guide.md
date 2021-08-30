---
title: 适用于 FHIR 的 IoT 连接器（预览版）
description: 如何对适用于 FHIR 的 Azure IoT 连接器（预览版）的常见错误消息和情况以及复制映射文件进行故障排除
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 0dbd592b3801d879e4950afc8082045693055248
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778231"
---
# <a name="iot-connector-for-fhir-preview-troubleshooting-guide"></a>适用于 FHIR 的 IoT 连接器（预览版）故障排除指南

本文介绍如何对适用于快速医疗保健互操作性资源 (FHIR&#174;)* 的 Azure IoT 连接器的常见错误消息和情况进行故障排除。  

你还将了解如何创建适用于 FHIR 的 Azure IoT 连接器转换映射 JSON（例如：设备和 FHIR）的副本。  

你可以使用转换映射 JSON 副本在 Azure 门户外进行编辑和存档。  

> [!TIP]
> 如果要为适用于 FHIR 的 Azure IoT 连接器开具 [Azure 技术支持](https://azure.microsoft.com/support/create-ticket/)票证，请确保提供转换映射 JSON 的副本，以帮助进行故障排除。

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>适用于 FHIR 的 Azure IoT 连接器（预览版）的设备和 FHIR 转换映射 JSON 模板验证
本部分介绍适用于 FHIR 的 Azure IoT 连接器执行的验证过程，其目的是在允许保存设备和 FHIR 转换映射 JSON 模板以供使用之前验证这些模板。  设备和 FHIR 转换映射 JSON 需要以下元素。

**设备映射**

|元素|必须|
|:-------|:------|
|TypeName|True|
|TypeMatchExpression|True|
|DeviceIdExpression|True|
|TimestampExpression|True|
|Values[].ValueName|正确|
|Values[].ValueExpression|正确|

> [!NOTE]
> Values[].ValueName and Values[].ValueExpression
>
> 仅当数组中有值条目时，才需要这些元素 - 没有映射任何值才有效。 这适用于发送的遥测数据是事件时的情形。 例如：当戴上或取下可穿戴 IoMT 设备时。 除了适用于 FHIR 的 Azure IoT 连接器匹配并发出的名称之外，元素没有任何值。 在 FHIR 转换中，适用于 FHIR 的 Azure IoT 连接器根据语义类型将其映射到可编码的概念 - 不填充任何实际值。

**FHIR 映射**

|元素|必须|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> 这是目前唯一一个已验证的必需 FHIR 映射元素。

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>适用于 FHIR 的 Azure IoT 连接器（预览版）的错误消息和修复

|消息|显示位置|条件|修复| 
|-------|---------|---------|---|
|映射名称无效，映射名称应为设备或 FHIR。|API|提供的映射类型不是设备或 FHIR。|使用两种受支持的映射类型之一（例如：设备或 FHIR）。|
|验证失败。 所需信息缺失或无效。|API 和 Azure 门户|尝试保存缺少所需信息或元素的转换映射。|添加缺少的转换映射信息或元素，并尝试再次保存转换映射。|
|重新生成未定义的密钥参数。|API|重新生成密钥请求。|在重新生成密钥请求中包含参数。|
|已达到可在此订阅中预配的 IoT 连接器实例的最大数量。|API 和 Azure 门户|已达到适用于 FHIR 的 Azure IoT 连接器的订阅配额（每个订阅的默认配额值为 (2)）。|删除适用于 FHIR 的 Azure IoT 连接器的现有实例之一。  使用其他尚未达到订阅配额的订阅。  请求提高订阅配额。|
|启用了 IoT 连接器的 Azure API for FHIR 资源不支持移动资源。|API 和 Azure 门户|尝试对 Azure API for FHIR 资源执行移动操作，该资源具有一个或多个适用于 FHIR 的 Azure IoT 连接器实例。|删除适用于 FHIR 的 Azure IoT 连接器的现有实例，以执行移动操作。|
|未预配 IoT 连接器。|API|在尚未预配父服务（适用于 FHIR 的 Azure IoT 连接器）时尝试使用子服务（连接和映射）。|预配适用于 FHIR 的 Azure IoT 连接器。|
|不支持该请求。|API|不支持特定的 API 请求。|请使用正确的 API 请求。|
|帐户不存在。|API|尝试添加适用于 FHIR 的 Azure IoT 连接器，但 Azure API for FHIR 资源不存在。|创建 Azure API for FHIR 资源，然后重新尝试该操作。|
|IoT 连接器不支持 Azure API for FHIR 资源 FHIR 版本。|API|尝试将适用于 FHIR 的 Azure IoT 连接器与不兼容的 Azure API for FHIR 资源版本结合使用。|创建新的 Azure API for FHIR 资源（版本 R4）或使用现有的 Azure API for FHIR 资源（版本 R4）。

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>为什么我的适用于 FHIR 的 Azure IoT 连接器（预览版）数据没有在 Azure API for FHIR 中显示？

|潜在问题|修复项|
|----------------|-----|
|数据仍在处理中。|数据成批流出到 Azure API for FHIR（大约每隔 15 分钟）。  数据可能仍在处理中，需要更多的时间才能将数据保存到 Azure API for FHIR 中。|
|尚未配置设备转换映射 JSON。|配置并保存一致的设备转换映射 JSON。|
|尚未配置 FHIR 转换映射 JSON。|配置并保存一致的 FHIR 转换映射 JSON。|
|设备消息不包含在设备映射中定义的预期表达式。|验证设备映射中定义的 JsonPath 表达式是否与设备消息中定义的令牌相匹配。|
|某个设备资源尚未在 Azure API for FHIR 中创建（解决方法类型：仅查找）*。|在 Azure API for FHIR 中创建有效的设备资源。 确保设备资源包含与传入消息中提供的设备标识符相匹配的标识符。|
|某个患者资源尚未在 Azure API for FHIR 中创建（解决方法类型：仅查找）*。|在 Azure API for FHIR 中创建有效的患者资源。|
|未设置 Device.patient 引用，或者引用无效（解决方法类型：仅查找）*。|确保设备资源包含对患者资源的有效[引用](https://www.hl7.org/fhir/device-definitions.html#Device.patient)。| 

*参考[快速入门：使用 Azure 门户部署 Azure IoT 连接器（预览版）](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview)，其中提供了适用于 FHIR 的 Azure IoT 连接器解决方法类型（例如：查找或创建）的功能说明。

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>使用指标排查适用于 FHIR 的 Azure IoT 连接器（预览版）中的问题

适用于 FHIR 的 Azure IoT 连接器可生成多个指标，以便深入了解数据流过程。 其中一个受支持的指标称为“总错误数”，它提供适用于 FHIR 的 Azure IoT 连接器实例中发生的所有错误的计数。

每个错误都是通过多个关联属性记录的。 每个属性都提供有关该错误的不同方面，这可以帮助你识别和排查问题。 本部分列出了为“总错误数”指标中的每个错误捕获的不同属性，以及这些属性的可能值。

> [!NOTE]
> 你可以导航到适用于 FHIR 的 Azure IoT 连接器（预览版）实例的“总错误数”指标，如[适用于 FHIR 的 Azure IoT 连接器（预览版）指标页面](iot-metrics-display.md)中所述。

单击“总错误数”图表，然后单击“添加筛选器”按钮，使用下面提到的任意属性对错误指标进行切片和切块。

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>适用于 FHIR 的 Azure IoT 连接器（预览版）执行的操作

此属性表示出现错误时 IoT 连接器正在执行的操作。 操作通常表示处理设备消息时的数据流阶段。 下面列出了此属性可能的值。

> [!NOTE]
> 若要详细了解适用于 FHIR 的 Azure IoT 连接器（预览版）中数据流的不同阶段，请参阅[此处](iot-data-flow.md)。

|数据流阶段|说明|
|---------------|-----------|
|安装|专用于设置 IoT 连接器实例的操作|
|标准化|规范化设备数据的数据流阶段|
|分组|对规范化数据分组的数据流阶段|
|FHIRConversion|将已分组的规范化数据转换为 FHIR 资源的数据流阶段|
|Unknown|出现错误时操作类型未知|

### <a name="the-severity-of-the-error"></a>错误的严重性

此属性表示出现的错误的严重性。 下面列出了此属性可能的值。

|严重性|说明|
|---------------|-----------|
|警告|数据流过程中存在一些小问题，但不会停止处理设备消息|
|错误|处理特定设备消息时遇到错误，且其他消息可能继续按预期执行|
|严重|IoT 连接器存在某些系统级问题，预计不会处理任何消息|

### <a name="the-type-of-the-error"></a>错误类型

此属性指示给定错误的类别，该类别表示类似类型的错误的大致逻辑分组。 下面列出了此属性可能的值。

|错误类型|说明|
|----------|-----------|
|DeviceTemplateError|与设备映射模板相关的错误|
|DeviceMessageError|处理特定设备消息时出现的错误|
|FHIRTemplateError|与 FHIR 映射模板相关的错误|
|FHIRConversionError|将消息转换为 FHIR 资源时出现的错误|
|FHIRResourceError|与 IoT 连接器引用的 FHIR 服务器中的现有资源相关的错误|
|FHIRServerError|与 FHIR 服务器通信时出现的错误|
|GeneralError|所有其他类型的错误|

### <a name="the-name-of-the-error"></a>错误的名称

此属性提供特定错误的名称。 下面列出了所有错误名称及其说明、关联的错误类型、严重性和数据流阶段。

|错误名称|说明|错误类型|错误严重性|数据流阶段|
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|在 FHIR 服务器中为设备消息中所示的相应标识符找到多个患者或设备资源时出现的错误|FHIRResourceError|错误|FHIRConversion|
|TemplateNotFoundException|IoT 连接器实例未配置设备或 FHIR 映射模板|DeviceTemplateError、FHIRTemplateError|严重|Normalization、FHIRConversion|
|CorrelationIdNotDefinedException|设备映射模板中未指定相关 ID。 CorrelationIdNotDefinedException 是一个条件错误，仅当 FHIR 观察必须使用相关 ID 对设备度量进行分组但未正确配置时才会出现|DeviceMessageError|错误|标准化|
|PatientDeviceMismatchException|当 FHIR 服务器上的设备资源对患者资源的引用与消息中显示的患者标识符不匹配时，会出现此错误|FHIRResourceError|错误|FHIRConversionError|
|PatientNotFoundException|与设备消息中显示的设备标识符关联的设备 FHIR 资源未引用患者 FHIR 资源。 请注意，仅当 IoT 连接器实例配置为“查找”解决方法类型时才会出现此错误|FHIRConversionError|错误|FHIRConversion|
|DeviceNotFoundException|FHIR 服务器上不存在与设备消息中显示的设备标识符关联的设备资源|DeviceMessageError|错误|标准化|
|PatientIdentityNotDefinedException|如果设备映射模板上未配置用于分析设备消息中患者标识符的表达式，或设备消息中未显示患者标识符，将出现此错误。 请注意，仅当 IoT 连接器的解决方法类型设置为“创建”时，才会出现此错误|DeviceTemplateError|严重|标准化|
|DeviceIdentityNotDefinedException|如果设备映射模板上未配置用于分析设备消息中设备标识符的表达式，或设备消息中未显示设备标识符，将出现此错误|DeviceTemplateError|严重|标准化|
|NotSupportedException|收到格式不受支持的设备消息时出现的错误|DeviceMessageError|错误|标准化|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>创建适用于 FHIR 的 Azure IoT 连接器（预览版）转换映射 JSON 的副本

如果要在 Azure 门户网站外进行编辑和存档，复制适用于 FHIR 的 Azure IoT 连接器映射文件非常有用。

开具支持票证以协助故障排除时，应将映射文件副本提供给 Azure 技术支持人员。

> [!NOTE]
> 对于设备和 FHIR 映射文件，目前仅支持 JSON 这种格式。

> [!TIP]
> 详细了解适用于 FHIR 的 Azure IoT 连接器的[设备和 FHIR 转换映射 JSON](iot-mapping-templates.md)

1. 在 Azure API for FHIR 资源仪表板左下方的“加载项”部分中选择“IoT 连接器(预览版)”。

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT 连接器 1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 选择要从中复制转换映射 JSON 的“连接器”的名称。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT 连接器 2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> 此过程还可用于复制和保存“配置 FHIR 映射”JSON 的内容。

3. 选择“配置设备映射”。

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT 连接器 3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. 选择 JSON 的内容，然后执行复制操作（例如：选择 Ctrl + C）。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT 连接器 4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 执行粘贴操作（例如：选择 Ctrl + V），粘贴到编辑器（例如：Visual Studio Code、记事本）中的新文件，并使用 *.json 扩展名保存文件。

> [!TIP]
> 如果要为适用于 FHIR 的 Azure IoT 连接器开具 [Azure 技术支持](https://azure.microsoft.com/support/create-ticket/)票证，请确保提供转换映射 JSON 的副本，以帮助进行故障排除。

## <a name="next-steps"></a>后续步骤

查看与适用于 FHIR 的 Azure IoT 连接器相关的常见问题解答。

>[!div class="nextstepaction"]
>[适用于 FHIR 的 Azure IoT 连接器常见问题解答](fhir-faq.yml)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。