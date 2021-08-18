---
title: Azure Functions Java 开发人员参考
description: 了解如何使用 Java 开发函数。
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fc827c8c03edecbdb10769f7830806ff01596bd1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742639"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 开发人员指南

本指南包含有助于成功使用 Java 开发 Azure Functions 的详细信息。

作为 Java 开发人员，如果不熟悉 Azure Functions，请考虑先阅读以下文章之一：

| 入门 | 概念| 
| -- | -- |  
| <ul><li>[使用 Visual Studio Code 的 Java 函数](./create-first-function-vs-code-java.md)</li><li>[使用终端/命令提示符的 Java/Maven 函数](./create-first-function-cli-java.md)</li><li>[使用 Gradle 的 Java 函数](functions-create-first-java-gradle.md)</li><li>[使用 Eclipse 的 Java 函数](functions-create-maven-eclipse.md)</li><li>[使用 IntelliJ IDEA 的 Java 函数](functions-create-maven-intellij.md)</li></ul> | <ul><li>[开发人员指南](functions-reference.md)</li><li>[托管选项](functions-scale.md)</li><li>[性能注意事项](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>Java 函数基础知识

Java 函数是一个 `public` 方法，使用注释 `@FunctionName` 进行修饰。 此方法定义了 Java 函数的输入，在特定包中必须唯一。 包可以有多个类，这些类具有使用 `@FunctionName` 进行批注的多个公共方法。 单个包在 Azure 中部署到函数应用。 在 Azure 中运行时，函数应用可为各个 Java 函数提供部署、执行和管理上下文。

## <a name="programming-model"></a>编程模型 

[触发器和绑定](functions-triggers-bindings.md)是 Azure Functions 的基本概念。 触发器启动代码的执行。 绑定可让你向函数传递数据以及从函数返回数据，而无需编写自定义的数据访问代码。

## <a name="create-java-functions"></a>创建 Java 函数

为了更轻松地创建 Java 函数，有一些基于 Maven 的工具和原型使用预定义的 Java 模板来帮助你创建包含特定函数触发器的项目。    

### <a name="maven-based-tooling"></a>基于 Maven 的工具

以下开发人员环境具有 Azure Functions 工具，可用于创建 Java 函数项目： 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

以上文章链接介绍了如何使用所选的 IDE 创建前几个函数。 

### <a name="project-scaffolding"></a>创建项目基架

如果更喜欢从终端进行命令行开发，那么要对基于 Java 的函数项目创建基架，最简单的方法是使用 `Apache Maven` 原型。 适用于 Azure Functions 的 Java Maven 原型发布在以下 groupId:artifactId 之下：[com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/) 。 

以下命令使用此原型生成新的 Java 函数项目：

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

---

若要开始使用此原型，请参阅 [Java 快速入门](./create-first-function-cli-java.md)。

## <a name="folder-structure"></a>文件夹结构

下面是某个 Azure Functions Java 项目的文件夹结构：

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

可使用共享的 [host.json](functions-host-json.md) 文件来配置函数应用。 每个函数都有自己的代码文件 (.java) 和绑定配置文件 (function.json)。

可在项目中放置多个函数。 不要将函数放入单独的 jar 中。 目标目录中的 `FunctionApp` 是部署到 Azure 中的函数应用的内容。

## <a name="triggers-and-annotations"></a>触发器和注释

 函数由触发器（例如 HTTP 请求、计时器或数据更新）调用。 函数需要处理该触发器和任何其他输入以生成一个或多个输出。

使用 [ com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 包中附带的 Java 注释将输入和输出绑定到方法。 有关详细信息，请参阅 [Java 参考文档](/java/api/com.microsoft.azure.functions.annotation)。

> [!IMPORTANT] 
> 必须在 [local.settings.json](./functions-develop-local.md#local-settings-file) 中配置一个 Azure 存储帐户，才能在本地运行 Azure Blob 存储、Azure 队列存储或 Azure 表存储触发器。

示例：

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

下面是 [azure-functions-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin) 生成的相应 `function.json`：

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "GET","POST" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="java-versions"></a>Java 版本

创建函数应用（用于在 Azure 中运行函数）时使用的 Java 版本在 pom.xml 文件中指定。 Maven 原型当前为 Java 8（可以在发布之前进行更改）生成 pom.xml。 pom.xml 中的 Java 版本应该与在本地对其开发和测试应用的版本相匹配。 

### <a name="supported-versions"></a>支持的版本

下表按操作系统显示了 Functions 运行时的每个主版本当前支持的 Java 版本：

| Functions 版本 | Java 版本 (Windows) | Java 版本 (Linux) |
| ----- | ----- | --- |
| 3.x | 11 <br/>8 | 11 <br/>8 |
| 2.x | 8 | 不适用 |

除非为部署指定 Java 版本，否则在部署到 Azure 期间，Maven 原型默认为 Java 8。

### <a name="specify-the-deployment-version"></a>指定部署版本

可以使用 `-DjavaVersion` 参数来控制 Maven 原型的目标 Java 版本。 该参数的值可以是 `8` 或 `11`。 

Maven 原型生成面向指定 Java 版本的 pom.xml。 pom.xml 文件中的以下元素指示要使用的 Java 版本：

| 元素 |  Java 8 值 | Java 11 值 | 描述 |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | maven-compiler-plugin 使用的 Java 版本。 |
| **`JavaVersion`** | 8 | 11 | Azure 中的函数应用托管的 Java 版本。 |

以下示例演示 pom.xml 文件的相关部分中用于 Java 8 的设置：

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> 必须将 JAVA_HOME 环境变量正确设置为在使用 Maven 编译代码期间使用的 JDK 目录。 确保 JDK 的版本至少与 `Java.version` 设置一样高。 

### <a name="specify-the-deployment-os"></a>指定部署操作系统

Maven 还允许指定用于在 Azure 中运行函数应用的操作系统。 使用 `os` 元素选择操作系统。 

| 元素 |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | windows | linux | docker |

以下示例显示了 pom.xml 文件的 `runtime` 部分中的操作系统设置：

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>JDK 运行时可用性和支持 

若要进行本地 Java 函数应用开发，请从 Azul Systems 下载并使用相应的[适用于 Azure 的 Azul Zulu Enterprise](https://www.azul.com/downloads/azure-only/zulu/) Java JDK。 将函数应用部署到云时，Azure Functions 使用 Azul Java JDK 运行时。

对于 JDK 和函数应用的问题，[Azure 支持](https://azure.microsoft.com/support/)可通过[限定的支持计划](https://azure.microsoft.com/support/plans/)获得。

## <a name="customize-jvm"></a>自定义 JVM

在 Functions 中可以自定义用于运行 Java 函数的 Java 虚拟机 (JVM)。 默认使用[以下 JVM 选项](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)。

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

可在名为 `JAVA_OPTS` 的应用设置中提供其他参数。 可在 Azure 门户或 Azure CLI 中将应用设置添加到部署给 Azure 的函数应用。

> [!IMPORTANT]  
> 在消耗计划中，还必须添加值为 0 的 WEBSITE_USE_PLACEHOLDER 设置，才能使自定义操作生效。 此设置将增加 Java 函数的冷启动时间。

### <a name="azure-portal"></a>Azure 门户

在 [Azure 门户](https://portal.azure.com)中，使用[“应用程序设置”选项卡](functions-how-to-use-azure-function-app-settings.md#settings)添加 `JAVA_OPTS` 设置。

### <a name="azure-cli"></a>Azure CLI

可使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令设置 `JAVA_OPTS`，如下例中所示：

# <a name="consumption-plan"></a>[消耗计划](#tab/consumption/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    "WEBSITE_USE_PLACEHOLDER=0" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="consumption-plan"></a>[消耗计划](#tab/consumption/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    "WEBSITE_USE_PLACEHOLDER=0" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[专用计划/高级计划](#tab/dedicated+premium/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[专用计划/高级计划](#tab/dedicated+premium/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

---

此示例将启用无外设模式。 将 `<APP_NAME>` 替换为函数应用的名称，将 `<RESOURCE_GROUP>` 替换为资源组。 

## <a name="third-party-libraries"></a>第三方库 

Azure Functions 支持使用第三方库。 默认情况下，项目 `pom.xml` 文件中指定的所有依赖项将在 [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) 目标期间自动进行绑定。 对于未在 `pom.xml` 文件中指定为依赖项的库，请将它们放在函数根目录的 `lib` 目录中。 放置在 `lib` 目录中的依赖项将在运行时添加到系统类加载器中。

默认情况下，类路径上提供了 `com.microsoft.azure.functions:azure-functions-java-library` 依赖项，不需要将其包含在 `lib` 目录中。 此外，[azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) 会将[此处](https://github.com/Azure/azure-functions-java-worker)列出的依赖项添加到类路径。

## <a name="data-type-support"></a>数据类型支持

可以使用无格式普通 Java 对象 (POJO)、`azure-functions-java-library` 中定义的类型或字符串、整数等基元数据类型绑定到输入或输出绑定。

### <a name="pojos"></a>POJO

[azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) 使用 [gson](https://github.com/google/gson) 库将输入数据转换为 POJO。 用作函数输入的 POJO 类型应是 `public`。

### <a name="binary-data"></a>Binary data

通过将 function.json 中的 `dataType` 字段设置为 `binary`，将二进制输入或输出绑定到 `byte[]`：

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

如果需要 null 值，请使用 `Optional<T>`。

## <a name="bindings"></a>绑定

输入和输出绑定提供从代码内连接到数据的声明性方式。 一个函数可以有多个输入和输出绑定。

### <a name="input-binding-example"></a>输入绑定示例

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData,
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

结合 HTTP 请求调用此函数。 
- 以 `String` 的形式为参数 `inputReq` 传递 HTTP 请求有效负载。
- 从表存储中检索一个项，并将其作为 `TestInputData` 传递给参数 `inputData`。

若要接收一批输入，可绑定到 `String[]`、`POJO[]`、`List<String>` 或 `List<POJO>`。

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

每当配置的事件中心内出现新数据时，就会触发此函数。 由于 `cardinality` 设置为 `MANY`，该函数将从事件中心接收一批消息。 来自事件中心的 `EventData` 将转换为函数执行的 `TestEventData`。

### <a name="output-binding-example"></a>输出绑定示例

可以使用 `$return` 将输出绑定绑定到返回值。 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

如果有多个输出绑定，请只使用其中一个绑定的返回值。

若要发送多个输出值，请使用 `azure-functions-java-library` 包中定义的 `OutputBinding<T>`。 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

针对 HttpRequest 调用此函数。 它会将多个值写入到队列存储。

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 和 HttpResponseMessage

 这些对象在 `azure-functions-java-library` 中定义。 它们是与 HttpTrigger 函数配合使用的帮助器类型。

| 专用类型      |       目标        | 典型用途                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 触发器     | 获取方法、标头或查询 |
| `HttpResponseMessage` | HTTP 输出绑定 | 返回除 200 以外的状态   |

## <a name="metadata"></a>元数据

少量的触发器会连同输入数据一起发送[触发器元数据](./functions-triggers-bindings.md)。 可使用注释 `@BindingName` 绑定到触发器元数据。


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
在前面的示例中，`queryValue` 绑定到 HTTP 请求 URL `http://{example.host}/api/metadata?name=test` 中的查询字符串参数 `name`。 下面是另一个示例，它展示了如何从队列触发器元数据绑定到 `Id`。

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> 在注释中提供的名称需与元数据属性相匹配。

## <a name="execution-context"></a>执行上下文

`azure-functions-java-library` 中定义的 `ExecutionContext` 包含用来与 Functions 运行时通信的帮助器方法。 有关详细信息，请参阅 [ExecutionContext 参考文章](/java/api/com.microsoft.azure.functions.executioncontext)。

### <a name="logger"></a>记录器

使用 `ExecutionContext` 中定义的 `getLogger` 从函数代码写入日志。

示例：

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>查看日志和跟踪

可以使用 Azure CLI 来流式传输 Java stdout 和 stderr 日志记录以及其他应用程序日志记录。 

下面说明了如何使用 Azure CLI 将函数应用配置为写入应用程序日志：

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

---

若要使用 Azure CLI 流式传输函数应用的日志记录输出，请打开新的命令行提示符、Bash 或终端会话，并输入以下命令：

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

---

[az webapp log tail](/cli/azure/webapp/log) 命令可使用 `--provider` 选项筛选输出。 

若要使用 Azure CLI 下载单个 ZIP 文件形式的日志文件，请打开新的命令提示符、Bash 或终端会话，并输入以下命令：

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

运行此命令之前，必须已在 Azure 门户或 Azure CLI 中启用了文件系统日志记录。

## <a name="environment-variables"></a>环境变量

在 Functions 中，服务连接字符串等[应用设置](functions-app-settings.md)在执行过程中将公开为环境变量。 可使用 `System.getenv("AzureWebJobsStorage")` 访问这些设置。

以下示例获取了[应用程序设置](functions-how-to-use-azure-function-app-settings.md#settings)，其中键名为 `myAppSetting`：

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> 对于优化的冷启动体验，AppSetting FUNCTIONS_EXTENSION_VERSION 的值应为 ~2 或 ~3。

## <a name="next-steps"></a>后续步骤

有关 Azure Functions Java 开发的详细信息，请参阅以下资源：

* [Azure Functions 最佳实践](functions-best-practices.md)
* [Azure Functions 开发人员参考](functions-reference.md)
* [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
* 使用 [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、[IntelliJ](functions-create-maven-intellij.md) 和 [Eclipse](functions-create-maven-eclipse.md) 的本地开发和调试
* [使用 Visual Studio Code 远程调试 Azure 函数](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [适用于 Azure Functions 的 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* 通过 `azure-functions:add` 目标简化函数创建并准备临时目录以用于 [ZIP 文件部署](deployment-zip-push.md)。
