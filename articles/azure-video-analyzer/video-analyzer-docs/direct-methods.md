---
title: 在 Azure 视频分析器中使用直接方法 - Azure
description: Azure 视频分析器公开多个直接方法。 直接方法基于本主题中描述的约定。
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 7b56404a371b0e50fe129d0fc5c2edb46b0c0f06
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724545"
---
# <a name="azure-video-analyzer-direct-methods"></a>Azure 视频分析器直接方法

Azure 视频分析器 IoT Edge 模块 `avaedge` 公开多个可从 IoT 中心调用的直接方法。 直接方法表示与设备进行的请求-答复式交互，类似于会立即成功或失败（在用户指定的超时时间后）的 HTTP 调用。 此方法用于即时操作过程不同的情况，即时操作的不同取决于设备能否响应。 有关详细信息，请参阅[了解直接方法并从 IoT 中心进行调用](../../iot-hub/iot-hub-devguide-direct-methods.md)。

本主题介绍了这些方法、约定和方法的架构。

## <a name="conventions"></a>约定

直接方法基于以下约定：

1. 直接方法具有以 MAJOR.MINOR 格式指定的版本（如以下示例所示）。 在此示例中，版本为编号“@apiVersion”，其中“1”为 MAJOR（主要版本），“0”为 MINOR（次要版本）：

```
  {
    "methodName": "pipelineTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "{TopologyName}",
        "properties": {
            // Desired Topology properties
        }
    }
  }
```

