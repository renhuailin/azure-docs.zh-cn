---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: d969b1639dbb39b920c985964abe1239f068d740
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185230"
---
选择“生成”  。 在打开的窗格中，选择要将 Xcode 项目导出到的文件夹。

   导出完成后，系统显示包含导出的 Xcode 项目的文件夹。

   > [!NOTE]
   > 如果出现一个窗口，其中的消息询问你是要替换还是要追加，建议选择“追加​​”，因为它速度更快。 仅当更改场景中的资产时，才应选择“替换”。 例如，添加、删除或更改父级/子级关系时，或者添加、删除或更改属性时。 如果仅更改源代码，“追加​​”就足够了。

## <a name="open-the-xcode-project"></a>打开 XCode 项目

现在可以在 Xcode 中打开 `Unity-iPhone.xcodeproj` 项目。 

你可以启动 Xcode 并打开导出的 `Unity-iPhone.xcodeproj` 项目；也可以通过从导出项目的位置运行以下命令，在 Xcode 中启动项目：

 ```bash
open ./Unity-iPhone.xcodeproj
```

选择根“Unity-iPhone”节点以查看项目设置，然后选择“常规”选项卡。

在“签名”下，确保已启用“自动管理签名” 。 如果未启用，请启用，然后在出现的窗格中选择“自动启用”重置生成设置。

在“部署信息”下，确保“部署目标”设置为“11.0”  。

## <a name="deploy-the-app-to-your-ios-device"></a>将应用部署到 iOS 设备

将 iOS 设备连接到 Mac 并将“活动方案”设置为 iOS 设备。

   ![用于选择设备的“我的 iPhone”按钮的屏幕截图。](./media/spatial-anchors-unity/select-device.png)

选择“生成并运行当前方案”。

   ![“部署并运行”箭头按钮的屏幕截图。](./media/spatial-anchors-unity/deploy-run.png)
