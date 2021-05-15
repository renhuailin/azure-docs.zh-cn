---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100552746"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>添加 StorSimple 备份策略

1. 转到 StorSimple 设备并单击“备份策略”。

2. 在“备份策略”边栏选项卡中，从命令栏中单击“+ 添加策略”。
   
    ![添加备份策略](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. 在“创建备份策略”边栏选项卡中，执行以下步骤：
   
   1. “选择设备”会根据你选择的设备自动填充。
   
   2. 指定备份“策略名称”，其中包含 3 到 150 个字符。 创建策略后，无法重命名该策略。
       
   3. 要向此备份策略分配卷，请选择“添加卷”，然后在卷的表格式列表中，单击相应的复选框来将一个或多个卷分配到此备份策略。

       ![添加备份策略 2](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. 要为此备份策略定义计划，请单击“第一个计划”，并修改以下参数：<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![添加备份策略 3](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. 从“快照类型”中，选择“云”或“本地”。

       2. 指示备份频率（指定一个数字，并从下拉列表中选择“天”或“周”。

       3. 输入保留计划。

       4. 输入备份策略开始的时间和日期。

       5. 单击“确定”来定义计划。

   5. 单击“创建”来创建备份策略。
   
   6. 在备份策略创建后，将收到通知。 新添加的策略会显示在“备份策略”边栏选项卡上的表格式视图中。

       ![添加备份策略 5](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
