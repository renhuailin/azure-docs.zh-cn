---
title: VMware 软件版本
description: Azure VMware 解决方案支持的 VMware 软件版本。
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 9a1781d163e67c2f9b6a4f756a616e59a969696f
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462502"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


新部署的 Azure VMware 解决方案私有云群集中使用的 VMware 软件版本包括：

| 软件              |    版本   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T <br />注意：NSX-T 是唯一支持的 NSX 版本。               |      2.5     |


新群集已添加到现有私有云，应用当前运行的软件版本。 有关详细信息，请参阅 [VMware 软件版本要求](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)。

私有云软件捆绑包升级的目的是使软件与 VMware 最新发布的版软件捆绑包保持同一版本。 私有云软件版本可能不同于各个软件组件（ESXi、NSX-T、vCenter、vSAN）的最新版本。 你可以找到[私有云更新和升级](../concepts-upgrades.md)中所述的 Azure VMware 解决方案平台软件的常规升级策略和过程。

