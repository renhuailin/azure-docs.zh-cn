---
title: 使用工业 IoT 将数据拉取到 Azure 数据资源管理器
description: 本教程介绍如何将 IIoT 数据移入 ADX，并创建一个仪表板来查看这些数据。
author: v-condav
ms.author: v-condav
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 6/16/2021
ms.openlocfilehash: 4ab409c067d83e44cb22c3a22589eca0a4a7785a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291288"
---
# <a name="tutorial-using-industrial-iot-to-pull-data-into-azure-data-explorer"></a>教程：使用工业 IoT 将数据拉取到 Azure 数据资源管理器

Azure 工业 IoT (IIoT) 平台将边缘模块和云微服务与 Azure 平台即服务 (PaaS) 服务相结合，为工业资产发现和资产数据采集提供所需的功能。 在工业环境中，这种任务的实际标准是 OPC UA。 本教程通过一个简单用例（即基于云的状态监视）来演示如何将 Azure 配置为使用 OPC UA。

在本教程中，您将学习如何执行以下操作：
- 获取 Azure 帐户并部署 IoT 中心。
- 部署 IoT Edge
- 安装并配置 OPC 发布服务器
- 设置时序数据库。
- 创建仪表板。
- 创建传入数据的图表。

## <a name="requirements"></a>要求

