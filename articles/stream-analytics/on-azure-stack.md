---
title: 在 Azure Stack 上运行 Azure 流分析
description: 创建 Azure 流分析 Edge 作业，然后通过 Azure IoT Edge 运行时将其部署到 Azure Stack Hub。
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 9cfb98e4d221ee0e8aae7f96ce241269bd56616d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726057"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>在 Azure Stack 上运行 Azure 流分析

可以将 Azure 流分析作为 Azure IoT Edge 模块在 Azure Stack Hub 上运行。 添加到 IoT Edge 模块中的配置使该模块可以通过允许每次利用 Azure Stack Hub 时找到的自定义 URL 来与 Azure Stack Hub 订阅中运行的 Azure Blob 存储、Azure 事件中心和 Azure IoT 中心进行交互。

借助 Azure Stack 上的流分析，你可以真正地生成混合体系结构，以便在自己的私有自治云中进行流处理。 通过在本地使用一致的 Azure 服务，云可以与云原生应用连接或断开连接。 

本文介绍如何将数据从 IoT 中心或事件中心流式传输到 Azure Stack Hub 订阅中的另一个事件中心或 Blob 存储，以及如何使用流分析进行处理。

## <a name="set-up-environments"></a>设置环境

流分析是 Azure Stack Hub 上的混合服务。 它是在 Azure 中配置但可以在 Azure Stack Hub 上运行的 IoT Edge 模块。

如果你不熟悉 Azure Stack Hub 或 IoT Edge，请按照以下说明设置环境。

### <a name="prepare-the-azure-stack-hub-environment"></a>准备 Azure Stack Hub 环境

创建 Azure Stack Hub 订阅。 有关详细信息，请参阅[创建 Azure Stack Hub 订阅的教程](/azure-stack/user/azure-stack-subscribe-services/)。

如果要在自己的服务器上评估 Azure Stack Hub，可以使用 Azure Stack 开发工具包 (ASDK)。 有关 ASDK 的详细信息，请参阅 [ASDK 概述](/azure-stack/asdk/)。

### <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

若要在 Azure Stack Hub 上运行流分析，你的设备必须具有 IoT Edge 运行时，且必须通过网络与 Azure Stack Hub 连接，或者必须是在 Azure Stack Hub 上运行的 VM。 IoT Edge 运行时支持将流分析边缘作业与 Azure Stack Hub 上运行的 Azure 存储和 Azure 事件中心集成。 有关详细信息，请参阅 [IoT Edge 上的 Azure 流分析](stream-analytics-edge.md)。 

除了需要具有对 Azure Stack Hub 资源的网络访问权限外，IoT Edge 设备（或 VM）还需要在 Azure 公有云中访问 IoT 中心来配置流分析模块。 

以下文章介绍了如何在设备或 VM 上设置 IoT Edge 运行时：

