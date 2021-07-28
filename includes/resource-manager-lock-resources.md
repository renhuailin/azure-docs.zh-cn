---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/03/2021
ms.author: tomfitz
ms.openlocfilehash: 8864e8025c77fb0b4b6efc694d9f4938e9ed8ae7
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108737444"
---
1. 在要锁定的资源、资源组或订阅的“设置”边栏选项卡中，选择“锁定”。

    :::image type="content" source="media/resource-manager-lock-resources/select-lock.png" alt-text="选择锁。":::

1. 要添加锁，请选择“添加”。 如果要在父级别创建锁，请选择父级。 当前选定的资源将从父级继承锁。 例如，可以锁定资源组，以便向其所有资源应用锁。

    :::image type="content" source="media/resource-manager-lock-resources/add-lock.png" alt-text="添加锁。":::

1. 为该锁提供名称和锁级别。 （可选）可以添加注释来描述该锁。

    :::image type="content" source="media/resource-manager-lock-resources/set-lock.png" alt-text="设置锁。":::

1. 要删除锁，请选择“删除”按钮。

    :::image type="content" source="media/resource-manager-lock-resources/delete-lock.png" alt-text="删除锁。":::
