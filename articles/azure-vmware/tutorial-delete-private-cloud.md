---
title: 教程 - 删除 Azure VMware 解决方案私有云
description: 了解如何删除不再需要的 Azure VMware 解决方案私有云。
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: ed1a4fdc2487bf470c96afa5ff8b3cde67336a40
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730216"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Tutorial:删除 Azure VMware 解决方案私有云

如果你不再需要某个 Azure VMware 解决方案私有云，可将其删除。 私有云包括：

* 独立的网络域

* 专用服务器主机上的一个或多个预配 vSphere 群集

* 多个虚拟机 (VM)

删除私有云时，将删除所有 VM、其数据、群集和预配的网络地址空间。 专用 Azure VMware 解决方案主机会被安全地擦除并返回到可用池。   

> [!CAUTION]
> 删除私有云会终止所有正在运行的工作负载和组件，并且此操作不可逆。 删除私有云后，将无法恢复数据。

## <a name="prerequisites"></a>必备知识

如果以后还会需要这些 VM 和其中的数据，请确保在删除私有云之前先备份这些数据。  不幸的是，这些 VM 及其数据是无法恢复的。


## <a name="delete-the-private-cloud"></a>删除私有云

1. 访问 [Azure 门户](https://portal.azure.com)中的“Azure VMware 解决方案”控制台。

2. 选择要删除的私有云。
 
3. 输入私有云的名称，并选择“是”。 

>[!NOTE]
>完成删除过程需要几小时。  