* [在 Windows 上安装 Azure IoT Edge 运行时](../iot-edge/how-to-install-iot-edge.md)
* [在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>创建 Azure 流分析 Edge 作业

流分析边缘作业在部署到 IoT Edge 设备的容器中运行。 它们由两个部分组成：
* 负责作业定义的云部分：用户在云中定义输入、输出、查询和其他设置（例如无序事件）。
* 在 IoT 设备上运行的模块。 该模块包含流分析引擎，并从云接收作业定义。

### <a name="create-a-storage-account"></a>创建存储帐户

创建要在 IoT Edge 设备上运行的流分析作业时，需采用能够从设备进行调用的方式存储该作业。 可以使用现有 Azure 存储帐户，也可以创建新的 Azure 存储帐户。
1. 在 Azure 门户中，转到“创建资源” > “存储” > “存储帐户 - Blob、文件、表、队列”。
1. 提供以下值来创建存储帐户。

   | 字段 | 值 |
   | --- | --- |
   | 名称 | 为存储帐户提供唯一的名称。 |
   | 位置 | 选择靠近你的位置。|
   | 订阅 | 选择与 IoT 中心相同的订阅。|
   | 资源组 | 对在 [IoT Edge 快速入门](../iot-edge/quickstart.md)和教程中创建的所有测试资源使用同一资源组。 例如，使用 IoTEdgeResources。 |

1. 将其他字段保留默认值，然后选择“创建”。


### <a name="create-a-new-job"></a>创建新作业

1. 在 Azure 门户中，转到“创建资源” > “物联网” > “流分析作业”。  
1. 提供以下值来创建存储帐户。

   | 字段 | 值 |
   | --- | --- |
   | 作业名称 | 为作业提供一个名称。 例如 IoTEdgeJob。 |
   | 订阅 | 选择与 IoT 中心相同的订阅。|
   | 资源组 | 对在 [IoT Edge 快速入门](../iot-edge/quickstart.md)和教程中创建的所有测试资源使用同一资源组。 例如，使用 IoTEdgeResources。 |
   | 位置 | 选择靠近你的位置。 |
   | 宿主环境 | 选择“边缘”。 |

1. 选择“创建”。

### <a name="configure-your-job"></a>配置作业

在 Azure 门户中创建流分析作业后，可使用输入、输出以及要在流过的数据上运行的查询对其进行配置。 可以在 Azure Stack Hub 订阅中手动指定 IoT 中心或事件中心的输入。

1. 转到 Azure 门户中的流分析作业。
1. 在“配置”中，选择“存储帐户设置”，然后选择在上一步中创建的存储帐户 。
   > [!div class="mx-imgBorder"]
   > [![显示作业存储帐户设置的屏幕截图。](media/on-azure-stack/storage-account-settings.png)](media/on-azure-stack/storage-account-settings.png#lightbox)
1. 在“作业拓扑”下，选择“输入” > “添加流输入”  。
1. 从下拉列表中，选择“IoT 中心”、“事件中心”或“Edge 中心”  。 
1. 如果输入是 Azure Stack Hub 订阅中的事件中心或 IoT 中心，请按如下所示手动提供信息。

   #### <a name="event-hub"></a>事件中心

   | 字段 | 值 |
   | --- | --- |
   | 输入别名 | 在作业查询中用于引用此输入的友好名称。 |
   | 服务总线命名空间 | 命名空间是包含一组消息传递实体的容器。 创建新的事件中心后，另请创建命名空间。 例如 sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com。 |
   | 事件中心名称 | 要用作输入的事件中心的名称。 |
   | 事件中心策略名称 | 提供对事件中心的访问权限的共享访问策略。 每个共享访问策略具有名称、所设权限以及访问密钥。 此选项会自动进行填充，除非已选择手动提供事件中心设置的选项。 |
   | 事件中心策略密钥 | 用于授予对事件中心访问权限的共享访问密钥。 此选项会自动进行填充，除非已选择手动提供事件中心设置的选项。 可以在事件中心设置中找到此选项。 |
   | 事件中心使用者组（可选） | 为每个流分析作业使用不同的使用者组。 此字符串标识的使用者组用于从事件中心引入数据。 如果未指定任何使用者组，流分析作业将使用 $Default 使用者组。 |
   | 分区计数 | 分区计数是事件中心中的分区数。 |

   > [!div class="mx-imgBorder"]
   > [![显示事件中心输入的屏幕截图。](media/on-azure-stack/event-hub-input.png)](media/on-azure-stack/event-hub-input.png#lightbox)

   #### <a name="iot-hub"></a>IoT 中心

   | 字段 | 值 |
   | --- | --- |
   | 输入别名 | 在作业查询中用于引用此输入的友好名称。 |
   | IoT 中心 | 要用作输入的 IoT 中心的名称。 例如 <IoT Hub Name>.shanghai.azurestack.corp.microsoft.com。 |
   | 共享访问策略名称 | 提供对 IoT 中心访问权限的共享访问策略。 每个共享访问策略具有名称、所设权限以及访问密钥。 |
   | 共享访问策略密钥 | 用于授予对 IoT 中心访问权限的共享访问密钥。 此选项会自动进行填充，除非已选择手动提供 IoT 中心设置的选项。 |
   | 使用者组（可选） | 为每个流分析作业使用不同的使用者组。 使用者组用于从 IoT 中心引入数据。 流分析使用 $Default 所有者组，除非指定了其他组。 |
   | 分区计数 | 分区计数是事件中心中的分区数。 |

   > [!div class="mx-imgBorder"]
   > [![显示 IoT 中心输入的屏幕截图。](media/on-azure-stack/iot-hub-input.png)](media/on-azure-stack/iot-hub-input.png#lightbox)

1. 保留其他字段的默认值，然后选择“保存”。
1. 在“作业拓扑”下，打开“输出”，然后选择“添加”  。
1. 从下拉列表中，选择“Blob 存储”、“事件中心”或“Edge 中心”  。
1. 如果输出是 Azure Stack Hub 订阅中的事件中心或 Blob 存储，请按如下所示手动提供信息。

   #### <a name="event-hub"></a>事件中心

   | 字段 | 值 |
   | --- | --- |
   | 输出别名 | 查询中使用的易记名称，用于将查询输出定向到此事件中心。 |
   | 服务总线命名空间 | 包含一组消息实体的容器。 创建新的事件中心后，还创建了 Service Bus 命名空间。 例如 sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com。 |
   | 事件中心名称 | 事件中心输出的名称。 |
   | 事件中心策略名称 | 共享访问策略，可以在事件中心的“配置”选项卡上创建。每个共享访问策略具有名称、所设权限以及访问密钥。 |
   | 事件中心策略密钥 | 用于对事件中心命名空间的访问权限进行身份验证的共享访问密钥。 |

   > [!div class="mx-imgBorder"]
   > [![显示事件中心输出的屏幕截图。](media/on-azure-stack/event-hub-output.png)](media/on-azure-stack/event-hub-output.png#lightbox)

   #### <a name="blob-storage"></a>Blob 存储 

   | 字段 | 值 |
   | --- | --- |
   | 输出别名 | 查询中使用的友好名称，用于将查询输出定向到此 blob 存储。 |
   | 存储帐户 | 存储帐户的名称（正在向该存储帐户发送输出）。 例如 <Storage Account Name>.blob.shanghai.azurestack.corp.microsoft.com。 |
   | 存储帐户密钥 | 与存储帐户关联的密钥。 此选项会自动进行填充，除非已选择手动提供 Blob 存储设置的选项。 |

> [!NOTE]
> Azure Stack Hub 上的边缘作业不支持 Parquet 格式。 对于“最小”行数和“最长”时间，请将其设为 0 或留空  。


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>在连接到 Azure Stack 的 VM 或设备上部署流分析

1. 在 Azure 门户中，打开 IoT 中心。 转到 IoT Edge，然后选择要用于此部署的设备或 VM。
1. 选择“设置模块” > “+ 添加”，然后选择“Azure 流分析模块”  。 
1. 选择订阅和你创建的流分析边缘作业。 依次选择“保存”和“下一步: 路由” 。

   > [!div class="mx-imgBorder"]
   > [![显示添加模块的屏幕截图。](media/on-azure-stack/edge-modules.png)](media/on-azure-stack/edge-modules.png#lightbox)

1. 选择“查看 + 创建”。
1. 在“查看 + 创建”步骤中，选择“创建” 。 
   > [!div class="mx-imgBorder"]
   > [![显示清单的屏幕截图。](media/on-azure-stack/module-content.png)](media/on-azure-stack/module-content.png#lightbox)
1. 确认模块已添加到列表。
   > [!div class="mx-imgBorder"]
   > [![显示部署页面的屏幕截图。](media/on-azure-stack/edge-deployment.png)](media/on-azure-stack/edge-deployment.png#lightbox)

## <a name="next-steps"></a>后续步骤
- [Azure IoT Edge 流分析](./stream-analytics-edge.md)
- [开发流分析 Edge 作业](/stream-analytics-query/stream-analytics-query-language-reference)
