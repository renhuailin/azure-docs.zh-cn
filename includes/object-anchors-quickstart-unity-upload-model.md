---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d8dfc3d4b7a8447250481b98c1adadc865a29da1
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102532600"
---
### <a name="upload-your-model"></a>上传模型

如果尚无 Object Anchors 模型，请按照[创建模型](/azure/object-anchors/quickstarts/get-started-model-conversion)中的说明进行操作，以创建一个模型。 然后，返回此处。

将 HoloLens 连接到 Windows 设备门户后，请按照以下步骤上传要供应用使用的模型：

1. 在 Windows 设备门户中，转到“系统”>“文件资源管理器”>“LocalAppData”。 在这里，你应该会在应用列表中看到你的应用程序。

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="文件资源管理器":::

2. 打开应用程序，并单击 `LocalState` 文件夹。

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="打开 LocalState 文件夹":::

3. 将模型文件上传到 `LocalState` 文件夹。

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="在门户中上传模型":::

    重新从 HoloLens 启动应用程序。 现在可以检测与该模型匹配的对象。