在开始学习本教程之前，需要具备一个 Azure 帐户。 如果你没有 Azure 帐户，可[单击此处](https://azure.microsoft.com/free/)免费创建一个。

## <a name="deploy-an-s1-iot-hub"></a>部署 S1 IoT 中心

对于 Azure 订阅，需要执行的第一项操作就是转到[门户](../azure-portal/azure-portal-quickstart-center.md)并[部署 S1 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 免费 Azure 帐户允许每天处理 40 万条 4K 大小的消息。 由于每条 4K 大小的消息中可以容纳大约 15 个 OPC UA PubSub 数据标记，因此每天可以装入 600 万个数据标记，这对于本用例而言已足够。 部署 IoT 中心后，复制 iothubowner 主连接字符串（共享访问策略下的 iothubowner）。 在安装 IoT Edge 时需要用到此连接字符串。

## <a name="deploy-iot-edge"></a>部署 IoT Edge

使用 [IoT Edge 安装程序](https://lnkd.in/ga6Ew4X)在 Windows 10 电脑或 VM 上部署 IoT Edge。 此电脑/VM 需已连接到 Internet，并可以访问你要使用的计算机或工业连接适配器软件（dataFEED、Zenon、Kepware 等）。 如果你的设施使用多个网络层从 OT 网络（计算机所在的位置）连接到 Internet，则你可以嵌套 IoT Edge 设备（即建立“菊花链”）。 还可以使用 [GitHub 上](https://github.com/Azure/Industrial-IoT-Gateway-Installer/blob/master/Releases/Linux.zip)的安装程序在 Linux 本地运行 IoT Edge。

运行 IoT Edge 安装程序时，请选中“使用 Docker Desktop”复选框，将“为 Azure 工业云平台(PaaS)配置 IoT Edge”单选按钮保持选中状态，然后将 iothubowner 连接字符串粘贴到提供的文本框中 。 安装程序会自动预先填充正在运行的电脑的名称作为 Edge 实例名称，但你可以根据需要更改此名称。 点击“安装”。 如果未检测到 Docker Desktop，安装程序会自动下载它。 安装该软件，并确保为其配置适用于 Linux 的 Windows 子系统 V2 (WSL2) 以获得最佳性能。 Docker Desktop 安装完成后，重启 IoT Edge 安装程序，再次对其进行配置，然后再次点击“安装”。 然后在 Azure 门户上的“IoT 中心”页中转到“IoT Edge”选项卡，等待新安装的 IoT Edge 设备显示出来（可能需要点击“刷新”多次） 。

## <a name="install-and-configure-opc-publisher"></a>安装并配置 OPC 发布服务器

接下来，安装并配置 OPC 发布服务器，用于通过 Edge 上的 PubSub OPC UA 来标准化和规范化所有遥测数据。 我们将使用 OPC UA JSON 编码，因为这种编码格式可以直接引入到云数据库，而无需先使用单独的云服务将二进制格式转换回到云数据库可识别的格式。 使用这种行业标准格式可以减少云中的管理开销和成本。 这里有一个要点：许多人会告诉你他们找到了更有效的遥测数据编码格式，但他们却总是低估大规模地将遥测数据转换为随时可在云数据库中使用的格式所带来的成本和管理工作量。 

现在，转到 OPC 发布服务器的“入门”页并按照说明进行操作。 对于“容器创建选项”，请指定以下设置。

```
{
    "Hostname": "OPCPublisher",
    "Cmd": [
        "PkiRootPath=/appdata/pki", 
        "--lf=/appdata/publisher.log", 
        "--pf=/appdata/publishednodes.json",
        "--aa",
        "--di=60",
        "--me=Json",
        "--mm=PubSub"
    ],
    "HostConfig": {
        "Binds": [
            "/c/IoTEdgeMapping:/appdata"
        ]
    }
}
```

接下来，从你的计算机（或连接适配器软件）中配置用于生成云时序遥测消息的 OPC UA 数据节点。 为此，请将此处的模板 publishednodes.json 文件复制到 C:\IoTEdgeMapping，并在其中填充要发送到云的数据的 OPC UA 服务器 EndpointURL 和 OPC UA 节点 ID。*__* 如果你不知道要发送哪些数据，请注意 ID 为 ns=0;i=2258 的数据节点是服务器的当前时间（每秒都会更改）。 仅当数据已更改时，OPC UA 才会发布数据更改。 但是，OPC 发布服务器还支持按特定的间隔读取和发送数据（即使数据未更改）。

编辑完该文件后，使用命令行命令“iotedge restart OPCPublisher”重启 OPC 发布服务器，然后，OPC 发布服务器将尝试连接到指定的所有 OPC UA 服务器并发布所有列出的节点。 我们的经验表明，应该先在运行 IoT Edge 的电脑中使用某个 OPC UA 客户端（例如 UA Expert）来确保可以连接到 OPC UA 服务器，并尝试在 EndpointURL 中使用 OPC UA 服务器的 IP 地址而不是主机名，以防 DNS 不能正常工作。 现在，返回到 Azure 门户中的“IoT 中心”页，并仔细检查“概述”选项卡中是否正在接收“设备到云”消息  。

## <a name="set-up-the-time-series-database"></a>设置时序数据库

现在，在云中设置接收 OPC UA 遥测数据的时序数据库。 最好是使用 Azure 数据资源管理器来执行此操作，因为它使用多个数据库层来实现最佳性能，并附带丰富的分析和仪表板功能，甚至还支持预测和异常情况检测。 通过 Azure 门户部署一个实例，选择开发/测试工作负载 SKU，另外请记得在配置中启用流引入。

OPC UA 凭借 OPC UA 服务器的 DatasetWriterID 和 ExpandedNodeID（包含命名空间和节点 ID）唯一标识遥测数据标记 。 此外，在数据提供给连接的客户端使用之前由 OPC UA 服务器读取这些数据时的时间戳称为 SourceTimeStamp。 因此，若要创建 OPC UA 遥测数据的时序，我们需要在数据库表中提供 DatasetWriterID、ExpandedNodeID、读取的数据值和 SourceTimeStamp  。 这是你现在要使用 Azure 数据资源管理器创建的内容。 请注意，数据值类型（float、int 等）也可以包含在 OPC UA PubSub 消息中；在 OPC UA 规范中，这称为可逆编码。

首先，使用默认的 10 年数据保留期（此期限对于本用例而言应已足够）创建一个数据库。 然后，点击“创建数据连接”并选择“IoT 中心”以配置直接从 IoT 中心引入流数据，为连接命名，然后从下拉列表中选择你的 IoT 中心 。 在“共享访问策略”下选择“iothubowner”，为“使用者组”选择“$Default”。 将“表名称”暂时留空，但在“数据格式”下选择“多行 JSON”，并在“映射名称”中输入“opcua_mapping”。 选择“创建”。

## <a name="create-the-database-tables"></a>创建数据库表

在新数据库中创建所需的表。 首先创建包含单个列的临时表，以接收原始的 OPC UA PubSub JSON 遥测消息。 Azure 数据资源管理器使用此处所述的内置 Kusto 查询语言 (KQL)。 选择“查询”选项卡并输入以下内容。

```
.create table opcua_raw(payload: dynamic)

```

运行以下命令，以创建一个指向使用前面设置的 IoT 中心的 opcua_mapping 遥测数据引入的映射。

```
.create table opcua_raw ingestion json mapping "opcua_mapping" @'[{"column":"payload","path":"$","datatype":"dynamic"}]'

```

使用刚刚创建的表的名称更新遥测数据引入，方法是选择“数据引入”选项卡，选择你的数据引入名称，选择“编辑”，在“表名称”中输入“opcua_raw”，然后点击“更新”  。 然后返回“查询”选项卡并等待几分钟，让前几条遥测消息进入表中。 可以通过查询该表中的 10 行记录来检查这些消息。

```
opcua_raw
| take 10
```

OPC UA PubSub 支持使用数据集（由 DataSetWriterID 标识）将遥测数据批处理到一条消息中。 需将这些数据集“拆分”为数据库中单独的行。 为此，可以使用 mv-expand 运算符来扩展引入到需要先创建的新中间表中的 JSON。

```
.create table opcua_intermediate(DataSetWriterID: string, payload: dynamic)
```

现在，请创建以下函数来执行扩展。

```
.create-or-alter function OPCUARawExpand() {
    opcua_raw
    |mv-expand records = payload.Messages
    |project 
        DataSetWriterID = tostring(records["DataSetWriterId"]),
        Payload = todynamic(records["Payload"])
}
```

然后通过应用新函数来更新中间表。

```
.alter table opcua_intermediate policy update @'[{"Source": "opcua_raw", "Query": "OPCUARawExpand()", "IsEnabled": "True"}]'
```

检查是否正确填充了新表。

```
opcua_intermediate
| take 10
```

 创建最后一个 OPC UA 遥测数据表，并通过进一步扩展 OPC UA 数据集中的每个条目来填充该表。

```
.create table opcua_telemetry (DataSetWriterID: string, ExpandedNodeID: string, Value: dynamic, SourceTimestamp: datetime)
```

创建如下所示的函数。

```
.create-or-alter function OPCUADatasetExpand() {
    opcua_intermediate
    | mv-apply payload on (
        extend key = tostring(bag_keys(payload)[0])
        | extend p = payload[key]
        | project ExpandedNodeId = key, Value = todynamic(p.Value), SourceTimestamp = todatetime(p.SourceTimestamp)
    )
}
```

通过应用新函数来更新第三个和最后一个表。

```
.alter table opcua_telemetry policy update @'[{"Source": "opcua_intermediate", "Query": "OPCUADatasetExpand()", "IsEnabled": "True"}]'
```

若要查看 OPC UA 遥测数据表，请使用以下命令。

```
opcua_telemetry
| take 10
```

## <a name="create-line-graph-of-the-data"></a>创建数据的折线图

现在，让我们通过将所有数据强制转换为浮点数来创建数据的折线图。 如果强制转换失败，则会忽略数据。 使用托管的 Azure 数据资源管理器仪表板执行此操作。 单击“在 Web UI 中打开”，在新窗口中单击“仪表板”，然后依次选择“创建新仪表板”、“添加磁贴”   。 然后选择“数据源”，并以 https://<YourInstanceName>.<Your RegionName>.kusto.windows.net 格式输入 Azure 数据资源管理器实例的名称。 选择你的数据库并点击“应用”。 然后输入以下查询。

```
opcua_telemetry
| project SourceTimestamp, yaxis = todouble(Value), DataSetWriterID,  ExpandedNodeID
```

点击“视觉对象”选项卡，为“Y 列”选择“yaxis”，为“X 列”选择“SourceTimeStamp”，为“时序列”选择“DataSetWriterID”。

现在，已为云中的状态监视用例数据创建了一个精致的遥测数据仪表板。