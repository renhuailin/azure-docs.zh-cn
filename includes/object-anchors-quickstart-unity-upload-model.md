---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 08/02/2021
ms.author: crtreasu
ms.openlocfilehash: ce942908ffa068b0502bdac7f9472543355dbbce
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122254177"
---
## <a name="upload-your-model"></a>上传模型

在运行应用之前，需要使模型可供应用使用。
如果尚无 Object Anchors 模型，请按照[创建模型](../articles/object-anchors/quickstarts/get-started-model-conversion.md)中的说明进行操作，以创建一个模型。 然后，返回此处。

打开 HoloLens 的电源并将其连接到开发设备 (PC) 后，按照以下步骤将模型上传到 HoloLens 上的“3D 对象”文件夹：

1. 同时按 Ctrl 和 C (Ctrl + C)，选择并复制要使用的模型。

2. 同时按 Windows 徽标键和 E (Win + E) 启动文件资源管理器。 你应会在左窗格中看到 HoloLens 与其他驱动器和文件夹一起列出。

    :::image type="content" source="./media/object-anchors-quickstarts-unity/file-explorer-launch.png" alt-text="文件资源管理器":::

3. 点击 HoloLens 链接，在右窗格中显示 HoloLens 设备上的存储。

    :::image type="content" source="./media/object-anchors-quickstarts-unity/file-explorer-internal-storage.png" alt-text="打开 HoloLens 内部存储":::

4. 在文件资源管理器中，转到“内部存储”>“3D 对象”。 现在，可以通过同时按 Ctrl 键和 V (Ctrl + V) 将模型粘贴到“3D 对象”文件夹中。

    :::image type="content" source="./media/object-anchors-quickstarts-unity/file-explorer-open-3d-objects.png" alt-text="将模型粘贴到“3D 对象”文件夹中":::