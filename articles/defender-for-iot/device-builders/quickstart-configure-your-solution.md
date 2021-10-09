---
title: 快速入门：将 Azure 资源添加到 IoT 解决方案
description: 在本快速入门中，了解如何使用 Azure Defender for IoT 配置端到端 IoT 解决方案。
ms.topic: quickstart
ms.date: 09/13/2021
ms.openlocfilehash: e66accdc1ba671941c13433eeca9b3e9c541781b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634817"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>快速入门：配置你的 Azure Defender for IoT 解决方案

本文介绍如何使用 Defender for IoT 首次配置 IoT 安全性解决方案。

## <a name="prerequisites"></a>先决条件

- 无

## <a name="what-is-defender-for-iot"></a>什么是 Defender for IoT？

Defender for IoT 为基于 Azure 的 IoT 解决方案提供全面的端到端安全性。

借助 Defender for IoT，可在一个仪表板中监视整个 IoT 解决方案，显示 Azure 中你的所有 IoT 设备、IoT 平台和后端资源。

在 IoT 中心启用 Defender for IoT 后，Defender for IoT 将自动标识其他 Azure 服务，并连接到隶属于 IoT 解决方案的相关服务。

你还可以选择属于 IoT 解决方案的其他 Azure 资源组。

做出选择后，可以添加整个订阅、资源组或单个资源。

定义所有资源关系后，Defender for IoT 将使用 Defender 向你提供安全建议，并针对这些资源发出警报。

## <a name="add-azure-resources-to-your-iot-solution"></a>将 Azure 资源添加到 IoT 解决方案

将新资源添加到 IoT 解决方案：

1. 在 Azure 门户中，搜索并选择“IoT 中心”。

1. 在“安全性”部分下，选择“设置” > “受监视资源” 。

1. 选择“编辑”，并选择属于 IoT 解决方案的受监视资源。

1. 选择 **添加** 。

现在，一个新的资源组将添加到 IoT 解决方案中。

Defender for IoT 现在监视新添加的资源组，并将相关的安全建议和警报作为 IoT 解决方案的一部分显示。

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何创建 Defender-IoT-micro-agent...

> [!div class="nextstepaction"]
> [创建 Defender-IoT-micro-agent](quickstart-create-security-twin.md)
