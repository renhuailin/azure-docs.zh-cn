---
title: 教程 - 删除 Azure VMware 解决方案私有云
description: 了解如何删除不再需要的 Azure VMware 解决方案私有云。
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 453e7a3316c342cd724a951eafea0ae9fa045506
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462093"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Tutorial:删除 Azure VMware 解决方案私有云

如果你不再需要某个 Azure VMware 解决方案私有云，可将其删除。 私有云包括隔离的网络域、专用服务器主机上的一个或多个预配的 vSphere 群集，以及多个虚拟机 (VM)。 在删除私有云时，会删除所有 VM、其中的数据，以及群集。 专用 Azure VMware 解决方案主机会被安全地擦除并返回到可用池。 预配的网络地址空间也会被删除。  

> [!CAUTION]
> 删除私有云是不可逆的操作。 删除私有云后，数据将无法恢复，因为此操作会终止所有正在运行的工作负载和组件，并销毁所有私有云数据和配置设置（包括公共 IP 地址）。

## <a name="prerequisites"></a>必备知识

如果以后还会需要这些 VM 和其中的数据，请确保在删除私有云之前先备份这些数据。  这些 VM 及其数据是无法恢复的。


## <a name="delete-the-private-cloud"></a>删除私有云

1. 访问 [Azure 门户](https://portal.azure.com)中的“Azure VMware 解决方案”控制台。

2. 选择要删除的私有云。
 
3. 输入私有云的名称，并选择“是”。 

>[!NOTE]
>完成删除过程需要几小时。  
