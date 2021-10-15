---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 1c85888c04859769d82b9c5ebe4691797e404adf
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367313"
---
在数据标签过程中，你可能想要添加更多标签以便对项目进行分类。  例如，你可能需要添加“未知”或“其他”标签来表明混淆的项目。

使用以下步骤将一个或多个标签添加到项目：

1. 在“数据标记”主页上选择该项目。
1. 在页面右上角，将“正在运行”切换为“已暂停”以使标记人员停止进行其活动。
1. 选择“详细信息”选项卡。
1. 在左侧的列表中，选择“标签类”。
1. 在列表顶部，选择“+ 添加标签”![添加标签](../articles/machine-learning/media/how-to-create-labeling-projects/add-label.png)
1. 在窗体中，添加新标签。 然后选择如何继续该项目。  由于已更改可用标签，因此请选择如何处理已标记的数据：
    * 重新开始，同时删除所有现有标签。  如果要从新的完整标签集开始标记，请选择此选项。 
    * 重新开始，同时保留所有现有标签。  选择此选项可将所有数据标记为“未标记”，但保留现有标签作为先前标记的图像的默认标记。
    * 继续，同时保留所有现有标签。 选择此选项可以保留所有按原样标记的数据，并开始对尚未标记的数据使用新标签。
1. 根据需要修改新标签的说明页。
1. 添加所有新标签后，请在页面右上方将“已暂停”切换为“正在运行”以重启项目。  