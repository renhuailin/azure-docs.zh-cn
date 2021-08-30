---
title: Horizon SDK
description: 使用 Horizon SDK，Azure Defender for IoT 开发者可以设计解析器插件用于解码网络流量，使流量可由自动 Defender for IoT 网络分析程序处理。
ms.date: 1/13/2021
ms.topic: article
ms.openlocfilehash: a6b9f69daeb39b79ccdabea273efe825f6a9d1ff
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114673975"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Horizon 专有协议解析器

Horizon 是一种开放式开发环境 (ODE)，用于保护运行专有协议的 IoT 和 ICS 设备。

这种环境为客户和技术合作伙伴提供以下解决方案：

- 无限制完全支持通用协议、专有协议、自定义协议或者偏离任何标准的协议。 

- 全新级别的 DPI 开发灵活度和范围。

- 可成倍扩展 OT 可见性和控制度的工具，无需升级 Defender for IoT 平台版本。

- 实现专有开发的安全性，不会泄漏敏感信息。

使用 Horizon SDK，Azure Defender for IoT 开发者可以设计解析器插件用于解码网络流量，使流量可由自动 Defender for IoT 网络分析程序处理。

协议解析器作为外部插件开发，可与大量的 Defender for IoT 服务相集成。 例如，提供监视、警报和报告功能的服务。

## <a name="secure-development-environment"></a>安全的开发环境 

Horizon ODE 支持开发不能在组织外共享的自定义或专有协议。 例如，由于法律法规或公司政策限制。

开发解析器插件而不会： 

- 泄漏有关协议定义方式的任何专有信息。

- 共享任何敏感 PCAP。

- 违反合规性规定。

有关开发协议插件的信息，请联系 <ms-horizon-support@microsoft.com>。
## <a name="customization-and-localization"></a>自定义和本地化  

