---
title: IoT 连接器故障排除指南和操作方法 - Azure 医疗保健 API
description: 本文可帮助用户排查 IoT 连接器常见错误消息、条件以及如何复制映射文件的问题。
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jasteppe
ms.openlocfilehash: 07f1ed78abe90b2967335322f0eea17e721a44bc
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005402"
---
# <a name="iot-connector-troubleshooting-guide"></a>IoT 连接器故障排除指南

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文提供排查常见 IoT 连接器错误消息和条件的步骤。 还将了解如何创建 IoT 连接器的设备和 FHIR 副本快速医疗保健互操作性资源 (FHIR&#174;) 映射。 此外，可以使用"设备"和"FHIR"目标映射副本在应用程序外部进行编辑和Azure 门户。  

> [!TIP]
> 为 IoT [连接器打开 Azure](https://azure.microsoft.com/support/create-ticket/) 技术支持票证时，请包含设备和 FHIR 目标映射的副本，以帮助进行故障排除。

## <a name="device-and-fhir-destination-mapping-validations"></a>设备和 FHIR 目标映射验证

本部分介绍 IoT 连接器的验证过程。 验证过程先验证设备和 FHIR 目标映射，然后允许保存它们供使用。 设备和 FHIR 目标映射中需要这些元素。

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
> 仅当数组中有值项时，才需要 `Values[].ValueName and Values[].ValueExpression` 元素。 不映射任何值也是有效的。 这适用于发送的遥测数据是事件时的情形。 
>
>例如：
> 
>打开或删除可穿戴 IoMT 设备时， (元素) IoT 连接器匹配和发出的名称以外的任何值。 在 FHIR 转换中，IoT 连接器根据语义类型将它映射到代码能力的概念。 这意味着没有填充实际值。

**FHIR 目标映射**

|元素|必须|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> 这是目前验证的唯一必需的 FHIR 目标映射元素。

## <a name="error-messages-and-fixes"></a>错误消息和修复

### <a name="iot-connector-resource"></a>IoT 连接器资源

|消息|显示|条件|修复| 
|-------|---------|---------|---|
|已达到资源类型 `iotconnectors` 的最大数目。|API 和 Azure 门户|达到 IoT 连接器订阅配额， (订阅订阅配额默认为 25) 。|删除 IoT 连接器的现有实例之一。 使用其他尚未达到订阅配额的订阅。 请求提高订阅配额。
|`deviceMapping` 映射无效。 验证错误: {错误列表}|API 和 Azure 门户|IoT `properties.deviceMapping` 连接器资源预配请求中提供的 无效。|更正在 `properties.deviceMapping` 属性中提供的映射 JSON 的错误。
|`fullyQualifiedEventHubNamespace` 为 null、空或格式不正确。|API 和 Azure 门户|IoT 连接器预配请求 `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` 无效。|将 IoT 连接器 `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` 更新为正确的格式。 应为 `{YOUR_NAMESPACE}.servicebus.windows.net`。
|必须先完全预配上级资源，然后才能预配子资源。|API|父工作区仍在预配。|等待父工作区预配完成后，重新提交预配请求。
|子资源的 `Location` 属性必须与父资源的 `Location` 属性相匹配。|API|IoT 连接器预配请求 `location` 属性不同于父工作区 `location` 属性。|将预配请求中 IoT 连接器的 属性设置为与父 `location` 工作区属性相同的 `location` 值。

### <a name="destination-resource"></a>目标资源

|消息|显示|条件|修复| 
|-------|---------|---------|---|
|已达到资源类型 `iotconnectors/destinations` 的最大数目。|API 和 Azure 门户|已达到 IoT 连接器目标资源配额，默认值为每个 IoT 连接器 1) 。|删除 IoT 连接器目标资源的现有实例。 每个 IoT 连接器只允许有一个目标资源。
|提供的 `fhirServiceResourceId` 无效。|API 和 Azure 门户|目标资源预配请求中提供的 不是 Azure 医疗保健 API FHIR 服务实例的有效资源 `properties.fhirServiceResourceId` ID。|确保资源 ID 的格式正确，并确保资源 ID 适用于 Azure 医疗保健 API FHIR 实例。 格式应为：`/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/`
|必须先完全预配上级资源，然后才能预配子资源。|API|父工作区或父 IoT 连接器仍在预配中。|等待父工作区或父 IoT 连接器预配完成，然后再次提交预配请求。
|子资源的 `Location` 属性必须与父资源的 `Location` 属性相匹配。|API|"目标预配请求" `location` 属性不同于父 IoT 连接器 `location` 属性。|将预配请求中 Destination 的 属性设置为与父 IoT 连接器属性 `location` 相同的 `location` 值。

## <a name="why-is-iot-connector-data-not-showing-up-in-the-fhir-service"></a>为什么 FHIR 服务中未显示 IoT 连接器数据？

|潜在问题|修复项|
|----------------|-----|
|数据仍在处理。|数据成批传出到 FHIR 服务（大约每隔 15 分钟）。  数据可能仍在处理，需要更多的时间才能将数据保存到 FHIR 服务中。|
|尚未配置设备映射。|配置并保存一致的设备映射。|
|尚未配置 FHIR 目标映射。|配置并保存符合 FHIR 目标映射。|
|设备消息不包含在"设备映射"中定义的预期表达式。|验证 `JsonPath` 设备映射中定义的表达式是否与设备消息中定义的令牌匹配。|
|某个设备资源尚未在 FHIR 服务中创建（解决方法类型：仅查找）*。|在 FHIR 服务中创建有效的设备资源。 确保设备资源包含与传入消息中提供的设备标识符相匹配的标识符。|
|某个患者资源尚未在 FHIR 服务中创建（解决方法类型：仅查找）*。|在 FHIR 服务中创建有效的患者资源。|
|未设置 `Device.patient` 引用，或者引用无效（解决方法类型：仅查找）*。|确保设备资源包含对患者资源的有效[引用](https://www.hl7.org/fhir/device-definitions.html#Device.patient)。| 

*参考 [快速入门：](deploy-iot-connector-in-azure.md) 使用 Azure 门户 部署 IoT 连接器，了解 IoT 连接器解析类型的功能说明 (例如：查找或创建) 。

### <a name="the-operation-performed-by-iot-connector"></a>IoT 连接器执行的操作

此属性表示发生错误时 IoT 连接器执行的操作。 操作通常表示处理设备消息时的数据流阶段。 下面列出了此属性可能的值。

> [!NOTE]
> 有关 IoT 连接器中数据流的不同阶段的信息，请参阅 [IoT 连接器数据流](iot-data-flow.md)。

|数据流阶段|说明|
|---------------|-----------|
|安装|设置数据流阶段是特定于设置 IoT 连接器实例的操作。|
|标准化|规范化是设备数据规范化的数据流阶段。|
|分组|规范化数据分组的分组数据流阶段。|
|FHIRConversion|FHIRConversion 是数据流阶段，其中分组规范化的数据转换为 FHIR 资源。|
|Unknown|未知是出现错误时未知的操作类型。|

### <a name="the-severity-of-the-error"></a>错误的严重性

此属性表示出现的错误的严重级别。 下面列出了此属性可能的值。

|严重性|说明|
|---------------|-----------|
|警告|数据流进程中存在一些小问题，但不会停止处理设备消息。|
|错误|当处理特定设备消息遇到错误且其他消息可能继续按预期执行时，会出现此消息。|
|严重|当 IoT 连接器存在某些系统级别问题，并且预期不会处理任何消息时，将发生此错误。|

### <a name="the-type-of-error"></a>错误类型

此属性指示给定错误的类别，该类别表示类似类型的错误的大致逻辑归组。 下面列出了此属性可能的值。

|错误类型|说明|
|----------|-----------|
|`DeviceTemplateError`|此错误类型与设备映射相关。|
|`DeviceMessageError`|处理特定设备消息时，会出现此错误类型。|
|`FHIRTemplateError`|此错误类型与 FHIR 目标映射相关|
|`FHIRConversionError`|将消息转换为 FHIR 资源时，会出现此错误类型。|
|`FHIRResourceError`|此错误类型与 IoT 连接器引用的 FHIR 服务中的现有资源相关。|
|`FHIRServerError`|与 FHIR 服务通信时，会发生此错误类型。|
|`GeneralError`|此错误类型与所有其他错误类型相关。|

### <a name="the-name-of-the-error"></a>错误的名称

此属性提供特定错误的名称。 下面列出了所有错误的名称，其说明、关联的错误类型、严重性和数据流阶段。

|错误名称|说明|错误类型|错误严重性|数据流阶段|
|----------|-----------|-------------|--------------|------------------|
|`MultipleResourceFoundException`|当在 FHIR 服务中为设备消息中显示各自的标识符找到多个患者或设备资源时，会发生此错误。|`FHIRResourceError`|Error|`FHIRConversion`|
|`TemplateNotFoundException`|未使用 IoT 连接器实例配置的设备或 FHIR 目标映射。|`DeviceTemplateError`, `FHIRTemplateError`|`Critical|Normalization`, `FHIRConversion`|
|`CorrelationIdNotDefinedException`|设备映射中未指定相关 ID。 `CorrelationIdNotDefinedException` 是一个条件错误，仅当 FHIR 观察必须使用相关 ID 对设备度量进行分组时才会出现，因为没有正确配置相关 ID。|`DeviceMessageError`|错误|标准化|
|`PatientDeviceMismatchException`|当 FHIR 服务上的设备资源引用患者资源时，会发生此错误。 此错误类型意味着它与消息中显示的患者标识符不匹配。|`FHIRResourceError`|错误|`FHIRConversionError`|
|`PatientNotFoundException`|与设备消息中显示的设备标识符关联的设备 FHIR 资源未引用患者 FHIR 资源。 请注意，只有在使用查找解析类型配置 IoT 连接器实例时，才 *发生* 此错误。|`FHIRConversionError`|Error|`FHIRConversion`|
|`DeviceNotFoundException`|与设备消息中的设备标识符关联的 FHIR 服务上不存在设备资源。|`DeviceMessageError`|错误|标准化|
|`PatientIdentityNotDefinedException`|如果设备映射上未配置用于分析设备消息患者标识符的表达式，或者设备消息中不存在患者标识符，则会发生此错误。 请注意，只有当 IoT 连接器的解析类型设置为"创建"时，才 *发生此错误*。|`DeviceTemplateError`|严重|标准化|
|`DeviceIdentityNotDefinedException`|如果设备映射上未配置用于分析设备消息中的设备标识符的表达式，或者设备消息中不存在设备标识符，则会发生此错误。|`DeviceTemplateError`|严重|标准化|
|`NotSupportedException`|收到格式不受支持的设备消息时出错。|`DeviceMessageError`|错误|标准化|

## <a name="creating-copies-of-iot-connector-device-and-fhir-destination-mappings"></a>创建 IoT 连接器设备和 FHIR 目标映射的副本

复制 IoT 连接器映射可用于编辑和存档 Azure 门户 网站。

创建支持票证时，映射副本应提供给 Azure 技术支持部门，以帮助进行故障排除过程。

> [!NOTE]
> 目前，JSON 是设备和 FHIR 目标映射唯一支持的格式。

> [!TIP]
> 详细了解 IoT 连接器 [设备和 FHIR 目标映射](how-to-use-fhir-mapping-iot.md)

1. 选择 **"医疗保健 API 工作区"** 左侧的"IoT 连接器"。

   :::image type="content" source="media/iot-troubleshoot/iot-connector-blade.png" alt-text="选择&quot;IoT 连接器&quot;。" lightbox="media/iot-troubleshoot/iot-connector-blade.png":::

2. 选择要从其中复制设备和 FHIR 目标映射的 **IoT** 连接器的名称。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT 连接器2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

   > [!NOTE]
   > 此过程还可用于复制和保存"目标 **"FHIR** 目标映射的内容。

3. 选择 JSON 的内容，然后执行复制操作（例如：按 Ctrl+C）。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT 连接器4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

4. 在 Microsoft Visual Studio Code 或记事本等编辑器中执行粘贴操作（例如：按 Ctrl+V），将内容粘贴到新文件中。 确保保存扩展名 **为 .json 的文件** 。

> [!TIP]
> 如果要为 IoT 连接器打开 [Azure](https://azure.microsoft.com/support/create-ticket/) 技术支持票证，请确保包含设备和 FHIR 目标映射的副本，以帮助完成故障排除过程。

## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[IoT 连接器概述](iot-connector-overview.md)

 (FHIR&#174;) 是 [HL7](https://hl7.org/fhir/) 的商标，并经 HL7 授权使用。