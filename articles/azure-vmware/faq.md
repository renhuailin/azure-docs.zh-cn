---
title: 常见问题
description: 提供有关 Azure VMware 解决方案的一些常见问题的解答。
ms.topic: conceptual
ms.date: 11/12/2020
ms.author: dikamath
ms.openlocfilehash: c9a8b5f9e02cc76789755bf81d309826de1459c5
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920587"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>有关 Azure VMware 解决方案的常见问题

有关 Azure VMware 解决方案的常见问题的解答。

## <a name="general"></a>常规

#### <a name="what-is-azure-vmware-solution"></a>什么是 Azure VMware 解决方案？

随着企业采用 IT 现代化策略来提高业务灵活性、降低成本和加速创新，混合云平台已成为客户数字转换的关键启用能力。 Azure VMware 解决方案将 VMware 的 Software-Defined 数据中心 (SDDC) 软件与 Microsoft Azure 全局云服务生态系统相结合。 Azure VMware 解决方案经过管理，以满足性能、可用性、安全性和合规性要求。

## <a name="azure-vmware-solution-service"></a>Azure VMware 解决方案服务

#### <a name="where-is-azure-vmware-solution-available-today"></a>Azure VMware 解决方案目前可用在何处？

正在将该服务连续添加到新区域，因此请查看 [最新的服务可用性信息](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) 以了解更多详细信息。 

#### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Azure VMware 解决方案实例中运行的工作负荷是使用还是与 Azure 服务集成？

所有 Azure 服务都将可供 Azure VMware 解决方案客户使用。 需要逐一解决特定服务的性能和可用性限制。

#### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>我是否使用与现在相同的工具来管理私有云资源？

是的。 Azure 门户用于部署和大量管理操作。 vCenter 和 NSX Manager 用于管理 vSphere 和 NSX-T 资源。

#### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>可以使用我的本地 vCenter 来管理私有云吗？

在启动时，Azure VMware 解决方案不支持跨本地和私有云环境的单一管理体验。 将通过私有云的本地 vCenter 和 NSX Manager 来管理私有云群集。

#### <a name="can-i-use-vrealize-suite-running-on-premises"></a>可以使用在本地运行的 vRealize Suite 吗？ 

可以根据具体情况评估特定集成和用例。

#### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>能否将 vSphere Vm 从本地环境迁移到 Azure VMware 解决方案私有云？

是的。 如果满足标准跨 vCenter [vMotion 要求](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952) ，则可以使用 VM 迁移和 VMotion 将 vm 移到私有云。

#### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>本地环境中是否需要特定版本的 vSphere？

所有云环境均随附于本地环境中的 HCX、vSphere 5.5 或更高版本。

#### <a name="what-does-the-change-control-process-look-like"></a>变更控制过程是怎样的？

对服务本身所做的更新将遵循 Microsoft Azure 的标准更改管理流程。 所有工作负载管理任务和关联的变更管理过程均由客户负责。

#### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>这与 Azure VMware Solution by CloudSimple 有何不同？

对于新的 Azure VMware 解决方案，Microsoft 与 VMware 具有云服务提供商直接合作伙伴关系。 新解决方案完全是由 Microsoft 设计、构建和支持的，并由 VMware 认可。 从体系结构上来说，解决方案是一致的，VMware 技术堆栈是在专用的 Azure 基础结构上运行的。



#### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Azure VMware 解决方案 Vm 是否可以通过 VMRC 来管理？
是的，如果安装它的系统可以访问私有云 vCenter，并使用公共 DNS 来解析 ESXi 主机名。

#### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>是否有关于在 Azure VMware 解决方案 Vm 中安装和使用 VMRC 的特殊说明？
否，使用 [VMware 提供的说明](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html) ，并满足这些说明中指定的 VM 先决条件。 

#### <a name="is-vmware-hcx-supported-on-vpns"></a>Vpn 上是否支持 VMware HCX？
没有，因为带宽和延迟要求。

#### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>Azure 堡垒是否可用于连接到 Azure VMware 解决方案 Vm？
Azure 堡垒是推荐用于连接到跳转盒的服务，以防止向 internet 公开 Azure VMware 解决方案。 不能使用 Azure 堡垒连接到 Azure VMware 解决方案 Vm，因为它们不是 Azure IaaS 对象。

#### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>Azure 负载均衡器是否可用于 Azure VMware 解决方案 Vm？
不是。 Azure 负载均衡器内部仅支持 Azure IaaS Vm。 Azure 负载均衡器不支持基于 IP 的后端池;只有 azure 虚拟机或虚拟机规模集对象（其中 Azure VMware 解决方案 Vm 不是 Azure 对象）。

#### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>是否可以使用现有 ExpressRoute 网关连接到 Azure VMware 解决方案？
是的，可以使用现有的 ExpressRoute 网关连接到 Azure VMware 解决方案，前提是它不超过每个虚拟网络的四个 ExpressRoute 线路的限制。  但是，若要通过 ExpressRoute 从本地访问 Azure VMware 解决方案，必须具有 ExpressRoute Global Reach，因为 ExpressRoute 网关不提供其连接线路之间的传递路由。

## <a name="compute-network-storage-and-backup"></a>计算、网络、存储和备份

#### <a name="is-there-more-than-one-type-of-host-available"></a>是否有多种类型的主机可用？

只有一种类型的主机可用。

#### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>每种主机类型的 CPU 规格是什么？

服务器具有双 18 核 2.3 GHz Intel CPU。

#### <a name="how-much-memory-is-in-each-host"></a>每个主机中有多少内存？

服务器的 RAM 为 576 GB。

#### <a name="what-is-the-storage-capacity-of-each-host"></a>每个主机的存储容量是多少？

每个 ESXi 主机都有两个 vSAN diskgroups，容量层为 15.2 TB，每个 diskgroup) 有 3.2 TB 个 NVMe 缓存层 (1.6 TB。

#### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>每个 ESXi 主机中提供多少网络带宽？

Azure VMware 解决方案中的每个 ESXi 主机都配置了 4 25 Gbps Nic，为 ESXi 系统流量预配了两个 Nic，为工作负荷流量配置了两个 Nic。 

#### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>VSAN 数据存储上存储的数据是否静态加密？

是的，默认情况下，使用存储在 Azure Key Vault 中的密钥加密所有 vSAN 数据。

#### <a name="you-document-that-commvault-veritas-and-veeam-have-extended-their-backup-solutions-to-work-with-azure-vmware-solution-what-about-other-independent-software-vendor-isv-backup-solutions"></a>你记录了 Commvault、Veritas 和 Veeam 已扩展其备份解决方案，以便与 Azure VMware 解决方案一起使用。 其他独立软件供应商 (ISV) 备份解决方案呢？

正如我们所知，使用 VMware VADP 和 HotAdd 传输模式的任何备份解决方案都应直接在 Azure VMware 解决方案中使用。

#### <a name="what-about-support-for-isv-backup-solutions"></a>对 ISV 备份解决方案的支持是怎样的？

由于这些备份解决方案由客户进行安装和管理，因此他们可以联系到相应的 ISV 提供支持。 

#### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>重复值设置的正确存储策略是什么？

使用 VM 模板的 *thin_provision* 存储策略。  默认值为 *thick_provision*。

#### <a name="are-the-snmp-infrastructure-logs-shared"></a>SNMP 基础结构日志是否共享？

不是。

## <a name="hosts-clusters-and-private-clouds"></a>主机、群集和私有云

#### <a name="is-the-underlying-infrastructure-shared"></a>是否共享了底层基础结构？

没有，私有云主机和群集是专用的，在使用前后都会安全擦除。

#### <a name="what-are-the-minimum-and-the-maximum-number-of-hosts-per-cluster"></a>每个群集的最小和最大主机数是多少？

群集可以在 3 到 16 个 ESXi 主机之间缩放。 试用群集限制为 3 个主机。

#### <a name="can-i-scale-my-private-cloud-clusters"></a>可以缩放私有云群集吗？

是的，群集在最小和最大 ESXi 主机数之间进行缩放。 试用群集限制为 3 个主机。

#### <a name="what-are-trial-clusters"></a>什么是试用群集？

试用群集是三个主机群集，用于 Azure VMware 解决方案私有云的一个月评估版。

#### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>可以为试用群集使用高端主机吗？

不是。 高端 ESXi 主机保留用于生产群集。

## <a name="azure-vmware-solution-and-vmware-software"></a>Azure VMware 解决方案和 VMware 软件

#### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>私有云中使用的是哪些版本的 VMware 软件？

私有云使用 vSphere 6.7、vSAN 6.7、VMware HCX 和版本2.5。  

#### <a name="do-private-clouds-use-vmware-nsx"></a>私有云是否使用 VMware NSX？

是的，NSX-T 2.5 用于 Azure VMware 解决方案私有云中软件定义的网络。

#### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>可以在私有云中使用 VMware NSX-V 吗？

不是。 NSX-T 是唯一支持的 NSX 版本。

#### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>本地环境中是否需要 NSX 或连接到私有云的网络？

不需要，不需要在本地使用 NSX。

#### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>在私有云中，VMware 软件的升级和更新计划是什么？

私有云软件捆绑升级的目的是将软件保存到 VMware 的最新软件捆绑版本之一。 私有云软件版本可能不同于各个软件组件的最新版本， (ESXi，NSX-T，vCenter，vSAN) 。

#### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>私有云软件堆栈的更新频率是多少？

私有云软件将按计划升级，以跟踪 VMware 的软件捆绑版本。 私有云无需停机即可升级。

## <a name="connectivity"></a>连接

#### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>将私有云与本地环境合并需要什么网络 IP 地址计划？

部署 Azure VMware 解决方案私有云需要专用网络/22 地址空间。 此专用地址空间不应与订阅中的其他虚拟网络或本地网络重叠。
 
#### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>如何实现从本地环境连接到 Azure VMware 解决方案私有云？

可以通过以下两种方法之一连接到服务： 

- 使用 Azure 虚拟网络上部署的 VM 或应用程序网关，通过 ExpressRoute 对等互连到私有云。
- 通过 ExpressRoute Global Reach 从本地数据中心到 Azure ExpressRoute 线路。

#### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>如何将工作负载 VM 连接到 Internet 或 Azure 服务终结点？

在 Azure 门户中，为私有云启用 Internet 连接。 使用 NSX-T 管理器，创建一个 NSX-T T1 路由器和逻辑交换机。 然后，使用 vCenter 在逻辑交换机定义的网络段上部署 VM。 该 VM 将具有对 Internet 和 Azure 服务的网络访问权限。

#### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>是否需要限制从 Internet 访问私有云中逻辑网络上的 VM？

不是。 不允许直接将网络流量从 Internet 传入私有云。

#### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>是否需要限制从逻辑网络上的 VM 到 Internet 的 Internet 访问？

是的。 需要使用 NSX-T 管理器创建防火墙，限制 VM 对 Internet 的访问。


#### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>Azure VMware 解决方案可以使用 Azure 虚拟 WAN 托管 ExpressRoute 网关吗？
是的。

#### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>能否通过 ExpressRoute Global Reach 上的 Azure 虚拟 WAN 在本地与 Azure VMware 解决方案之间建立传输连接？
Azure 虚拟 WAN 不提供两个已连接的 ExpressRoute 线路和非虚拟 WAN ExpressRoute 网关之间的可传递路由。 使用 ExpressRoute Global Reach 允许在本地与 Azure VMware 解决方案之间建立连接，但会通过 Microsoft 的全球网络而不是虚拟 WAN 集线器进行连接。


## <a name="accounts-and-privileges"></a>帐户和特权

#### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>我的新的 Azure VMware 解决方案私有云会获得哪些帐户和特权？

你可获得 vCenter 中 cloudadmin 用户的凭据和 NSX-T 管理器的管理员访问权限。 还可通过一个 CloudAdmin 组合并 Azure Active Directory。 有关详细信息，请参阅[访问权限和标识的概念](concepts-identity.md)。

#### <a name="can-have-administrator-access-to-esxi-hosts"></a>是否可以拥有 ESXi 主机的管理员访问权限？

不可以，对 ESXi 的管理员访问权限受到限制，以满足解决方案的安全要求。

#### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>我可在 vCenter 中拥有哪些特权和权限？

你将拥有 CloudAdmin 组特权。 有关详细信息，请参阅[访问权限和标识的概念](concepts-identity.md)。

#### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>我可以拥有对 NSX-T 管理器的哪些特权和权限？

你将拥有对 NSX-T 的完全管理员权限，并且可以管理 vSphere 基于角色的访问控制，就像在本地使用 NSX-T 数据中心一样。 有关详细信息，请参阅[访问权限和标识的概念](concepts-identity.md)。

> [!NOTE]
> 创建了 T0 路由器，并将其配置为私有云部署的一部分。 对该逻辑路由器或 NSX-T 边缘节点 VM 的任何修改都可能会影响与私有云的连接。

## <a name="billing-and-support"></a>计费和支持

#### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>如何为 Azure VMware 解决方案构造定价？

有关定价的一般问题，请参阅 Azure VMware 解决方案 [定价](https://azure.microsoft.com/pricing/details/azure-vmware) 页。 

#### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>Azure VMware 解决方案是否可以通过 Microsoft CSP 购买？

是的，客户可以在由 CSP 管理的 Azure 订阅中部署 Azure VMware 解决方案。

#### <a name="who-supports-azure-vmware-solution"></a>谁支持 Azure VMware 解决方案？

Microsoft 提供对 Azure VMware 解决方案的支持。 你可以提交 [支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。 

对于 CSP 托管的订阅，解决方案提供商提供的第一个级别的支持方式与 CSP 对其他 Azure 服务的使用方式相同。

#### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>需要哪些帐户才能创建 Azure VMware 解决方案私有云？

需要 Azure 订阅中的 Azure 帐户。

#### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>Azure VMware 解决方案是否支持 Red Hat 解决方案？

Microsoft 和 Red Hat 共享集成的定位支持团队，为在 Azure 平台上运行的 Red Hat 生态系统提供统一的联系点。  与其他使用 Red Hat Enterprise Linux 的 Azure 平台服务一样，Azure VMware 解决方案位于云访问和集成的支持涵盖下，并支持 Red Hat Enterprise Linux 在 azure 中的 Azure VMware 解决方案之上运行。

#### <a name="is-vmware-hcx-enterprise-available-and-if-so-how-much-does-it-cost"></a>VMware HCX Enterprise 是否可用，如果是，这会产生多少费用？

VMware HCX Enterprise 提供 Azure VMware 解决方案作为 *预览* 功能/服务。 尽管 VMware HCX Enterprise for Azure VMware 解决方案处于预览状态，但它是免费的功能/服务，并受预览版服务条款和条件的约束。 VMware HCX Enterprise 服务推出后，你将获得30天的通知，计费将会切换。 你可以将其关闭或退出服务。

#### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>如何实现请求 Azure VMware 解决方案的主机配额增加？

对于 CSP 托管的订阅，客户必须将请求提交给合作伙伴。 然后，合作伙伴团队与 Microsoft 合作，以便为订阅增加配额。 有关详细信息，请参阅 [如何启用 Azure VMware 解决方案资源一文](enable-azure-vmware-solution.md) 。 

对于 EA 订阅，请使用以下过程。 首先，你将需要：

* [Azure 企业协议 (EA) ](../cost-management-billing/manage/ea-portal-agreements.md)与 Microsoft 配合使用。
* Azure 订阅中的一个 Azure 帐户。

在创建 Azure VMware 解决方案资源之前，请提交支持票证以分配节点。 最多需要五个工作日内确认请求并分配节点。 如果你有现有的 Azure VMware 解决方案私有云，但需要分配更多的节点，你会经历相同的过程。

1. 在 Azure 门户中，在 " **帮助 + 支持**" 下创建 **[新的支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)** ，并为票证提供以下信息：
   - **问题类型：** 技术方面
   - **订阅：** 选择你的订阅
   - **服务：** 所有服务 > Azure VMware 解决方案
   - **资源：** 一般问题 
   - **摘要：** 需要容量
   - **问题类型：** 容量管理问题
   - **问题子类型：** 客户请求额外的主机配额/容量

1. 在支持票证 **说明** 的 " **详细信息** " 选项卡上，提供：

   - POC 或生产 
   - 区域名称
   - 节点数
   - 任何其他详细信息

   >[!NOTE]
   >Azure VMware 解决方案建议至少使用三个节点来启动私有云和冗余的 N + 1 节点。 

1. 选择 " **查看 + 创建** " 以提交请求。

   支持代表需要5个工作日内确认你的请求。

   >[!IMPORTANT] 
   >如果你已有一个现有的 Azure VMware 解决方案，但你请求的是其他节点，请注意，我们需要5个工作日来分配节点。 

1. 预配节点之前，请确保在 Azure 门户中注册了 **MICROSOFT AVS** 资源提供程序。  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>

   For additional ways to register the resource provider, see [Azure resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md). 

#### Are Reserved Instances available for purchasing through the Cloud Solution Provider (CSP) program?

Yes. CSP can purchase reserved instances for their customers. For more information, see the [Save costs with a reserved instance](reserved-instance.md) article. 

#### Does Azure VMware Solution offer multi-tenancy for hosting CSP partners?

No. Currently Azure VMware Solution doesn't offer multi-tenancy.

#### Will traffic between on-premises and Azure VMware Solution over ExpressRoute incur any outbound data transfer charge in the metered data plan?

Traffic in the Azure VMware Solution ExpressRoute circuit isn't metered in any way. Traffic from your ExpressRoute circuit connecting to your on-premises to Azure is charged according to ExpressRoute pricing plans.


## Customer communication

#### How can I receive an alert when Azure sends service health notifications to my Azure subscription?

Service issues, planned maintenance, health advisories, security advisories notifications are published through **Service Health** in the Azure portal.  You can take timely actions when you set up activity log alerts for these notifications. For more information, see [Create service health alerts using the Azure portal](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Screenshot of Service Health notifications":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
