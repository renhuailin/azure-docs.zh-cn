---
title: Horizon SDK
titleSuffix: Azure Defender for IoT
description: 使用地平线 SDK，适用于 IoT 开发人员的 Azure Defender 会设计 dissector 的插件，以便对网络流量进行解码，使其能够由 IoT 网络分析程序自动 Defender 处理。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 06663e12bbcaee2243be75d6aa9ea9cf4fd125bf
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523356"
---
# <a name="horizon-proprietary-protocol-dissector"></a>地平线专用协议 dissector

范围是开放开发环境 (O) ) 用于保护运行专用协议的 IoT 和 ICS 设备。

此环境为客户和技术合作伙伴提供以下解决方案：

- 对公共、专用、自定义协议或协议的完全支持，这与任何标准不相同。 

- DPI 开发的新级别的灵活性和范围。

- 一个以指数方式展开可见和控制的工具，无需升级适用于 IoT 平台版本的 Defender。

- 允许专用开发而不泄漏敏感信息的安全。

使用地平线 SDK，适用于 IoT 开发人员的 Azure Defender 会设计 dissector 的插件，以便对网络流量进行解码，使其能够由 IoT 网络分析程序自动 Defender 处理。

协议 dissectors 作为外部插件开发，并与大量的 IoT 服务 Defender 集成。 例如，提供监视、警报和报告功能的服务。

## <a name="secure-development-environment"></a>安全开发环境 

使用地平线 O) ，可以开发自定义或专用协议，而不能在组织外共享。 例如，由于法律法规或公司政策。

开发 dissector 插件而不执行以下操作： 

- 泄漏有关协议定义方式的任何专有信息。

- 共享任何敏感 PCAPs。

- 违反符合性规定。

## <a name="customization-and-localization"></a>自定义和本地化  

