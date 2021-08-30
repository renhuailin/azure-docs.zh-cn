---
title: 使用无线方式 (OTA) 更新 Azure Percept DK
description: 了解如何以无线方式 (OTA) 接收 Azure Percept DK 更新
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: c5045aed59be4c8903672691241b68f69e353e13
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733351"
---
# <a name="update-your-azure-percept-dk-using-over-the-air-ota-updates"></a>使用无线方式 (OTA) 更新 Azure Percept DK

按照本指南学习如何使用 Device Update for IoT Hub 以无线方式 (OTA) 更新 Azure Percept DK 的载板的 OS 和固件。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将开发工具包连接到 Wi-Fi 网络，创建了 IoT 中心，并已将开发工具包连接到 IoT 中心
- [已成功配置 Device Update for IoT Hub](./how-to-set-up-over-the-air-updates.md)

## <a name="import-your-update-file-and-manifest-file"></a>导入更新文件和清单文件

> [!NOTE]
> 如果你已导入更新，则可以直接跳到“创建设备更新组”。

1. 确定适合你的开发工具包的[清单和更新包](./how-to-select-update-package.md)。

1. 导航到用于 Azure Percept 设备的 Azure IoT Hub。 在左侧菜单面板中的“自动设备管理”下选择“设备更新”。 

1. 屏幕顶部会显示几个选项卡。 选择“更新”选项卡。

1. 在“准备部署”标头下方选择“+ 导入新更新”。 

1. 选择“选择导入清单文件”和“选择更新文件”下的框，以选择你的清单文件 (.json) 和更新文件 (.swu)。

1. 选择“选择存储容器”下的文件夹图标或文本框，并选择相应的存储帐户。 如果你已创建存储容器，可以重复使用它。 否则，请选择“+ 容器”，为 OTA 更新创建一个新的存储容器。 选择要使用的容器，然后单击“选择”。

1. 选择“提交”以开始导入过程。 由于映像大小，提交过程最多可能需要 5 分钟。

    > [!NOTE]
    > 系统可能会要求你添加一项跨域请求 (CORS) 规则来访问所选存储容器。 选择“添加规则并重试”以继续操作。

1. 导入过程开始时，将重定向到“设备更新”页面的“导入历史记录”选项卡。 在导入过程完成时单击“刷新”以监视进度。 这可能需要几分钟或更长的时间，具体取决于更新的大小（在高峰期，导入服务最多可能需要 1 小时）。

1. 当“状态”列指示导入已成功时，请选择“准备部署”选项卡，然后单击“刷新”。   现在应会在列表中看到导入的更新。

## <a name="create-a-device-update-group"></a>创建设备更新组

使用 Device Update for IoT Hub，你可以针对特定的 Azure Percept DK 组进行更新。 若要创建组，必须在 Azure IoT Hub 中向你的目标设备集添加标记。

> [!NOTE]
> 如果你已经创建组，可以跳到下一部分。

组标记要求：

- 你可以将任何值添加到标记中，但“Uncategorized”除外，这是一个保留值。
- 标记值的长度不得超过 255 个字符。
- 标记值只能包含以下特殊字符：“.”、“-”、“_”、“~”。
- 标记和组的名称区分大小写。
- 一个设备只能有一个标记。 添加到设备中的任何后续标记将替代上一个标记。
- 一个设备只能属于一个组。

1. 将标记添加到设备：

    1. 从左侧导航窗格上的“IoT Edge”，找到你的 Azure Percept DK 并导航到其“设备孪生”。

    1. 添加新的 Device Update for IoT Hub 标记值，如下所示（```<CustomTagValue>``` 表示标记值/名称，例如 AzurePerceptGroup1）。 详细了解设备孪生 [JSON 文档标记](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)。

        ```
        "tags": {
        "ADUGroup": "<CustomTagValue>"
        },
        ```

1. 单击“保存”并解决任何格式问题。

1. 通过选择一个现有 Azure IoT 中心标记来创建组：

    1. 导航回你的 Azure IoT 中心页面。

    1. 在左侧菜单面板中的“自动设备管理”下选择“设备更新”。 

    1. 选择“组”选项卡。此页面会显示已连接到“设备更新”的未分组设备数。

    1. 选择“+ 添加”以创建一个新组。

    1. 从列表中选择一个 IoT 中心标记并单击“提交”。

    1. 创建组后，更新符合性图表和组列表也会更新。 该图表显示了处于下述各种符合性状态的设备的计数：“应用了最新更新”、“有新的更新可用”、“正在进行更新”和“尚未分组”。   

## <a name="deploy-an-update"></a>部署更新

1. 你应该会看到新创建的组，其中有一个新的更新列在“可用更新”下（你可能需要刷新一次）。 选择该更新。

1. 确认已选择了正确的设备组作为目标设备组。 为你的部署选择一个“开始日期”和“开始时间”，然后单击“创建部署”。

    > [!CAUTION]
    > 将开始时间设置为过去的时间会立即触发部署。

1. 检查符合性图表。 此时会看到更新正在进行。

1. 在更新完成后，你的符合性图表会反映你的新更新状态。

1. 在“设备更新”页面的顶部选择“部署”选项卡。 

1. 选择你的部署以查看部署详细信息。 在“状态”变为“已成功”之前，你可能需要单击“刷新”。  

## <a name="next-steps"></a>后续步骤

你的开发工具包现在已成功更新。 你可以继续通过开发工具包进行开发和操作。
