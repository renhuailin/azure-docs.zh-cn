---
title: 载入 Defender for IoT 基于代理的解决方案
description: 了解如何在 Azure IoT 中心加入和启用 Defender for IoT 安全服务。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/20/2021
ms.author: shhazam
ms.openlocfilehash: 127e439a7740cb97cbe126071aaaa5245cd85782
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809113"
---
# <a name="onboard-to-defender-for-iot-agent-based-solution"></a>载入 Defender for IoT 基于代理的解决方案

本文介绍如何在现有 IoT 中心启用 Defender for IoT 服务。 如果当前没有 IoT 中心，请参阅[使用 Azure 门户创建 IoT 中心](../iot-hub/iot-hub-create-through-portal.md)以开始操作。

可在 Defender for IoT 通过 IoT 中心管理 IoT 安全性。 通过 IoT 中心内的管理门户，可执行以下操作： 

- 管理 IoT 中心安全性。

- 对 IoT 设备安全性的基本管理，无需基于 IoT 中心遥测安装代理。 

- 基于微代理对 IoT 设备安全性进行高级管理。

> [!NOTE]
> Defender for IoT 目前仅支持标准层 IoT 中心。

## <a name="onboard-to-defender-for-iot-in-iot-hub"></a>在 IoT 中心加入 Defender for IoT

对于所有新的 IoT 中心，Defender for IoT 都默认设置为“开”。 可在 IoT 中心创建过程中验证 Defender for IoT 是否已切换为“开”。

若要验证开关是否设置为“开”：

1. 导航到 Azure 门户。

1. 从 Azure 服务列表中选择“IoT 中心”。

1. 选择“创建”。

    :::image type="content" source="media/quickstart-onboard-iot-hub/create-iot-hub.png" alt-text="选择顶部工具栏中的“创建”按钮。" lightbox="media/quickstart-onboard-iot-hub/create-iot-hub-expanded.png":::

1. 选择“管理”选项卡，然后验证“Defender for IoT”开关是否设置为“开”  。

    :::image type="content" source="media/quickstart-onboard-iot-hub/management-tab.png" alt-text="确保 Defender for IoT 开关是否已设置为“开”。":::

## <a name="onboard-defender-for-iot-to-an-existing-iot-hub"></a>将 Defender for IoT 加入现有 IoT 中心

可监视设备标识管理、设备到云的模式、云到设备的模式，还可执行以下步骤来启动服务： 

1. 导航到 IoT 中心。 

1. 选择“安全概述”菜单。 ****   

1. 单击“保护 IoT 解决方案”，然后填写加入窗体。 


## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何配置解决方案...

> [!div class="nextstepaction"]
> [创建 Defender IoT 微代理模块孪生（预览）](quickstart-create-micro-agent-module-twin.md)
