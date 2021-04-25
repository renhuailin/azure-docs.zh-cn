---
title: VMware 软件版本
description: Azure VMware 解决方案支持的 VMware 软件版本。
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: a6441b55bbc6a8f694c50bbf022a6a2ae52d60bf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097607"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


新部署的 Azure VMware 解决方案私有云群集中使用的 VMware 软件版本包括：

| 软件              |    版本   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3l    | 
| ESXi                  |    6.7 U3l    | 
| vSAN                  |    6.7 U3l    |
| NSX-T <br />注意：NSX-T 是唯一支持的 NSX 版本。               |      3.1.1     |


新群集已添加到现有私有云，应用当前运行的软件版本。 有关详细信息，请参阅 [VMware 软件版本要求](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)。

私有云软件捆绑包升级的目的是使软件与 VMware 最新发布的版软件捆绑包保持同一版本。 私有云软件版本可能不同于各个软件组件（ESXi、NSX-T、vCenter、vSAN）的最新版本。 你可以找到[私有云更新和升级](../concepts-upgrades.md)中所述的 Azure VMware 解决方案平台软件的常规升级策略和过程。

