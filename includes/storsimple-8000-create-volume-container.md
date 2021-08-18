---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 4ad58078207a90966134ec4d6cf8815f614e4341
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449828"
---
#### <a name="to-create-a-volume-container"></a>创建卷容器

1. 转到 StorSimple Device Manager 服务并单击“设备”。 从设备的表格式列表中，选择并单击某个设备。 

    ![卷容器边栏选项卡](./media/storsimple-8000-create-volume-container/create-volume-container-01.png)

2. 在设备仪表板中，单击“+ 添加卷容器”。

    ![卷容器边栏选项卡 2](./media/storsimple-8000-create-volume-container/create-volume-container-02.png)

3. 在“添加卷容器”边栏选项卡中：
   
   1. 将自动选择设备。
   2. 提供卷容器的“名称”。 名称长度必须为 3 到 32 个字符。 创建卷容器后，无法重命名卷容器。
   3. 选择“启用云存储加密”  启用从设备发送到云中的数据加密。
   4. 提供并确认长度在 8 到 32 个字符之间的 **“云存储加密密钥”**。 设备使用此密钥访问加密的数据。
   5. 选择与该卷容器关联的 **“存储帐户”**。 可以选择现有存储帐户或选择在创建服务时生成的默认帐户。 还可以使用 **“添加新帐户”** 选项，指定未与此服务订阅链接的存储帐户。
   6. 如果你希望使用所有可用带宽，请在 **“指定带宽”** 下拉列表中选择 **“无限制”**。
   
      如果你已获取带宽使用信息，则可以通过指定“选择带宽模板”来根据计划分配带宽。 有关分步过程，请转到 [添加带宽模板](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template)。

      ![卷容器边栏选项卡 3](./media/storsimple-8000-create-volume-container/create-volume-container-06-b.png)<!--New graphic. Source: add-volume-container-bw-setting.-->

   7. 单击“创建”。

        <!--![Volume container blade 4](./media/storsimple-8000-create-volume-container/create-volume-container-06.png)-->
   
       成功创建卷容器后，会收到通知。

       ![卷容器创建通知](./media/storsimple-8000-create-volume-container/create-volume-container-08.png)

   新创建的卷容器会在设备的卷容器列表中列出。

   ![“添加卷容器”边栏选项卡](./media/storsimple-8000-create-volume-container/create-volume-container-09.png)
