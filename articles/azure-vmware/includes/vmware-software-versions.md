---
title: VMware 软件版本
description: Azure VMware 解决方案支持的 VMware 软件版本。
ms.topic: include
ms.date: 07/20/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: c80aed722a58fd8178e78d1ea98b888abdd8b830
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122228933"
---
<!-- Used in faq.md and concepts-private-clouds-clusters#host-maintenance-and-lifecycle-management -->


新部署的 Azure VMware 解决方案私有云群集中使用的 VMware 软件版本包括：

| 软件              |    版本   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6.7 U3l   | 
| ESXi                  |    6.7 U3l   | 
| vSAN                  |    6.7 U3l   |
| HCX                   |    4.1       |
| NSX-T <br />注意：NSX-T 是唯一支持的 NSX 版本。               |      [[!INCLUDE [nsxt-version](nsxt-version.md)]](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/rn/VMware-NSX-T-Data-Center-312-Release-Notes.html)     |


当前运行的软件版本将应用到添加到现有私有云的新群集中。 有关详细信息，请参阅 [VMware 软件版本要求](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)。


