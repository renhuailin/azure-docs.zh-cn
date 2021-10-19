---
title: VMware 软件版本
description: Azure VMware 解决方案支持的 VMware 软件版本。
ms.topic: include
ms.date: 07/20/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 1c773d723e9d43ee8a4ad0335c569dbf2e1f6388
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855150"
---
<!-- Used in faq.md and concepts-private-clouds-clusters#host-maintenance-and-lifecycle-management -->


新部署的 Azure VMware 解决方案私有云群集中使用的 VMware 软件版本包括：

| 软件              |    版本   |
| :---                  |     :---:    |
| vCenter               |    6.7 U3o   | 
| ESXi                  |    6.7 P05   | 
| vSAN                  |    6.7 P05   |
| HCX                   |    4.1       |
| NSX-T <br />注意：NSX-T 是唯一支持的 NSX 版本。               |      [[!INCLUDE [nsxt-version](nsxt-version.md)]](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/rn/VMware-NSX-T-Data-Center-312-Release-Notes.html)     |


当前运行的软件版本将应用到添加到现有私有云的新群集中。 有关详细信息，请参阅 [VMware 软件版本要求](https://docs.vmware.com/en/VMware-HCX/4.1/hcx-user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)。