SDK 支持各种自定义选项，包括：

  - 函数代码的文本。 

  - 警报、事件和协议参数的完整本地化文本。 有关详细信息，请参阅“[创建映射文件 (JSON)](#create-mapping-files-json)”。

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="查看完全本地化的警报。":::

## <a name="horizon-architecture"></a>Horizon 体系结构

体系结构模型包含三个产品层。

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Defender for IoT 平台层

支持立即集成和实时监控 Defender for IoT 平台中的自定义解析器插件，而无需升级 Defender for IoT 平台版本。

## <a name="defender-for-iot-services-layer"></a>Defender for IoT 服务层

每个服务都设计为与特定协议分离的管道，从而实现更高效的独立开发。

每个服务都设计为与特定协议分离的管道。 服务侦听管道上的流量。 它们与插件数据和传感器捕获的流量进行交互，以便为部署的协议编制索引并分析流量有效负载，从而实现更高效的独立开发。

## <a name="custom-dissector-layer"></a>自定义解析器层 

支持使用 Defender for IoT 专有 SDK（包括 C++ 实现和 JSON 配置）创建插件来执行以下操作： 

- 定义如何识别协议

- 定义如何映射要从流量中提取的字段并提取它们 

- 定义如何与 Defender for IoT 服务集成

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="内置层。":::

Defender for IoT 提供用于通用协议的基本解析器。 用户可以基于这些协议构建自己的解析器。

## <a name="before-you-begin"></a>开始之前

## <a name="what-this-sdk-contains"></a>SDK 包含的内容 

此工具包包含开发所需的头文件。 如本 SDK 中所述，开发过程需要执行基本步骤和高级步骤（可选）。

有关接收头文件和其他资源的信息，请联系 <ms-horizon-support@microsoft.com>。

## <a name="about-the-environment-and-setup"></a>关于环境和设置 

### <a name="requirements"></a>要求

- 首选开发环境为 Linux。 如果在 Windows 环境中进行开发，请考虑将 VM 与 Linux 系统结合使用。

- 对于编译过程，请使用 GCC 7.4.0 或更高版本。 使用 stdlib 中 C++17 支持的任何标准类。

- Defender for IoT 3.0 及更高版本。

### <a name="process"></a>进程

1. [下载](https://www.eclipse.org/)适用于 C/C++ 开发人员的 Eclipse IDE。 可以使用自己偏好的任何其他 IDE。 本文档将指导你使用 Eclipse IDE 完成配置。

1. 启动 Eclipse IDE 并配置工作区（将项目存储到的位置）后，按“Ctrl + n”，并将其创建为 C++ 项目。

1. 在下一个屏幕中，将名称设置为要开发的协议，然后选择项目类型 `Shared Library` 和 `AND Linux GCC`。

1. 编辑项目属性，在“C/C++ 生成” > “设置” > “工具设置” > “GCC C++ 编译器” > “其他” > “勾选位置无关代码”。

1. 粘贴 SDK 附带的示例代码并进行编译。

1. 将项目（库、config.json 和元数据）添加到 tar.gz 文件，并将文件扩展名更改为 \<XXX>.hdp，其中 \<XXX> 是插件的名称。

### <a name="research"></a>研究 

在开始之前，请先确认：

- 阅读协议规范（如果可用）。

- 了解打算提取哪些协议字段。

- 已计划映射目标。

## <a name="about-plugin-files"></a>关于插件文件 

在开发过程中定义了三个文件。

### <a name="json-configuration-file-required"></a>JSON 配置文件（必选） 

此文件应定义解析器 ID 和声明、依赖项、集成要求、验证参数和映射定义，以便将值转换为名称，将数字转换为文本。 有关详细信息，请参阅下列链接：

- [准备配置文件 (JSON)](#prepare-the-configuration-file-json)

- [准备实现代码验证](#prepare-implementation-code-validations)

- [提取设备元数据](#extract-device-metadata)

- [连接到索引服务（基线）](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>实现代码：C++（必选）

实现代码 (CPP) 可分析原始流量，并将其映射到值，如服务、类和函数代码。 它将提取层字段，并将它们映射到 JSON 配置文件中的索引名称。 配置文件中定义了要从 CPP 中提取的字段。 有关详细信息，请参阅“[准备实现代码 (C++)](#prepare-the-implementation-code-c)”。

### <a name="mapping-files-optional"></a>映射文件（可选）

用户可以自定义插件输出文本，以满足自己的企业环境需求。

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="迁移":::

用户可以定义和更新映射文件，从而在无需更改代码的情况下更新文本。 每个文件均可映射一个或多个字段：

  - 将字段值映射到名称，例如 1：重置、2：启动、3：停止。

  - 映射文本以支持多种语言。

有关详细信息，请参阅“[创建映射文件 (JSON)](#create-mapping-files-json)”。

## <a name="create-a-dissector-plugin-overview"></a>创建解析器插件（概述）

1. 请查看“[关于环境和设置](#about-the-environment-and-setup)”部分。

2.  [准备实现代码 (C++)](#prepare-the-implementation-code-c)。 复制 template.json 文件，并对其进行编辑以满足自己的需求。 请勿更改关键值。 

3. [准备配置文件 (JSON)](#prepare-the-configuration-file-json)。 复制“template.cpp”文件并实现替代方法。 有关详细信息，请参阅“[horizon::protocol::BaseParser](#horizonprotocolbaseparser)”。

4. [准备实现代码验证](#prepare-implementation-code-validations)。

## <a name="prepare-the-implementation-code-c"></a>准备实现代码 (C++)

CPP 文件是负责执行以下操作的分析程序：

- 验证数据包头和有效负载（例如头长度或有效负载结构）。

- 将数据从头和有效负载提取到定义的字段中。

- 通过 JSON 文件实现提取已配置字段。

### <a name="what-to-do"></a>要执行的操作

复制模板“.cpp”文件并实现替代方法。 有关详细信息，请参阅“[horizon::protocol::BaseParser](#horizonprotocolbaseparser)”。

### <a name="basic-c-template-sample"></a>基本 C++ 模板示例 

本部分提供基本协议模板，包含用于示例 Defender for IoT Horizon 协议的标准函数。

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>基本 C++ 模板说明  

本部分提供基本协议模板，包含用于示例 Defender for IoT Horizon 协议的标准函数说明。 

### <a name="include-pluginpluginh"></a>#include “plugin/plugin.h”

插件使用的定义。 头文件包含完成开发所需的一切内容。

### <a name="horizonprotocolbaseparser"></a>horizon::protocol::BaseParser

Horizon 基础结构和插件层之间的通信接口。 有关详细信息，请参阅“[Horizon 体系结构](#horizon-architecture)”了解有关层的概述。

ProcessLayer 是用于处理数据的方法。

- 函数代码中的第一个参数是处理实用工具，用于检索以前处理过的数据、创建新字段和层。

- 函数代码中的第二个参数是从以前的分析程序传递的当前数据。

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

用于创建分析程序的实例。

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>协议函数代码示例 

本部分提供如何提取代码编号（2 个字节）和消息长度（4 个字节）的示例。

这是根据 JSON 配置文件中提供的字节序来完成的，这意味着，如果该协议是“小字节序”，传感器将在具有小字节序的计算机上运行，并将对其进行转换。

此外，还会创建一个层来存储数据。 使用处理实用工具中的“fieldsManager”来创建新字段。 一个字段只能具有以下类型之一：字符串、数字、原始数据、数组（特定类型）或复合类型    。 此层可以包含数字、原始数据或具有 ID 的字符串。

在以下示例中，提取了以下两个字段：

- `function_code_number`

- `headerLength`

系统将创建一个新层，并将提取的字段复制到其中。

以下示例介绍了一个特定函数，这是插件处理实现的主要逻辑。

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>相关 JSON 字段 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="相关 json 字段。":::

## <a name="prepare-the-configuration-file-json"></a>准备配置文件 (JSON)。

Horizon SDK 使用标准 JavaScript 对象表示法 (JSON)，这是一种用于存储和传输数据的轻型格式，不需要专用的脚本语言。

本部分介绍最低 JSON 配置声明和相关结构，并提供定义协议的示例配置文件。 此协议将自动与设备发现服务集成。

## <a name="file-structure"></a>文件结构

以下示例描述了文件结构。

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="文件结构的示例。":::

### <a name="what-to-do"></a>要执行的操作

复制模板 `config.json` 文件，并对其进行编辑以满足自己的需求。 请勿更改关键值。 关键值在[示例 JSON 配置文件](#sample-json-configuration-file)中标记为红色。 

### <a name="file-naming-requirements"></a>文件命名要求 

必须将 JSON 配置文件另存为 `config.json`。

### <a name="json-configuration-file-fields"></a>JSON 配置文件字段

本部分介绍将要定义的 JSON 配置字段。 请勿更改“标签”字段。

### <a name="basic-parameters"></a>基本参数

本部分介绍基本参数。

| 参数标签 | 说明 | 类型 |
|--|--|--|
| **ID** | 协议的名称。 删除默认值，并在显示协议名称后添加该名称。 | 字符串 |
| endianess | 定义多字节数据的编码方式。 只能使用词语“小”或“大”。 从协议规范或流量记录中提取 | 字符串 |
| sanity_failure_codes | 这些是代码标识存在健全性冲突时，从分析程序返回的代码。 请参阅 C++ 部分中的幻数验证。 | 字符串 |
| malformed_codes | 这些是已正确识别但检测到错误的代码。 例如，当字段长度太短或太长或值无效时。 | 字符串 |
| dissect_as | 定义特定协议流量应到达位置的数组。 | TCP/UDP 端口等。 |
| **字段** | 从流量中提取哪些字段的声明。 每个字段都有自己的 ID（名称）和类型（数字、字符串、原始数据、数组、复合）。 例如，在实现分析程序文件中提取的字段[函数](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k)。 只能将配置文件中写入的字段添加到层中。 |  |

### <a name="other-advanced-fields"></a>其他高级字段 

本部分介绍其他字段。

| 参数标签 | 说明 |
|-----------------|--------|
| allow_lists | 用户可以为协议值编制索引，并在数据挖掘报表中显示它们。 这些报表反映网络基线。 :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="数据挖掘视图的示例。"::: <br /> 有关详细信息，请参阅“[连接到索引服务（基线）](#connect-to-an-indexing-service-baseline)”。 |
| firmware | 可以提取固件信息、定义索引值，并触发插件协议的固件警报。 有关详细信息，请参阅“[提取固件数据](#extract-firmware-data)”。 |
| value_mapping | 可以通过定义和更新映射文件自定义插件输出文本，以满足企业环境的需要。 例如，映射到语言文件。 可以轻松实现文本更改，而无需更改或影响代码。 有关详细信息，请参阅“[创建映射文件 (JSON)](#create-mapping-files-json)”。 |

## <a name="sample-json-configuration-file"></a>示例 JSON 配置文件

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>准备实现代码验证

本部分介绍实现 C++ 代码验证函数并提供示例代码。 提供两层验证：

- 健全性。

- 代码格式错误。

不需要创建验证代码即可生成正常运行的插件。 如果未准备验证代码，可以查看传感器数据挖掘报表，作为成功处理的指标。

字段值可以映射到映射文件中的文本并无缝更新，而不会影响处理。

## <a name="sanity-code-validations"></a>健全性代码验证 

该项验证传输的数据包是否与协议的验证参数匹配，进而帮助识别流量中的协议。

例如，使用前 8 个字节作为“幻数”。 如果健全性出现故障，则返回健全性故障响应。

例如：

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

如果已部署其他相关插件，则会对照这些插件再次验证数据包。

## <a name="malformed-code-validations"></a>代码验证格式错误 

在协议通过验证后对其进行格式错误验证。

如果基于协议处理数据包失败，则返回失败响应。

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="代码格式错误":::

## <a name="c-sample-with-validations"></a>C++ 验证示例

根据函数执行进程，如以下示例中所示。

### <a name="function-20"></a>函数 20 

- 将其作为固件处理。

- 根据函数读取字段。

- 这些字段将被添加到层中。

### <a name="function-10"></a>函数 10 

- 该函数包含其他执行更具体操作的子函数

- 读取子函数并将其添加到层中。

完成此操作后，处理即完成。 返回值将指示是否已成功处理解析器层。 如果成功，该层将变得可用。

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>提取设备元数据

可以在资产上提取以下元数据：

  - `Is_distributed_control_system` - 指示协议是否为分布式控制系统的一部分。 （示例：SCADA 协议应为 false）

  - `Has_protocol_address` - 指示是否存在协议地址以及当前协议的具体地址，例如 MODBUS 单元标识符

  - `Is_scada_protocol` - 指示协议是否专用于 OT 网络

  - `Is_router_potential` - 指示协议是否主要由路由器使用。 例如 LLDP、CDP 或 STP。

要实现此目的，需要使用元数据属性更新 JSON 配置文件。

## <a name="json-sample-with-metadata"></a>带有元数据的 JSON 示例 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>提取编程代码 

发生编程事件时，可以提取代码内容。 通过提取的内容可以：

- 比较不同编程事件的代码文件内容。

- 对未经授权的编程触发警报。  

- 对接收编程代码文件触发事件。

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="编程更改日志。":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="通过单击按钮查看编程。":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="未经授权的 PLC 编程警报。":::

要实现此目的，需要使用 `code_extraction` 属性更新 JSON 配置文件。 

### <a name="json-configuration-fields"></a>JSON 配置字段 

本部分介绍 JSON 配置字段。 

- **method**

  指示接收编程事件文件的方式。 

  所有（每个编程操作都将导致接收所有代码文件，即使文件没有更改）。

- **file_type**  

  指示代码内容类型。  

  文本（每个代码文件都包含文本信息）。

- code_data_field
  
  指示用于提供代码内容的实现字段。

  字段。

- code_name_field

  指示用于提供编码文件名称的实现字段。

  字段。

- size_limit

  指示每个编码文件内容的大小限制（以字节为单位），如果代码文件超出设置的限制，则会将其删除。 如果未指定此字段，则默认值为 15,000,000，即 15 MB。

  数字。

- **metadata**

  指示代码文件的其他信息。

  包含具有以下两个属性的对象数组：

    - 名称（字符串）- 指示元数据密钥 XSense 当前仅支持用户名密钥。
 
    - 值（字段）- 指示用于提供元数据的实现字段。

## <a name="json-sample-with-programming-code"></a>带编程代码的 JSON 示例

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>自定义 Horizon 警报

有些协议的函数代码可能指示错误。 例如，如果使用协议控制一个包含特定化学品的容器，这种化学品必须始终在特定温度下存储。 在这种情况下，可设置函数代码来指示温度计中的错误。 例如，如果函数代码为 25，则可以在 Web 控制台中触发一项警报，指示容器存在问题。 在这种情况下，可以定义深层数据包警报。

向插件的 `config.json` 添加“alerts”参数。

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>JSON 配置字段

本部分介绍 JSON 配置字段。 

| 字段名称 | 说明 | 可能值 |
|--|--|--|
| **ID** | 表示单个警报 ID。 它在此上下文中必须是唯一的。 | 数值 0 - 10000 |
| **message** | 向用户显示的信息。 此字段允许使用不同的字段。 | 使用你的协议或任何较低层协议中的任何字段。 |
| **title** | 警报标题 |  |
| **expression** | 当希望弹出此警报时。 | 使用位于较低层或当前层的任何数字字段。</br></br> 每个字段都应使用 `{}` 包装，以便 SDK 将其作为字段进行检测，支持的逻辑运算符为</br> == - 等于</br> <= - 小于或等于</br> >= - 大于或等于</br> > - 大于</br> < - 小于</br> ~= - 不等于 |

## <a name="more-about-expressions"></a>更多表达式信息

每次 Horizon SDK 调用表达式且其变为“true”时，传感器中都将触发一次警报。

同一警报下可以包含多个表达式。 例如，

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

此表达式仅在数据包 ipv4 src 为 10.10.10.10 时验证函数代码。 这是 IP 地址的原始表示形式——数字表示形式。

可以使用 `and` 或 `or` 来连接表达式。

## <a name="json-sample-custom-horizon-alerts"></a>JSON 示例自定义 Horizon 警报

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>连接到索引服务（基线）

用户可以为协议值编制索引，并在数据挖掘报表中显示它们。

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="数据挖掘选项的视图。":::

稍后，可以将这些值映射到任何语言的特定文本，例如将数字作为文本映射或添加信息。

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="迁移":::

有关详细信息，请参阅“[创建映射文件 (JSON)](#create-mapping-files-json)”。

此外，还可以使用以前分析的协议中的值来提取其他信息。

例如，对于基于 TCP 的值，可以使用 IPv4 层中的值。 在此层中，可以提取数据包的源和目标等值。

要实现此目的，需要使用 `whitelists` 属性更新 JSON 配置文件。

## <a name="allow-list-data-mining-fields"></a>允许列表（数据挖掘）字段

以下允许列表字段可用：

- name - 用于索引的名称。

- alert_title – 用于解释事件的简短而唯一的标题。

- alert_text – 其他信息

可以添加多个允许列表，实现索引的完全灵活性。

## <a name="json-sample-with-indexing"></a>带有索引的 JSON 示例 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>提取固件数据

可以提取固件信息、定义索引值，并触发插件协议的固件警报。 例如，

- 提取固件型号或版本。 可以进一步利用该信息来识别 CVE。

- 当检测到新固件版本时，触发警报。

要实现此目的，需要使用固件属性更新 JSON 配置文件。

## <a name="firmware-fields"></a>固件字段

本部分介绍 JSON 固件配置字段。

- **name**
  
  指示字段在传感器控制台中的显示方式。

- **value**

 指示用于提供数据的实现字段。 

- firmware_index:

  选择下列选项之一：  
  - model：设备型号。 启用 CVE 检测。
  - serial：设备序列号。 序列号并不总是适用于所有协议。 每个设备的此值是唯一的。
  - rack：指示机架标识符（如果设备是机架的一部分）。
  - slot：槽标识符（如果设备是机架的一部分）。  
  - module_address：用于呈现层次结构（如果模块可在另一台设备后面显示）。 反之如果是机架槽组合则适用，这是一种更简单的表示形式。  
  - firmware_version：指示设备版本。 启用 CVE 检测。
  - alert_text：指示描述固件偏差的文本，例如版本更改。  
  - index_by：指示用于标识和索引设备的字段。 在以下示例中，通过 IP 地址标识设备。 某些协议中可以使用更复杂的索引。 例如，如果已连接另一个设备，并且知道如何提取其内部路径。 例如，可以将 MODBUS 单元 ID 作为索引的一部分，用作 IP 地址和单元标识符的不同组合。
  - firmware_fields：指示哪些字段为设备元数据字段。 在此示例中，使用的字段如下所示：型号、版本和名称。 每个协议都可以定义自己的固件数据。

## <a name="json-sample-with-firmware"></a>带有固件的 JSON 示例 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>提取设备属性 

可以为设备增强库存设备、数据挖掘和其他报表中的可用信息。

- 名称

- 类型

- Vendor

- 操作系统

要实现此目的，需要使用“属性”属性更新 JSON 配置文件。 

在写入基本插件并提取必填字段后可以执行此操作。

## <a name="properties-fields"></a>属性字段

本部分介绍 JSON 属性配置字段。 

config_file 

包含文件名，用于定义如何处理 `key` 字段中的每个密钥。 配置文件本身应为 JSON 格式，并作为插件协议文件夹的一部分包含在内。

JSON 中的每个密钥定义从数据包提取此密钥时应执行的操作集。

每个密钥均可包含：

- 数据包数据 - 指示需根据从数据包中提取的数据进行更新的属性（用于提供该数据的实现字段）。

- 静态数据 - 指示应更新的预定义 `property-value` 操作集。

可在此文件中配置的属性包括： 

- 名称 - 指示设备名称。

- 类型 - 指示设备类型。

- 供应商 - 指示设备供应商。

- 说明 - 指示设备固件型号（优先级低于“型号”）。

- 操作系统 - 指示设备操作系统。

### <a name="fields"></a>字段

| 字段 | 描述 |
|--|--|
| key | 指示密钥。 |
| value | 指示用于提供数据的实现字段。 |
| is_static_key | 指示 `key` 字段是根据数据包中的值派生还是预定义值。 |

### <a name="working-with-static-keys-only"></a>仅适用于静态密钥

如果使用的是静态密钥，则无需配置 `config.file`。 只能配置 JSON 文件。

## <a name="json-sample-with-properties"></a>带属性的 JSON 示例 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>创建映射文件 (JSON)

通过定义和更新映射文件，可以自定义插件输出文本，以满足企业环境的需要。 可以轻松实现文本更改，而无需更改或影响代码。 每个文件均可映射一个或多个字段。

- 将字段值映射到名称，例如 1：重置、2：启动、3：停止。

- 映射文本以支持多种语言。

可以定义两种类型的映射文件。

  - [示例映射文件](#simple-mapping-file)。

  - [依赖项映射文件](#dependency-mapping-file)。

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="以太网":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="未处理警报的视图。":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="已知策略违规的列表。":::

## <a name="file-naming-and-storage-requirements"></a>文件命名和存储要求 

映射文件应保存在元数据文件夹下。

文件名应与 JSON 配置文件 ID 匹配。

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="JSON 配置文件的示例。":::

## <a name="simple-mapping-file"></a>简单映射文件 

以下示例将一个基本 JSON 文件作为关键值显示。

在创建允许列表时，其中包含一个或多个映射的字段。 该值将从数字、字符串或任何类型转换为映射文件中显示的格式化文本。

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>依赖项映射文件 

要指示文件为依赖项文件，请向映射配置中添加关键字 `dependency`。

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

文件中包含依赖项字段与函数字段之间的映射。 例如，在函数和子函数之间。 子函数根据所提供的函数进行更改。

之前配置的允许列表中没有依赖项配置，如下所示。

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

依赖项可以基于特定值或字段。 以下示例中是基于字段。 如果要基于值，请定义要从映射文件中读取的提取值。

在以下示例中，该字段相同值的依赖项如下所示。

例如，在子函数 5 中，含义随函数而更改。

  - 如果是读取函数，则 5 表示读取内存。

  - 如果是写入函数，则使用相同的值表示从文件中读取。

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>示例文件

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>带有映射的 JSON 示例

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>打包、上传和监视插件 

本部分介绍了操作方法

  - 打包插件。

  - 上传插件。

  - 监视和调试插件，以评估其运行状况。

打包插件：

1. 向 `tar.gz` 文件中添加“项目”（可以是库、config.json 或元数据）。

1. 将文件扩展名更改为 \<XXX.hdp>，其中 \<XXX> 是插件的名称。

登录到 Horizon 控制台：

1.  以管理员、CyberX 或支持用户的身份登录传感器 CLI。

2.  在文件中：`/var/cyberx/properties/horizon.properties` 将“ui.enabled”属性更改为“true”(`horizon.properties:ui.enabled=true`)。

3.  登录传感器控制台。

4.  从主菜单中选择“Horizon”选项。

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="从左侧窗格中选择 Horizon 选项。":::

    Horizon 控制台将随即打开。

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Horizon 控制台及其所有插件的视图。":::

## <a name="plugins-pane"></a>“插件”窗格

“插件”窗格将列出：

  - 基础结构插件：Defender for IoT 默认安装的基础结构插件。

  - 应用程序插件：Defender for IoT 默认安装的应用程序插件，以及 Defender for IoT 或外部开发者开发的其他插件。
    
通过切换启用和禁用已上传的插件。

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="CIP 切换。":::

### <a name="uploading-a-plugin"></a>上传插件

创建并打包插件后，可以将其上传到 Defender for IoT 传感器。 要完全覆盖网络，应将插件上传到组织中的每个传感器。

若要上传，请执行以下操作：

1.  登录到传感器。


2. 选择“上传”。

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="上传插件。":::

3. 浏览找到插件并将其拖拽到插件对话框中。 验证前缀是否为 `.hdp`。 加载插件。

## <a name="plugin-status-overview"></a>插件状态概述 

Horizon 控制台“概述”窗口提供有关已上传插件的基本信息，并可在其中禁用和启用插件。

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Horizon 控制台概述。":::

| 字段 | 说明 |
|--|--|
| 应用程序 | 已上传插件的名称。 |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="开启和关闭开关。"::: | 开启或关闭 插件。 关闭插件后，Defender for IoT 不会处理该插件中定义的协议流量。 |
| 时间 | 上次分析数据的时间。 每 5 秒钟更新一次。 |
| PPS | 每秒的数据包数。 |
| 带宽 | 在过去 5 秒内检测到的平均带宽。 |
| 格式错误 | 在协议通过了验证后使用了格式不当的验证。 如果基于协议处理数据包失败，则返回失败响应。   <br><br>此列指示过去 5 秒内发生的格式错误数。 有关详细信息，请参阅“[代码格式错误验证](#malformed-code-validations)”。 |
| 警告 | 数据包与结构和规范匹配，但基于插件警告配置检测到了意外的行为。 |
| 错误 | 基本协议验证失败的数据包数。 验证数据包是否与协议定义匹配。 此处显示的数字指示在过去 5 秒内检测到的错误数。 有关详细信息，请参阅“[健全性代码验证](#sanity-code-validations)”。 |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="监视图标。"::: | 请查看针对插件检测到的格式错误和警告的详细信息。 |

## <a name="plugin-details"></a>插件详细信息

通过分析针对插件检测到的“格式错误”和“警告”数，可以实时监视插件行为。  用户可以选择冻结屏幕并导出信息，以供进一步调查

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="SNMP 监视屏幕。":::

监视：

从“概述”中选择插件的“监视”按钮。

后续步骤

设置 [Horizon API](references-horizon-api.md)
