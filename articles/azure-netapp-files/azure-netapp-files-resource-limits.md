---
title: Azure NetApp 文件的资源限制 | Microsoft Docs
description: 介绍 Azure NetApp 文件资源的限制以及如何请求调高资源限制。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2021
ms.author: b-juche
ms.openlocfilehash: 06be68fb1de224bbbcad13e71e7f4069e44f8309
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725554"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp 文件的资源限制

了解 Azure NetApp 文件的资源限制，可帮助你管理卷。

## <a name="resource-limits"></a>资源限制

下表描述 Azure NetApp 文件的资源限制：

|  资源  |  默认限制  |  可通过支持请求进行调整  |
|----------------|---------------------|--------------------------------------|
|  [每个订阅的区域容量配额](#regional-capacity-quota)   |  25 TiB  |  是  |
|  每个 Azure 区域每个订阅的 NetApp 帐户数  |  10    |  是   |
|  每个 NetApp 帐户的容量池数   |    25     |   是   |
|  每个订阅的卷数   |    500     |   是   |
|  每个容量池的卷数     |    500   |    是     |
|  每个卷的快照数       |    255     |    否        |
|  每个 Azure 虚拟网络委托给 Azure NetApp 文件的子网数    |   1   |    否    |
|  含有 Azure NetApp 文件（包括立即对等互连的 VNet）的 VNet 中使用的 IP 数   |    1000   |    否   |
|  单个容量池的最小大小   |  4 TiB     |    否  |
|  单个容量池的最大大小    |  500 TiB   |   否   |
|  单个卷的最小大小    |    100 GiB    |    否    |
|  单个卷的最大大小     |    100 TiB    |    否    |
|  单个文件的最大大小     |    16 TiB    |    否    |    
|  单个目录中目录元数据的最大大小      |    320 MB    |    否    |    
|  单个目录中的文件数上限  | 大约 400 万。 <br> 参阅[确定目录是否将达限制大小](#directory-limit)。  |    否    |   
|  每个卷的最大文件数 ([maxfiles](#maxfiles))     |    1 亿    |    是    |    
|  每个卷的最大导出策略规则数     |    5  |    否    | 
|  分配给手动 QoS 卷的最小吞吐量     |    1 MiB/s   |    否    |    
|  分配给手动 QoS 卷的最大吞吐量     |    4,500 MiB/s    |    否    |    
|  跨区域复制数据保护卷（目标卷）的数量     |    5    |    是    |     

有关详细信息，请参阅[容量管理的常见问题解答](azure-netapp-files-faqs.md#capacity-management-faqs)。

## <a name="determine-if-a-directory-is-approaching-the-limit-size"></a>确定目录是否将达限制大小<a name="directory-limit"></a>  

可以从客户端使用 `stat` 命令来查看目录是否即将达到目录元数据的最大大小限制 (320 MB)。   

对于 320-MB 的目录，块数为 655360，每个块的大小为 512 字节。  （即 320x1024x1024/512。）根据此数字，相当于一个 320-MB 的目录最多可包含大约 400 万个文件。 但是，实际的最大文件数目可能更小，具体取决于多种因素，例如，目录中包含非 ASCII 字符的文件数。 因此，应按如下所示使用 `stat` 命令来确定目录是否接近其限制。  

示例：

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```

## <a name="maxfiles-limits"></a>Maxfiles 限制 <a name="maxfiles"></a> 

Azure NetApp 文件卷有一个名为 maxfiles 的限制。 Maxfiles 限制是一个卷可以包含的文件数。 Linux 文件系统将此限制称为 Inode。 Azure NetApp 文件卷的 maxfiles 限制是基于卷的大小（配额）编制索引。 卷的 maxfiles 限制按照每 TiB 预配的卷大小 2000 万个文件的速率递增或递减。 

服务基于其预配的大小动态调整卷的 maxfiles 限制。 例如，最初配置为 1 TiB 大小的卷的 maxfiles 限制将为 2000 万。 对卷大小的后续更改会根据以下规则自动重新调整 maxfiles 限制： 

|    卷大小（配额）     |  Maxfiles 限制的自动重新调整    |
|----------------------------|-------------------|
|    <= 1 TiB                |    2000 万     |
|    > 1 TiB 但 <= 2 TiB    |    4000 万     |
|    > 2 TiB 但 <= 3 TiB    |    6000 万     |
|    > 3 TiB 但 <= 4 TiB    |    8000 万     |
|    > 4 TiB                 |    1 亿    |

如果已为卷分配至少 4 TiB 的配额，则可发起[支持请求](#limit_increase)以将 maxfiles (Inode) 限制提高到 1 亿以上。 每增加 1 亿个文件（或低于此数量），需要将相应的卷配额增加 4 TiB。  例如，如果将 maxfiles 限制从 1 亿个文件增加到 2 亿个文件（或二者之间的任意数量），则需要将卷配额从 4 TiB 增加到 8 TiB。

如果卷配额至少为 20 TiB，则可以将 maxfiles 限制增加到 5 亿。 <!-- ANF-11854 --> 

## <a name="regional-capacity-quota"></a>区域容量配额

Azure NetApp 文件具有基于容量的区域限制。 每个订阅的标准容量限制为每个区域 25 TiB，涵盖所有服务级别。   

可以通过提交特定服务和订阅限制（配额）支持票证来请求增加容量，如下所示：

1. 请在门户中的“支持 + 故障排除”中启动支持请求过程：  

    ![显示“支持 + 故障排除”菜单的屏幕截图。](../media/azure-netapp-files/support-troubleshoot-menu.png)   

2.  选择服务和订阅限制（配额）问题类型，并输入所有相关的详细信息：

    ![显示“服务和订阅限制”菜单的屏幕截图。](../media/azure-netapp-files/service-subscription-limits-menu.png)   

3. 单击“详细信息”选项卡中的“输入详细信息”链接，然后选择“每个订阅的 TiB”配额类型：   

    ![显示“详细信息”选项卡中“输入详细信息”链接的屏幕截图。](../media/azure-netapp-files/support-details.png)   

    ![显示“配额详细信息”窗口的屏幕截图。](../media/azure-netapp-files/support-quota-details.png)   

4.  一定要在“支持方法”页上选择“严重级别 B - 中等影响”：  

    ![显示“支持方法”窗口的屏幕截图。](../media/azure-netapp-files/support-method-severity.png)   

5. 完成请求过程以发出请求。 
 
提交票证后，请求将发送到 Azure 容量管理团队进行处理。 你通常会在 2 个工作日内收到回复。 Azure 容量管理团队可能会与你联系，以处理大容量请求。
 
区域容量配额增加不会导致计费增加。 计费依据依旧为预配的容量池。

## <a name="request-limit-increase"></a>请求调高限制 <a name="limit_increase"></a> 

可创建 Azure 支持请求以上调[资源限制](#resource-limits)表中的可调整限制。 

从 Azure 门户导航平面： 

1. 单击“帮助 + 支持”。
2. 单击“+ 新建支持请求”。
3. 在“基本信息”选项卡中提供以下信息： 
    1. 问题类型： 选择“服务和订阅限制(配额)”。
    2. 订阅：选择需要增加配额的资源的订阅。
    3. 配额类型：选择“存储: Azure NetApp 文件限制”。
    4. 单击“下一步: 解决方案”
4. 在“详细信息”选项卡上：
    1. 在“说明”框中，为相应的资源类型提供以下信息：

        |  资源  |    父资源      |    请求的新限制     |    配额增加的原因       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  帐户 |  *订阅 ID*   |  请求的新的最大帐户数    |  哪个方案或用例提示了请求？  |
        |  池    |  订阅 ID、NetApp 帐户 URI  |  请求的新的最大池数   |  哪个方案或用例提示了请求？  |
        |  Volume  |  订阅 ID、NetApp 帐户 URI、容量池 URI   |  请求的新的最大卷数     |  哪个方案或用例提示了请求？  |
        |  Maxfiles  |  订阅 ID、NetApp 帐户 URI、容量池 URI、卷 URI   |  请求的新的最大 maxfiles 数     |  哪个方案或用例提示了请求？  |    
        |  跨区域复制数据保护卷  |  订阅 ID、目标 NetApp 帐户 URI、目标容量池 URI、源 NetApp 帐户 URI、源容量池 URI、源卷 URI   |  请求的跨区域复制数据保护卷（目标卷）的新的最大数量     |  哪个方案或用例提示了请求？  |    

    2. 指定相应的支持方法并提供你的协定信息。

    3. 单击“下一步: 查看 + 创建”以创建请求。 


## <a name="next-steps"></a>后续步骤  

- [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)
