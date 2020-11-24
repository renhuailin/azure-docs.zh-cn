---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 2b592e1f7ea227e528915c1531e56dabf1a4bc4d
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2020
ms.locfileid: "95556142"
---
#### <a name="to-add-or-remove-a-volume"></a>添加或删除卷

1. 转到 StorSimple 设备并单击“备份策略”。

2. 在策略的表格式列表中，选择并单击要修改的策略。 单击右键以调用上下文菜单，并选择“添加/删除卷”。

    ![备份策略在左侧的设置窗格中突出显示。 在备份策略中，只会突出显示策略 mybupol1。 上下文菜单中突出显示了 "添加/删除卷"。](./media/storsimple-8000-add-remove-volume-backup-policy-u2/addvolbupol1.png)

3. 在 " **添加/删除卷** " 边栏选项卡中，选中或清除 " (es) 添加或删除卷的复选框。 通过选中或清除相应的复选框来选择/清除多个卷。

    ![选择 myssvolsrch2 卷。 卷信息会突出显示，就像 "添加" 按钮一样。](./media/storsimple-8000-add-remove-volume-backup-policy-u2/addvolbupol3.png)

    如果将不同卷容器中的卷分配到某个备份策略，则需要记得一起对这些卷容器进行故障转移。 会看到有关此影响的警告。

    ![其中突出显示了一个容器中的卷，另一个容器中的两个卷将一起突出显示。](./media/storsimple-8000-add-remove-volume-backup-policy-u2/addvolbupol2.png)

4. 修改备份策略时，将收到通知。 备份策略列表也将更新。

    ![在 "备份策略" 列表中，将突出显示 "策略 mybupol1 的卷数"。 其值为1。](./media/storsimple-8000-add-remove-volume-backup-policy-u2/addvolbupol6.png)




