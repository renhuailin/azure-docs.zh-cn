---
title: Azure VMware 解决方案的平台更新
description: 了解 Azure VMware 解决方案的平台更新。
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: ce25df2cb221c032f6dd430f292522fe86e69ceb
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771764"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware 解决方案的平台更新

Azure VMware 解决方案的重要更新将于 2021 年 3 月开始应用。 你将通过 Azure 服务运行状况收到通知，其中包括维护时间线。 有关 Azure VMware 解决方案中密钥升级过程和功能的更多详细信息，请参阅 [Azure VMAware 解决方案私有云更新和升级](concepts-upgrades.md)。

## <a name="march-15-2021"></a>2021 年 3 月 15 日 

- Azure VMware 解决方案服务执行维护工作将持续至 2021 年 3 月 19 日，以将私有云中的 vCenter Server 更新为 vCenter Server 6.7 Update 3l 版本。

- 在此期间，VMware vCenter 将不可用，你将无法管理 VM（停止、启动、创建、删除）。 私有云缩放（添加/删除服务器和群集）也将不可用。 VMware 高可用性 (HA) 将继续运行，以便为现有 VM 提供保护。 
 
有关此 vCenter 版本的详细信息，请参阅 [VMware vCenter Server 6.7 Update 3l 发行说明](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)。

## <a name="march-4-2021"></a>2021 年 3 月 4 日

- Azure VMware 解决方案会将 2021 年 3 月 15 日之前针对现有私有云中 ESXi 的补丁应用到 [VMware ESXi 6.7 补丁版本 ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html)。

- 根据 [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)，2021 年 3 月 15 日前还将应用对 vSphere stack 记录的解决方法。

>[!NOTE]
>这是非中断性的，不会影响 Azure VMware 服务或工作负载。 在维护过程中，各种 VMware 警报（如 DVPort 上的网络连接丢失和 DVPort 上的上行链冗余丢失）会出现在 vCenter 中，并在维护过程中自动清除 。

## <a name="post-update"></a>发布更新
完成后，将显示 VMware 组件的较新版本。 如果你注意到任何问题或有任何疑问，请创建支持工单，联系我们的支持团队。





