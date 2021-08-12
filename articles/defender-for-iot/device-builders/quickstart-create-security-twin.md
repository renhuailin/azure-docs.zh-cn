---
title: 快速入门：创建安全模块孪生
description: 在本快速入门中，了解如何创建适用于 IoT 的 Defender 模块孪生以用于 Azure Defender for IoT。
ms.topic: quickstart
ms.date: 05/26/2021
ms.openlocfilehash: 5f2d6b4e4ab747988fe41322aab61c90b7c15b18
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113011054"
---
# <a name="quickstart-create-an-azureiotsecurity-module-twin"></a>快速入门：创建 azureiotsecurity 模块孪生

本快速入门介绍如何为新设备创建单个 _azureiotsecurity_ 模块孪生，或者为 IoT 中心内的所有设备批量创建模块孪生。

## <a name="prerequisites"></a>先决条件

- 无

## <a name="understanding-azureiotsecurity-module-twins"></a>了解 azureiotsecurity 模块孪生

对于在 Azure 中生成的 IoT 解决方案，设备孪生在设备管理和流程自动化方面发挥着关键作用。

Defender for IoT 可与现有的 IoT 设备管理平台完全集成，使你能够管理设备的安全状态，并利用现有的设备控制功能。
适用于 IoT 的 Defender 集成是使用 IoT 中心孪生机制实现的。

请参阅 [IoT 中心模块孪生](../../iot-hub/iot-hub-devguide-module-twins.md)[IoT 中心模块孪生]详细了解 Azure IoT 中心内模块孪生的一般概念。

适用于 IoT 的 Defender 利用模块孪生机制，并为每个设备维护一个名为 azureiotsecurity 的安全模块孪生。

Defender-IoT-micro-agent 孪生体为每台设备保存与设备安全性相关的所有信息。

若要充分利用 Defender for IoT 功能，需要对服务中的每台设备创建、配置和使用此 Defender-IoT-micro-agent 孪生体。

## <a name="create-azureiotsecurity-module-twin"></a>创建 azureiotsecurity 模块孪生

可通过两种方式创建 _azureiotsecurity_ 模块孪生：

1. [模块批处理脚本](https://aka.ms/iot-security-github-create-module) - 使用默认配置为新的设备或者不包含模块孪生的设备自动创建模块孪生。
1. 使用每个设备的特定配置单独手动编辑每个模块孪生。

>[!NOTE]
> 使用批处理方法不会覆盖现有的 azureiotsecurity 模块孪生。 使用批处理方法只会为尚不包含安全模块孪生的设备创建新的模块孪生。

请参阅[代理配置](how-to-agent-configuration.md)，了解如何修改或更改现有模块孪生的配置。

若要手动为设备创建新的 azureiotsecurity 模块孪生：

1. 在 IoT 中心，找到并选择要为其创建安全模块孪生的设备。

1. 选择你的设备，然后选择“添加模块标识”。

1. 在“模块标识名称”字段中输入 **azureiotsecurity**。 

1. 选择“保存”。 

## <a name="verify-creation-of-a-module-twin"></a>验证是否要创建模块孪生

验证特定的设备是否存在安全模块孪生：

1. 在 Azure IoT 中心，从“资源管理器”菜单中选择“IoT 设备”。  

1. 输入设备 ID，或者在“查询设备字段”中选择一个选项，然后选择“查询设备” 。

    :::image type="content" source="./media/quickstart/verify-security-module-twin.png" alt-text="查询设备":::

1. 选择该设备或双击选中它，打开“设备详细信息”页面。

1. 选择“模块标识”菜单，在与设备关联的模块标识列表中，确认是否存在 **azureiotsecurity** 模块。 

    :::image type="content" source="./media/quickstart/verify-security-module-twin-3.png" alt-text="与设备关联的模块":::

若要详细了解如何自定义适用于 IoT 的 Defender 模块孪生的属性，请参阅[代理配置](how-to-agent-configuration.md)。

## <a name="next-steps"></a>后续步骤

请转到下一篇文章了解如何调查安全建议…

> [!div class="nextstepaction"]
> [调查安全建议](quickstart-investigate-security-recommendations.md)