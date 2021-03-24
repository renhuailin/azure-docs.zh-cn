---
title: 快速入门：将 Defender for IoT 加入到基于代理的解决方案
description: 在本快速入门中，了解如何在 Azure IoT 中心加入和启用 Defender for IoT 安全服务。
ms.topic: quickstart
ms.date: 1/20/2021
ms.openlocfilehash: b795773ae0eb667cf7f53f4209b6476ba937b17f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104780950"
---
# <a name="quickstart-onboard-defender-for-iot-to-an-agent-based-solution"></a>快速入门：将 Defender for IoT 加入到基于代理的解决方案

本文介绍如何在现有 IoT 中心启用 Defender for IoT 服务。 如果当前没有 IoT 中心，请参阅[使用 Azure 门户创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)以开始操作。

可在 Defender for IoT 通过 IoT 中心管理 IoT 安全性。 通过 IoT 中心内的管理门户，可执行以下操作： 

- 管理 IoT 中心安全性。

- 对 IoT 设备安全性的基本管理，无需基于 IoT 中心遥测安装代理。 

- 基于微代理对 IoT 设备安全性进行高级管理。

> [!NOTE]
> Defender for IoT 目前仅支持标准层 IoT 中心。

## <a name="prerequisites"></a>先决条件

无

## <a name="onboard-defender-for-iot-to-an-iot-hub"></a>将 Defender for IoT 加入 IoT 中心

对于所有新的 IoT 中心，Defender for IoT 都默认设置为“开”。 可在 IoT 中心创建过程中验证 Defender for IoT 是否已切换为“开”。

若要验证开关是否设置为“开”：

1. 导航到 Azure 门户。

1. 从 Azure 服务列表中选择“IoT 中心”。

1. 选择“创建”。

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="选择顶部工具栏中的“创建”按钮。" lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. 选择“管理”选项卡，然后验证“Defender for IoT”开关是否设置为“开”  。

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="确保 Defender for IoT 开关是否已设置为“开”。":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>将 Defender for IoT 加入现有 IoT 中心

可以通过将 Defender for IoT 加入现有的 IoT 中心，监视设备标识管理、设备到云和云到设备的通信模式。

若要将 Defender for IoT 加入现有的 IoT 中心：

1. 导航到 IoT 中心。 

1. 选择要加入的 IoT 中心。

1. 在“安全性”部分下选择任何选项。

1. 单击“保护 IoT 解决方案”，然后填写加入窗体 ****  。 

    :::image type="content" source="media/quickstart-onboard-iot-hub/secure-your-iot-solution.png" alt-text="选择“保护 IoT 解决方案”按钮以保护解决方案。":::

仅在 IoT 中心尚未加入，或者在加入时将 Defender for IoT 切换为了“关闭”，才会显示“保护 IoT 解决方案”按钮 。

:::image type="content" source="media/quickstart-onboard-iot-hub/toggle-is-off.png" alt-text="如果在加入过程中切换设置为关闭。":::

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何配置解决方案...

> [!div class="nextstepaction"]
> [创建 Defender IoT 微代理模块孪生（预览）](quickstart-create-micro-agent-module-twin.md)
