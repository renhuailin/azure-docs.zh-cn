---
title: 在 Azure 实验室服务中指定实验室的市场映像
description: 本文介绍如何在 Azure 实验室服务中指定可供实验室创建者用来在实验室帐户中创建实验室的市场映像。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434730"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>指定可供实验室创建者使用的市场映像
作为实验室帐户所有者，你可以指定可供实验室创建者用来在实验室帐户中创建实验室的市场映像。 

## <a name="select-images-available-for-labs"></a>选择可用于实验室的映像
在左侧的菜单上选择“市场映像”。 默认情况下，可以看到映像的完整列表（包括启用的和禁用的）。 可以通过从顶部的下拉列表中选择“仅启用的/仅禁用的”选项对列表进行筛选来仅查看启用的/禁用的映像。  
    
![“市场映像”页](./media/tutorial-setup-lab-account/marketplace-images-page.png)

列表中显示的市场映像只是满足以下条件的映像：
    
- 创建单个 VM。
- 使用 Azure 资源管理器预配 VM
- 不需要购买额外的许可计划

## <a name="disable-images-for-a-lab"></a>禁用实验室的映像 
若要禁用实验室的单个映像，请选择最后一列中的“...”（省略号），然后选择“禁用映像” 。 

![禁用一个映像](./media/tutorial-setup-lab-account/disable-one-image.png) 

或者，可选择映像名称前面的复选框，然后选择工具栏上的“禁用所选映像”。 

若要同时禁用多个映像，请选择映像名称前面的复选框，然后选择工具栏上的“禁用所选映像”。 

![禁用多个映像](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>启用实验室的映像
若要启用已禁用的映像，请选择最后一列中的“...”（省略号），然后选择“启用映像” 。 或者，可选择映像名称前面的复选框，然后选择工具栏上的“启用所选映像”。 

若要同时启用多个映像，请选择映像名称前面的复选框，然后选择工具栏上的“启用所选映像”。 

## <a name="enable-images-at-the-time-of-lab-creation"></a>在创建实验室时启用映像
可以在创建实验室时启用更多映像： 

1. 使用“实验室帐户所有者”凭据登录到 [Azure 实验室服务网站](https://labs.azure.com)
2. 选择默认虚拟机映像或向下箭头。 
3. 选择“启用更多映像选项”。 

    ![启用更多映像选项](./media/specify-marketplace-images/enable-more-images-menu.png)
4. 按照上一部分中的说明来启用你选择的映像。 
5. 可能需要关闭“新建实验室”窗口，然后将其重新打开以查看在上一步中选择的映像。 



## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份创建并管理实验室](how-to-manage-classroom-labs.md)
- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问实验室](how-to-use-classroom-lab.md)
