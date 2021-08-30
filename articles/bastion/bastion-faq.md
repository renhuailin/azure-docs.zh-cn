---
title: Azure Bastion 常见问题解答 | Microsoft Docs
description: 了解 Azure Bastion 的常见问题解答。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: 8ff219e326f7c8a2bbc075594ff8ffb30e9b04f5
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289170"
---
# <a name="azure-bastion-faq"></a>Azure Bastion 常见问题解答

## <a name="faqs"></a>常见问题

### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>我的虚拟机上是否需要公共 IP 才能通过 Azure Bastion 进行连接？

否。 使用 Azure Bastion 连接到 VM 时，不需要在要连接到的 Azure 虚拟机上具有公共 IP。 Bastion 服务会通过虚拟网络中的虚拟机的专用 IP 打开到虚拟机的 RDP/SSH 会话/连接。

### <a name="is-ipv6-supported"></a>是否支持 IPv6？

目前不支持 IPv6。 Azure Bastion 仅支持 IPv4。

### <a name="can-i-use-azure-bastion-with-azure-private-dns-zones"></a>是否可以将 Azure Bastion 与 Azure 专用 DNS 区域一起使用？

Azure Bastion 需要能够与某些内部终结点通信，才能成功连接到目标资源。 因此，只要你选择的区域名称与这些内部终结点的命名不重叠，就可以将 Azure Bastion 与 Azure 专用 DNS 区域结合使用。 在部署 Azure Bastion 资源之前，请确保主机虚拟网络未链接到名称中包含以下内容的专用 DNS 区域：
* core.windows.net
* azure.com