SDK 支持各种自定义选项，包括：

  - 函数代码的文本。 

  - 警报、事件和协议参数的完整本地化文本。 有关详细信息，请参阅 [创建映射文件 (JSON) ](#create-mapping-files-json)。

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="查看完全本地化的警报。":::

## <a name="horizon-architecture"></a>范围体系结构

体系结构模型包含三个产品层。

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>用于 IoT 平台层的 Defender

支持对 IoT 平台中的自定义 dissector 插件进行即时集成和实时监视，无需升级 IoT 平台版本的 Defender。

## <a name="defender-for-iot-services-layer"></a>Defender for IoT 服务层

每个服务都设计为与特定协议分离的管道，从而实现更高效的独立开发。

每个服务都设计为与特定协议分离的管道。 服务侦听管道上的流量。 它们与插件数据和传感器捕获的流量进行交互，以便为部署的协议编制索引并分析流量负载，并实现更有效、更独立的开发。

## <a name="custom-dissector-layer"></a>自定义 dissector 层 

使用用于 IoT 专有 SDK (（包括 c + + 实现和 JSON 配置) ）创建插件： 

- 定义如何识别协议

- 定义如何映射要从流量中提取的字段并提取它们 

- 定义如何与 Defender for IoT 服务集成

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="内置层。":::

用于 IoT 的 Defender 为常见协议提供了基本 dissectors。 你可以在这些协议之上构建你的 dissectors。

## <a name="before-you-begin"></a>准备阶段

## <a name="what-this-sdk-contains"></a>此 SDK 包含的内容 

此工具包包含开发所需的标头文件。 开发过程需要基本步骤和可选的高级步骤，如此 SDK 中所述。

<support@cyberx-labs.com>有关接收头文件和其他资源的信息，请联系。

## <a name="about-the-environment-and-setup"></a>关于环境和设置 

### <a name="requirements"></a>要求 

- 首选开发环境为 Linux。 如果在 Windows 环境中进行开发，请考虑将 VM 与 Linux 系统一起使用。

- 对于编译过程，请使用 GCC v7.4.0 或更高版本。 使用在 c + + 17 下受支持的 stdlib.h 中的任何标准类。

- 用于 IoT 版本3.0 及更高版本的 Defender。

### <a name="process"></a>过程

1. [下载](https://www.eclipse.org/) 适用于 c/c + + 开发人员的 Eclipse IDE。 你可以使用任何其他所需的 IDE。 本文档将指导你使用 Eclipse IDE 完成配置。

1. 启动 Eclipse IDE 并配置工作区 (要将项目存储到的位置) 中，按 **Ctrl + n**，并将其创建为 c + + 项目。

1. 在下一个屏幕中，将 "名称" 设置为要开发的协议，然后选择 "项目类型" 和 "" `Shared Library` `AND Linux GCC` 。

1. 编辑项目属性，在 " **c/c + + 生成**  >  **设置**"  >  **工具设置**"  >  **GCC c + + 编译器**  >  **其他**  >  **刻度位置" 独立代码**。

1. 粘贴 SDK 附带的示例代码并进行编译。

1. 将项目 (库、config.json 和 metadata) 添加到 gz 文件，并将文件扩展名更改为 \<XXX> hdp，其中是 \<XXX> 插件的名称。

### <a name="research"></a>研究 

在开始之前，请验证：

- 阅读协议规范（如果可用）。

- 了解打算提取哪些协议字段。

- 已计划映射目标。

## <a name="about-plugin-files"></a>关于插件文件 

在开发过程中定义了三个文件。

### <a name="json-configuration-file-required"></a>JSON 配置文件 (必需)  

此文件应定义 dissector ID 和声明、依赖项、集成要求、验证参数和映射定义，以将值转换为名称，将数字转换为文本。 有关详细信息，请参阅以下链接：

- [ (JSON) 准备配置文件 ](#prepare-the-configuration-file-json)

- [准备实现代码验证](#prepare-implementation-code-validations)

- [提取设备元数据](#extract-device-metadata)

- [ (基准连接到索引服务) ](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>实现代码：需要 c + + () 

实现代码 (CPP) 分析原始流量，并将其映射到值（如服务、类和函数代码）。 它提取层字段，并将它们映射到 JSON 配置文件中的索引名称。 要从 CPP 中提取的字段在配置文件中定义。 有关详细信息，请参阅 [准备实现代码 (c + +) ](#prepare-the-implementation-code-c)。

### <a name="mapping-files-optional"></a>映射文件 (可选) 

您可以自定义插件输出文本以满足您的企业环境的需求。

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="迁移":::

你可以定义和更新映射文件，以便在不更改代码的情况下更新文本。 每个文件可以映射一个或多个字段：

  - 字段值到名称的映射，例如1： Reset、2： Start、3： Stop。

  - 映射文本以支持多种语言。

有关详细信息，请参阅 [创建映射文件 (JSON) ](#create-mapping-files-json)。

## <a name="create-a-dissector-plugin-overview"></a> (概述创建 dissector 插件) 

1. 查看 [有关环境和设置](#about-the-environment-and-setup) 部分的信息。

2.  [ (c + +) 准备实现代码 ](#prepare-the-implementation-code-c)。 复制文件的 **template.js** ，并对其进行编辑以满足你的需求。 不要更改这些密钥。 

3. [ (JSON) 准备配置文件 ](#prepare-the-configuration-file-json)。 复制 **模板 .cpp** 文件并实现重写方法。 有关详细信息，请参阅 [地平线：:p 协议：： BaseParser](#horizonprotocolbaseparser) 获取详细信息。

4. [准备实现代码验证](#prepare-implementation-code-validations)。

## <a name="prepare-the-implementation-code-c"></a> (c + + 准备实现代码) 

CPP 文件是负责执行以下操作的分析器：

- 验证包标头和负载 (例如标头长度或负载结构) 。

- 将数据从标头和负载提取到定义的字段中。

- 实现由 JSON 文件提取的已配置字段。

### <a name="what-to-do"></a>要执行的操作

复制模板 **.cpp** 文件并实现重写方法。 有关详细信息，请参阅 [地平线：:p 协议：： BaseParser](#horizonprotocolbaseparser)。

### <a name="basic-c-template-sample"></a>基本 c + + 模板示例 

本部分提供基本协议模板，其中包含用于 IoT 范围协议的示例 Defender 的标准函数。

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

### <a name="basic-c-template-description"></a>基本 c + + 模板说明  

本部分提供了基本协议模板，并对用于 IoT 地平线协议的示例 Defender 的标准功能进行了说明。 

### <a name="include-pluginpluginh"></a>#include "插件/插件 .h"

插件使用的定义。 标头文件包含完成开发所需的一切。

### <a name="horizonprotocolbaseparser"></a>地平线：:p 协议：： BaseParser

水平基础结构和插件层之间的通信接口。 有关详细信息，请参阅层次 [结构](#horizon-architecture) 概述。

ProcessLayer 是用于处理数据的方法。

- 函数代码中的第一个参数是处理实用工具，用于检索以前处理过的数据、创建新字段和层。

- 函数代码中的第二个参数是从以前的分析器传递的当前数据。

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

使用创建分析器的实例。

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>协议函数代码示例 

本部分提供了一个示例，说明代码编号 (2 个字节) ，以及提取 (4) 个字节的消息长度。

这是根据 JSON 配置文件中提供的字节序来完成的，这意味着，如果该协议的 *字节是小*，且该传感器运行在具有较少的字节序的计算机上，则将转换它。

还会创建一个层来存储数据。 使用处理 utils 中的 *fieldsManager* 创建新字段。 字段只能具有以下类型之一： *STRING*、 *NUMBER*、 *RAW DATA*、 *ARRAY* () 特定类型或 *复杂* 类型。 此层可以包含数字、原始或具有 ID 的字符串。

在下面的示例中，提取了以下两个字段：

- `function_code_number`

- `headerLength`

系统将创建一个新层，并将提取的字段复制到其中。

下面的示例描述了一个特定函数，该函数是为插件处理实现的主要逻辑。

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

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="相关的 json 字段。":::

## <a name="prepare-the-configuration-file-json"></a> (JSON) 准备配置文件

范围 SDK 使用标准 JavaScript 对象表示法 (JSON) ，这是一种用于存储和传输数据的轻型格式，不需要专用的脚本语言。

本部分介绍最低的 JSON 配置声明和相关的结构，并提供定义协议的示例配置文件。 此协议自动与设备发现服务集成。

## <a name="file-structure"></a>文件结构

下面的示例描述了文件结构。

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="文件结构的示例。":::

### <a name="what-to-do"></a>要执行的操作

复制模板 `config.json` 文件并对其进行编辑以满足你的需求。 不要更改该密钥。 密钥在 [示例 JSON 配置文件](#sample-json-configuration-file)中标记为红色。 

### <a name="file-naming-requirements"></a>文件命名要求 

JSON 配置文件必须另存为 `config.json` 。

### <a name="json-configuration-file-fields"></a>JSON 配置文件字段

本部分介绍你将定义的 JSON 配置字段。 请勿更改字段 *标签*。

### <a name="basic-parameters"></a>基本参数

本部分介绍基本参数。

| 参数标签 | 说明 | 类型 |
|--|--|--|
| **ID** | 协议的名称。 删除默认值，并在显示的同时添加协议名称。 | String |
| **endianess** | 定义如何对多字节数据进行编码。 请仅使用 "小" 或 "大" 这一术语。 取自协议规范或流量记录 | String |
| **sanity_failure_codes** | 当存在与代码标识有关的稳定冲突时，从分析器返回这些代码。 请参阅 c + + 部分中的幻数验证。 | String |
| **malformed_codes** | 这些是已正确标识但检测到错误的代码。 例如，如果字段长度太短或太长，或值无效。 | String |
| **dissect_as** | 定义特定协议流量应到达的位置的数组。 | TCP/UDP、端口等。 |
| **字段** | 将从流量中提取的字段的声明。 每个字段都有自己的 ID (名称) ，类型 (numeric、string、raw、array、复杂) 。 例如，在实现分析器文件中提取的字段 [函数](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) 。 在配置文件中写入的字段是可以添加到层中的字段。 |  |

### <a name="other-advanced-fields"></a>其他高级字段 

本部分介绍其他字段。

| 参数标签 | 说明 |
|-----------------|--------|
| **允许列表** | 您可以为协议值编制索引并将其显示在数据挖掘报表中。 这些报告反映了你的网络基线。 :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="数据挖掘视图的示例。"::: <br /> 有关详细信息，请参阅 [连接到索引服务 (基准) ](#connect-to-an-indexing-service-baseline) 以获取详细信息。 |
| **固件** | 可以提取固件信息、定义索引值，并触发插件协议的固件警报。 有关详细信息，请参阅 [提取固件数据](#extract-firmware-data) 获取详细信息。 |
| **value_mapping** | 您可以通过定义和更新映射文件来自定义插件输出文本，以满足企业环境的需要。 例如，映射到语言文件。 更改可以轻松地实现到文本，而无需更改或影响代码。 有关详细信息，请参阅 [创建映射文件 (JSON) ](#create-mapping-files-json) 获取详细信息。 |

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

本节介绍实现 c + + 代码验证函数并提供示例代码。 提供两层验证：

- 健全.

- 代码格式不正确。

不需要创建验证代码即可生成正常运行的插件。 如果不准备验证代码，可以查看传感器数据挖掘报表作为成功处理的指示。

字段值可以映射到映射文件中的文本并无缝更新，而不会影响处理。

## <a name="sanity-code-validations"></a>稳定代码验证 

这会验证传输的数据包是否与协议的验证参数匹配，这有助于识别流量中的协议。

例如，使用前8个字节作为 *幻数*。 如果出现故障，则返回一个稳定故障响应。

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

如果已部署了其他相关插件，则会对照这些插件验证数据包。

## <a name="malformed-code-validations"></a>代码验证错误 

验证格式不当后，将使用正确的验证。

如果基于协议处理数据包失败，将返回失败响应。

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="代码格式错误":::

## <a name="c-sample-with-validations"></a>验证的 c + + 示例

根据函数，将执行进程，如以下示例中所示。

### <a name="function-20"></a>函数20 

- 它将作为固件处理。

- 根据函数读取字段。

- 这些字段将添加到层中。

### <a name="function-10"></a>函数10 

- 函数包含其他子函数，这是一个更具体的操作

- 将读取 subfunction 并将其添加到层。

完成此操作后，处理已完成。 返回值指示是否已成功处理 dissector 层。 如果是，该层将变为可用。

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

可以提取资产上的以下元数据：

  - `Is_distributed_control_system` -指示协议是否为分布式控制系统的一部分。  (示例： SCADA protocol 应为 false) 

  - `Has_protocol_address` -指示是否存在协议地址;当前协议的特定地址，例如 MODBUS unit 标识符

  - `Is_scada_protocol` -指示协议是否特定于 OT 网络

  - `Is_router_potential` -指示协议是否主要由路由器使用。 例如，LLDP、CDP 或 STP。

若要实现此目的，需要使用元数据属性更新 JSON 配置文件。

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

编程事件发生时，可以提取代码内容。 提取的内容允许：

- 比较不同编程事件中的代码文件内容。

- 在未经授权的编程时触发警报。  

- 触发用于接收编程代码文件的事件。

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="编程更改日志。":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="通过单击 &quot;&quot; 按钮来查看编程。":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="未经授权的 PLC 编程警报。":::

若要实现此目的，需要使用属性更新 JSON 配置文件 `code_extraction` 。 

### <a name="json-configuration-fields"></a>JSON 配置字段 

本部分介绍 JSON 配置字段。 

- **method**

  指示接收编程事件文件的方式。 

  所有 (每个编程操作都将导致接收所有代码文件，即使文件没有更改) 也是如此。

- **file_type**  

  指示代码内容类型。  

  每个代码文件 (文本包含) 的文本信息。

- **code_data_field**
  
  指示用于提供代码内容的实现字段。

  定义域.

- **code_name_field**

  指示为了提供编码文件的名称而要使用的实现字段。

  定义域.

- **size_limit**

  指示每个编码文件内容的大小限制（以字节为单位），如果代码文件超出设置的限制，则会将其删除。 如果未指定此字段，则默认值将为15000000，即 15 MB。

  多种.

- **metadata**

  指示代码文件的其他信息。

  包含具有两个属性的对象的数组：

    - name (String) -表示元数据密钥 XSense 当前仅支持用户名密钥。
 
    - 值 (字段) -指示提供元数据数据所要使用的实现字段。

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
## <a name="custom-horizon-alerts"></a>自定义地平线警报

某些协议函数代码可能指示错误。 例如，如果协议控制的容器具有必须始终存储在特定温度的特定化学。 在这种情况下，可能存在指示温度计中错误的函数代码。 例如，如果函数代码为25，则可以在 Web 控制台中触发一个警报，指出容器存在问题。 在这种情况下，可以定义深层数据包警报。

将 **警报** 参数添加到 `config.json` 插件的。

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
| **ID** | 表示单个警报 ID。 它在此上下文中必须是唯一的。 | 数值 0-10000 |
| **message** | 向用户显示的信息。 此字段允许使用不同的字段。 | 使用协议中的任何字段或任何较低层协议。 |
| **title** | 警报标题 |  |
| **expression** | 当你希望弹出此警报时。 | 使用在较低层或当前层中找到的任何数字字段。</br></br> 每个字段都应使用包装 `{}` ，以便 SDK 将其作为字段进行检测，支持的逻辑运算符为</br> = =-等于</br> <=-小于或等于</br> >=-大于或等于</br> >-大于</br> <-小于</br> ~ =-不等于 |

## <a name="more-about-expressions"></a>有关表达式的详细信息

当范围 SDK 每次调用该表达式且变为 *true* 时，将在传感器中触发警报。

同一警报中可以包含多个表达式。 例如，

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

此表达式仅在数据包 ipv4 src 为10.10.10.10 时验证函数代码。 这是数字表示形式的 IP 地址的原始表示形式。

您可以使用 `and` 或 `or` 来连接表达式。

## <a name="json-sample-custom-horizon-alerts"></a>JSON 示例自定义地平线警报

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

## <a name="connect-to-an-indexing-service-baseline"></a> (基准连接到索引服务) 

您可以为协议值编制索引并将其显示在数据挖掘报表中。

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="数据挖掘选项的视图。":::

这些值以后可以映射到特定的文本，例如，以任何语言将数字映射为文本或添加信息。

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="迁移":::

有关详细信息，请参阅 [创建映射文件 (JSON) ](#create-mapping-files-json) 获取详细信息。

你还可以使用以前分析的协议中的值来提取其他信息。

例如，对于基于 TCP 的值，可以使用 IPv4 层中的值。 在此层中，可以提取值，如数据包的源和目标。

若要实现此目的，需要使用属性更新 JSON 配置文件 `whitelists` 。

## <a name="allow-list-data-mining-fields"></a>允许列表 (数据挖掘) 字段

以下允许列表字段可用：

- 名称-用于索引的名称。

- alert_title –解释事件的简短、唯一的标题。

- alert_text –附加信息

可以添加多个允许列表，以实现索引的完全灵活性。

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

- 提取固件型号或版本。 可以进一步利用此信息来识别标识符。

- 检测到新的固件版本时触发警报。

若要实现此目的，需要使用固件属性更新 JSON 配置文件。

## <a name="firmware-fields"></a>固件字段

本部分介绍 JSON 固件配置字段。

- name 
  
  指示如何在传感器控制台中显示字段。

- **value**

 指示用于提供数据的实现字段。 

- **firmware_index：**

  选择下列选项之一：  
  - **模型**：设备型号。 启用标识符检测。
  - **串行**：设备序列号。 序列号并不总是适用于所有协议。 每个设备的此值是唯一的。
  - **机架**：指示机架标识符（如果设备是机架的一部分）。
  - **槽**：插槽标识符（如果设备是机架的一部分）。  
  - **module_address**：如果模块可在另一台设备后面显示，则使用来呈现层次结构。 改为适用于机架槽组合，这是一个更简单的表示形式。  
  - **firmware_version**：指示设备版本。 启用标识符检测。
  - **alert_text**：指示描述固件偏差的文本，例如，版本更改。  
  - **index_by**：指示用于标识和索引设备的字段。 在下面的示例中，设备通过其 IP 地址进行标识。 在某些协议中，可以使用更复杂的索引。 例如，如果另一个设备已连接，并且你知道如何提取其内部路径。 例如，可以将 MODBUS 单元 ID 作为索引的一部分，用作 IP 地址和单位标识符的不同组合。
  - **firmware_fields**：指示哪些字段为设备元数据字段。 在此示例中，使用了以下各项：模型、修订和名称。 每个协议都可以定义自己的固件数据。

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

你可以在清单、数据挖掘和其他报表中将设备中的可用信息增强到设备。

- 名称

- 类型

- Vendor

- 操作系统

若要实现此目的，需要使用 **Properties** 属性更新 JSON 配置文件。 

您可以在编写基本插件并提取必填字段后执行此操作。

## <a name="properties-fields"></a>属性字段

本部分介绍 JSON 属性配置字段。 

**config_file** 

包含定义如何处理字段中每个键的文件名 `key` 。 配置文件本身应为 JSON 格式，并作为插件协议文件夹的一部分包含在内。

JSON 中的每个密钥定义了从数据包提取此密钥时应执行的一组操作。

每个密钥都可以有：

- **数据包数据** -指示将根据从包中提取的数据进行更新的属性 (用于提供该数据) 的实现字段。

- **静态数据** -指示应更新的预定义 `property-value` 操作集。

可在此文件中配置的属性包括： 

- **名称** -指示设备名称。

- **类型** -指示设备类型。

- **供应商** -表示设备供应商。

- **描述** -指示设备固件型号 (优先级低于 "model" ) 。

- **操作系统** -指示设备操作系统。

### <a name="fields"></a>字段

| 字段 | 描述 |
|--|--|
| key | 指示键。 |
| value | 指示用于提供数据的实现字段。 |
| is_static_key | 指示 `key` 字段是派生自数据包中的值还是预定义的值。 |

### <a name="working-with-static-keys-only"></a>仅使用静态密钥

如果使用的是静态密钥，则无需配置 `config.file` 。 只能配置 JSON 文件。

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

## <a name="create-mapping-files-json"></a> (JSON 创建映射文件) 

您可以通过定义和更新映射文件来自定义插件输出文本，以满足企业环境的需求。 更改可以轻松地实现到文本，而无需更改或影响代码。 每个文件都可以映射一个或多个字段。

- 字段值到名称的映射，例如1： Reset，2： Start，3：停止。

- 映射文本以支持多种语言。

可以定义两种类型的映射文件。

  - [简单映射文件](#simple-mapping-file)。

  - [依赖关系映射文件](#dependency-mapping-file)。

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="网网络":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="未处理警报的视图。":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="已知策略冲突的列表。":::

## <a name="file-naming-and-storage-requirements"></a>文件命名和存储要求 

映射文件应保存在元数据文件夹下。

文件的名称应与 JSON 配置文件 ID 匹配。

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="JSON 配置文件的示例。":::

## <a name="simple-mapping-file"></a>简单映射文件 

下面的示例演示一个基本的 JSON 文件作为键值。

创建允许列表时，它包含一个或多个映射的字段。 该值将从数字、字符串或任何类型转换为映射文件中显示的格式化文本。

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>依赖关系映射文件 

若要指示该文件是依赖项文件，请将关键字添加 `dependency` 到映射配置。

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

文件包含依赖项字段与函数字段之间的映射。 例如，在函数和子函数之间。 子函数根据所提供的函数更改。

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

依赖项可以基于特定值或字段。 在下面的示例中，它基于字段。 如果将其基于某个值，请定义要从映射文件中读取的提取值。

在下面的示例中，与此字段的相同值相关的依赖关系。

例如，在子函数5中，根据函数更改含义。

  - 如果它是 read 函数，则5表示读取内存。

  - 如果它是写入函数，则使用相同的值从文件进行读取。

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

1. 向文件添加 **项目** (可以为、库、config.js或元数据) `tar.gz` 。

1. 将文件扩展名更改为 \<XXX.hdp> ，其中是 \<XXX> 插件的名称。

若要登录到地平线控制台：

1.  以管理员、CyberX 或支持用户的身份登录传感器 CLI。

2.  在文件中： `/var/cyberx/properties/horizon.properties` 将 " **启用** " 属性更改为 **true** (`horizon.properties:ui.enabled=true`) 。

3.  登录到传感器控制台。

4.  从主菜单中选择 " **水平** " 选项。

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="从左侧窗格中选择 &quot;水平&quot; 选项。":::

    水平控制台将打开。

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="地平线控制台及其所有插件的视图。":::

## <a name="plugins-pane"></a>插件窗格

"插件" 窗格列出：

  - 基础结构插件：默认情况下，使用适用于 IoT 的 Defender 安装基础结构插件。

  - 应用程序插件：默认情况下，使用适用于 iot 的 Defender 和用于 IoT 的其他插件或外部开发人员安装应用程序插件。
    
启用和禁用已使用切换上传的插件。

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="CIP 切换。":::

### <a name="uploading-a-plugin"></a>上传插件

创建并打包插件后，可以将其上传到 IoT 传感器的 Defender。 若要完全覆盖网络，应将插件上传到组织中的每个传感器。

上载：

1.  登录到传感器。


2. 选择“上传”。

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="上传插件。":::

3. 浏览到插件并将其拖到插件对话框。 验证前缀是否为 `.hdp` 。 加载插件。

## <a name="plugin-status-overview"></a>插件状态概述 

"水平控制台 **概述** " 窗口提供有关上传的插件的信息，并允许您禁用和启用它们。

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="地平线控制台概述。":::

| 字段 | 说明 |
|--|--|
| 应用程序 | 上传的插件的名称。 |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="开关。"::: | 开启 **或****关闭** 插件。 关闭插件时，适用于 IoT 的 Defender 将不会处理在插件中定义的协议流量。 |
| 时间 | 上次分析数据的时间。 每5秒钟更新一次。 |
| PPS | 每秒的数据包数。 |
| 带宽 | 过去5秒内检测到的平均带宽。 |
| Malforms | 验证格式不当后，将使用正确的验证。 如果基于协议处理数据包失败，将返回失败响应。   <br><br>此列指示过去5秒内 malform 错误的数目。 有关详细信息，请参阅 [格式错误的代码验证](#malformed-code-validations) 。 |
| 警告 | 数据包与结构和规范匹配，但根据插件警告配置，会出现意外的行为。 |
| 错误 | 基本协议验证失败的数据包数。 验证数据包是否与协议定义匹配。 此处显示的数字表示过去5秒检测到的错误数。 有关详细信息，请参阅 [健全代码验证](#sanity-code-validations) 以了解详细信息。 |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="监视器图标。"::: | 查看有关为你的插件检测到的 malform 和警告的详细信息。 |

## <a name="plugin-details"></a>插件详细信息

可以通过分析插件检测到的 *Malform* 和 *警告* 的数目来监视实时插件的行为。 一个选项可用于冻结屏幕和导出以便进一步调查

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="SNMP 监视器屏幕。":::

监视：

从 "概述" 中选择插件的 "监视" 按钮。

## <a name="next-steps"></a>后续步骤

设置你的 [水平 API](references-horizon-api.md)
