---
title: 适用于 FHIR 的 Azure IoT 连接器 (预览版) -疑难解答指南和操作方法
description: 如何排查常见的 Azure IoT Connector for FHIR (预览) 错误消息和条件以及复制映射文件
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: jasteppe
ms.openlocfilehash: 3eef7354f7197f60e8abd1b5522393bf00a6203f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017943"
---
# <a name="azure-iot-connector-for-fhir-preview-troubleshooting-guide"></a>适用于 FHIR 的 Azure IoT 连接器 (预览版) 故障排除指南

本文提供了针对快速医疗保健互操作性资源（ (FHIR&#174;) * 错误消息和条件）进行故障排除的常见 Azure IoT 连接器的步骤。  

你还将了解如何创建 Azure IoT 连接器的副本用于 FHIR 转换映射 JSON (例如： Device and FHIR) 。  

可以使用转换映射 JSON 副本在 Azure 门户之外进行编辑和存档。  

> [!TIP]
> 如果要为 FHIR 的 Azure IoT 连接器打开 [Azure 技术支持](https://azure.microsoft.com/support/create-ticket/) 票证，请确保包含转换映射 JSON 的副本，以帮助进行故障排除过程。

## <a name="device-and-fhir-conversion-mapping-json-template-validations-for-azure-iot-connector-for-fhir-preview"></a>设备和 FHIR 转换映射适用于 Azure IoT Connector for FHIR 的 JSON 模板验证 (预览) 
在本部分中，你将了解 Azure IoT Connector for FHIR 在允许将其保存以供使用之前对其执行验证的验证过程。  这些元素在设备和 FHIR 转换映射 JSON 中是必需的。

**设备映射**

|元素|必须|
|:-------|:------|
|TypeName|正确|
|TypeMatchExpression|正确|
|DeviceIdExpression|正确|
|TimestampExpression|正确|
|值 []。ValueName|正确|
|值 []。ValueExpression|正确|

> [!NOTE]
> 值 []。ValueName 和值 []。ValueExpression
>
> 仅当数组中有值项时，才需要这些元素-不映射任何值是有效的。 当发送的遥测是事件时，使用此。 例如：在放置或删除可穿戴 IoMT 设备时。 元素 () 不包含除 Azure IoT Connector for FHIR 匹配和发出的名称以外的任何值。 FHIR 转换时，适用于 FHIR 的 Azure IoT 连接器会将其映射到基于语义类型的代码概念，而不会填充实际值。

**FHIR 映射**

|元素|必须|
|:------|:-------|
|TypeName|正确|

> [!NOTE]
> 这是目前唯一验证的必需 FHIR 映射元素。

## <a name="error-messages-and-fixes-for-azure-iot-connector-for-fhir-preview"></a>适用于 FHIR 的 Azure IoT Connector 的错误消息和修补程序 (预览) 

|Message|会|条件|Fix| 
|-------|---------|---------|---|
|映射名称无效，映射名称应为 device 或 FHIR。|API|提供的映射类型不是设备或 FHIR。|使用两种受支持的映射类型之一 (例如： Device 或 FHIR) 。|
|验证失败。 缺少必需的信息或该信息无效。|API 和 Azure 门户|尝试保存的转换映射缺少所需的信息或元素。|添加缺少的转换映射信息或元素，然后尝试再次保存转换映射。|
|未定义重新生成密钥参数。|API|重新生成密钥请求。|在重新生成密钥请求中包含参数。|
|已达到可在此订阅中预配的 IoT 连接器实例的最大数量。|API 和 Azure 门户|已达到 FHIR 订阅配额的 Azure IoT 连接器 (默认值为每个订阅)  (2 个) 。|删除 FHIR 的 Azure IoT 连接器的现有实例之一。  请使用未达到订阅配额的其他订阅。  请求提高订阅配额。|
|启用 IoT 连接器的 Azure API for FHIR 资源不支持移动资源。|API 和 Azure 门户|正在尝试对 Azure API for FHIR 资源执行移动操作，该资源具有一个或多个用于 FHIR 的 Azure IoT 连接器实例。|删除 () Azure IoT Connector for FHIR 的现有实例，以执行移动操作。|
|未预配 IoT 连接器。|API|尝试使用子服务 (连接 & 映射) 父 (未预配 FHIR 的 Azure IoT 连接器) 。|为 FHIR 预配 Azure IoT 连接器。|
|不支持该请求。|API|不支持特定的 API 请求。|使用正确的 API 请求。|
|帐户不存在。|API|尝试添加适用于 FHIR 的 Azure IoT 连接器和用于 FHIR 的 Azure API 资源不存在。|创建用于 FHIR 资源的 Azure API，然后重试该操作。|
|IoT 连接器不支持 Azure API for FHIR 资源 FHIR 版本。|API|尝试将适用于 FHIR 的 Azure IoT 连接器用于 FHIR 资源的不兼容版本的 Azure API。|创建新的 Azure API for FHIR 资源 (版本 R4) 或使用现有的 Azure API for FHIR 资源 (version R4) 。

## <a name="why-is-my-azure-iot-connector-for-fhir-preview-data-not-showing-up-in-azure-api-for-fhir"></a>为什么 FHIR 的 Azure IoT 连接器 (预览) 在 Azure API for FHIR 中未显示数据？

|潜在问题|修复项|
|----------------|-----|
|仍在处理数据。|每隔大约15分钟) ，出口的数据会成批地用于 FHIR 的 Azure API (。  可能仍在处理数据，并且需要额外的时间将数据保存在 Azure API for FHIR 中。|
|尚未配置设备转换映射 JSON。|配置并保存符合的设备转换映射 JSON。|
|尚未配置 FHIR 转换映射 JSON。|配置并保存符合 FHIR 的转换映射 JSON。|
|设备消息不包含设备映射中定义的所需表达式。|验证设备映射中定义的 JsonPath 表达式是否匹配设备消息中定义的令牌。|
|尚未在用于 FHIR 的 Azure API 中创建设备资源 (解决方法类型：仅查找) *。|在用于 FHIR 的 Azure API 中创建有效的设备资源。 请确保设备资源包含与传入消息中提供的设备标识符匹配的标识符。|
|尚未在用于 FHIR 的 Azure API 中创建患者资源 (解析类型：仅查找) *。|在用于 FHIR 的 Azure API 中创建有效的患者资源。|
|未设置设备的患者引用，或者引用无效 (解析类型：仅查找) *。|请确保设备资源包含对患者资源的有效 [引用](https://www.hl7.org/fhir/device-definitions.html#Device.patient) 。| 

* 参考快速入门： Azure 门户使用适用于 FHIR 解析类型的 Azure IoT 连接器的功能说明 [部署 Azure Iot 连接器 (预览版) ](iot-fhir-portal-quickstart.md#create-new-azure-iot-connector-for-fhir-preview) (例如：查找或创建) 。

## <a name="use-metrics-to-troubleshoot-issues-in-azure-iot-connector-for-fhir-preview"></a>使用指标解决 Azure IoT Connector for FHIR (预览版中的问题) 

适用于 FHIR 的 Azure IoT 连接器可生成多个指标，以提供对数据流过程的见解。 其中一个受支持的指标称为 " *总数错误*"，它提供了用于 FHIR 的 Azure IoT 连接器实例中发生的所有错误的计数。

每个错误都是通过多个关联属性记录的。 每个属性都提供有关错误的不同方面，这有助于识别和解决问题。 此部分列出了在 " *错误总数* " 指标中为每个错误捕获的不同属性，以及这些属性的可能值。

> [!NOTE]
> 你可以导航到用于 FHIR (预览的 Azure IoT Connector 实例的 " *总错误* " 指标) ，如 [azure IOT connector for FHIR (Preview) 度量值 "页](iot-metrics-display.md)中所述。

单击 " *错误总数* " 图形，然后单击 " *添加筛选器* " 按钮，使用下面所述的任何属性对错误指标进行切片和切值。

### <a name="the-operation-performed-by-the-azure-iot-connector-for-fhir-preview"></a>用于 FHIR 的 Azure IoT 连接器 (预览版执行的操作) 

此属性表示在发生错误时 IoT 连接器正在执行的操作。 操作通常表示处理设备消息时的数据流阶段。 下面是此属性的可能值的列表。

> [!NOTE]
> 若要详细了解 Azure IoT Connector 中的数据流的不同阶段，请参阅 [此处](iot-data-flow.md)的 FHIR (preview) 。

|数据流阶段|说明|
|---------------|-----------|
|设置|特定于设置 IoT 连接器实例的操作|
|标准化|设备数据被规范化的数据流阶段|
|分组|对标准化数据进行分组的数据流阶段|
|FHIRConversion|将分组标准化数据转换为 FHIR 资源的数据流阶段|
|未知|出现错误时操作类型未知|

### <a name="the-severity-of-the-error"></a>错误的严重性

此属性表示发生的错误的严重级别。 下面是此属性的可能值的列表。

|严重性|说明|
|---------------|-----------|
|警告|数据流进程中存在一些小问题，但无法停止对设备消息的处理|
|错误|特定设备消息的处理已遇到错误，其他消息可能会继续按预期方式执行|
|严重|IoT 连接器中存在一些系统级别问题，因此没有任何消息需要处理|

### <a name="the-type-of-the-error"></a>错误的类型

此属性表示给定错误的类别，该类别本质上表示类似错误类型的逻辑分组。 下面是此属性的可能值的列表。

|错误类型|说明|
|----------|-----------|
|DeviceTemplateError|与设备映射模板相关的错误|
|DeviceMessageError|处理特定设备消息时出错|
|FHIRTemplateError|与 FHIR 映射模板相关的错误|
|FHIRConversionError|将消息转换为 FHIR 资源时出现错误|
|FHIRResourceError|与 IoT 连接器引用的 FHIR 服务器中的现有资源相关的错误|
|FHIRServerError|与 FHIR 服务器通信时发生的错误|
|GeneralError|所有其他类型的错误|

### <a name="the-name-of-the-error"></a>错误的名称

此属性提供特定错误的名称。 下面是所有错误名称的列表及其说明和关联错误类型 (s) 、严重性和数据流阶段 () 。

|错误名称|说明|错误类型 (s) |错误严重性|数据流阶段 (s) |
|----------|-----------|-------------|--------------|------------------|
|MultipleResourceFoundException|在 FHIR 服务器中找到多个患者或设备资源，以便在设备消息中提供相应标识符时出现错误|FHIRResourceError|错误|FHIRConversion|
|TemplateNotFoundException|设备或 FHIR 映射模板未配置 IoT 连接器的实例|DeviceTemplateError, FHIRTemplateError|严重|规范化，FHIRConversion|
|CorrelationIdNotDefinedException|设备映射模板中未指定相关 ID。 CorrelationIdNotDefinedException 是一个条件错误，仅当 FHIR 观测值必须使用关联 ID 对设备度量进行分组，但其配置不正确时才会出现此错误。|DeviceMessageError|错误|标准化|
|PatientDeviceMismatchException|当 FHIR 服务器上的设备资源引用了与消息中存在的患者标识符不匹配的患者资源时，将发生此错误|FHIRResourceError|错误|FHIRConversionError|
|PatientNotFoundException|与设备消息中存在的设备标识符关联的设备 FHIR 资源未引用患者 FHIR 资源。 请注意，仅当 IoT Connector 实例配置了 *查找* 解析类型时才会出现此错误。|FHIRConversionError|错误|FHIRConversion|
|DeviceNotFoundException|与设备消息中存在的设备标识符相关联的 FHIR 服务器上不存在任何设备资源|DeviceMessageError|错误|标准化|
|PatientIdentityNotDefinedException|如果未在设备映射模板上配置设备消息中的分析患者标识符，或者设备消息中不存在患者标识符，则会出现此错误。 请注意，仅当 IoT 连接器的解决方案类型设置为 "*创建*" 时才会出现此错误。|DeviceTemplateError|严重|标准化|
|DeviceIdentityNotDefinedException|如果未在设备映射模板上配置设备消息的表达式，或者设备标识符中不存在设备标识符，则会出现此错误。|DeviceTemplateError|严重|标准化|
|NotSupportedException|收到不支持格式的设备消息时出错|DeviceMessageError|错误|标准化|

## <a name="creating-copies-of-the-azure-iot-connector-for-fhir-preview-conversion-mapping-json"></a>为 FHIR (预览创建 Azure IoT 连接器的副本) 转换映射 JSON

为 FHIR 映射文件复制 Azure IoT 连接器对于在 Azure 门户网站外进行编辑和存档可能非常有用。

在建立支持票证时，应将映射文件副本提供给 Azure 技术支持部门，以帮助进行故障排除。

> [!NOTE]
> JSON 是目前唯一受支持的设备和 FHIR 映射文件格式。

> [!TIP]
> 详细了解适用于 FHIR[设备和 FHIR 转换映射 JSON](iot-mapping-templates.md)的 Azure IoT 连接器

1. 在 **"外接程序"** 部分的 "FHIR 资源" 仪表板的左下角，选择 **"IoT 连接器 (预览") "** 。

   :::image type="content" source="media/iot-troubleshoot/map-files-main-with-box.png" alt-text="IoT Connector1" lightbox="media/iot-troubleshoot/map-files-main-with-box.png":::

2. 选择要从中复制转换映射 JSON 的 **"连接器"** 。

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT Connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

> [!NOTE]
> 此过程也可用于复制和保存 **"配置 FHIR 映射"** JSON 的内容。

3. 选择 **"配置设备映射"**。

    :::image type="content" source="media/iot-troubleshoot/map-files-select-device-with-box.png" alt-text="IoT Connector3" lightbox="media/iot-troubleshoot/map-files-select-device-with-box.png":::

4. 选择 JSON 的内容并执行复制操作 (例如：选择 Ctrl + c) 。 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT Connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

5. 执行粘贴操作 (例如：在编辑器中选择 "Ctrl + v) 到新文件中 (例如： Visual Studio Code、记事本) 并使用 *. json 扩展名保存文件。

> [!TIP]
> 如果要为 FHIR 的 Azure IoT 连接器打开 [Azure 技术支持](https://azure.microsoft.com/support/create-ticket/) 票证，请确保包含转换映射 JSON 的副本，以帮助进行故障排除过程。

## <a name="next-steps"></a>后续步骤

查看有关适用于 FHIR 的 Azure IoT 连接器的常见问题。

>[!div class="nextstepaction"]
>[适用于 FHIR 常见问题的 Azure IoT 连接器](fhir-faq.md)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。 FHIR 是 HL7 的注册商标，经 HL7 许可使用。