---
title: Azure VMware 解决方案的平台更新
description: 了解 Azure VMware 解决方案的平台更新。
ms.topic: reference
ms.date: 09/21/2021
ms.openlocfilehash: 3159323fa567bc622c9627ded4e8f70bfac20b4f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636109"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Azure VMware 解决方案的平台更新

从 2021 年 3 月开始，Azure VMware 解决方案将应用重要更新。 你将通过 Azure 服务运行状况收到通知，其中包括维护时间线。 有关详细信息，请参阅[主机维护和生命周期管理](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management)。

## <a name="september-21-2021"></a>2021 年 9 月 21 日
根据 VMware 安全公告 [VMSA-2021-0020](https://www.vmware.com/security/advisories/VMSA-2021-0020.html)，VMware vCenter Server 中的多个漏洞已报告给 VMware。
 
为了解决 VMware 安全公告 [VMSA-2021-0020](https://www.vmware.com/security/advisories/VMSA-2021-0020.html) 中报告的漏洞（CVE-2021-21980、CVE-2021-21991、CVE-2021-21992、CVE-2021-21993、CVE-2021-22005、CVE-2021-22006、CVE-2021-22007、CVE-2021-22008、CVE-2021-22009、CVE-2021-22010、CVE-2021-22011、CVE-2021-22012、CVE-2021-22013、CVE-2021-22014、CVE-2021-22015、CVE-2021-22016、CVE-2021 -22017、CVE-2021-22018、CVE-2021-22019、CVE-2021-22020），vCenter Server 已在所有 Azure VMware 解决方案私有云中更新到 6.7 Update 3o。 所有新的 Azure VMware 解决方案私有云都使用 vCenter Server 版本 6.7 Update 3o 进行部署。
 
有关详细信息，请参阅 [VMware vCenter Server 6.7 Update 3o 发行说明](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3o-release-notes.html)
 
无需进一步执行操作。

## <a name="september-10-2021"></a>2021 年 9 月 10 日

所有新的 Azure VMware 解决方案私有云现在都使用 ESXi 版本 ESXi670-202103001（生成号：17700523）进行部署。 现有私有云中的 ESXi 主机已修补到此版本。

有关此 ESXi 版本的详细信息，请参阅 [VMware ESXi 6.7 补丁版本 ESXi670-202103001](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202103001.html)。




## <a name="july-23-2021"></a>2021 年 7 月 23 日

所有新的 Azure VMware 解决方案私有云现在都使用 NSX-T 版本 [!INCLUDE [nsxt-version](includes/nsxt-version.md)] 进行部署。 现有私有云中的 NSX-T 版本将在 2021 年 9 月升级到 NSX-T [!INCLUDE [nsxt-version](includes/nsxt-version.md)] 版本。
 
你将收到一封电子邮件，其中包括计划内维护日期和时间。 你可以重新计划升级。 该电子邮件还提供了有关升级的组件及其对工作负载、私有云访问和其他 Azure 服务的影响的详细信息。 

有关此 NSX-T 版本的详细信息，请参阅 [VMware NSX-T 数据中心[!INCLUDE [nsxt-version](includes/nsxt-version.md)]发行说明](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/3.1/rn/VMware-NSX-T-Data-Center-312-Release-Notes.html)。




## <a name="may-25-2021"></a>2021 年 5 月 25 日
根据 VMware 安全公告 [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html)，VMware ESXi 和 vSphere 客户端 (HTML5) 中的多个漏洞已报告给 VMware。 

为了解决 VMware 安全公告 [VMSA-2021-0010](https://www.vmware.com/security/advisories/VMSA-2021-0010.html) 中报告的漏洞（[CVE-2021-21985](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21985) 和 [CVE-2021-21986](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-21986)），vCenter Server 已在所有 Azure VMware 解决方案私有云中更新。

不需要采取任何进一步措施。

## <a name="may-21-2021"></a>2021 年 5 月 21 日
 
Azure VMware 解决方案服务将执行维护工作，此维护将持续到 2021 年 5 月 23 日，以向私有云中的 vCenter Server 应用重要更新。  你将通过 Azure 服务运行状况收到通知，其中包括私有云的维护时间线。
 
在此期间，VMware vCenter 将不可用，你将无法管理 VM（停止、启动、创建或删除）。 建议在这段时间内不要规划任何其他活动，例如纵向扩展私有云、创建新网络，等等。
 
在私有云中运行的工作负载不会受到影响。


## <a name="april-26-2021"></a>2021 年 4 月 26 日
所有新的 Azure VMware 解决方案私有云现已与 VMware vCenter 版本 6.7U3l 和 NSX-T 版本 2.5.2 一起部署。 由于在 NSX-T 3.1.1 中识别的某个问题会影响客户 VM 连接，因此我们不会为新的私有云使用 NSX-T 3.1.1。 

VMware 建议的缓解措施适用于当前在 Azure VMware 解决方案上运行 NSX-T 3.1.1 的所有现有私有云。 该解决方法已得到确认，不会对客户 VM 连接产生任何影响。

## <a name="march-24-2021"></a>2021 年 3 月 24 日
所有新的 Azure VMware 解决方案私有云已与 VMware vCenter 版本 6.7U3l 和 NSX-T 版本 3.1.1 一起部署。 截止 2021 年 6 月，所有现有私有云将更新并升级到上述版本。

你将收到一封电子邮件，其中包括计划内维护日期和时间。 你可以重新计划升级。 该电子邮件还提供了有关升级的组件及其对工作负载、私有云访问和其他 Azure 服务的影响的详细信息。  在升级前的一小时，你将收到通知，完成升级后会再次收到通知。

## <a name="march-15-2021"></a>2021 年 3 月 15 日 

- Azure VMware 解决方案服务将执行维护工作，此维护将持续到 2021 年 3 月 19 日，以将私有云中的 vCenter Server 更新为 vCenter Server 6.7 Update 3l 版本。

- VMware vCenter 在此期间将不可用，因此你无法管理 VM（停止、启动、创建、删除）或私有云缩放（添加/删除服务器和群集）。 但是，VMware 高可用性 (HA) 将继续运行，以保护现有 VM。 
 
有关此 vCenter 版本的详细信息，请参阅 [VMware vCenter Server 6.7 Update 3l 发行说明](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)。

## <a name="march-4-2021"></a>2021 年 3 月 4 日

- Azure VMware 解决方案会将 [VMware ESXi 6.7、补丁版本 ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) 应用到现有私有云，此过程将持续到 2021 年 3 月 15 日。

- 根据 [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html)，2021 年 3 月 15 日前还将应用对 vSphere 堆栈记录的解决方法。

>[!NOTE]
>这是非中断性的，不会影响 Azure VMware 服务或工作负载。 在维护过程中，各种 VMware 警报（如 DVPort 上的网络连接丢失和 DVPort 上的上行链冗余丢失）会出现在 vCenter 中，并在维护过程中自动清除 。

## <a name="post-update"></a>发布更新
完成后，将显示 VMware 组件的较新版本。 如果你注意到任何问题或有任何疑问，请创建支持工单，联系我们的支持团队。
