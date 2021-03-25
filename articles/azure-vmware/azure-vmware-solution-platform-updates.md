---
title: Azure VMware 解决方案的平台更新
description: 了解 Azure VMware 解决方案的平台更新。
ms.topic: reference
ms.date: 03/05/2021
ms.openlocfilehash: 1f1a0c29ffde20b54abb9e4d1d1127fc93a712d9
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443997"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware 解决方案的平台更新


## <a name="march-4-2021"></a>2021 年 3 月 4 日

Azure VMware 解决方案的重要更新将于 2021 年 3 月开始应用。 你将通过 Azure 服务运行状况收到通知，其中包括维护时间线。 本文将介绍在此维护操作期间将发生的事件以及对私有云的更改。

- Azure VMware 解决方案会将对现有私有云中 ESXi 的修补程序应用到 [VMware ESXi 6.7、补丁版本 ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html)，此更新会持续到 2021 年 3 月 15 日。

- 根据 [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)，2021 年 3 月 15 日前还将应用对 vSphere stack 记录的解决方法。

>[!NOTE]
>这是非中断性的，不会影响 Azure VMware 服务或工作负载。 在维护过程中，各种 VMware 警报（如 DVPort 上的网络连接丢失和 DVPort 上的上行链冗余丢失）会出现在 vCenter 中，并在维护过程中自动清除 。


## <a name="post-update"></a>发布更新
完成后，将显示 VMware 组件的较新版本。 如果你注意到任何问题或有任何疑问，请创建支持工单，联系我们的支持团队。



