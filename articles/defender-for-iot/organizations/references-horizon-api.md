---
title: Horizon API
description: 本指南介绍常用的 Horizon 方法。
ms.date: 1/5/2021
ms.topic: article
ms.openlocfilehash: b65f7663df29e2c82faa5d1aeec3b820d5fbaf70
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113014612"
---
# <a name="horizon-api"></a>Horizon API 

本指南介绍常用的 Horizon 方法。

### <a name="getting-more-information"></a>获取更多信息

有关使用 Horizon 和 Defender for IoT 平台的详细信息，请参阅以下信息：

- 对于 Horizon 开放式开发环境 (ODE) SDK，请联系 Defender for IoT 代表。
- 有关支持和故障排除信息，请联系 <support@cyberx-labs.com>。

- 若要从 Defender for IoT 控制台访问 Defender for IoT 用户指南，请选择 :::image type="icon" source="media/references-horizon-api/profile.png":::，然后选择“下载用户指南”。


## `horizon::protocol::BaseParser`

所有插件的摘要。 其中包含两个方法：

- 用于处理上面定义的插件筛选器。 这样 Horizon 就知道如何与分析程序通信。
- 用于处理实际数据。

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

为插件调用的第一个函数将为分析程序创建一个实例，使 Horizon 识别并注册它。

### <a name="parameters"></a>参数 

无。

### <a name="return-value"></a>返回值

shared_ptr 到分析程序实例。

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

将为上面注册的每个插件调用此函数。 

大多数情况下都会为空。 引发一个异常，使 Horizon 知道发生了错误。

### <a name="parameters"></a>参数 

- 包含 dissect_as 结构的映射，如要通过你注册的其他插件的 config.json 中定义。

### <a name="return-value"></a>返回值 

uint64_t 的数组，它是处理成一种 uint64_t 的注册。 这意味着，映射中端口列表中的端口值将为 uin64_t。

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

主函数。 具体来说，就是每次新的数据包到达分析程序时的插件逻辑。 将调用此函数，与数据包处理相关的所有操作都应在此处执行。

### <a name="considerations"></a>注意事项

插件应该是线程安全的，因为可能从不同的线程调用此函数。 最好是在堆栈上定义所有内容。

### <a name="parameters"></a>参数

- 负责存储数据和创建与 SDK 相关的对象（例如 ILayer 和字段）的 SDK 控制单元。
- 用于读取原始数据包的数据的帮助程序。 它已使用在 config.json 中定义的字节顺序设置。

### <a name="return-value"></a>返回值 

处理的结果。 可以是“成功”、“格式错误”或“健全”。

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

将处理标记为健全性失败，这意味着当前协议无法识别数据包，如果在同一筛选器上注册任何数据包，Horizon 应将其传递到其他分析程序。

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

构造函数

### <a name="parameters"></a>参数 

- 定义 Horizon 记录所用的错误代码，如 config.json 中定义。

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

结果为“格式错误”，表明我们已经将数据包识别为协议，但某些验证出错（保留的位已打开，或某个字段缺失）。

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

构造函数

### <a name="parameters"></a>参数  

- 错误代码，如 config.json 中定义。

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

通知 Horizon 成功处理。 如果成功，则表明接受了该数据包，数据属于我们，并且提取了所有数据。

## `horizon::protocol::SuccessResult()`

构造函数。 创建了基本的成功结果。 这意味着我们不知道此数据包的相关方向或任何其他元数据。

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

构造函数。

### <a name="parameters"></a>参数 

- 如果识别，则为数据包的方向。 值可以为“请求”或“响应”。

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

构造函数。

### <a name="parameters"></a>参数

- 如果我们识别了数据包，则为数据包的方向，可以是“请求”、“响应”。
- 警告。 这些事件不会失败，但会通知 Horizon。

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

构造函数。

### <a name="parameters"></a>参数 

-  警告。 这些事件不会失败，但会通知 Horizon。

## `HorizonID HORIZON_FIELD(const std::string_view &)`

