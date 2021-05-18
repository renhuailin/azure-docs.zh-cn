---
title: 适用于 Azure HDInsight 中 Storm 的 SCP.NET 编程指南
description: 了解如何通过 SCP.NET 创建可在 Azure HDInsight 中运行的 Storm 中使用的基于 .NET 的 Storm 拓扑。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: c993b3f70f609fb79c51ba9be08fa3d5dc7e8317
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864102"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Azure HDInsight 中 Apache Storm 的 SCP 编程指南

SCP 是用于生成实时、可靠、一致且高性能的数据处理应用程序的平台。 它在 [Apache Storm](https://storm.incubator.apache.org/) 之上生成，后者是由开源软件社区设计的流处理系统。 Nathan Marz 创建了 Storm。 它由 Twitter 以开源形式发布。 Storm 使用 [Apache ZooKeeper](https://zookeeper.apache.org/)，后者是另一个 Apache 项目，可实现高度可靠的分布式协调和状态管理。

SCP 项目不仅将 Storm 移植到 Windows 上，还移植了适用于 Windows 环境的项目添加扩展和自定义。 扩展包括 .NET 开发人员体验和 .NET 库。 自定义包括基于 Windows 的部署。

借助扩展和自定义，你无需创建开源软件项目分支。 可以使用在 Storm 之上生成的派生环境。

## <a name="processing-model"></a>处理模型

SCP 中的数据以连续的元组流形式建模。 通常情况下，元组：

1. 流入队列。
1. 由 Storm 拓扑中托管的业务逻辑选取和转换。
1. 要么将其输出作为元组通过管道传输到另一个 SCP 系统，要么提交到存储（如分布式文件系统）和数据库（如 SQL Server）。

:::image type="content" source="./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png" alt-text="将数据依次馈送到处理流程和数据存储的队列的关系图" border="false":::

在 Storm 中，应用程序拓扑定义计算图。 拓扑中的每个节点都包含处理逻辑。 节点之间的链接指示数据流。

将输入数据注入拓扑的节点称为 Spout。 可以使用它们对数据进行排序。 输入数据可能来自文件日志、事务性数据库或系统性能计数器等来源。

同时具有输入和输出数据流的节点称为 Bolt。 它们执行实际的数据筛选、选择和聚合。

SCP 支持“尽力”、“至少一次”和“恰好一次”数据处理。

在分布式流处理应用程序中，数据处理期间可能会发生错误。 此类错误包括网络中断、计算机故障或代码错误。 “至少一次”处理会在出现错误时自动重放原来的数据，从而确保所有数据均至少被处理一次。

“至少一次”处理简单且可靠，适用于许多应用程序。 但是，当应用程序需要确切计数时，进行“至少一次”处理是不够的，因为相同的数据可能在应用程序拓扑中重放。 在这种情况下，“恰好一次”处理可确保即使数据被多次重放和处理，结果也是正确的。

SCP 使 .NET 开发人员可以在将 Java 虚拟机 (JVM) 与 Storm 配合使用时创建实时数据处理应用程序。 JVM 和 .NET 通过 TCP 本地套接字进行通信。 每个 Spout/Bolt 都是一个 .NET/Java 进程对，其中用户逻辑作为插件在 .NET 进程中运行。

若要在 SCP 之上生成数据处理应用程序，请执行以下步骤：

1. 设计并实现 Spout，以从队列中提取数据。
1. 设计并实现用于处理输入数据并将其保存到数据库等外部存储的 Bolt。
1. 设计拓扑，然后提交并运行它。

拓扑定义顶点以及在它们之间流动的数据。 SCP 采用拓扑规范并将其部署到 Storm 群集上，在 Storm 群集中，每个顶点在一个逻辑节点上运行。 Storm 任务计划程序负责故障转移和缩放。

本文使用一些简单示例来介绍如何使用 SCP 生成数据处理应用程序。

## <a name="scp-plug-in-interface"></a>SCP 插件接口

SCP 插件是独立应用程序。 它们可以在开发期间在 Visual Studio 内部运行，并在生产部署后插入 Storm 管道。

编写 SCP 插件就像编写任何其他 Windows 控制台应用程序一样。 SCP.NET 平台为 Spout/Bolt 声明一些接口。 插件代码实现这些接口。 此设计的主要目的是让你专注于业务逻辑，同时让 SCP.NET 平台处理其他事项。

插件代码实现以下接口之一。 具体接口取决于拓扑是事务性的还是非事务性的，以及组件是 Spout 还是 Bolt。

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

ISCPPlugin 是许多插件的通用接口。目前，该接口是虚拟接口。

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

ISCPSpout 是适用于非事务性 Spout 的接口。

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

调用 NextTuple 时，C# 代码可能会发出一个或多个元组。 如果没有要发出的内容，则此方法应该返回，而不发出任何内容。

NextTuple、Ack 和 Fail 方法都在 C# 进程单一线程的紧凑循环中调用。 如果没有要发出的元组，请让 NextTuple 休眠一小段时间，如 10 毫秒。 这种休眠有助于避免浪费 CPU 可用性。

仅当规范文件启用确认机制时，才会调用 Ack 和 Fail 方法。 seqId 参数标识已确认或已失败的元组。 如果在非事务性拓扑中启用了确认，则应在 Spout 中使用以下 Emit 函数：

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

如果非事务性拓扑不支持确认，则可以将 Ack 和 Fail 保留为空函数。

这些函数中的 parms 输入参数指定空字典，并保留供以后使用。

### <a name="iscpbolt"></a>ISCPBolt

ISCPBolt 是适用于非事务性 Bolt 的接口。

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

有新的元组可用时，将调用 Execute 函数来处理元组。

### <a name="iscptxspout"></a>ISCPTxSpout

ISCPTxSpout 是适用于事务性 Spout 的接口。

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

与非事务性接口一样，NextTx、Ack 和 Fail 都在 C# 进程单一线程的紧凑循环中调用。 如果没有要发出的元组，请让 NextTx 休眠一小段时间，如 10 毫秒。 这种休眠有助于避免浪费 CPU 可用性。

调用 NextTx 开始新事务时，seqId 输出参数会标识该事务。 该事务还用于 Ack 和 Fail。 NextTx 方法可以将数据发出到 Java 端。 然后，数据会被存储在 ZooKeeper 中，以支持重用。 因为 ZooKeeper 的容量有限，所以代码应仅在事务性 Spout 中发出元数据，而不发出批量数据。

由于 Storm 自动重放失败的事务，通常不会调用 Fail。 但是，如果 SCP 可以检查事务性 Spout 发出的元数据，当元数据无效时，SCP 可以调用 Fail。

这些函数中的 parms 输入参数指定空字典，并保留供以后使用。

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

ISCPBatchBolt 是适用于事务性 Bolt 的接口。

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

当新的元组到达 Bolt 时，将调用 Execute 方法。 此事务结束时，将调用 FinishBatch 方法。 parms 输入参数保留供以后使用。

对于事务性拓扑，StormTxAttempt 是一个重要的类。 它拥有两个成员：TxId 和 AttemptId。 TxId 成员标识特定事务。 如果事务失败并重放，则可能会尝试多次。

SCP.NET 创建新的 ISCPBatchBolt 对象来处理每个 StormTxAttempt 对象，就像 Storm 在 Java 中所做的一样。 此设计旨在支持并行事务处理。 事务尝试完成后，将销毁相应的 ISCPBatchBolt 对象并回收所生产的垃圾。

## <a name="object-model"></a>对象模型

SCP.NET 还会提供可供开发人员用于编程的简单密钥对象集。 这些对象包括 Context、StateStore 和 SCPRuntime。 本部分将对其进行讨论。

### <a name="context"></a>上下文

Context 对象为应用程序提供运行环境。 ISCPSpout、ISCPBolt、ISCPTxSpout 或 ISCPBatchBolt 的每个 ISCPPlugin 实例都有相应的 Context 实例。 Context 提供的功能分为以下两个部分：

* 静态部分，在整个 C# 进程中都可用
* 动态部分，仅适用于特定的 Context 实例

### <a name="static-part"></a>静态部分

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

提供 Logger 对象用于日志记录目的。

pluginType 对象指示 C# 进程的插件类型。 如果进程在本地测试模式下运行（没有 Java），则插件类型是 SCP_NET_LOCAL。

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

Config 属性从 Java 端获取配置参数，并在初始化 C# 插件时传递这些参数。 Config 参数分为两个部分：stormConf 和 pluginConf。

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

stormConf 部分是 Storm 定义的参数，pluginConf 部分是 SCP 定义的参数。 下面是一个示例：

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

TopologyContext 类型获取拓扑上下文。 它最适用于多个并行组件。 下面是一个示例：

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>动态部分

以下接口与某个 Context 实例相关，该实例由 SCP.NET 平台创建并传递到代码：

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

对于支持确认的非事务性 Spout，提供以下方法：

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

支持确认的非事务性 Bolt 应使用收到的元组显式调用 Ack 或 Fail。 发出新的元组时，Bolt 还必须指定元组的定位点。 提供了以下方法：

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

StateStore 对象提供元数据服务、单调序列生成和无等待协调。 可以在 StateStore 上生成较高级别的分布式并发抽象。 这些抽象包括分布式锁、分布式队列、屏障和事务服务。

SCP 应用程序可以使用 State 对象来序列化 [Apache ZooKeeper](https://zookeeper.apache.org/) 中的信息。 此功能对于事务性拓扑特别有价值。 如果事务性 Spout 停止响应并重启，则 State 可以从 ZooKeeper 检索必要的信息并重启管道。

StateStore 对象具有以下主要方法：

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

State 对象具有以下主要方法：

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

将 simpleMode 设置为 true 时，Commit 方法将删除 ZooKeeper 中相应的 ZNode。 否则，该方法将删除当前 ZNode 并在 COMMITTED\_PATH 中添加新节点。

### <a name="scpruntime"></a>SCPRuntime

SCPRuntime 类提供以下两种方法：

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Initialize 方法初始化 SCP 运行时环境。 在此方法中，C# 进程会连接到 Java 端，以获取配置参数和拓扑上下文。

LaunchPlugin 方法启动消息处理循环。 在此循环中，C# 插件从 Java 端接收消息。 这些消息包括元组和控制信号。 然后，插件可能会通过调用代码提供的接口方法来处理消息。

LaunchPlugin 的输入参数是一个委托。 该方法可以返回实现 ISCPSpout、ISCPBolt、ISCPTxSpout 或 ISCPBatchBolt 接口的对象。

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

对于 ISCPBatchBolt，可以从 parms 参数获取 StormTxAttempt 对象，并使用它来判断尝试是否为重放尝试。 针对重放尝试的检查通常在提交 Bolt 中执行。 本文后面的 HelloWorldTx 示例演示了此检查。

SCP 插件通常可以在两种模式下运行：本地测试模式和常规模式。

#### <a name="local-test-mode"></a>本地测试模式

在此模式下，C# 代码中的 SCP 插件在开发阶段在 Visual Studio 内部运行。 可以在此模式下使用 ILocalContext 接口。 该接口提供将发出的元组序列化为本地文件并将其重新读取到 RAM 的方法。

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>常规模式

在此模式下，Storm Java 进程运行 SCP 插件。下面是一个示例：

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>拓扑规范语言

SCP 拓扑规范是一种特定于域的语言 (DSL)，用于描述和配置 SCP 拓扑。 它基于 [Storm 的 Clojure DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) 并通过 SCP 进行扩展。

可以通过 runSpec 命令将拓扑规范直接提交到 Storm 群集进行执行。

SCP.NET 添加了以下函数来定义事务性拓扑：

| 新建函数 | 参数 | 说明 |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |使用拓扑名称、Spout 定义图和 Bolt 定义图来定义事务性拓扑。 |
| **scp-tx-spout** |*exec-name*<br />*args*<br />*字段* |定义事务性 Spout。 该函数运行由 exec-name 指定的应用程序，并使用 args。<br /><br />fields 参数指定 Spout 的输出字段。 |
| **scp-tx-batch-bolt** |*exec-name*<br />*args*<br />*字段* |定义事务性批处理 Bolt。 该函数运行由 exec-name 指定的应用程序，并使用 args。<br /><br />fields 参数指定 Bolt 的输出字段。 |
| **scp-tx-commit-bolt** |*exec-name*<br />*args*<br />*字段* |定义事务性提交 Bolt。 该函数运行由 exec-name 指定的应用程序，并使用 args。<br /><br />fields 参数指定 Bolt 的输出字段。 |
| **nontx-topology** |*topology-name*<br />*spout-map*<br />*bolt-map* |使用拓扑名称、Spout 定义图和 Bolt 定义图来定义非事务性拓扑。 |
| **scp-spout** |*exec-name*<br />*args*<br />*字段*<br />*parameters* |定义非事务性 Spout。 该函数运行由 exec-name 指定的应用程序，并使用 args。<br /><br />fields 参数指定 Spout 的输出字段。<br /><br />parameters 参数是可选的。 使用它来指定“nontransactional.ack.enabled”等参数。 |
| **scp-bolt** |*exec-name*<br />*args*<br />*字段*<br />*parameters* |定义非事务性 Bolt。 该函数运行由 exec-name 指定的应用程序，并使用 args。<br /><br />fields 参数指定 Bolt 的输出字段<br /><br />parameters 参数是可选的。 使用它来指定“nontransactional.ack.enabled”等参数。 |

SCP.NET 定义以下关键字：

| 关键字 | 说明 |
| --- | --- |
| **:name** |拓扑名称 |
| **:topology** |使用上表中的函数和内置函数的拓扑 |
| **:p** |每个 Spout 或 Bolt 的并行提示 |
| **:config** |是配置参数还是更新现有参数 |
| **:schema** |数据流的架构 |

SCP.NET 还定义以下常用参数：

| 参数 | 说明 |
| --- | --- |
| "plugin.name" |C# 插件的 .exe 文件名 |
| "plugin.args" |插件参数 |
| "output.schema" |输出架构 |
| "nontransactional.ack.enabled" |是否为非事务性拓扑启用确认 |

runSpec 命令与位元一起部署。 以下是命令用法：

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

resource-dir 参数是可选的。 在想要插入 C# 应用程序时指定它。 指定的目录包含应用程序、依赖关系和配置。

*classpath* 参数也是可选参数。 如果规范文件包含 Java Spout 或 Bolt，则它指定 Java classpath。

## <a name="miscellaneous-features"></a>其他功能

### <a name="input-and-output-schema-declarations"></a>输入和输出架构声明

C# 进程可以发出元组。 为此，平台将元组序列化为 byte[] 对象，并将对象传输到 Java 端。 然后，Storm 将这些元组传输到目标。

在下游组件中，C# 进程从 Java 端重新接收元组并将其转换为平台的原始类型。 所有这些操作都被平台隐藏。

若要支持序列化和反序列化，代码需要声明输入和输出的架构。 架构定义为字典。 数据流 ID 是字典键。 键值为列的类型。 一个组件可以声明多个数据流。

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

以下函数已添加到 Context 对象中：

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

开发人员必须确保发出的元组遵循为数据流定义的架构。 否则，系统将引发运行时异常。

### <a name="multistream-support"></a>多数据流支持

SCP 允许用户代码同时向多个不同数据流发出元组或同时接收来自多个不同数据流的元组。 这种支持在 Context 对象中体现为 Emit 方法的可选数据流 ID 参数。

在 SCP.NET Context 对象中添加了两种方法， 它们向特定数据流发出一个或多个元组。 streamId 参数是一个字符串。 在 C# 代码和拓扑定义规范中，其值必须相同。

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

向不存在的数据流发出元组会导致运行时异常。

### <a name="fields-grouping"></a>字段分组

Storm 中的内置字段分组在 SCP.NET 中无法正常使用。 在 Java 代理端，所有字段的数据类型实际为 byte[]。 字段分组使用 byte[] 对象的哈希代码进行分组。 哈希代码是此对象在 RAM 中的地址。 因此，如果多字节对象共享相同的内容但地址不相同，分组会不正确。

SCP.NET 添加了自定义的分组方法，该方法会使用 byte[] 对象的内容来进行分组。 在规范文件中，语法如以下示例所示：

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

在以上规范文件中：

* `scp-field-group` 指定分组是 SCP 实现的自定义字段分组。
* `:tx` 或 `:non-tx` 指定拓扑是否为事务性。 你需要此信息，因为事务性拓扑和非事务性拓扑之间的起始索引不同。
* `[0,1]` 指定以零开头的字段 ID 的哈希集。

### <a name="hybrid-topology"></a>混合拓扑

本机 Storm 代码使用 Java 编写。 SCP.NET 增强了 Storm，使你可以编写 C# 代码来处理业务逻辑。 但 SCP.NET 还支持混合拓扑，这种拓扑不仅包含 C# Spout/Bolt，还包含 Java Spout/Bolt。

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>在规范文件中指定 Java Spout/Bolt

你可以在规范文件中使用 scp-spout 和 scp-bolt 来指定 Java Spout 和 Bolt。 下面是一个示例：

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

其中，`microsoft.scp.example.HybridTopology.Generator` 是 Java Spout 类的名称。

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>在 runSpec 命令中指定 Java classpath

如果要提交包含 Java Spout 或 Bolt 的拓扑，请先对其进行编译以生成 JAR 文件。 然后，在提交拓扑时指定包含 JAR 文件的 Java classpath。 下面是一个示例：

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

其中，`examples\HybridTopology\java\target\` 是包含 Java Spout/Bolt JAR 文件的文件夹。

### <a name="serialization-and-deserialization-between-java-and-c"></a>Java 和 C# 之间的序列化和反序列化

SCP 组件包括 Java 端和 C# 端。 若要与本机 Java Spout/Bolt 交互，必须在 Java 端和 C# 端之间进行序列化和反序列化，如下图所示：

:::image type="content" source="./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png" alt-text="Java 组件发送到 SCP 组件之后 SCP 组件再发送到另一个 Java 组件的关系图" border="false":::

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Java 端的序列化和 C# 端的反序列化

首先，默认在 Java 端进行序列化并在 C# 端进行反序列化。

在规范文件中指定 Java 端的序列化方法。

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

在 C# 代码中指定 C# 端的反序列化方法。

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

如果数据类型不是太复杂，这种默认实现应该能够应对大多数情况。 在以下情况下，可以插入自己的实现：

* 数据类型对于默认实现而言过于复杂。
* 默认实现的性能不符合要求。

Java 端的序列化接口定义为：

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

C# 端的反序列化接口定义为：

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>C# 端的序列化和 Java 端的反序列化

在 C# 代码中指定 C# 端的序列化方法。

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

在规范文件中指定 Java 端的反序列化方法。

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

其中，`"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` 是反序列化程序的名称，`"microsoft.scp.example.HybridTopology.Person"` 是数据反序列化到的目标类。

也可以插入自己的 C# 序列化程序和 Java 反序列化程序的实现。

此代码是 C# 序列化程序的接口：

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

此代码是 Java 反列化程序的接口：

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP 主机模式

在此模式下，可以将代码编译为 DLL，并使用 SCP 提供的 SCPHost.exe 提交拓扑。 规范文件如以下代码所示：

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

其中，`"plugin.name"` 被指定为 `"SCPHost.exe"`（由 SCP SDK 提供）。 SCPHost.exe 按以下顺序接受三个参数：

1. DLL 名称，在此示例中为 `"HelloWorld.dll"`。
1. 类名，在此示例中为 `"Scp.App.HelloWorld.Generator"`。
1. 公共静态方法的名称，调用此参数可获得 ISCPPlugin 的实例。

在主机模式下，将代码编译为 DLL，以供 SCP 平台调用。 因为平台随后可以完全控制整个处理逻辑，所以我们建议在 SCP 主机模式下提交拓扑。 这样做可以简化开发体验。 它还为你提供更高的灵活性以及与以后版本的更好后向兼容性。

## <a name="scp-programming-examples"></a>SCP 编程示例

### <a name="helloworld"></a>HelloWorld

以下 HelloWorld 示例是简单的 SCP.NET 示例。 它使用非事务性拓扑，带有一个名为 generator 的 Spout，以及两个分别名为 splitter 和 counter 的 Bolt。 generator Spout 会随机生成一些句子，并将这些句子发出到 splitter。 splitter Bolt 会将句子拆分为字词，并将这些字词发出到 counter Bolt。 counter Bolt 使用字典记录每个字词的出现次数。

此示例有两个规范文件：HelloWorld.spec 和 HelloWorld\_EnableAck.spec。通过从 Java 端获取 `pluginConf` 对象，C# 代码可以确定是否启用了确认。

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

如果在 Spout 中启用了确认，则字典将缓存尚未确认的元组。 如果调用了 `Fail`，则会重放失败的元组。

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

以下 HelloWorldTx 示例演示如何实现事务性拓扑。 该示例有一个名为 generator 的 Spout、一个名为 partial-count 的批处理 Bolt 以及一个名为 count-sum 的提交 Bolt。 该示例还有三个现有的文本文件：DataSource0.txt、DataSource1.txt 和 DataSource2.txt。

在每个事务中，generator Spout 从现有的三个文件中随机选择两个文件，并将这两个文件的名称发出到 partial-count Bolt。 partial-count Bolt：

1. 从收到的元组获取文件名。
1. 打开相应的文件。
1. 对文件中的字词进行计数。
1. 将字词计数发出到 count-sum Bolt。

count-sum Bolt 对总计数进行汇总。

为了实现“恰好一次”语义，count-sum Bolt 需要判断事务是否为重放事务。 在此示例中，它具有以下静态成员变量：

```csharp
public static long lastCommittedTxId = -1; 
```

创建 ISCPBatchBolt 实例后，它将从输入参数中获取 `txAttempt` 对象的值。

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

调用 `FinishBatch` 时，如果不是重放事务，则更新 `lastCommittedTxId`。

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

此拓扑包含一个 Java Spout 和一个 C# Bolt。 它使用 SCP 平台提供的默认序列化和反序列化实现。 有关规范文件的详细信息，请参阅 examples\\HybridTopology 文件夹中的 HybridTopology.spec 文件。 有关如何指定 Java classpath 的信息，另请参阅 SubmitTopology.bat。

### <a name="scphostdemo"></a>SCPHostDemo

本质上，此示例与 HelloWorld 相同。 唯一不同之处在于，代码被编译为 DLL，并使用 SCPHost.exe 提交拓扑。 有关更详细的说明，请参阅 SCP 主机模式部分。

## <a name="next-steps"></a>后续步骤

有关使用 SCP 创建的 Apache Storm 拓扑的示例，请参阅以下文章：

* [使用 Visual Studio 开发 Apache Storm on HDInsight 的 C# 拓扑](apache-storm-develop-csharp-visual-studio-topology.md)
* [使用 Apache Storm on HDInsight 从 Azure 事件中心处理事件](apache-storm-develop-csharp-event-hub-topology.md)
* [使用 Apache Storm on HDInsight 处理事件中心的车辆传感器数据](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [从 Azure 事件中心提取、转换和加载 (ETL) 到 Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
