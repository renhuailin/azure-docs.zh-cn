---
title: include 文件
description: include 文件
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9151a84aa03e9b87b02278672622d458fbc5281e
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538668"
---
若要使用 Azure 门户创建 IoT 中心，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从 Azure 主页中选择“创建资源”按钮，然后在“在市场中搜索”中输入“IoT 中心”。

1. 在搜索结果中选择“IoT 中心”，然后选择“创建” 。

1. 在“基本信息”选项卡上，按如下所示填写字段：

   - 订阅：选择要用于中心的订阅。

   - **资源组**：选择一个资源组或新建一个资源组。 若要新建资源组，请选择“新建”并填写要使用的名称。 若要使用现有的资源组，请选择它。 有关详细信息，请参阅[管理 Azure 资源管理器资源组](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)。 本教程使用名称 **tutorials-iot-hub-rg**。

   - **区域**：选择中心所在的区域。 选择最靠近你的位置。 某些功能（如 [IoT 中心设备流](../articles/iot-hub/iot-hub-device-streams-overview.md)）仅适用于特定区域。 对于这些受限功能，你必须选择受支持的区域之一。 本教程使用“美国西部”区域。

   - **IoT 中心名称**：输入中心的名称。 该名称必须全局唯一。 本教程使用 tutorials-iot-hub。 创建中心时，必须选择自己的唯一名称。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="在 Azure 门户中创建中心":::

1. 在完成时选择“下一步:网络”，继续创建中心。

   选择可以连接到 IoT 中心的终结点。 你可以选择默认设置“公共终结点(所有网络)”，也可选择“公共终结点(选定的 IP 范围)”或“专用终结点”  。 在本教程中接受默认设置。

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="选择可以连接的终结点。":::

1. 在完成时选择“下一步:管理”，继续创建中心。

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="使用 Azure 门户为新的中心设置大小和规模。":::

   可在此处接受默认设置。 如果需要，可以修改以下任何字段：

   - **定价和缩放层**：选择的层。 选择免费层。 免费层适用于测试和评估。 允许 500 台设备连接到中心，每天最多可传输 8,000 条消息。 每个 Azure 订阅可以在免费层中创建一个 IoT 中心。

   - **IoT 中心单元**：每日每单位允许的消息数取决于中心的定价层。 例如，如果希望中心支持 700,000 条消息引入，请选择两个 S1 层单位。
   每个 Azure 订阅可以在免费层中创建一个 IoT 中心。 有关其他层选项的详细信息，请参阅[选择合适的 IoT 中心层](../articles/iot-hub/iot-hub-scaling.md)。

   - **Defender for IoT**：启用此功能可为 IoT 和设备添加额外的一层威胁防护。 此选项不可用于免费层的中心。 有关此功能的详细信息，请参阅[适用于 IoT 的 Azure 安全中心](/azure/asc-for-iot/)。

   - **高级设置** > **设备到云的分区**：此属性将设备到云消息与这些消息的同步读取器数目相关联。 大多数中心只需要 4 个分区。 免费层中心限制为两个分区。

1. 在完成时选择“下一步:标记”继续到下一屏幕。

   标记是名称/值对。 可以为多个资源和资源组分配相同的标记，以便对资源进行分类并合并计费。 有关详细信息，请参阅[使用标记来组织 Azure 资源](../articles/azure-resource-manager/management/tag-resources.md)。

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="使用 Azure 门户为中心分配标记。":::

1. 在完成时选择“下一步:查看+创建”可查看选择。 你会看到类似于此屏幕的内容，但其中包含创建中心时选择的值。

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="查看用于创建新中心的信息。":::

1. 记下所选的 IoT 中心名称。 本教程稍后会用到此值。
