---
title: Azure Functions JavaScript 开发者参考
description: 了解如何使用 JavaScript 开发函数。
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 10/07/2021
ms.custom: devx-track-js
ms.openlocfilehash: fbd291494554c7bbb6e7f79a932717bf81a124c6
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667585"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 开发人员指南

本指南包含有助于成功使用 JavaScript 开发 Azure Functions 的详细信息。

作为 Express.js、Node.js 或 JavaScript 开发人员，如果不熟悉 Azure Functions，请考虑先阅读以下文章之一：

| 入门 | 概念| 指导式学习 |
| -- | -- | -- | 
| <ul><li>[使用 Visual Studio Code 的 Node.js 函数](./create-first-function-vs-code-node.md)</li><li>[使用终端/命令提示符的 Node.js 函数](./create-first-function-cli-node.md)</li><li>[使用 Azure 门户的 Node.js 函数](functions-create-function-app-portal.md)</li></ul> | <ul><li>[开发人员指南](functions-reference.md)</li><li>[托管选项](functions-scale.md)</li><li>[TypeScript 函数](#typescript)</li><li>[性能&nbsp;注意事项](functions-best-practices.md)</li></ul> | <ul><li>[创建无服务器应用程序](/learn/paths/create-serverless-applications/)</li><li>[将 Node.js 和 Express API 重构到无服务器 API](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>JavaScript 函数基础知识

JavaScript (Node.js) 函数是导出的 `function`，它在触发时执行（[触发器在 function.json 中配置](functions-triggers-bindings.md)）。 传递给每个函数的第一个参数是 `context` 对象，该对象用于接收和发送绑定数据、日志记录以及与运行时通信。

## <a name="folder-structure"></a>文件夹结构

JavaScript 项目所需的文件夹结构如下所示。 可更改此默认值。 有关详细信息，请参阅下面的 [scriptFile](#using-scriptfile) 部分。

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

项目的根目录中有共享的 [host.json](functions-host-json.md) 文件，可用于配置函数应用。 每个函数都具有一个文件夹，其中包含其代码文件 (.js) 和绑定配置文件 (function.json)。 `function.json` 父目录的名称始终是函数的名称。

[2.x 版](functions-versions.md) Functions 运行时中所需的绑定扩展在 `extensions.csproj` 文件中定义，实际库文件位于 `bin` 文件夹中。 本地开发时，必须[注册绑定扩展](./functions-bindings-register.md#extension-bundles)。 在 Azure 门户中开发函数时，系统将为你完成此注册。

## <a name="exporting-a-function"></a>导出函数

必须通过 [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports)（或 [`exports`](https://nodejs.org/api/modules.html#modules_exports)）导出 JavaScript 函数。 导出的函数应是触发时执行的 JavaScript 函数。

默认情况下，Functions 运行时会在 `index.js` 中查找你的函数，其中，`index.js` 与其相应的 `function.json` 共享同一个父目录。 默认情况下，导出的函数应该是其文件中的唯一导出，或者名为 `run` 或 `index` 的导出。 若要配置文件位置和导出函数名称，请阅读下面的[配置函数的入口点](functions-reference-node.md#configure-function-entry-point)。

在执行时，将为导出的函数传递一些参数。 采用的第一个参数始终是 `context` 对象。 如果函数是同步的（不返回 Promise），则必须传递 `context` 对象，因为需要调用 `context.done` 才能正常使用该函数。

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>导出异步函数
在 Functions 运行时版本 2.x 中使用 [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 声明或普通 JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)，无需显式调用 [`context.done`](#contextdone-method) 回调即可通知函数已完成。 导出的异步函数/Promise 完成时，函数将完成。 对于面向版本 1.x 运行时的函数，在代码完成执行后，仍必须调用 [`context.done`](#contextdone-method)。

以下示例是一个简单的函数，用于记录其已被触发并立即完成执行。

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

导出异步函数时，还可配置输出绑定，以使用 `return` 值。 如果只有一个输出绑定，则建议使用此值。

若要使用 `return` 分配输出，请在 `function.json` 中将 `name` 属性更改为 `$return`。

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

在这种情况下，函数应如以下示例所示：

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>绑定 
在 JavaScript 中，需在函数的 function.json 中配置和定义[绑定](functions-triggers-bindings.md)。 函数通过多种方式来与绑定交互。

### <a name="inputs"></a>输入
在 Azure Functions 中，输入分为两种类别：一种是触发器输入，另一种则是附加输入。 函数可通过三种方式读取触发器和其他输入绑定（`direction === "in"` 的绑定）：
 - **_[建议]_ 以传递给函数的参数的形式。** 它们以与 function.json 中定义的顺序相同的顺序传递给函数。 *function.json* 中定义的 `name` 属性不需要与参数名称匹配，不过两者应该匹配。
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **以 [`context.bindings`](#contextbindings-property) 对象的成员的形式。** 每个成员由 *function.json* 中定义的 `name` 属性命名。
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **使用 JavaScript [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) 对象以输入的形式。** 这实质上与作为参数传递输入相同，但可以动态处理输入。
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Outputs
函数可通过多种方式写入输出（`direction === "out"` 的绑定）。 在所有情况下，*function.json* 中定义的绑定属性 `name` 对应于函数中所写入到的对象成员的名称。 

可通过以下方式之一将数据分配到输出绑定（不要结合使用这些方法）：

- **_[有多个输出时建议使用]_ 返回对象。** 如果使用异步函数/返回 Promise 的函数，可以返回分配有输出数据的对象。 在以下示例中，*function.json* 中的输出绑定名为“httpResponse”和“queueOutput”。

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  如果使用同步函数，可以使用 [`context.done`](#contextdone-method) 返回此对象（请参阅示例）。
- **_[有单个输出时建议使用]_ 直接返回值，并使用 $return 绑定名称。** 这仅适用于异步函数/返回 Promise 的函数。 请参阅[导出异步函数](#exporting-an-async-function)中的示例。 
- **向 `context.bindings` 赋值** 可以直接向 context.bindings 赋值。

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>绑定数据类型

若要定义输入绑定的数据类型，请使用绑定定义中的 `dataType` 属性。 例如，若要以二进制格式读取 HTTP 请求的内容，请使用类型 `binary`：

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType` 的选项为 `binary`、`stream` 和 `string`。

## <a name="context-object"></a>上下文对象

运行时使用 `context` 对象将数据传入和传出函数和运行时。 `context` 对象用于从绑定读取和设置数据并用于写入日志，它始终是传递到函数的第一个参数。

对于具有同步代码的函数，上下文对象包括在函数完成处理时调用的 `done` 回叫。 编写异步代码时，无需显式调用 `done`；`done` 回叫是隐式调用的。

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

传递到函数的上下文公开了一个 `executionContext` 属性，该属性是一个具有以下属性的对象：

| 属性名称  | 类型  | 说明 |
|---------|---------|---------|
| `invocationId` | String | 提供特定函数调用的唯一标识符。 |
| `functionName` | String | 提供正在运行的函数的名称 |
| `functionDirectory` | String | 提供函数应用目录。 |

以下示例演示如何返回 `invocationId`。

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>context.bindings 属性

```js
context.bindings
```

返回用于读取或分配绑定数据的命名对象。 可以通过读取 `context.bindings` 上的属性来访问输入和触发器绑定数据。 可以通过将数据添加到 `context.bindings` 来分配输出绑定数据

例如，function.json 中的以下绑定定义允许通过 `context.bindings.myInput` 访问队列的内容和使用 `context.bindings.myOutput` 将输出分配给队列。

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

可以选择使用 `context.done` 方法而不是 `context.binding` 对象来定义输出绑定数据（参阅下文）。

### <a name="contextbindingdata-property"></a>context.bindingData 属性

```js
context.bindingData
```

返回包含触发器元数据和函数调用数据（`invocationId`、`sys.methodName`、`sys.utcNow`、`sys.randGuid`）的命名对象。 有关触发器元数据的示例，请参阅此[事件中心示例](functions-bindings-event-hubs-trigger.md)。

### <a name="contextdone-method"></a>context.done 方法

同步方法使用 context.done 方法。

|同步执行|[异步](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function)执行<br>（节点 8+，Functions 运行时 2+）|
|--|--|
|需要：`context.done([err],[propertyBag])`，通知运行时函数已完成。 如果缺少它，则执行将会超时。<br>使用 `context.done` 方法可向运行时传回用户定义的错误，以及传回包含输出绑定数据的 JSON 对象。 传递给 `context.done` 的属性将覆盖 `context.bindings` 对象上设置的任何内容。|不需要：`context.done` - 隐式调用。| 


```javascript
// Synchronous code only
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log 方法  

```js
context.log(message)
```

利用该方法，可以在其他日志记录级别可用的情况下在默认跟踪级别写入到流式处理函数日志。 在下一部分中会详细说明跟踪日志记录。 

## <a name="write-trace-output-to-logs"></a>将跟踪输出写入到日志

在 Functions 中，可使用 `context.log` 方法将跟踪输出写入到日志和控制台。 在调用 `context.log()` 时，消息会在默认跟踪级别（即，信息跟踪级别）写入到日志。 Functions 与 Azure Application Insights 集成，从而能够更好地捕获函数应用日志。 Application Insights 是 Azure Monitor 的一部分，它提供了对应用程序遥测数据和跟踪输出都可以进行收集、视觉呈现和分析的工具。 若要了解详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。

下面的示例在信息跟踪级别写入日志，包括调用 ID：

```javascript
context.log("Something has happened. " + context.invocationId); 
```

所有 `context.log` 方法都支持 Node.js [util.format 方法](https://nodejs.org/api/util.html#util_util_format_format)支持的同一参数格式。 请考虑以下代码，它使用默认跟踪级别写入函数日志：

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

还可以采用以下格式编写同一代码：

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

> [!NOTE]  
> 请勿使用 `console.log` 来写入跟踪输出。 因为 `console.log` 的输出是在函数应用级别捕获的，所以它不会绑定到特定的函数调用，并且不会显示在特定函数的日志中。 此外，Functions 运行时的 1.x 版本不支持使用 `console.log` 写入到控制台。

### <a name="trace-levels"></a>跟踪级别

除了默认级别外，通过使用以下日志记录方法还可以在特定的跟踪级别写入函数日志。

| 方法                 | 说明                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | 将错误级别的事件写入到日志。   |
| **warn(_message_)**    | 将警告级别的事件写入到日志。 |
| **info(_message_)**    | 向信息级日志记录或更低级别进行写入。    |
| **verbose(_message_)** | 向详细级日志记录进行写入。           |

以下示例在警告跟踪级别（而不是信息级别）写入同一个日志：

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

因为 _错误_ 是最高跟踪级别，所以，只要启用了日志记录，此跟踪会在所有跟踪级别写入到输出中。

### <a name="configure-the-trace-level-for-logging"></a>配置跟踪级别以便进行日志记录

利用 Functions，可以定义阈值跟踪级别以便写入到日志或控制台。 特定的阈值设置取决于 Functions 运行时的版本。

# <a name="v2x"></a>[v2.x+](#tab/v2)

若要为写入到日志的跟踪设置阈值，请在 host.json 文件中使用 `logging.logLevel` 属性。 利用此 JSON 对象，可以为函数应用中的所有函数定义默认阈值，另外还可以为单个函数定义特定阈值。 若要了解详细信息，请参阅[如何配置对 Azure Functions 的监视](configure-monitoring.md)。

# <a name="v1x"></a>[v1.x](#tab/v1)

若要为写入到日志和控制台的所有跟踪设置阈值，请在 host.json 文件中使用 `tracing.consoleLevel` 属性。 此设置应用于 Function App 中的所有函数。 以下示例设置跟踪阈值来启用详细日志记录：

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

**consoleLevel** 的值对应于 `context.log` 方法的名称。 要为控制台禁用所有跟踪日志记录，请将 **consoleLevel** 设置为 _off_。 有关详细信息，请参阅 [host.json v1.x 参考](functions-host-json-v1.md)。

---

### <a name="log-custom-telemetry"></a>记录自定义遥测数据

默认情况下，Functions 将输出作为跟踪写入到 Application Insights。 为了加强控制，可以改用 [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js) 将自定义遥测数据发送到 Application Insights 实例。 

# <a name="v2x"></a>[v2.x+](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[v1.x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

`tagOverrides` 参数将 `operation_Id` 设置为函数的调用 ID。 通过此设置，可为给定的函数调用关联所有自动生成的遥测和自定义遥测。

## <a name="http-triggers-and-bindings"></a>HTTP 触发器和绑定

HTTP 和 webhook 触发器以及 HTTP 输出绑定使用请求和响应对象来表示 HTTP 消息。

### <a name="request-object"></a>请求对象

`context.req`（请求）对象具有以下属性：

| 属性      | 说明                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 一个包含请求正文的对象。               |
| _headers_     | 一个包含请求标头的对象。                   |
| _method_      | 请求的 HTTP 方法。                                |
| _originalUrl_ | 请求的 URL。                                        |
| _params_      | 一个包含请求的路由参数的对象。 |
| _query_       | 一个包含查询参数的对象。                  |
| _rawBody_     | 字符串形式的消息正文。                           |


### <a name="response-object"></a>响应对象

`context.res`（响应）对象具有以下属性：

| 属性  | 说明                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 一个包含响应正文的对象。         |
| _headers_ | 一个包含响应标头的对象。             |
| _isRaw_   | 指示是否为响应跳过格式设置。    |
| _status_  | 响应的 HTTP 状态代码。                     |
| _cookies_ | 在响应中设置的 HTTP cookie 对象的数组。 HTTP cookie 对象有 `name`、`value` 和其他 cookie 属性（如 `maxAge` 或 `sameSite`）。 |

### <a name="accessing-the-request-and-response"></a>访问请求和响应 

使用 HTTP 触发器时，可采用多种方式来访问 HTTP 响应和请求对象：

+ **通过 `context` 对象的 `req` 和 `res` 属性。** 采用此方式时，可以使用传统模式通过上下文对象访问 HTTP 数据，而不必使用完整的 `context.bindings.name` 模式。 以下示例展示了如何访问 `context` 上的 `req` 和 `res` 对象：

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **通过已命名的输入和输出绑定。** 采用此方式时，HTTP 触发器和绑定的工作方式与其他绑定相同。 以下示例使用已命名的 `response` 绑定设置响应对象： 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[仅响应]_ 通过调用 `context.res.send(body?: any)`。** HTTP 响应是使用输入 `body` 作为响应正文创建的。 隐式调用 `context.done()`。

+ **_[仅响应]_ 通过调用 `context.done()`。** 有一种特殊类型的 HTTP 绑定可返回传递到 `context.done()` 方法的响应。 以下 HTTP 输出绑定定义了一个 `$return` 输出参数：

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

请注意，请求和响应密钥采用小写形式。

## <a name="scaling-and-concurrency"></a>缩放和并发

默认情况下，Azure Functions 会自动监视应用程序上的负载，并按需为 Node.js 创建更多主机实例。 Functions 针对不同触发器类型使用内置（用户不可配置）阈值来确定何时添加实例，例如 QueueTrigger 的消息和队列大小。 有关详细信息，请参阅[消耗计划和高级计划的工作原理](event-driven-scaling.md)。

此缩放行为足以满足多个 Node.js 应用程序的需求。 对于占用大量 CPU 的应用程序，可使用多个语言工作进程进一步提高性能。

默认情况下，每个 Functions 主机实例都有一个语言工作进程。 使用 [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) 应用程序设置可增加每个主机的工作进程数（最多 10 个）。 然后，Azure Functions 会尝试在这些工作进程之间平均分配同步函数调用。 

FUNCTIONS_WORKER_PROCESS_COUNT 适用于 Functions 在横向扩展应用程序以满足需求时创建的每个主机。 

## <a name="node-version"></a>Node 版本

下表按操作系统显示了 Functions 运行时的每个主版本当前支持的 Node.js 版本：

| Functions 版本 | Node 版本 (Windows) | Node 版本 (Linux) |
|---|---| --- |
| 3.x（建议） | `~14`（推荐）<br/>`~12`<br/>`~10` | `node|14`（推荐）<br/>`node|12`<br/>`node|10` |
| 2.x  | `~12`<br/>`~10`<br/>`~8` | `node|10`<br/>`node|8`  |
| 1.x | 6.11.2（运行时锁定） | 不适用 |

可以通过从任何函数中记录 `process.version` 来查看运行时使用的当前版本。

### <a name="setting-the-node-version"></a>设置 Node 版本

对于 Windows 函数应用，通过将 `WEBSITE_NODE_DEFAULT_VERSION` [应用设置](functions-how-to-use-azure-function-app-settings.md#settings)设为受支持的 LTS 版本（例如 `~14`）来针对 Azure 中的版本进行操作。

对于 Linux 函数应用，请运行以下 Azure CLI 命令更新 Node 版本。

```bash
az functionapp config set --linux-fx-version "node|14" --name "<MY_APP_NAME>" --resource-group "<MY_RESOURCE_GROUP_NAME>"
```

若要详细了解 Azure Functions 运行时支持策略，请参阅[本文](./language-support-policy.md)

## <a name="dependency-management"></a>依赖项管理
若要在 JavaScript 代码中使用社区库（如下面的示例所示），需要确保在 Azure 中的 Function App 上安装所有依赖项。

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> 应当在 Function App 的根目录下定义一个 `package.json` 文件。 定义该文件将允许应用中的所有函数共享所缓存的相同包，从而获得最佳性能。 如果发生版本冲突，可以通过在具体函数的文件夹中添加一个 `package.json` 文件来解决冲突。  

部署过程中，从源控件中部署 Function App 时，存储库中存在的任何 `package.json` 文件都将在其文件夹中触发 `npm install`。 但在通过门户或 CLI 部署时，必须手动安装包。

可通过两种方法在 Function App 上安装包： 

### <a name="deploying-with-dependencies"></a>使用依赖项部署
1. 通过运行 `npm install` 在本地安装所有必需的包。

2. 部署代码，并确保部署中包含 `node_modules` 文件夹。 


### <a name="using-kudu"></a>使用 Kudu
1. 转到  `https://<function_app_name>.scm.azurewebsites.net` 。

2. 单击“调试控制台”，选择“CMD”。 > 

3. 转到 `D:\home\site\wwwroot`，然后将 package.json 文件拖到页面上半部分中的 **wwwroot** 文件夹上。  
    还可采用其他方式将文件上传到 Function App。 有关详细信息，请参阅[如何更新 Function App 文件](functions-reference.md#fileupdate)。 

4. 上传 package.json 文件后，在 **Kudu 远程执行控制台** 中运行 `npm install` 命令。  
    此操作将下载 package.json 文件中指定的包并重新启动 Function App。

## <a name="environment-variables"></a>环境变量

在本地环境和云环境中，向函数应用添加你自己的环境变量，如操作机密（连接字符串、密钥和终结点）或环境设置（例如分析变量）。 在函数代码中使用 `process.env` 访问这些设置。

### <a name="in-local-development-environment"></a>在本地开发环境中

在本地运行时，函数项目包括一个 [`local.settings.json` 文件](./functions-run-local.md)，你可以在其中将环境变量存储到 `Values` 对象中。 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "translatorTextEndPoint": "https://api.cognitive.microsofttranslator.com/",
    "translatorTextKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "languageWorkers__node__arguments": "--prof"
  }
}
```

### <a name="in-azure-cloud-environment"></a>在 Azure 云环境中

在 Azure 中运行时，函数应用允许你设置和使用[应用程序设置](functions-app-settings.md)（例如服务连接字符串），并在执行期间将这些设置作为环境变量公开。 

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="access-environment-variables-in-code"></a>在代码中访问环境变量

使用 `process.env` 将应用程序设置作为环境变量访问，如此处对 `context.log()` 的第二次和第三次调用所示，其中记录了 `AzureWebJobsStorage` 和 `WEBSITE_SITE_NAME` 环境变量：

```javascript
module.exports = async function (context, myTimer) {

    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

## <a name="ecmascript-modules-preview"></a><a name="ecmascript-modules"></a>ECMAScript 模块（预览）

> [!NOTE]
> 由于 ECMAScript 模块当前在 Node.js 14 标记为“试验”，因此在 Node.js 14 Azure Functions 中可将其作为预览功能提供。 在 Node.js 14 对 ECMAScript 模块的支持变为“稳定”之前，可能会对其 API 或行为进行更改。

[ECMAScript 模块](https://nodejs.org/docs/latest-v14.x/api/esm.html#esm_modules_ecmascript_modules)（ES 模块）是 Node.js 的新官方标准模块系统。 到目前为止，本文中的代码示例使用 CommonJS 语法。 在 Node.js 14 中运行 Azure Functions 时，可以选择使用 ES 模块语法编写函数。

若要在函数中使用 ES 模块，请将其文件名更改为使用 `.mjs` 扩展名。 下面的 index.mjs 文件示例是一个 HTTP 触发函数，它使用 ES 模块语法导入 `uuid` 库并返回值。

```js
import { v4 as uuidv4 } from 'uuid';

export default async function (context, req) {
    context.res.body = uuidv4();
};
```

## <a name="configure-function-entry-point"></a>配置函数入口点

`function.json` 属性 `scriptFile` 和 `entryPoint` 可用于配置导出函数的位置和名称。 转译 JavaScript 时，这些属性可能非常重要。

### <a name="using-scriptfile"></a>使用 `scriptFile`

默认情况下通过 `index.js`（与其对应的 `function.json` 共享相同父目录的文件）执行 JavaScript 函数。

`scriptFile` 可用于获取以下示例所示的文件夹结构：

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`myNodeFunction` 的 `function.json` 应包含 `scriptFile` 属性，该属性指向包含要运行的导出函数的文件。

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>使用 `entryPoint`

在 `scriptFile`（或 `index.js`）中，必须使用 `module.exports` 导出函数才能使其被找到和运行。 默认情况下，触发时执行的函数是该文件的唯一导出（导出名为 `run` 或 `index`）。

可以使用 `function.json` 中的 `entryPoint` 配置此项设置，如以下示例所示：

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

Functions v2.x 支持用户函数中的 `this` 参数，其中的函数代码可能如以下示例所示：

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

请在此示例中务必注意，尽管正在导出对象，但无法保证可保留两次执行之间的状态。

## <a name="local-debugging"></a>本地调试

使用 `--inspect` 参数启动时，Node.js 进程会在指定端口上侦听调试客户端。 在 Azure Functions 2.x 中，可以指定要传递到运行代码的 Node.js 进程中的参数，方法是添加环境变量或应用设置 `languageWorkers:node:arguments = <args>`。 

若要在本地进行调试，请在 [local.settings.json](./functions-develop-local.md#local-settings-file) 文件的 `Values` 下添加 `"languageWorkers:node:arguments": "--inspect=5858"`，然后将调试程序附加到端口 5858。

使用 VS Code 进行调试时，系统会使用项目的 launch.json 文件中的 `port` 值自动添加 `--inspect` 参数。

在版本 1.x 中，设置 `languageWorkers:node:arguments` 将无效。 可以在 Azure Functions Core Tools 中使用 [`--nodeDebugPort`](./functions-run-local.md#start) 参数来选择调试端口。

## <a name="typescript"></a>TypeScript

如果将目标限定为 2.x 版 Functions 运行时，可以在 [Azure Functions for Visual Studio Code](./create-first-function-cli-typescript.md) 和 [Azure Functions Core Tools](functions-run-local.md) 中使用支持 TypeScript 函数应用项目的模板创建函数应用。 该模板会生成 `package.json` 和 `tsconfig.json` 项目文件，以方便使用这些工具从 TypeScript 代码转译、运行和发布 JavaScript 函数。

生成的 `.funcignore` 文件用于指示将项目发布到 Azure 时会排除哪些文件。  

TypeScript 文件 (.ts) 转译为 `dist` 输出目录中的 JavaScript (.js) 文件。 TypeScript 模板使用 `function.json` 中的 [`scriptFile` 参数](#using-scriptfile)来指示 `dist` 文件夹中相应 .js 文件的位置。 模板使用 `tsconfig.json` 文件中的 `outDir` 参数设置输出位置。 如果更改此设置或文件夹的名称，则运行时将找不到要运行的代码。

在本地通过 TypeScript 项目进行开发和部署的方式取决于所用的开发工具。

### <a name="visual-studio-code"></a>Visual Studio Code

[Azure Functions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) 扩展允许使用 TypeScript 开发函数。 Azure Functions 扩展要求安装 Core Tools。

若要在 Visual Studio Code 中创建 TypeScript 函数应用，请在创建函数应用时选择 `TypeScript` 作为语言。

按下 **F5** 在本地运行应用时，会先执行转译，然后再初始化主机 (func.exe)。 

使用“部署到函数应用...”按钮将函数应用部署到 Azure 时，Azure Functions 扩展首先会基于 TypeScript 源文件生成一个可随时在生产环境中使用的 JavaScript 文件版本。

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

使用 Core Tools 时，TypeScript 项目与 JavaScript 项目有几种不同之处。

#### <a name="create-project"></a>创建项目

若要使用 Core Tools 创建 TypeScript 函数应用项目，必须在创建函数应用时指定 TypeScript 语言选项。 可通过以下方式之一执行此操作：

- 运行 `func init` 命令，选择 `node` 作为语言堆栈，然后选择 `typescript`。

- 运行 `func init --worker-runtime typescript` 命令。

#### <a name="run-local"></a>在本地运行

若要使用 Core Tools 在本地运行函数应用代码，请使用以下命令而不是 `func host start`： 

```command
npm install
npm start
```

`npm start` 命令等效于以下命令：

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>发布到 Azure

在使用 [`func azure functionapp publish`] 命令部署到 Azure 之前，请基于 TypeScript 源文件创建一个随时可在生产环境中使用的 JavaScript 文件版本。 

以下命令使用 Core Tools 准备和发布 TypeScript 项目： 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

在此命令中，将 `<APP_NAME>` 替换为函数应用的名称。

## <a name="considerations-for-javascript-functions"></a>JavaScript 函数的注意事项

使用 JavaScript 函数时，请注意以下各节中的注意事项。

### <a name="choose-single-vcpu-app-service-plans"></a>选择单 vCPU 应用服务计划

创建使用应用服务计划的函数应用时，建议选择单 vCPU 计划，而不是选择具有多个 vCPU 的计划。 目前，Functions 在单 vCPU VM 上运行 JavaScript 函数更为高效；使用更大的 VM 不会产生预期的性能提高。 需要时，可以通过添加更多单 vCPU VM 实例来手动横向扩展，也可以启用自动缩放。 有关详细信息，请参阅[手动或自动缩放实例计数](../azure-monitor/autoscale/autoscale-get-started.md?toc=/azure/app-service/toc.json)。

### <a name="cold-start"></a>冷启动

对于无服务器托管模型中开发 Azure Functions，冷启动已成为现实。 “冷启动”是指在函数应用处于非活动状态一段时间后进行第一次启动时，将需要较长时间才能启动。 具体而言，对于具有较大依赖项树的 JavaScript 函数，冷启动可能不足以解决问题。 为了加快冷启动过程，请尽量[以包文件的形式运行函数](run-functions-from-deployment-package.md)。 许多部署方法默认使用包模型中的运行，但如果遇到大规模的冷启动而不是以这种方式运行，则此项更改可以提供明显的改善。

### <a name="connection-limits"></a>连接限制

在 Azure Functions 应用程序中使用特定于服务的客户端时，不要在每次函数调用时都创建新的客户端。 而是，应在全局范围内创建单个静态客户端。 有关详细信息，请参阅[在 Azure Functions 中管理连接](manage-connections.md)。

### <a name="use-async-and-await"></a>使用 `async` 和 `await`

在 JavaScript 中编写 Azure Functions 时，应使用 `async` 和 `await` 关键字编写代码。 使用 `async` 和 `await` 编写代码，而不是使用回调或者结合约定使用 `.then` 和 `.catch`，将有助于避免两个常见问题：
 - 引发未经捕获的异常，从而导致 [Node.js 进程崩溃](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly)，并可能影响其他函数的执行。
 - 意外的行为，例如，未正常等待的异步调用导致 context.log 中缺少日志。

以下示例使用错误优先回调函数作为第二个参数调用异步方法 `fs.readFile`。 此代码会导致出现上述两个问题。 未在正确范围内显式捕获的异常导致整个进程崩溃（问题 #1）。 在回调函数范围以外调用 `context.done()` 意味着函数调用可能会在读取文件之前结束（问题 #2）。 在此示例中，过早调用 `context.done()` 导致缺少以 `Data from file:` 开头的日志条目。

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

使用 `async` 和 `await` 关键字有助于避免这两个错误。 应使用 Node.js 实用工具函数 [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) 将错误优先回调样式函数转换为可等待函数。

在以下示例中，在执行函数过程中引发的任何未经处理的异常只会导致引发异常的单个调用失败。 `await` 关键字表示只有在完成 `readFile` 后，才执行 `readFileAsync` 后面的步骤。 此外，如果使用 `async` 和 `await`，则无需调用 `context.done()` 回调。

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下资源：

+ [Azure Functions 最佳实践](functions-best-practices.md)
+ [Azure Functions 开发人员参考](functions-reference.md)
+ [Azure Functions 触发器和绑定](functions-triggers-bindings.md)

[`func azure functionapp publish`]: functions-run-local.md#project-file-deployment