请注意，如果使用的是集成了专用终结点的 Azure 专用 DNS 区域，则多个 Azure 服务的[推荐 DNS 区域名称](../private-link/private-endpoint-dns.md#azure-services-dns-zone-configuration)将与上面列出的名称重叠。 这些设置不支持使用 Azure Bastion。

国家云中的 Azure 专用 DNS 区域也不支持使用 Azure Bastion。

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>是否需要 RDP 或 SSH 客户端？

否。 无需 RDP 或 SSH 客户端即可在 Azure 门户中访问 RDP/SSH 来连接到 Azure 虚拟机。 使用 [Azure 门户](https://portal.azure.com) 能够直接在浏览器中通过 RDP/SSH 来访问虚拟机。

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>是否需要在 Azure 虚拟机中运行代理？

否。 无需在浏览器或 Azure 虚拟机上安装代理或任何软件。 Bastion 服务没有代理，不需要任何其他软件即可使用 RDP/SSH。

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>RDP 会话支持哪些功能？

目前仅支持文本复制/粘贴。 不支持文件复制等功能。 请随时在 [Azure Bastion 反馈页](https://feedback.azure.com/forums/217313-networking?category_id=367303)上分享有关新功能的反馈。

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Bastion 强化是否适用于 AADJ VM 扩展加入的 VM？

此功能不适用于使用 Azure AD 用户的 AADJ VM 扩展加入的计算机。 有关详细信息，请参阅 [Microsoft Azure VM 和 Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements)。

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>支持哪些浏览器？

浏览器必须支持 HTML 5。 使用 Windows 上的 Microsoft Edge 浏览器或 Google Chrome。 对于 Apple Mac，可使用 Google Chrome 浏览器。 Windows 和 Mac 上也支持 Microsoft Edge Chromium。

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>定价是多少？

有关详细信息，请参阅[定价页](https://aka.ms/BastionHostPricing)。

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Azure Bastion 将客户数据存储在何处？

Azure Bastion 不会将客户数据移出部署的区域或存储到部署区域以外的区域。

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>是否需要通过角色来访问虚拟机？

需要使用以下角色进行连接：

* 虚拟机上的读者角色。
* NIC 上的读者角色（使用虚拟机的专用 IP）。
* Azure Bastion 资源上的读者角色。
* 虚拟网络上的读者角色（如果没有对等互连虚拟网络，则不需要）。

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Azure Bastion 是否需要 RDS CAL 才能在 Azure 托管的 VM 上实现管理目的？

不需要，通过 Azure Bastion 访问 Windows Server VM 时，不需要 [RDS CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab)（如果仅用于管理目的）。

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Bastion 远程会话期间支持哪些键盘布局？

Azure Bastion 目前在 VM 内支持 en-us-qwerty 键盘布局。  对其他区域设置的键盘布局的支持尚在开发中。

### <a name="does-azure-bastion-support-timezone-configuration-or-timezone-redirection-for-target-vms"></a><a name="timezone"></a>Azure Bastion 是否支持目标 VM 的时区配置或时区重定向？

Azure Bastion 当前不支持时区重定向并且不可配置时区。

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Azure Bastion 子网是否支持用户定义的路由 (UDR)？

不是。 Azure Bastion 子网不支持 UDR。

对于在同一虚拟网络中同时包含 Azure Bastion 和 Azure 防火墙/网络虚拟设备 (NVA) 的方案，无需强制流量从 Azure Bastion 子网发往 Azure 防火墙，因为 Azure Bastion 与 VM 之间的通信是专用的。 有关详细信息，请参阅[通过 Bastion 访问 Azure 防火墙后的 VM](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)。

### <a name="can-i-upgrade-from-a-basic-sku-to-a-standard-sku"></a><a name="upgradesku"></a>是否可以从基本 SKU 升级到标准 SKU 吗？

是的。 有关步骤，请参阅[升级 SKU](upgrade-sku.md)。 有关 SKU 的详细信息，请参阅[配置设置](configuration-settings.md#skus)一文。

### <a name="can-i-downgrade-from-a-standard-sku-to-a-basic-sku"></a><a name="downgradesku"></a>是否可以从标准 SKU 降级到基本 SKU？

不是。 不支持从标准 SKU 降级到基本 SKU。 有关 SKU 的详细信息，请参阅[配置设置](configuration-settings.md#skus)一文。

### <a name="can-i-deploy-multiple-azure-resources-in-my-azure-bastion-subnet"></a><a name="subnet"></a>是否可以在 Azure Bastion 子网中部署多个 Azure 资源？

不是。 保留 Azure Bastion 子网 (AzureBastionSubnet) 仅用于部署 Azure Bastion 资源。

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>为什么在 Bastion 会话启动前收到了“你的会话已过期”的错误消息？

会话只能从 Azure 门户启动。 登录到 Azure 门户，并重新开始会话。 如果直接从另一个浏览器会话或选项卡转到 URL，则会出现此错误。 它有助于确保会话更安全，并且该会话只能通过 Azure 门户来访问。

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>如何处理部署失败？

查看任何错误消息并根据需要[在 Azure 门户中提出支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。 [Azure 订阅限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md)可能会导致部署失败。 具体来说，客户可能会遇到对每个订阅允许的公共 IP 地址数的限制，这会导致 Azure Bastion 部署失败。

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>如何在灾难恢复计划中纳入 Azure Bastion？

Azure Bastion 是在 VNet 或对等的 VNet 中部署的，与 Azure 区域相关联。 由你负责将 Azure Bastion 部署到灾难恢复 (DR) 站点 VNet。 如果出现 Azure 区域故障，请将 VM 故障转移到 DR 区域。 然后，使用 DR 区域中部署的 Azure Bastion 主机连接到现在此处部署的 VM。

## <a name="vnet-peering"></a><a name="peering"></a>VNet 对等互连

### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>是否仍可将多个 Bastion 主机部署到对等互连虚拟网络中？

是的。 默认情况下，用户会看到在 VM 所在的虚拟网络中部署的 Bastion 主机。 但是，在“连接”菜单中，用户可以看到在对等互连网络上检测到的多个 Bastion 主机。 他们可以选择首选用于连接到虚拟网络中部署的 VM 的 Bastion 主机。

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>如果将对等互连的 VNet 部署在不同的订阅中，通过 Bastion 进行的连接是否可以正常工作？

可以，对于单个租户的跨不同订阅的对等互连 VNet，通过 Bastion 进行的连接会继续正常工作。 不支持跨两个不同租户的订阅。 若要在“连接”下拉菜单中查看 Bastion，用户必须在“订阅”>“全局订阅”中选择他们有权访问的订阅。

:::image type="content" source="./media/bastion-faq/global-subscriptions.png" alt-text="全局订阅筛选器。" lightbox="./media/bastion-faq/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>我有权访问对等互连 VNet，但看不到在其中部署的 VM。

请确保用户对 VM 和对等互连 VNet 都具有“读取”访问权限。 此外，请在 IAM 下检查用户是否对以下资源具有“读取”访问权限：

* 虚拟机上的读者角色。
* NIC 上的读者角色（使用虚拟机的专用 IP）。
* Azure Bastion 资源上的读者角色。
* 虚拟网络上的读者角色（如果没有对等互连虚拟网络，则不需要）。

|权限|描述|权限类型|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |获取守护主机|操作|
|Microsoft.Network/virtualNetworks/BastionHosts/action |获取虚拟网络中的守护主机引用。|操作|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|获取虚拟网络中的守护主机引用。|操作|
|Microsoft.Network/networkInterfaces/read|获取网络接口定义。|操作|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|获取网络接口 IP 配置定义。|操作|
|Microsoft.Network/virtualNetworks/read|获取虚拟网络定义|操作|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|获取对虚拟网络子网中的所有虚拟机的引用|操作|
|Microsoft.Network/virtualNetworks/virtualMachines/read|获取对虚拟网络中的所有虚拟机的引用|操作|