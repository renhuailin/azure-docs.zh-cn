---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 71f18cf8448060385ea38be9b2719b1ed545c5d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545211"
---
> [!NOTE] 
> 在创建卷容器后，无法修改与其关联的加密设置和存储帐户凭据。

#### <a name="to-modify-a-volume-container"></a>修改卷容器

1. 转到 StorSimple 设备管理器服务，并导航到“管理” > “卷容器”。

2. 从卷容器的表格式列表中，选择要修改的卷容器。 在 **“设备”** 页上选择设备，双击它，然后单击 **“卷容器”** 选项卡。

3. 在卷容器的表格式列表中，选择要修改的卷容器。 在打开的边栏选项卡的命令栏中，单击“修改”。

    ![修改卷容器](./media/storsimple-8000-modify-volume-container/modify-volume-container-01.png)

4. 在“修改卷容器”边栏选项卡中，执行以下步骤：
   
   1. 指定与卷容器关联的名称、加密密钥和存储帐户后，无法将其更改。 更改关联的带宽设置。<!--STEPS NEED WORK. Updated screen doesn't show alternative to Unlimited or subsequent steps if they customize bandwidth. Can we talk them through this (briefly)?-->
      
       ![更改带宽设置](./media/storsimple-8000-modify-volume-container/modify-volume-container-02.png)<!--New graphic based on: modify-volume-container-bw-setting.png-->

   1.  单击" **确定**"。<!--If they choose Custom, do they still click OK, or are there more steps?-->

5. 在“修改卷容器”对话框的下一页中：<!--This step happens only if they choose Custom bandwidth? Are the steps similar to those in "Add volume container," step 3f, above?"-->
   
   1. 从下拉列表中选择现有的带宽模板。
   1. 查看指定的带宽模板的计划设置。
   1. 单击“保存”，确认所做的更改。
      
       ![确认所做的更改](./media/storsimple-8000-modify-volume-container/modify-volume-container-03.png)

      “卷容器”边栏选项卡随即会更新，以反映所做的更改。
