---
title: 适用于 FHIR 的 Azure IoT 连接器（预览版） - 故障排除指南和操作说明
description: 本文介绍如何对常见错误消息和情况进行故障排除，以及如何复制映射文件。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 07/20/2021
ms.author: jasteppe
ms.openlocfilehash: 0edc58bd10cfdab0b7b6cff29c3513ab3c73025b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779697"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>适用于 FHIR 的 Azure IoT 连接器（预览版）故障排除指南

本文介绍如何对适用于快速医疗保健互操作性资源 (FHIR&#174;) 的 Azure IoT 连接器的常见错误消息和情况进行故障排除。 你还将了解如何创建适用于 FHIR 的 Azure IoT 连接器（预览版）转换映射 JSON 模板（例如设备和 FHIR）的副本。 此外，还可以使用转换映射 JSON 模板在 Azure 门户外进行编辑和存档。  

> [!NOTE]
> 在本文剩余部分中，将适用于 FHIR 的 Azure IoT 连接器（预览版）称为 IoT 连接器。

> [!TIP]
> 如果必须要为 IoT 连接器开立 [Azure 技术支持](https://azure.microsoft.com/support/create-ticket/)工单，请确保包含转换映射 JSON 的副本，用于协助故障排除过程。

## <a name="device-and-fhir-conversion-mapping-json-template-validations"></a>设备和 FHIR 转换映射 JSON 模板验证

本部分介绍 IoT 连接器执行的验证过程。 将由验证过程先验证设备和 FHIR 转换映射 JSON 模板，然后模板才能保存以供使用。 设备和 FHIR 转换映射 JSON 中需要以下元素。

**设备映射**

|元素|必须|
|:-------|:------|
|TypeName|True|
|TypeMatchExpression|正确|
|DeviceIdExpression|True|
|TimestampExpression|正确|
|Values[].ValueName|正确|
|Values[].ValueExpression|正确|

> [!NOTE]
> 仅当数组中有值项时，才需要 `Values[].ValueName and Values[].ValueExpression` 元素。 不映射任何值也是有效的。 这适用于发送的遥测数据是事件时的情形。 
>
>例如：
> 
>佩戴或摘下 IoMT 设备时，除了 IoT 连接器匹配并发出的一个名称外，该元素没有任何值。 在 FHIR 转换中，IoT 连接器基于语义类型将其映射为可编码的概念。 这意味着没有填充实际值。

**FHIR 映射**

|元素|必须|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> 这是目前唯一需要验证的 FHIR 映射元素。

## <a name="error-messages-and-fixes"></a>错误消息和修复

### <a name="iot-connector-resource"></a>IoT 连接器资源

|消息|显示|条件|修复| 
|-------|---------|---------|---|
|已达到资源类型 `iotconnectors` 的最大数目。|API 和 Azure 门户|已达到 Azure IoT 连接器订阅配额，每个订阅的默认配额值为 25。|删除其中一个 IoT 连接器的现有实例。 使用其他尚未达到订阅配额的订阅。 请求提高订阅配额。
|`deviceMapping` 映射无效。 验证错误: {错误列表}|API 和 Azure 门户|IoT 连接器资源预配请求中提供的 `properties.deviceMapping` 无效。|更正在 `properties.deviceMapping` 属性中提供的映射 JSON 的错误。
|`fullyQualifiedEventHubNamespace` 为 null、空或格式不正确。|API 和 Azure 门户|IoT 连接器预配请求 `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` 无效。|将 IoT 连接器 `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` 更新为正确的格式。 应为 `{YOUR_NAMESPACE}.servicebus.windows.net`。
|必须先完全预配上级资源，然后才能预配子资源。|API|父工作区仍在预配。|等待父工作区预配完成后，重新提交预配请求。
|子资源的 `Location` 属性必须与父资源的 `Location` 属性相匹配。|API|IoT 连接器预配请求 `location` 属性与父工作区 `location` 属性不同。|将预配请求中 IoT 连接器的 `location` 属性设置为与父工作区 `location` 属性相同的值。

### <a name="destination-resource"></a>目标资源

|消息|显示|条件|修复| 
|-------|---------|---------|---|
|已达到资源类型 `iotconnectors/destinations` 的最大数目。|API 和 Azure 门户|已达到 IoT 连接器目标资源配额（每个 IoT 连接器的默认值为 1）。|删除 IoT 连接器目标的现有实例。 每个 IoT 连接器只能有 1 个目标资源。
|提供的 `fhirServiceResourceId` 无效。|API 和 Azure 门户|目标资源预配请求中提供的 `properties.fhirServiceResourceId` 不是 Azure API for FHIR 实例适用的有效资源 ID。|确保资源 ID 的格式正确，并确保资源 ID 适用于 Azure API for FHIR 实例。 格式应为：`/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/`
|必须先完全预配上级资源，然后才能预配子资源。|API|父工作区或父 IoT 连接器仍在预配。|待父工作区或父 IoT 连接器预配完成后，重新提交预配请求。
|子资源的 `Location` 属性必须与父资源的 `Location` 属性相匹配。|API|目标预配请求 `location` 属性与父 IoT 连接器 `location` 属性不同。|将预配请求中目标的 `location` 属性设置为与父 IoT 连接器 `location` 属性相同的值。

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-fhir-service"></a>为什么我的适用于 FHIR 的 Azure IoT 连接器（预览版）数据没有在 FHIR 服务中显示？

|潜在问题|修复项|
|----------------|-----|
|数据仍在处理。|数据成批传出到 FHIR 服务（大约每隔 15 分钟）。  数据可能仍在处理，需要更多的时间才能将数据保存到 FHIR 服务中。|
|尚未配置设备转换映射 JSON。|配置并保存一致的设备转换映射 JSON。|
|尚未配置 FHIR 转换映射 JSON。|配置并保存一致的 FHIR 转换映射 JSON。|
|设备消息不包含在设备映射中定义的预期表达式。|验证设备映射中定义的 `JsonPath` 表达式是否与设备消息中定义的令牌相匹配。|
|某个设备资源尚未在 FHIR 服务中创建（解决方法类型：仅查找）*。|在 FHIR 服务中创建有效的设备资源。 确保设备资源包含与传入消息中提供的设备标识符相匹配的标识符。|
|某个患者资源尚未在 FHIR 服务中创建（解决方法类型：仅查找）*。|在 FHIR 服务中创建有效的患者资源。|
|未设置 `Device.patient` 引用，或者引用无效（解决方法类型：仅查找）*。|确保设备资源包含对患者资源的有效[引用](https://www.hl7.org/fhir/device-definitions.html#Device.patient)。| 

*参考[快速入门：使用 Azure 门户部署 Azure IoT 连接器（预览版）](deploy-iot-connector-in-azure.md)，其中提供了适用于 FHIR 的 Azure IoT 连接器解决方法类型（例如：查找或创建）的功能说明。

### <a name="the-operation-performed-by-the-iot-connector"></a>由 IoT 连接器执行的操作

此属性表示出现错误时由 IoT 连接器执行的操作。 操作通常表示处理设备消息时的数据流阶段。 下面列出了此属性可能的值。

> [!NOTE]
> 有关 IoT 连接器中数据流不同阶段的信息，请参阅[适用于 FHIR 的 IoT 连接器：数据流](iot-data-flow.md)。

|数据流阶段|说明|
|---------------|-----------|
|安装|设置数据流阶段是特定于设置 IoT 连接器实例的操作。|
|标准化|规范化是设备数据规范化的数据流阶段。|
|分组|规范化数据分组的分组数据流阶段。|
|FHIRConversion|FHIRConversion 是将分组规范化的数据转换为 FHIR 资源的数据流阶段。|
|Unknown|未知是出现错误时未知的操作类型。|

### <a name="the-severity-of-the-error"></a>错误的严重性

此属性表示出现的错误的严重级别。 下面列出了此属性可能的值。

|严重性|说明|
|---------------|-----------|
|警告|数据流进程中存在一些小问题，但不会停止处理设备消息。|
|错误|当处理特定设备消息遇到错误且其他消息可能继续按预期执行时，会出现此消息。|
|严重|IoT 连接器存在某些系统级问题，并且没有任何消息需要处理时，会出现此错误。|

### <a name="the-type-of-the-error"></a>错误类型

此属性指示给定错误的类别，该类别表示类似类型的错误的大致逻辑归组。 下面列出了此属性可能的值。

|错误类型|说明|
|----------|-----------|
|`DeviceTemplateError`|此错误类型与设备映射模板相关。|
|`DeviceMessageError`|处理特定设备消息时，会出现此错误类型。|
|`FHIRTemplateError`|此错误类型与 FHIR 映射模板相关。|
|`FHIRConversionError`|将消息转换为 FHIR 资源时，会出现此错误类型。|
|`FHIRResourceError`|此错误类型与 IoT 连接器引用的 FHIR 服务器中的现有资源相关。|
|`FHIRServerError`|与 FHIR 服务器通信时，会出现此错误类型。|
|`GeneralError`|此错误类型与所有其他错误类型相关。|

### <a name="the-name-of-the-error"></a>错误的名称

此属性提供特定错误的名称。 下面列出了所有错误的名称，其说明、关联的错误类型、严重性和数据流阶段。

|错误名称|说明|错误类型|错误严重性|数据流阶段|
|----------|-----------|-------------|--------------|------------------|
|`MultipleResourceFoundException`|当 FHIR 服务器中显示了设备消息中所示相应标识符所对应的多个患者或设备资源时，会出现此错误。|`FHIRResourceError`|错误|`FHIRConversion`|
|`TemplateNotFoundException`|未使用 IoT 连接器实例配置的设备或 FHIR 映射模板。|`DeviceTemplateError`, `FHIRTemplateError`|`Critical|Normalization`, `FHIRConversion`|
|`CorrelationIdNotDefinedException`|设备映射模板中未指定相关 ID。 `CorrelationIdNotDefinedException` 是一个条件错误，仅当 FHIR 观察必须使用相关 ID 对设备度量进行分组时才会出现，因为没有正确配置相关 ID。|`DeviceMessageError`|错误|标准化|
|`PatientDeviceMismatchException`|FHIR 服务器上的设备资源引用患者资源时，会出现此错误。 此错误类型意味着它与消息中显示的患者标识符不匹配。|`FHIRResourceError`|错误|`FHIRConversionError`|
|`PatientNotFoundException`|与设备消息中显示的设备标识符关联的设备 FHIR 资源未引用患者 FHIR 资源。 请注意，仅当 IoT 连接器实例配置为“查找”解决方法类型时才会出现此错误。|`FHIRConversionError`|错误|`FHIRConversion`|
|`DeviceNotFoundException`|FHIR 服务器上不存在与设备消息中显示的设备标识符关联的设备资源。|`DeviceMessageError`|错误|标准化|
|`PatientIdentityNotDefinedException`|如果设备映射模板上未配置用于分析设备消息中患者标识符的表达式，或设备消息中未显示患者标识符，将出现此错误。 请注意，仅当 IoT 连接器的解决方法类型设置为“创建”时，才会出现此错误。|`DeviceTemplateError`|严重|标准化|
|`DeviceIdentityNotDefinedException`|如果设备映射模板上未配置用于分析设备消息中设备标识符的表达式，或设备消息中未显示设备标识符，将出现此错误。|`DeviceTemplateError`|严重|标准化|
|`NotSupportedException`|收到格式不受支持的设备消息时出错。|`DeviceMessageError`|错误|标准化|

## <a name="creating-copies-of-the-iot-connector-conversion-mapping-json"></a>创建 IoT 连接器转换映射 JSON 的副本

如果要在 Azure 门户网站外进行编辑和存档，复制 IoT 连接器映射文件会有用。

开立支持工单时，应将映射文件副本提供给 Azure 技术支持人员，以获得有关故障排除过程的帮助。

> [!NOTE]
> 对于设备和 FHIR 映射文件，目前仅支持 JSON 这种格式。

> [!TIP]
> 详细了解适用于 FHIR 的 Azure IoT 连接器[设备和 FHIR 转换映射 JSON](how-to-use-fhir-mapping-iot.md)

1. 选择 Azure Healthcare APIs 工作区左侧的“IoT 连接器(预览版)”边栏选项卡。

   :::image type="content" source="media/iot-troubleshoot/iot-connector-blade.png" alt-text="选择“IoT 连接器”边栏选项卡。" lightbox="media/iot-troubleshoot/iot-connector-blade.png":::

2. 选择要从中复制转换映射 JSON 的 IoT 连接器的名称。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> 此过程还可用于复制和保存“配置 FHIR 映射”的内容。

3. 选择 JSON 的内容，然后执行复制操作（例如：按 Ctrl+C）。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

4. 在 Microsoft Visual Studio Code 或记事本等编辑器中执行粘贴操作（例如：按 Ctrl+V），将内容粘贴到新文件中。 确保将其保存为扩展名为 .json 的文件。

> [!TIP]
> 如果要为适用于 FHIR 的 Azure IoT 连接器开立 [Azure 技术支持](https://azure.microsoft.com/support/create-ticket/)工单，请确保包含转换映射 JSON 的副本，以获得有关故障排除过程的帮助。

## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[Azure IoT 连接器概述](iot-connector-overview.md)

FHIR 是 [HL7](https://hl7.org/fhir/) 的注册商标，经 HL7 许可使用。
