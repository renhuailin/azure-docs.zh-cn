---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044540"
---
### <a name="build-and-deploy-the-app"></a>生成并部署应用

打开 Unity 生成的 `.sln` 文件。 将生成配置更改为以下配置。

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="生成配置":::

接下来，需要配置“远程计算机 IP 地址”，以便部署和调试应用。

右键单击“应用”项目，然后选择“属性”。 在“属性”页中，选择“配置属性”->“调试”。 将“计算机名称”值更改为 HoloLens 设备的 IP 地址，然后单击“应用”。

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="远程调试":::

关闭“属性”页。 单击“远程计算机”。 应用应开始生成并部署到远程设备。 请确保设备处于活动状态。
