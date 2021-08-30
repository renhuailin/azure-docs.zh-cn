---
title: 在 Azure 流分析中使用 .NET 自定义反序列化程序读取任何格式的输入
description: 本文介绍了序列化格式，以及用来为 Azure 流分析云和边缘作业定义 .NET 自定义反序列化程序的接口。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/16/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: d66879a4f3aac37bb08cfcf916c0cb44027c3729
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112295446"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers-preview"></a>使用 .NET 自定义反序列化程序读取任何格式的输入（预览版）

Azure 流分析作业可以通过 .NET 自定义反序列化程序来读取三种[内置数据格式](stream-analytics-parsing-json.md)之外的格式的数据。 本文介绍序列化格式，以及用来为 Azure 流分析云和边缘作业定义 .NET 自定义反序列化程序的接口。 此外，还提供了协议缓冲区和 CSV 格式的示例反序列化程序。

## <a name="net-custom-deserializer"></a>.NET 自定义反序列化程序

以下代码示例是用于定义自定义反序列化程序并实现 `StreamDeserializer<T>` 的接口。

`UserDefinedOperator` 是所有自定义流式处理运算符的基类。 它初始化 `StreamingContext`，后者提供上下文，其中包含用于发布在调试反序列化程序的任何问题时所需的诊断信息的机制。

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

以下代码片段用于实现流数据的反序列化。 

可跳过的错误应使用通过 `UserDefinedOperator` 的 Initialize 方法传递的 `IStreamingDiagnostics` 来发出。 所有异常均会被视为错误，并且反序列化程序将被重新创建。 出现特定数量的错误后，该作业将进入失败状态。

`StreamDeserializer<T>` 将流反序列化为 `T` 类型的对象。 必须满足以下条件：

1. T 是一个类或结构。
1. T 中的所有公共字段是
    1. [sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] 中的一个，或者是其可为 null 的等效项。
    1. 遵循相同规则的另一个结构或类。
    1. 遵循相同规则的 `T2` 类型的数组。
    1. IList`T2`，其中的 T2 遵循相同的规则。
    1. 没有任何递归类型。

参数 `stream` 是包含已序列化对象的流。 `Deserialize` 返回 `T` 实例的集合。

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` 提供上下文，其中包含用来为用户运算符发布诊断信息的机制。

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` 用于实现对用户定义的运算符（包括序列化程序、反序列化程序和用户定义的函数）的诊断。

`WriteError` 将错误消息写入资源日志，并将错误发送到诊断信息。

`briefMessage` 是简短的错误消息。 此消息显示在诊断信息中，供产品团队用于调试。 请不要包含敏感信息，并将消息保留在 200 个字符以下

`detailedMessage` 是详细的错误消息，只会添加到存储中的资源日志。 此消息的长度应小于 2000 个字符。

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>反序列化程序示例

本部分介绍如何编写适用于 Protobuf 和 CSV 的自定义反序列化程序。 有关更多示例（例如“事件中心捕获”的 AVRO 格式），请访问 [GitHub 上的 Azure 流分析](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)。

### <a name="protocol-buffer-protobuf-format"></a>协议缓冲区 (Protobuf) 格式

这是一个使用协议缓冲区格式的示例。

假设协议缓冲区定义如下。

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

从 **Google.Protobuf.Tools** NuGet 运行 `protoc.exe` 会生成一个包含定义的 .cs 文件。 此处未显示生成的文件。 必须确保在流分析项目中使用的 Protobuf Nuget 版本与用于生成输入的 Protobuf 版本相匹配。 

以下代码片段是反序列化程序的实现，它假设生成的文件已包含在项目中。 此实现只是基于生成的文件的精简包装器。

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

以下代码片段是一个简单的 CSV 反序列化程序，它也演示如何传播错误。

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>REST API 的序列化格式

每个流分析输入都有一种 **序列化格式**。 有关输入选项的详细信息，请参阅[输入 REST API](/rest/api/streamanalytics/2016-03-01/inputs) 文档。

以下 Javascript 代码是使用 REST API 时的 .NET 反序列化程序序列化格式示例：

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` 应是实现 `StreamDeserializer<T>` 的类。 以下部分对此进行了介绍。

## <a name="region-support"></a>区域支持

使用标准 SKU 时，此功能在以下区域可用：

* 美国中西部
* 北欧
* 美国东部
* 美国西部
* 美国东部 2
* 西欧

其他区域的用户可以[请求支持](https://aka.ms/ccodereqregion)。 但是，使用[流分析群集](./cluster-overview.md)时没有此类区域限制。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>此功能何时在所有 Azure 区域中推出？

此功能已在 [6 个区域](#region-support)中推出。 如果你希望在其他区域使用此功能，可以[提交请求](https://aka.ms/ccodereqregion)。 在所有 Azure 区域中支持此功能已在规划中。

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>是否可以从类似于 GetMetadataPropertyValue 函数的输入访问 MetadataPropertyValue？

现在不支持此功能。 如果需要此功能，可以在 [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese) 中为此请求投票。

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>是否可以在社区中分享我的反序列化程序实现，使其他人能够受益？

实现反序列化程序后，可以在社区中分享，以便为其他人提供帮助。 请将代码提交到 [Azure 流分析 GitHub 存储库](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)。

### <a name="what-are-the-other-limitations-of-using-custom-deserializers-in-stream-analytics"></a>在流分析中使用自定义反序列化程序还有其他哪些限制？

如果输入采用 Protobuf 格式，并且该格式的架构包含 `MapField` 类型，则无法实现自定义反序列化程序。 此外，自定义反序列化程序不支持示例数据或预览数据。 

## <a name="next-steps"></a>后续步骤

* [用于 Azure 流分析云作业的 .NET 自定义反序列化程序](custom-deserializer.md)
