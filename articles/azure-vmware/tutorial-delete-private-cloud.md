---
title: 教程 - 删除 Azure VMware 解决方案私有云
description: 了解如何删除不再需要的 Azure VMware 解决方案私有云。
ms.topic: tutorial
ms.date: 02/09/2021
ms.openlocfilehash: b11b8f902691db4bd71fd3f52aaa67d46efea643
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100875"
---
# <a name="tutorial-delete-an-azure-vmware-solution-private-cloud"></a>Tutorial:删除 Azure VMware 解决方案私有云

如果你不再需要某个 Azure VMware 解决方案私有云，可将其删除。 私有云包括隔离的网络域、专用服务器主机上的一个或多个预配的 vSphere 群集，以及多个虚拟机 (VM)。 在删除私有云时，会删除所有 VM、其中的数据，以及群集。 专用主机在安全地擦除后会返回到可用池。 为客户预配的网络域也会被删除。  

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
