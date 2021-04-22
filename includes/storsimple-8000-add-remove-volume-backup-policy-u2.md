---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2b592e1f7ea227e528915c1531e56dabf1a4bc4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95556142"
---
#### <a name="to-add-or-remove-a-volume"></a>添加或删除卷

1. 转到 StorSimple 设备并单击“备份策略”。

2. 在策略的表格式列表中，选择并单击要修改的策略。 单击右键以调用上下文菜单，并选择“添加/删除卷”。

    ![左侧“设置”窗格中突出显示了备份策略。 在“备份策略”中，突出显示了唯一的策略 mybupol1。 上下文菜单中突出显示了“添加/删除卷”。](./media/storsimple-8000-add-remove-volume-backup-policy-u2/addvolbupol1.png)

3. 在“添加/删除卷”边栏选项卡中，选择或清除复选框以添加或删除卷。 可以通过选中或清除对应的复选框来选择/清除多个卷。

    ![myssvolsrch2 卷处于选中状态。 突出显示了卷信息和“添加”按钮。](./media/storsimple-8000-add-remove-volume-backup-policy-u2/addvolbupol3.png)

    如果将不同卷容器中的卷分配到某个备份策略，则需要记得一起对这些卷容器进行故障转移。 会看到有关此影响的警告。

    ![突出显示了一个容器中的一个卷，同时突出显示了另一个容器中的两个卷。](./media/storsimple-8000-add-remove-volume-backup-policy-u2/addvolbupol2.png)

4. 修改备份策略时，将收到通知。 备份策略列表也将更新。

    ![在“备份策略”列表中，突出显示了策略 mybupol1 的卷数。 其值为 1。](./media/storsimple-8000-add-remove-volume-backup-policy-u2/addvolbupol6.png)




