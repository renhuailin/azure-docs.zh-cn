---
title: VMware 软件版本
description: Azure VMware 解决方案支持的 VMware 软件版本。
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825087"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Azure VMware 解决方案私有云群集中使用的 VMware 软件的当前软件版本包括：

| 软件              |    版本   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>NSX-T 是唯一支持的 NSX 版本。

对于私有云中的任何新群集，软件版本均与当前运行的版本匹配。 对于订阅中的任何新私有云，将安装软件堆栈的最新版本。 有关详细信息，请参阅 [VMware 软件版本要求](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)。

私有云软件捆绑升级将软件保存在 VMware 的最新软件捆绑版本中。 私有云软件版本可能与各个软件组件的最新版本不同 (ESXi，NSX-T，vCenter，vSAN) 。 你可以找到 [私有云更新和升级](../concepts-upgrades.md)中所述的 Azure VMware 解决方案平台软件的常规升级策略和过程。

