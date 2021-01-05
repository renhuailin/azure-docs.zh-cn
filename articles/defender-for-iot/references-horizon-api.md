---
title: 范围 API
description: 本指南介绍常用的范围方法。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/19/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 3266517b9ddc8fc7ac7b06a137286c05ea9a28fa
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97838133"
---
# <a name="horizon-api"></a>范围 API 

本指南介绍常用的范围方法。

### <a name="getting-more-information"></a>获取详细信息

有关使用地平线和 CyberX 平台的详细信息，请参阅以下内容：

- 对于水平开放开发环境 (O) ) SDK，请联系 CyberX 代表。
- 有关支持和故障排除信息，请联系 <support@cyberx-labs.com> 。
- 若要从 CyberX 控制台访问 Cyberx 用户指南，请选择， :::image type="icon" source="media/references-horizon-api//image3.png"::: 然后选择 **下载用户指南**。

## `horizon::protocol::BaseParser`

所有插件的抽象。 这包括两种方法：

- 用于处理在你上面定义的插件筛选器。 这种方法范围知道如何与分析器通信
- 用于处理实际数据。

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

为您的插件调用的第一个函数将创建一个分析程序的实例，以使其识别并注册它。

### <a name="parameters"></a>参数 

None

### <a name="return-value"></a>返回值

shared_ptr 到分析器实例。

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

将为以上注册的每个插件调用此函数。 

在大多数情况下，此项将为空。 引发一个异常，使其知道发生了错误。

### <a name="parameters"></a>参数 

- 一个包含 dissect_as 结构的映射，该结构在要通过你进行注册的另一个插件的 config.js中定义。

### <a name="return-value"></a>返回值 

Uint64_t 的数组，该数组将注册到一类 uint64_t。 这意味着，在映射中，你将获得端口的列表，其值将是 uin64_t。

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Main 函数。 具体来说，就是插件的逻辑，每次新的数据包到达分析器。 此函数将被调用，应在此处执行与数据包处理相关的所有操作。

### <a name="considerations"></a>注意事项

你的插件应该是线程安全的，因为可以从不同的线程调用此函数。 最好的方法是定义堆栈上的所有内容。

### <a name="parameters"></a>参数

- 负责存储数据和创建 SDK 相关对象（如 ILayer、字段等）的 SDK 控制单元。
- 用于读取原始数据包的数据的帮助器。 它已设置为您在的 config.js中定义的字节顺序。

### <a name="return-value"></a>返回值 

处理的结果。 这可以是成功/格式不当/稳定。

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

将处理标记为下水道失败，这意味着当前协议无法识别数据包，并且如果在同一筛选器上注册，则地平线应将其传递到其他分析器。

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

构造函数

### <a name="parameters"></a>参数 

- 定义按下的 config.js中定义的日志记录所用的错误代码。

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

结果格式不正确，指出我们已经将数据包视为协议，但有些验证 (保留的位已打开，某些字段缺失等 ) 

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

构造函数

### <a name="parameters"></a>参数  

- 错误代码，在上 config.js中定义。

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

通知成功处理的范围。 如果成功，则接受该数据包;数据属于我们，并提取了所有数据。

## `horizon::protocol::SuccessResult()`

构造函数。 已创建基本的成功结果。 这意味着我们不知道此数据包的方向或任何其他元数据。

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

构造函数

### <a name="parameters"></a>参数 

- 如果识别，则为数据包的方向。 值可以为 REQUEST、RESPONSE

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

构造函数

### <a name="parameters"></a>参数

- 如果我们识别了数据包，则它可以是请求、响应
- 警告。 这些事件不会失败，但会通知地平线。

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

构造函数

### <a name="parameters"></a>参数 

-  警告。 这些事件不会失败，但会通知地平线。

## `HorizonID HORIZON_FIELD(const std::string_view &)`

将基于字符串的引用转换为字段名称 (例如 function_code) 到 HorizonID

### <a name="parameters"></a>参数 

- 要转换的字符串

### <a name="return-value"></a>返回值

- 从字符串创建的 HorizonID。

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

创建新层，因此水平范围将知道插件要存储一些数据。 这是你应使用的基本存储单元。

### <a name="return-value"></a>返回值

对已创建的层的引用，因此，您可以向其中添加数据。

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

获取字段管理对象，该对象负责在不同的对象上创建字段，例如在 ILayer 上

### <a name="return-value"></a>返回值

对管理器的引用

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

使用请求的 ID 在层上创建64位的新数字字段。

### <a name="parameters"></a>参数 

- 前面创建的层
- HORIZON_FIELD 宏创建的 HorizonID
- 要存储的原始值

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

在具有请求的 ID 的层上创建新的字符串字段。 内存将被移动，因此请小心。 你将无法再次使用此值。

### <a name="parameters"></a>参数  

- 前面创建的层
- HORIZON_FIELD 宏创建的 HorizonID
- 要存储的原始值

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

使用请求的 ID (层上的) 字段的字节数组创建新的原始值。 内存将移动，因此请注意，你将无法再次使用此值

### <a name="parameters"></a>参数

- 前面创建的层
- HORIZON_FIELD 宏创建的 HorizonID
- 要存储的原始值

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

使用请求的 ID 在指定类型层上 (数组) 字段创建数组值。

### <a name="parameters"></a>参数

- 前面创建的层
- HORIZON_FIELD 宏创建的 HorizonID
- 要存储在数组中的值的类型

### <a name="return-value"></a>返回值

对应将值追加到的数组的引用

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

向前面创建的数组追加新的整数值

### <a name="parameters"></a>参数

- 先前创建的数组
- 要存储在数组中的原始值

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

向前面创建的数组追加新的字符串值。 内存将移动，因此请注意，你将无法再次使用此值

### <a name="parameters"></a>参数

- 先前创建的数组
- 要存储在数组中的原始值

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

将新的原始值追加到前面创建的数组。 内存将移动，因此请注意，你将无法再次使用此值

### <a name="parameters"></a>参数

- 先前创建的数组
- 要存储在数组中的原始值

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

检查缓冲区是否包含至少 X 个字节。

### <a name="parameters"></a>参数

应存在的字节数 

### <a name="return-value"></a>返回值

如果缓冲区至少包含 X 个字节，则为 True。 否则为 False。

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

根据字节顺序从缓冲区读取 uint8 值 (1 个字节) 。

### <a name="return-value"></a>返回值

从缓冲区中读取的值。

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

根据字节顺序从缓冲区读取 uint16 值 (2 个字节) 。

### <a name="return-value"></a>返回值

从缓冲区中读取的值。

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

根据字节顺序从缓冲区读取 uint32 值 (4 个字节) 。

### <a name="return-value"></a>返回值

从缓冲区中读取的值。

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

根据字节顺序从缓冲区中读取 uint64 值 (8 字节) 。

### <a name="return-value"></a>返回值

从缓冲区中读取的值。

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

读取指定大小的预分配内存会将数据实际复制到内存区域。

### <a name="parameters"></a>参数 

- 要将数据复制到其中的内存区域
- 内存区域的大小，此参数还定义了将复制的字节数

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

从缓冲区读取到字符串

### <a name="parameters"></a>参数 

- 读取的字节数。

### <a name="return-value"></a>返回值

对字符串的内存区域的引用。

## `size_t horizon::general::IDataBuffer::getRemainingData()`

告诉您缓冲区中剩余的字节数。

### <a name="return-value"></a>返回值

缓冲区的剩余大小。

## `void horizon::general::IDataBuffer::skip(size_t)`

在缓冲区中跳过 X 字节。

### <a name="parameters"></a>参数

- 要跳过的字节数。