2. 给定版本的视频分析器模块支持直接方法调用的当前版本及其下的所有次要版本。 不保证提供跨主要版本的支持。
3. 所有直接方法都是同步的。
4. 错误结果基于 [OData 错误架构](http://docs.oasis-open.org/odata/odata-json-format/v4.01/odata-json-format-v4.01.html#sec_ErrorResponse)。
5. 名称应遵守以下约束：
    
    * 仅限字母数字字符和短划线，不能以短划线开头和结尾
    * 不含空格
    * 最多 32个字符

### <a name="example-of-response-from-a-direct-method"></a>直接方法的示例响应

```
-----------------------  Request: livePipelineList  --------------------------------------------------

{
  "@apiVersion": "1.0"
}

---------------  Response: livePipelineList - Status: 200  ---------------

{
  "value": []
}

--------------------------------------------------------------------------
```    
### <a name="error-codes"></a>错误代码
如以下示例中所示，当你收到直接方法的错误响应时，会看到最高级别的错误代码，进一步的信息在 `details` 下提供。


```json
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "The request is invalid",
      "details": [
        {
          "code": "ApiVersionNotSupported",
          "message": "The API version '1.1' is not supported. Supported version(s): 1.0."
        }
      ]
    }
  }
}
```

下面是在最高级别使用的错误代码。

|状态 |代码   |Message|
|---|---|---|
|400|   BadRequest| 请求无效|
|400|   InvalidResource|    资源无效。|
|400|   InvalidVersion| API 版本无效|
|402|   ConnectivityRequired    |Edge 模块需要云连接才能正常工作。|
|404|   NotFound    |未找到资源|
|409|   Conflict|   操作冲突|
|500|   InternalServerError|    内部服务器错误|
|503|   ServerBusy| 服务器繁忙|

下面是在详细信息级别使用的一些错误代码。

|状态|    详细代码   |说明|
|---|---|---|
|400|   PipelineValidationError|    常规管道错误，如循环或分区等。|
|400|   ModuleValidationError|  模块特定的验证错误。|
|409|   PipelineTopologyInUse|  管道拓扑仍由一个或多个实时管道引用。|
|409|   OperationNotAllowedInState| 无法在当前状态下执行请求的操作。|
|409|   ResourceValidationError|    引用的资源（例如视频资源）不处于有效状态。|

## <a name="supported-direct-methods"></a>支持的直接方法  
下面是视频分析器边缘模块公开的直接方法。 可在[此处](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzerSdkDefinitions.json)找到直接方法的架构。

### <a name="pipelinetopologylist"></a>pipelineTopologyList

此直接方法列出已创建的所有管道拓扑。

#### <a name="request"></a>请求

```
{
  "@apiVersion": "1.0"
}
```
#### <a name="response"></a>响应

```
{
  "status": 200,
  "value": [
    {
      "systemData": {
        "createdAt": "2021-05-05T14:19:22.16Z",
        "lastModifiedAt": "2021-05-05T16:20:41.505Z"
      },
      
      // first pipeline topology payload
      
    },
      "systemData": {
        "createdAt": "2021-05-06T14:19:22.16Z",
        "lastModifiedAt": "2021-05-06T16:20:41.505Z"
      },
      
      // next pipeline topology payload
      
    }    
  ]
}
```

#### <a name="status-codes"></a>状态代码

| 条件 | 状态代码 |
|--|--|
| 已找到实体 | 200 |
| 一般用户错误 | 400 range |
| 找不到实体 | 404 |
| 一般服务器错误 | 500 range |

### <a name="pipelinetopologyset"></a>pipelineTopologySet

创建具有给定名称的管道拓扑（如果不存在此类拓扑），或更新具有该名称的现有拓扑。

关键方面：

* 可以任意更新未由实时管道引用的管道拓扑。
* 只要满足以下条件，在所有引用方实时管道都已停用的情况下，就可以任意更新管道拓扑：

    * 新添加的参数具有默认值
    * 任何管道均未引用已删除的参数
* 当实时管道处于活动状态时，只允许进行某些管道拓扑更新。

#### <a name="request"></a>请求

```
  {
    "methodName": "pipelineTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "{TopologyName}",
        "properties": {
            // Desired pipeline topology properties
        }
    }
  }
```

#### <a name="response"></a>响应

```
  {
    "status": 201,
    "payload": {
        "systemData": {
           "createdAt": "2021-05-11T18:16:46.491Z",
           "lastModifiedAt": "2021-05-11T18:16:46.491Z"
        },
        "name": "{TopologyName}",
        "properties": {
            // Complete pipeline topology
        }
    }
  }
```
#### <a name="status-codes"></a>状态代码

|条件  |状态代码    |详细错误代码|
|---|---|---|
已更新现有实体 |200|   空值|
已创建新的实体  |201|   空值|
一般用户错误 |400 range  ||
管道验证错误  |400    |PipelineValidationError|
模块验证错误|   400 |ModuleValidationError|
一般服务器错误   |500 range  ||

### <a name="pipelinetopologyget"></a>pipelineTopologyGet

检索具有指定名称的管道拓扑（如果存在）。

#### <a name="request"></a>请求

```
  {
        "@apiVersion": "1.0",
        "name": "{TopologyName}"       
  }
```
#### <a name="response"></a>响应

```
{
  "status": 200,
  "payload": {
    "value": [
      {
        "systemData": {
          "createdAt": "2021-05-06T10:28:04.560Z",
          "lastModifiedAt": "2021-05-06T10:28:04.560Z"
        },
        "name": "{TopologyName}",
        // Complete pipeline topology
      }
    ]
  }
}
```

#### <a name="status-codes"></a>状态代码

| 条件 | 状态代码 |
|--|--|
| Success | 200 |
| 一般用户错误 | 400 range |
| 一般服务器错误 | 500 range |

### <a name="pipelinetopologydelete"></a>pipelineTopologyDelete

删除单个管道拓扑。

* 请注意，不能有任何实时管道[正在引用所要删除的管道拓扑](pipeline.md#pipeline-states)。 如果存在此类实时管道，将会出现 `TopologyInUse` 错误。

#### <a name="request"></a>请求

```
  {
    "methodName": "pipelineTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "{TopologyName}"
    }
  }
```
#### <a name="response"></a>响应

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>状态代码

| 条件 | 状态代码 | 详细错误代码 |
|--|--|--|
| 已删除实体 | 200 | 空值 |
| 找不到实体 | 204 | 空值 |
| 一般用户错误 | 400 range |  |
| 一个或多个管道正在引用管道拓扑 | 409 | PipelineTopologyInUse |
| 一般服务器错误 | 500 range |  |

### <a name="livepipelinelist"></a>livePipelineList

列出所有实时管道。

#### <a name="request"></a>请求

```
  {
        "@apiVersion": "1.0"
  }
```
#### <a name="response"></a>响应

```
{
  "status": 200,
  "value": [
    {
      "systemData": {
        "createdAt": "2021-05-05T14:19:22.16Z",
        "lastModifiedAt": "2021-05-05T16:20:41.505Z"
      },      
      // first live pipeline payload  
    },
      "systemData": {
        "createdAt": "2021-05-06T14:19:22.16Z",
        "lastModifiedAt": "2021-05-06T16:20:41.505Z"
      },
      // next live pipeline payload
    }    
  ]
}
```
#### <a name="status-codes"></a>状态代码

| 条件 | 状态代码 |
|--|--|
| 已找到实体 | 200 |
| 一般用户错误 | 400 range |
| 找不到实体 | 404 |
| 一般服务器错误 | 500 range |

### <a name="livepipelineset"></a>livePipelineSet

创建具有给定名称的实时管道（如果不存在此类实时管道），或更新具有该名称的现有实时管道。

关键方面：

* 可以任意更新处于“已停用”状态的实时管道。 处于其他状态时，只允许进行某些更新。
* 每次更新时都会重新验证实时管道。

#### <a name="request"></a>请求

```
  {
        "@apiVersion": "1.0",
        "name": "{livePipelineName}",
        "properties": {
            // Desired live pipeline properties
        }
  }
```
#### <a name="response"></a>响应

````
  {
    "status": 201,
    "payload": {
        "systemData": {
           "createdAt": "2021-05-11T18:16:46.491Z",
           "lastModifiedAt": "2021-05-11T18:16:46.491Z"
        },
        "name": "{livePipelineName}",
        "properties": {
            // Complete live pipeline
        }
    }
  }
````
#### <a name="status-codes"></a>状态代码

| 条件 | 状态代码 | 详细错误代码 |
|--|--|--|
| 已更新现有实体 | 200 | 空值 |
| 已创建新的实体 | 201 | 空值 |
| 一般用户错误 | 400 range | 不适用 |
| 实时管道验证错误 | 400 | PipelineValidationError |
| 模块验证错误 | 400 | ModuleValidationError |
| 资源验证错误 | 409 | ResourceValidationError |
| 一般服务器错误 | 500 range | 不适用 |

### <a name="livepipelinedelete"></a>livePipelineDelete

删除单个实时管道。

关键方面：

* 只能删除已停用的实时管道。

#### <a name="request"></a>请求

```
  {
        "@apiVersion": "1.0",
        "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>响应

```
  {
    "status": 200,
    "payload": null
  }
```
#### <a name="status-codes"></a>状态代码

| 条件 | 状态代码 | 详细错误代码 |
|--|--|--|
| 已成功删除实时管道 | 200 | 空值 |
| 找不到实时管道 | 204 | 空值 |
| 一般用户错误 | 400 range |  |
| 管道不处于“已停用”状态 | 409 | OperationNotAllowedInState |
| 一般服务器错误 | 500 range |  |

### <a name="livepipelineget"></a>livePipelineGet

类似于 liveTopologyGet。 检索具有指定名称的实时管道（如果存在）。

#### <a name="request"></a>请求

```
  {
        "@apiVersion": "1.0",
        "name": "{livePipelineName}"       
  }
```
#### <a name="response"></a>响应

```
{
  "status": 200,
  "payload": {
    "value": [
      {
        "systemData": {
          "createdAt": "2021-05-06T10:28:04.560Z",
          "lastModifiedAt": "2021-05-06T10:28:04.560Z"
        },
        "name": "{livePipelineName}",
        // Complete live pipeline
      }
    ]
  }
}
```

#### <a name="status-codes"></a>状态代码

| 条件 | 状态代码 |
|--|--|
| Success | 200 |
| 一般用户错误 | 400 range |
| 一般服务器错误 | 500 range |

### <a name="livepipelineactivate"></a>livePipelineActivate

激活实时管道。 

关键方面

* 当实时管道处于“正在激活”或“已激活”状态时，方法将返回。 
* 针对实时管道执行 List/Set 操作会返回当前状态。
* 只要实时管道不处于（暂时性）“正在停用”状态，就可以调用此方法。
* 幂等性：
    * 对已处于“正在激活”状态的实时管道调用 `livePipelineActivate` 的行为方式就像该实时管道已停用一样。
    * 激活处于“活动”状态的管道会立即返回成功代码。

#### <a name="request"></a>请求

```
  {
        "@apiVersion": "1.0",
        "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>响应

```
{
    "status": 200,
    "payload": null
}
```
#### <a name="status-codes"></a>状态代码

| 条件 | 状态代码 | 详细错误代码 |
|--|--|--|
| 管道已成功激活 | 200 | 空值 |
| 已创建新的实体 | 201 | 空值 |
| 一般用户错误 | 400 range |  |
| 模块验证错误 | 400 | ModuleValidationError |
| 资源验证错误 | 409 | ResourceValidationError |
| 实时管道处于正在停用状态 | 409 | OperationNotAllowedInState |
| 一般服务器错误 | 500 range |  |

### <a name="livepipelinedeactivate"></a>livePipelineDeactivate

停用实时管道。 停用管道会正常地停用视频处理，并确保在适当的情况下将边缘上缓存的所有事件和视频提交到云中。

关键方面：

* 仅当已停用实时管道时，方法才会返回。
* 只要实时管道不处于（暂时性）“正在激活”状态，就可以调用此方法。
* 管道在被停用时将进入“正在停用”状态。
    * 针对实时管道执行 List/Set 操作会返回当前状态。
    * 当所有媒体均已上传到云时，停用即告完成（如果管道具有[视频接收器](pipeline.md#video-sink)节点）。
* 幂等性：
    * 对已处于“正在停用”状态的实时管道调用 `livePipelineDeactivate` 的行为方式就像该实时管道处于“活动”状态一样。
    * 停用处于“非活动”状态的管道会立即返回成功代码。


#### <a name="request"></a>请求

```
  {
    "@apiVersion": "1.0",
    "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>响应

```
{
 "status": 200,
 "payload": null
}
```
| 条件 | 状态代码 | 详细错误代码 |
|--|--|--|
| 管道已成功激活 | 200 | 空值 |
| 已创建新的实体 | 201 | 空值 |
| 一般用户错误 | 400 range |  |
| 管道处于正在激活状态 | 409 | OperationNotAllowedInState |
| 一般服务器错误 | 500 range |  |

## <a name="next-steps"></a>后续步骤

[模块孪生配置架构](module-twin-configuration-schema.md)
