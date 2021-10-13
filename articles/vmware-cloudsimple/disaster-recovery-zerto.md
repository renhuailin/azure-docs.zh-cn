---
title: Azure VMware Solution by CloudSimple - 使用私有云作为本地工作负荷的灾难站点
description: 介绍如何将 CloudSimple 私有云设置为本地 VMware 工作负荷的灾难恢复站点
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f9700b5d25a7d564c72bf959d48ad7cf1afce4e7
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612382"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>将 CloudSimple 私有云设置为本地 VMware 工作负荷的灾难恢复站点

可以将 CloudSimple 私有云设置为本地应用程序的恢复站点，以便在发生灾难时实现业务连续性。 恢复解决方案基于 Zerto 虚拟复制，其用作复制和业务流程平台。 可将本地 vCenter 中的关键基础结构和应用程序虚拟机持续复制到私有云。 你可以使用私有云进行故障转移测试，并确保应用程序在发生灾难时的可用性。 可以遵循类似的方法，将私有云设置为受其他位置的恢复站点保护的主站点。

> [!NOTE]
> 有关调整灾难恢复环境规模的指导原则，请参阅 Zerto 文档 [Zerto 虚拟复制的规模调整注意事项](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf)。

CloudSimple 解决方案的优点如下：

* 无需专为灾难恢复 (DR) 设置数据中心。
* 可以利用为全球地理复原能力而部署了 CloudSimple 的 Azure 位置。
* 有机会降低用于 DR 的部署成本和总拥有成本。

此解决方案的要求如下：

* 在私有云中安装、配置和管理 Zerto。
* 在私有云是受保护的站点时，为 Zerto 提供你自己的许可证。 可以将 CloudSimple 站点上运行的 Zerto 与本地站点配对，以获取许可。

下图显示了 Zerto 解决方案的体系结构。

![体系结构](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解决方案

以下部分介绍如何在私有云中使用 Zerto 虚拟复制来部署 DR 解决方案。

1. [必备条件](#prerequisites)
2. [CloudSimple 私有云上的可选配置](#optional-configuration-on-your-private-cloud)
3. [在 CloudSimple 私有云上设置 ZVM 和 VRA](#set-up-zvm-and-vra-on-your-private-cloud)
4. [设置 Zerto 虚拟保护组](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>先决条件

若要启用从本地环境到私有云的 Zerto 虚拟复制，请完成以下先决条件。

1. [在本地网络与 CloudSimple 私有云之间设置站点到站点 VPN 连接](set-up-vpn.md)。
2. [设置 DNS 查找，以便将私有云管理组件转发到私有云 DNS 服务器](on-premises-dns-setup.md)。  若要启用 DNS 查找转发，请在本地 DNS 服务器中为 `*.cloudsimple.io` 创建到 CloudSimple DNS 服务器的转发区域条目。
3. 设置 DNS 查找，以便将本地 vCenter 组件转发到本地 DNS 服务器。  必须通过站点到站点 VPN 从 CloudSimple 私有云访问 DNS 服务器。 若要获得帮助，请提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，并提供以下信息。  

    * 本地 DNS 域名
    * 本地 DNS 服务器 IP 地址

4. 在私有云上安装 Windows Server。 此服务器用于安装 Zerto 虚拟管理器。
5. [提升 CloudSimple 特权](escalate-private-cloud-privileges.md)。
6. 在私有云 vCenter 上创建具有管理角色的新用户，以将其用作 Zerto 虚拟管理器的服务帐户。

### <a name="optional-configuration-on-your-private-cloud"></a>私有云上的可选配置

1. 在私有云 vCenter 上创建一个或多个资源池，以将其用作本地环境中的 VM 的目标资源池。
2. 在私有云 vCenter 上创建一个或多个文件夹，以将其用作本地环境中的 VM 的目标文件夹。
3. 为故障转移网络创建 VLAN 并设置防火墙规则。 打开[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)以获得帮助。
4. 为故障转移网络和测试网络创建分布式端口组，以便测试 VM 故障转移。
5. 在私有云环境中安装 [DHCP 和 DNS 服务器](dns-dhcp-setup.md)或使用 Active Directory 域控制器。

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>在私有云上设置 ZVM 和 VRA

1. 在私有云中的 Windows Server 上安装 Zerto 虚拟管理器 (ZVM)。
2. 使用在前面的步骤中创建的服务帐户登录到 ZVM。
3. 设置 Zerto 虚拟管理器许可。
4. 在私有云的 ESXi 主机上安装 Zerto 虚拟复制设备 (VRA)。
5. 将私有云 ZVM 与本地 ZVM 配对。

### <a name="set-up-zerto-virtual-protection-group"></a>设置 Zerto 虚拟保护组

1. 创建新的虚拟保护组 (VPG)，并指定 VPG 的优先级。
2. 选择需要保护以实现业务连续性的虚拟机，并根据需要自定义启动顺序。
3. 将恢复站点选择为私有云，并将默认恢复服务器选择为私有云群集或创建的资源组。 针对私有云上的恢复数据存储选择“vsanDatastore”。

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > 可以在“VM 设置”选项下为单个 VM 自定义主机选项。

4. 根据需要自定义存储选项。
5. 指定要用于故障转移网络和故障转移测试网络的恢复网络作为之前创建的分布式端口组，并根据需要自定义恢复脚本。
6. 根据需要自定义单个 VM 的网络设置，并创建 VPG。
7. 完成复制后，测试故障转移。

## <a name="reference"></a>参考

[Zerto 文档](https://www.zerto.com/myzerto/technical-documentation/)
