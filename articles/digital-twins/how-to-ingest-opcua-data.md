---
title: 使用 Azure 数字孪生引入 OPC UA 数据
titleSuffix: Azure Digital Twins
description: 将 Azure OPC UA 数据引入 Azure 数字孪生的步骤
author: danhellem
ms.author: dahellem
ms.date: 8/27/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: be6d4dd328d3b8cd0f02bede7bcba7b6a4e32ae9
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534531"
---
# <a name="ingesting-opc-ua-data-with-azure-digital-twins"></a>使用 Azure 数字孪生引入 OPC UA 数据

[OPC 统一体系结构 (OPC UA)](https://opcfoundation.org/about/opc-technologies/opc-ua/) 是一种独立于平台的面向服务的体系结构，适用于制造领域。 它用于从设备获取遥测数据。 

要使 OPC UA 服务器数据流入 Azure 数字孪生，需要在不同设备上安装的多个组件，还需要配置一些自定义代码和设置。 

本文演示了如何将所有这些部分连接在一起，以将 OPC UA 节点加入 Azure 数字孪生中。 可以继续根据本指南构建自己的解决方案。

> [!NOTE]
> 本文不解决将 OPC UA 节点转换为 DTDL 的问题。 它仅解决将遥测数据从 OPC UA 服务器引入现有 Azure 数字孪生的问题。 如果有兴趣从 OPC UA 数据生成 DTDL 模型，请访问 [UANodeSetWebViewer](https://github.com/barnstee/UANodesetWebViewer) 和 [OPCUA2DTDL](https://github.com/khilscher/OPCUA2DTDL) 存储库。

## <a name="prerequisites"></a>先决条件

若要完成本文，请完成以下先决条件：
* **下载示例存储库**：本文使用一个 [DTDL 模型](concepts-models.md)文件和 [“OPC UA 到 Azure 数字孪生”GitHub 存储库](https://github.com/Azure-Samples/opcua-to-azure-digital-twins)中的一个 Azure 函数体。 首先，将示例存储库下载到计算机上。 可以选择存储库的“代码”按钮，克隆存储库或将其作为 .zip 文件下载到计算机。

    :::image type="content" source="media/how-to-ingest-opcua-data/download-repo.png" alt-text="GitHub 上 digital-twins-samples 存储库的屏幕截图，突出显示了克隆或下载代码的步骤。" lightbox="media/how-to-ingest-opcua-data/download-repo.png":::
    
    如果将存储库下载为 .zip，请务必解压缩并提取文件。
* **下载 Visual Studio**：本文使用 Visual Studio 发布 Azure 函数。 可以从 [Visual Studio 下载](https://visualstudio.microsoft.com/downloads/)中下载最新版本的 Visual Studio。

## <a name="architecture"></a>体系结构

以下是将包含在此解决方案中的组件。

 :::image type="content" source="media/how-to-ingest-opcua-data/opcua-to-adt-diagram-1.png" alt-text="“opc ua 到 Azure 数字孪生”体系结构的图像" lightbox="media/how-to-ingest-opcua-data/opcua-to-adt-diagram-1.png":::    

| 组件 | 说明 |
| --- | --- |
| OPC UA 服务器 | [ProSys](https://www.prosysopc.com/products/opc-ua-simulation-server/) 或 [Kepware](https://www.kepware.com/en-us/products/#KEPServerEX) 中的 OPC UA 服务器，用于模拟 OPC UA 数据。 |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) | IoT Edge 是安装在本地 Linux 网关设备的 IoT 中心服务。 OPC 发布服务器模块需要运行数据并将其发送到 IoT 中心。 |
| [OPC 发布服务器](https://github.com/Azure/iot-edge-opc-publisher) | 此组件是由 Azure 工业 IoT 团队构建的 IoT Edge 模块。 此模块连接到 OPC UA 服务器并将节点数据发送到 Azure IoT 中心。 |
| [Azure IoT 中心](../iot-hub/about-iot-hub.md) | OPC 发布服务器将 OPC UA 遥测数据发送到 Azure IoT 中心。 从那里，可以通过 Azure 函数处理数据并将数据引入 Azure 数字孪生。 |
| Azure 数字孪生 | 通过该平台，可以创建真实世界事物、地点、业务流程和人的数字表示形式。 |
| [Azure 函数](../azure-functions/functions-overview.md) | 自定义 Azure 函数用于处理在 Azure IoT 中心流向 Azure 数字孪生中适当孪生体和属性的遥测数据。 |

## <a name="set-up-edge-components"></a>设置边缘组件

第一步是在边缘设置设备和软件。 下面是要设置的边缘组件，顺序如下：
1. [OPC UA 模拟服务器](#set-up-opc-ua-server)
1. [IoT 中心和 IoT Edge 设备](#set-up-iot-edge-device)
1. [网关设备](#set-up-gateway-device)

本部分将逐步讲解每个组件的简要设置。 

有关安装其中每个组件的详细信息，请参阅以下资源：
* [在 Azure IoT Edge 上安装 OPC 发布服务器的分步指南](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) 
* [在 Linux 上安装 IoT Edge](../iot-edge/how-to-install-iot-edge.md) 
* [GitHub 上的 OPC 发布服务器](https://github.com/Azure/iot-edge-opc-publisher)
* [配置 OPC 发布服务器](/previous-versions/azure/iot-accelerators/howto-opc-publisher-configure)

### <a name="set-up-opc-ua-server"></a>设置 OPC UA 服务器

对于本文，你无需访问运行真实 OPC UA 服务器的物理设备。 而可以在 Windows VM 上安装免费的 [Prosys OPC UA 模拟服务器](https://www.prosysopc.com/products/opc-ua-simulation-server/)，以生成 OPC UA 数据。 本部分将详细介绍此设置。

如果已有物理 OPC UA 设备或其他想要使用的 OPC UA 模拟服务器，可以继续下一部分：[设置 IoT Edge 设备](#set-up-iot-edge-device)。

#### <a name="create-windows-10-virtual-machine"></a>创建 Windows 10 虚拟机

Prosys Software 需要一个简单的虚拟资源。 使用 [Azure 门户](https://portal.azure.com)，[创建一个 Windows 10 虚拟机 (VM)](../virtual-machines/windows/quick-create-portal.md)，规格如下：
* **可用性选项**：无需基础结构冗余
* **映像**：Windows 10 专业版，版本 2004 - Gen2
* **大小**：Standard_B1s - 1 个 vcpu，1 GiB 内存

:::image type="content" source="media/how-to-ingest-opcua-data/create-windows-virtual-machine-1.png" alt-text="Azure 门户的屏幕截图，显示了 Windows 虚拟机设置的“基本信息”选项卡。" lightbox="media/how-to-ingest-opcua-data/create-windows-virtual-machine-1.png":::

VM 必须可通过 Internet 访问。 为简化本演练，可以打开所有端口并为 VM 分配公共 IP 地址。 可在虚拟机设置的“网络”选项卡中完成此操作。

:::image type="content" source="media/how-to-ingest-opcua-data/create-windows-virtual-machine-2.png" alt-text="Azure 门户的屏幕截图，显示了 Windows 虚拟机设置的“网络”选项卡。":::

> [!WARNING]
> 对于生产解决方案，建议不要对 Internet 打开所有端口，因为这可能会带来安全风险。 可能需要为环境考虑其他安全策略。

收集“公共 IP”值，以便在下一步中使用。
 
完成 VM 设置。

#### <a name="install-opc-ua-simulation-software"></a>安装 OPC UA 模拟软件

在新的 Windows 虚拟机中，安装 [Prosys OPC UA 模拟服务器](https://www.prosysopc.com/products/opc-ua-simulation-server/)。

下载和安装完成后，启动服务器。 启动 OPC UA 服务器可能需要一些时间。 准备就绪后，服务器状态应会显示为“正在运行”。

接下来，复制“连接地址(UA TCP)”的值。 将其粘贴到安全位置，以便稍后使用。 在粘贴的值中，将地址的计算机名称部分替换为之前 VM 的“公共 IP”，如下所示： 

`opc.tcp://<ip-address>:53530/OPCUA/SimulationServer`

将在本文后面使用此更新后的值。

最后，通过选择“对象”选项卡并展开 Objects::FolderType 和 Simulation::FolderType 文件夹来查看服务器默认提供的模拟节点。 你将看到模拟节点，每个节点都有其唯一的 `NodeId` 值。 

捕获要发布的模拟节点的 `NodeId` 值。 在本文的后续部分，需要这些 ID 来模拟来自这些节点的数据。

> [!TIP]
> 按照 [Azure IoT Edge 上安装 OPC 发布服务器的分步指南](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher)中的“验证 OPC UA 服务正在运行且可访问”步骤操作，验证 OPC UA 服务器是否可访问。

#### <a name="verify-completion"></a>验证完成

在本部分中，设置 OPC UA 服务器以模拟数据。 验证是否已完成以下清单：

> [!div class="checklist"]
> * Prosys 模拟服务器已设置并正在运行
> * 已复制 UA TCP 连接地址 (`opc.tcp://<ip-address>:53530/OPCUA/SimulationServer`)
> * 已捕获要发布的模拟节点的 `NodeId` 列表 

### <a name="set-up-iot-edge-device"></a>设置 IoT Edge 设备

在本部分中，设置 IoT 中心实例和 IoT Edge 设备。 

首先，[创建一个 Azure IoT 中心实例](../iot-hub/iot-hub-create-through-portal.md)。 对于本文，可以在“F1 - 免费”层创建实例。

:::image type="content" source="media/how-to-ingest-opcua-data/iot-hub.png" alt-text="显示 IoT 中心属性的 Azure 门户屏幕截图。":::

创建 Azure IoT 中心实例后，从实例左侧导航菜单中选择“IoT Edge”，然后选择“添加 IoT Edge 设备” 。

:::image type="content" source="media/how-to-ingest-opcua-data/iot-edge-1.png" alt-text="在 Azure 门户中添加 IoT Edge 设备的屏幕截图。":::

按照提示操作以创建一个新设备。 

创建设备后，复制“主连接字符串”或“辅助连接字符串”值 。 稍后设置边缘设备时，将需要此值。

:::image type="content" source="media/how-to-ingest-opcua-data/iot-edge-2.png" alt-text="显示 IoT Edge 设备连接字符串的 Azure 门户屏幕截图。":::

#### <a name="verify-completion"></a>验证完成

在本部分中，设置 IoT Edge 和 IoT 中心以准备创建网关设备。 验证是否已完成以下清单：
> [!div class="checklist"]
> * 已创建 IoT 中心实例。
> * IoT Edge 设备已预配。

### <a name="set-up-gateway-device"></a>设置网关设备

为了将 OPC UA 服务器数据引入 IoT 中心，需要一台设备来运行带有 OPC 发布服务器模块的 IoT Edge。 然后，OPC 发布服务器会侦听 OPC UA 节点更新，并将遥测数据以 JSON 格式发布到 IoT 中心。

#### <a name="create-ubuntu-server-virtual-machine"></a>创建 Ubuntu Server 虚拟机

使用[Azure 门户](https://portal.azure.com)，创建一个 Ubuntu Server 虚拟机，规格如下：
* **可用性选项**：无需基础结构冗余
* **映像**：Ubuntu Server 18.04 LTS - Gen1
* **大小**：Standard_B1ms - 1 个 vcpu，2 GiB 内存
    - 对于 RDP，默认大小（Standard_b1s - vcpu，1GiB 内存）太慢。 将其更新为 2 GiB 内存将提供更好的 RDP 体验。

:::image type="content" source="media/how-to-ingest-opcua-data/ubuntu-virtual-machine.png" alt-text="显示 Ubuntu 虚拟机设置的 Azure 门户屏幕截图。":::

> [!NOTE]
> 如果选择使用 RDP 连接到 Ubuntu VM，则可以按照说明[安装和配置 xrdp，以将远程桌面与 Ubuntu 配合使用](../virtual-machines/linux/use-remote-desktop.md)。

#### <a name="install-iot-edge-container"></a>安装 IoT Edge 容器

按照说明[在 Linux 上安装 IoT Edge](../iot-edge/how-to-install-iot-edge.md)。

安装完成后，运行以下命令以验证安装状态：

```bash
admin@gateway:~$ sudo iotedge check
```

此命令将运行多个测试，以确保安装已准备就绪。

#### <a name="install-opc-publisher-module"></a>安装 OPC 发布服务器模块

接下来，在网关设备上安装 OPC 发布服务器模块。 

首先从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.iotedge-opc-publisher)获取模块。

:::image type="content" source="media/how-to-ingest-opcua-data/opc-publisher-1.png" alt-text="Azure 市场中的 OPC 发布服务器的屏幕截图。":::

然后，按照[“OPC 发布服务器”GitHub 存储库](https://github.com/Azure/iot-edge-opc-publisher)中所述的安装步骤，在 Ubuntu VM 上安装该模块。

在[指定容器创建选项](https://github.com/Azure/iot-edge-opc-publisher#specifying-container-create-options-in-the-azure-portal)步骤中，请确保添加以下 json：

```JSON
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=/appdata/publishednodes.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

:::image type="content" source="media/how-to-ingest-opcua-data/opc-publisher-2.png" alt-text="OPC 发布服务器容器创建选项的屏幕截图。":::

>[!NOTE]
>上面的创建选项应该在大多数情况下无需任何更改即可工作，但是如果你使用的网关设备与前文的指南不同，则可能需要根据自己的情况调整设置。

按照其余提示创建模块。 

大约 15 秒后，可以在网关设备上运行 `iotedge list` 命令，该命令将列出 IoT Edge 设备上运行的所有模块。 应会看到 OPCPublisher 模块启动并运行。

:::image type="content" source="media/how-to-ingest-opcua-data/iotedge-list.png" alt-text="IoT Edge 列表结果的屏幕截图。":::

最后，转到 `/iiotedge` 目录并创建一个“publishednodes.json”文件。 文件中的 ID 需要与[之前从 OPC 服务器收集](#install-opc-ua-simulation-software)的 `NodeId` 值相匹配。 文件应如下所示：

```JSON
[
    {
        "EndpointUrl": "opc.tcp://20.185.195.172:53530/OPCUA/SimulationServer",
        "UseSecurity": false,
        "OpcNodes": [
            {
                "Id": "ns=3;i=1001"
            },
            {
                "Id": "ns=3;i=1002"
            },
            {
                "Id": "ns=3;i=1003"
            },
            {
                "Id": "ns=3;i=1004"
            },
            {
                "Id": "ns=3;i=1005"
            },
            {
                "Id": "ns=3;i=1006"
            }
        ]
    }
]
```

保存对“publishednodes.json”文件所做的更改。

然后，运行以下命令：

```bash
sudo iotedge logs OPCPublisher -f
```

命令将生成 OPC 发布服务器日志的输出。 如果所有内容都已正确配置和运行，你将看到如以下屏幕截图所示的内容：

:::image type="content" source="media/how-to-ingest-opcua-data/iotedge-logs.png" alt-text="终端中 IoT Edge 日志的屏幕截图。左侧有一列诊断信息字段，右侧有一列值。":::

数据现在应从 OPC UA 服务器流入 IoT 中心。

若要监视流入 Azure IoT 中心的消息，可以使用以下命令：

```azurecli-interactive
az iot hub monitor-events -n <iot-hub-instance> -t 0
```

> [!TIP]
> 尝试使用 [Azure IoT 资源管理器](../iot-fundamentals/howto-use-iot-explorer.md)监视 IoT 中心消息。

#### <a name="verify-completion"></a>验证完成

在本部分中，设置一个运行 IoT Edge 的网关设备，该设备将从 OPC UA 服务器接收数据。 验证是否已完成以下清单：
> [!div class="checklist"]
> * 已创建 Ubuntu Server VM。
> * IoT Edge 已安装并位于 Ubuntu VM 上。
> * 已安装 OPC 发布服务器模块。
> * 已创建并配置“publishednodes.json”文件。
> * OPC 发布服务器模块正在运行，遥测数据正流向 IoT 中心。

在下一步中，你会将此遥测数据引入 Azure 数字孪生。

## <a name="set-up-azure-digital-twins"></a>设置 Azure 数字孪生

现已使数据从 OPC UA 服务器流向 Azure IoT 中心，下一步便是设置和配置 Azure 数字孪生。 

对于此示例，使用单个模型和单个孪生实例来匹配模拟服务器上的属性。 

>[!TIP]
>如果有兴趣了解具有更多模型和孪生体的更复杂场景，请查看[“OPC UA 到 Azure 数字孪生”GitHub 存储库](https://github.com/Azure-Samples/opcua-to-azure-digital-twins)中的巧克力工厂示例。

### <a name="create-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

首先，按照[设置实例和身份验证](how-to-set-up-instance-portal.md)中的指导部署一个新 Azure 数字孪生实例。

### <a name="upload-model-and-create-twin"></a>上传模型并创建孪生体

接下来，向实例添加模型和孪生体。 要上传到实例的模型文件是你在[先决条件](#prerequisites)部分下载的示例项目的一部分，位于“Simulation Example/simulation-dtdl-model.json”。

可以使用 [Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) 上传“Simulation”模型，并创建名为“simulation-1”的新孪生体。

:::image type="content" source="media/how-to-ingest-opcua-data/azure-digital-twins-explorer.png" alt-text="Azure Digital Twins Explorer 的屏幕截图，显示“Simulation”模型和 simulation-1 孪生体。":::

### <a name="verify-completion"></a>验证完成

在本部分中，设置一个包含模型和孪生体的 Azure 数字孪生实例。 验证是否已完成以下清单：
> [!div class="checklist"]
> * Azure 数字孪生实例已部署。
> * Simulation 模型已上传到 Azure 数字孪生实例。
> * 已从 Simulation 模型中创建 simulation-1 孪生体。

## <a name="set-up-azure-function"></a>设置 Azure 函数

现在，你已使 OPC UA 节点将数据发送到 IoT 中心，并且 Azure 数字孪生实例已准备好接收数据，接下来需要将数据映射并保存到 Azure 数字孪生中的适当孪生体和属性。 在本部分中，使用 Azure 函数和“opcua-mapping.json”文件完成该设置。

本部分中的数据流涉及以下步骤：

1. Azure 函数使用事件订阅接收来自 IoT 中心的消息。
1. Azure 函数处理每个到达的遥测事件。 它从事件中提取 `NodeId`，并根据“opcua-mapping.json”文件中的项目进行查找。 该文件将每个 `NodeId` 映射到 Azure 数字孪生中应该保存节点值的特定 `twinId` 和属性。
1. Azure 函数生成相应的补丁文档以更新相应的数字孪生体，并运行孪生属性更新命令。

### <a name="create-opcua-mappingjson-file"></a>创建 opcua-mapping.json 文件

首先，创建“opcua-mapping.json”文件。 从空白 JSON 文件开始，根据下面的示例和架构填写将 `NodeId` 值映射到 Azure 数字孪生中的 `twinId` 值和属性的条目。 需要为每个 `NodeId` 创建一个映射条目。

```JSON
[
    {
        "NodeId": "1001",
        "TwinId": "simulation",
        "Property": "Counter",
        "ModelId": "dtmi:com:microsoft:iot:opcua:simulation;1"
    },
    ...
]
```

下面是条目的架构：

| 属性 | 说明 | 必须 |
| --- | --- | --- |
| NodeId | OPC UA 节点的值。 例如：ns=3;i={value} | ✔ |
| TwinId | 要为其保存遥测值的孪生体的 TwinId ($dtId) | ✔ |
| 属性 | 用于保存遥测值的孪生体属性的名称 | ✔ |
| ModelId  | TwinId 不存在时用于创建孪生体的 modelId |  |

> [!TIP]
> 有关“opcua-mapping.json”文件的完整示例，请参阅[“OPC UA 到 Azure 数字孪生”GitHub 存储库](https://github.com/Azure-Samples/opcua-to-azure-digital-twins)。

添加完映射后，请保存该文件。

现在，你已拥有映射文件，需要将其存储在 Azure 函数可以访问的位置。 其中一个位置是 [Azure Blob 存储](../storage/blobs/storage-blobs-overview.md)。

按照说明[创建存储容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)，并将“opcua-mapping.json”文件导入到容器中。 还可以使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)执行存储管理事件。 

接下来，[为容器创建共享访问签名](../storage/common/storage-sas-overview.md)并保存该 URL。 稍后，向 Azure 函数提供该 URL，以便它可以访问存储的文件。

:::image type="content" source="media/how-to-ingest-opcua-data/azure-storage-explorer.png" alt-text="Azure 存储资源管理器的屏幕截图，其中显示了用于创建 SAS 令牌的对话框。":::

### <a name="publish-azure-function"></a>发布 Azure 函数

在本部分中，发布在[先决条件](#prerequisites)中下载的 Azure 函数，该函数将处理 OPC UA 数据并更新 Azure 数字孪生。

1. 导航到本地计算机上下载的 [OPC UA 到 Azure 数字孪生](https://github.com/Azure-Samples/opcua-to-azure-digital-twins) 项目，然后进入“Azure Functions/OPCUAFunctions”文件夹。 在 Visual Studio 中打开“OPCUAFunctions.sln”解决方案。
2. 将项目发布到 Azure 中的函数应用。 若要了解如何执行此操作，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#publish-to-azure)。

#### <a name="configure-the-function-app"></a>配置函数应用

接下来，为函数分配访问角色并配置应用程序设置，使之能够访问 Azure 数字孪生实例。

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

#### <a name="add-application-settings"></a>添加应用程序设置

此外，还需要添加一些应用程序设置，才能全面设置环境和 Azure 函数。 转到 [Azure 门户](https://portal.azure.com)，并通过在门户搜索栏中搜索名称来导航到新创建的 Azure 函数。

从函数左侧导航菜单中选择“配置”。 使用“+ 新建应用程序设置”按钮，开始创建新设置。

:::image type="content" source="media/how-to-ingest-opcua-data/azure-function-settings-1.png" alt-text="在 Azure 门户中向 Azure 函数添加应用程序设置的屏幕截图。":::

需要创建三个应用程序设置：

| 设置 | 说明 | 必须 |
| --- | --- | --- |
| ADT_SERVICE_URL | Azure 数字孪生实例的 URL。 示例：`https://example.api.eus.digitaltwins.azure.net` | ✔ |
| JSON_MAPPINGFILE_URL | opcua-mapping.json 的共享访问签名的 URL | ✔ |
| LOG_LEVEL | 日志级别详细程度。 默认值为 100。 详细值为 300 | |

:::image type="content" source="media/how-to-ingest-opcua-data/azure-function-settings-2.png" alt-text="Azure 门户中 Azure 函数的应用程序设置的屏幕截图。已添加上述设置。":::

> [!TIP]
> 将函数的 `LOG_LEVEL` 应用程序设置设置为 300，以获得更详细的日志记录体验。 

### <a name="create-event-subscription"></a>创建事件订阅

最后，创建一个事件订阅，用于将函数应用和 ProcessOPCPublisherEventsToADT 函数连接到 IoT 中心。 需要事件订阅，以便数据可以通过随后更新 Azure 数字孪生的函数从网关设备流入 IoT 中心。

有关说明，请按照 Azure 数字孪生“教程：连接端到端解决方案”中的[将 IoT 中心连接到 Azure 函数](tutorial-end-to-end.md#connect-the-iot-hub-to-the-azure-function)中所用的相同步骤操作。

事件订阅的终结点类型为“Azure 函数”，终结点为“ProcessOPCPublisherEventsToADT” 。

:::image type="content" source="media/how-to-ingest-opcua-data/event-subscription.png" alt-text="Azure 门户的屏幕截图，显示新事件订阅的创建。":::

完成此步骤后，所有必需的组件都应已安装并运行。 数据应该从 OPC UA 模拟服务器流过 Azure IoT 中心，然后流入 Azure 数字孪生实例。 

### <a name="verify-completion"></a>验证完成

在本部分中，设置一个 Azure 函数，用于将 OPC UA 数据连接到 Azure 数字孪生。 验证是否已完成以下清单：
> [!div class="checklist"]
> * 已创建“opcua-mapping.json”文件并将其导入到 blob 存储容器。 
> * 已将示例函数 ProcessOPCPublisherEventsToADT 发布到 Azure 中的函数应用。
> * 向 Azure Functions 应用添加了三个新的应用程序设置。
> * 创建了用于将 IoT 中心事件发送到函数应用的事件订阅。

下一部分提供了一些 Azure CLI 命令，可以运行这些命令来监视事件并验证一切是否正常工作。

## <a name="verify-and-monitor"></a>验证和监视

本部分中的命令可在 [Azure Cloud Shell](https://shell.azure.com) 或[本地 Azure CLI 窗口](/cli/azure/install-azure-cli)中运行。

运行此命令以监视 IoT 中心事件：
```azurecli-interactive
az iot hub monitor-events -n <IoT-hub-name> -t 0
```

运行此命令以监视 Azure 函数事件处理：
```azurecli-interactive
az webapp log tail –name <function-name> --resource-group <resource-group-name>
```

最后，可以使用 Azure Digital Twins Explorer 手动监视孪生属性更新。 

:::image type="content" source="media/how-to-ingest-opcua-data/adt-explorer-2.png" alt-text="使用 Azure Digital Twins Explorer 监视孪生属性更新的屏幕截图":::

## <a name="next-steps"></a>后续步骤

在本文中，你设置了一个完整的数据流，用于将模拟 OPC UA 服务器数据引入 Azure 数字孪生，并在此过程中更新了数字孪生体上的一个属性。

接下来，使用以下资源详细了解本文中所用的支持工具和过程：

* [在 Azure IoT Edge 上安装 OPC 发布服务器的分步指南](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) 
* [在 Linux 上安装 IoT Edge](../iot-edge/how-to-install-iot-edge.md) 
* [OPC 发布服务器](https://github.com/Azure/iot-edge-opc-publisher)
* [配置 OPC 发布服务器](/previous-versions/azure/iot-accelerators/howto-opc-publisher-configure)
* [UANodeSetWebViewer](https://github.com/barnstee/UANodesetWebViewer) 
* [OPCUA2DTDL](https://github.com/khilscher/OPCUA2DTDL)