将对字段名称基于字符串的引用（例如，function_code）转换为 HorizonID。

### <a name="parameters"></a>参数 

- 要转换的字符串。

### <a name="return-value"></a>返回值

- 从字符串创建 HorizonID。

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

创建一个新层，使 Horizon 知道插件要存储一些数据。 这是应使用的基本存储单元。

### <a name="return-value"></a>返回值

对已创建的层的引用，用于向其中添加数据。

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

获取字段管理对象，该对象负责在不同的对象上创建字段，例如，在 ILayer 上创建字段。

### <a name="return-value"></a>返回值

对管理器的引用。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

使用请求的 ID 在层上创建一个新的 64 位数字字段。

### <a name="parameters"></a>参数 

- 之前创建的层。
- “HORIZON_FIELD”宏创建的 HorizonID。
- 要存储的原始值。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

使用请求的 ID 在层上创建新的字符串字段。 将移动内存，因此请小心谨慎。 你将无法再次使用此值。

### <a name="parameters"></a>参数  

- 之前创建的层。
- “HORIZON_FIELD”宏创建的 HorizonID。
- 要存储的原始值。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

使用请求的 ID 在层上创建新的原始值（字节数组）字段。 将移动内存，因此请小心谨慎，你将无法再次使用此值。

### <a name="parameters"></a>参数

- 之前创建的层。
- “HORIZON_FIELD”宏创建的 HorizonID。
- 要存储的原始值。

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

使用请求的 ID 在指定类型的层上创建数组值（数组）字段。

### <a name="parameters"></a>参数

- 之前创建的层。
- “HORIZON_FIELD”宏创建的 HorizonID。
- 要存储在数组中的值的类型。

### <a name="return-value"></a>返回值

对应向其追加值的数组的引用。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

将一个新的整数值追加到前面创建的数组中。

### <a name="parameters"></a>参数

- 前面创建的数组。
- 要存储在数组中的原始值。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

将一个新的字符串值追加到前面创建的数组中。 将移动内存，因此请小心谨慎，你将无法再次使用此值。

### <a name="parameters"></a>参数

- 前面创建的数组。
- 要存储在数组中的原始值。

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

将一个新的原始值追加到前面创建的数组中。 将移动内存，因此请小心谨慎，你将无法再次使用此值。

### <a name="parameters"></a>参数

- 前面创建的数组。
- 要存储在数组中的原始值。

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

检查缓冲区中是否包含至少 X 个字节。

### <a name="parameters"></a>参数

应存在的字节数。

### <a name="return-value"></a>返回值

如果缓冲区中包含至少 X 个字节，则为 True。 否则为 `False`。

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

根据字节顺序从缓冲区读取 uint8 值（1 个字节）。

### <a name="return-value"></a>返回值

从缓冲区中读取的值。

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

根据字节顺序从缓冲区读取 uint16 值（2 个字节）。

### <a name="return-value"></a>返回值

从缓冲区中读取的值。

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

根据字节顺序从缓冲区读取 uint32 值（4 个字节）。

### <a name="return-value"></a>返回值

从缓冲区中读取的值。

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

根据字节顺序从缓冲区读取 uint64 值（8 个字节）。

### <a name="return-value"></a>返回值

从缓冲区中读取的值。

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

读入指定大小的预分配内存会将数据实际复制到内存区域。

### <a name="parameters"></a>参数 

- 要将数据复制到的内存区域。
- 内存区域的大小，此参数还定义了要复制的字节数。

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

从缓冲区读入字符串。

### <a name="parameters"></a>参数 

- 应读取的字节数。

### <a name="return-value"></a>返回值

对字符串的内存区域的引用。

## `size_t horizon::general::IDataBuffer::getRemainingData()`

告诉你缓冲区中剩余的字节数。

### <a name="return-value"></a>返回值

缓冲区的剩余大小。

## `void horizon::general::IDataBuffer::skip(size_t)`

在缓冲区中跳过 X 个字节。

### <a name="parameters"></a>参数

- 要跳过的字节数。
