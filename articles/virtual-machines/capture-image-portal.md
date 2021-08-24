---
title: 使用门户捕获 VM 的映像
description: 使用 Azure 门户创建 VM 的映像。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/21/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: b9218f6eebedd8f94c06a664518fc718f0eb7d19
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112992110"
---
# <a name="create-an-image-of-a-vm-in-the-portal"></a>在门户中创建 VM 的映像

可以从 VM 创建映像，然后使用该映像创建多个 VM。

对于存储在共享映像库中的映像，可以使用已在其上创建了帐户（专用）的 VM，也可以在创建映像之前通用化该 VM 以删除计算机帐户和其他特定于计算机的信息。 若要通用化 VM，请参阅[通用化 Windows VM](generalize.md)。 有关详细信息，请参阅[通用和专用映像](shared-image-galleries.md#generalized-and-specialized-images)。


## <a name="capture-a-vm-in-the-portal"></a>在门户中捕获 VM 

1. 转到 [Azure 门户](https://portal.azure.com)，然后搜索并选择“虚拟机”。

2. 从列表中选择 VM。

3. 在 VM 的“虚拟机”页面的上方菜单中，选择“捕获”。

   此时会显示“创建映像”页面。

5. 对于“资源组”，选择“新建”并输入名称，或者从下拉列表中选择要使用的资源组。 如果要使用现有库，请选择要使用的库的资源组。

1. 若要在库中创建该映像，请选择“是，将其作为映像版本共享到库”。
    
   若要仅创建托管映像，请选择“否，仅捕获托管映像”。 若要创建托管映像，必须已将 VM 通用化。 其他必需的唯一信息是映像的名称。

6. 如果想要在创建映像后删除源 VM，选择“创建映像后自动删除此虚拟机”。 不建议这样做。

1. 对于“库详细信息”，请选择库，或通过选择“新建”创建一个新库。 

1. 在“操作系统状态”中选择“通用”或“专用”。 有关详细信息，请参阅[通用和专用映像](shared-image-galleries.md#generalized-and-specialized-images)。
 
1. 选择一个映像定义或选择“新建”，为新的[映像定义](shared-image-galleries.md#image-definitions)提供名称和信息。

1. 输入[映像版本](shared-image-galleries.md#image-versions)号。 如果这是此映像的第一个版本，请键入“1.0.0”。

1. 如果你希望在指定“最新版本”作为映像版本时包含此版本，请让“从最新版本中排除”选项保持未选中状态。

1. 选择一个生命周期结束日期。 此日期可用于跟踪需要停用较旧映像的时间。

1. 在[复制](shared-image-galleries.md#replication)下，选择默认的副本计数，然后选择要将映像复制到其中的任何其他区域。

8. 完成操作后，选择“查看 + 创建”。

1. 验证通过后，选择“创建”以创建映像。



## <a name="next-steps"></a>后续步骤

- [共享映像库概述](shared-image-galleries.md)  
