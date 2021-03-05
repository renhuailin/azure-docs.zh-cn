---
title: 快速入门：使用 Unity 创建 HoloLens 应用
description: 此快速入门介绍如何使用 Object Anchors 生成 HoloLens Unity 应用。
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: caf64883635d7fa1746d12caf24333a22ba2fa98
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747315"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity"></a>快速入门：在 Unity 中使用 Azure Object Anchors 创建 HoloLens 应用

本快速入门介绍如何创建使用 [Azure Object Anchors](../overview.md) 的 Unity HoloLens 应用。 Azure Object Anchors 是一项托管的云服务，它将 3D 资产转换为 AI 模型，以便为 HoloLens 启用对象感知混合现实体验。 完成本教程后，你将拥有一个使用 Unity 生成的 HoloLens 应用，可检测到物理世界中的对象。

将了解如何执行以下操作：

> [!div class="checklist"]
> * 准备 Unity 生成设置。
> * 导出 HoloLens Visual Studio 项目。
> * 部署应用并在 HoloLens 2 设备上运行它。

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>打开示例项目

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

在 Unity 中，打开 `quickstarts/apps/unity/basic` 项目。

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build and run](../../../includes/object-anchors-quickstart-unity-build-run.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>运行示例应用

打开设备，选择“所有应用”，然后找到并启动应用。 在 Unity 初始屏幕后，你将看到一条消息，指示对象观察程序已经初始化。 但是，你需要将模型添加到应用。

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

应用在当前视野中查找对象，然后在检测到对象后进行跟踪。 当某个实例与用户的位置相距 6 米时，它将被删除。 调试文本显示有关实例的详细信息，例如 ID、更新的时间戳和表面覆盖率。

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Unity HoloLens 与 MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [概念：SDK 概述](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [常见问题](../faq.md)
