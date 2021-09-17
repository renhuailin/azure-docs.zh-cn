---
title: Azure 中 Linux VM 的概述
description: Azure 中的 Linux 虚拟机概述。
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 100a1c8c1222416201ead23c436d064273cc2a5b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692826"
---
# <a name="linux-virtual-machines-in-azure"></a>Azure 中的 Linux 虚拟机

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

Azure 虚拟机 (VM) 是 Azure 提供的多种[可缩放按需分配计算资源](/azure/architecture/guide/technology-choices/compute-decision-tree)之一。 通常情况下，如果需要以更大的力度（相对于其他控制选项）控制计算环境，则应选择 VM。 本文介绍创建 VM 之前的注意事项，以及 VM 的创建方法和管理方式。

使用 Azure VM 可以灵活进行虚拟化，而无需购买和维护运行 VM 的物理硬件。 不过，仍然需要通过执行任务来维护 VM，例如，配置、修补和安装在 VM 上运行的软件。

可通过多种方式使用 Azure 虚拟机。 一些示例如下：

* **开发和测试** – 在 Azure VM 上，可以快速轻松地创建具有特定配置的计算机来满足编写代码和应用程序测试的需要。
* **云中的应用程序** – 由于应用程序的需求会不断变化，在 Azure 中的 VM 上运行应用程序可能会节省成本。 使用 VM 时，需要支付额外的费用；不需要 VM 时，则需要关闭它们。
* **扩展的数据中心** – Azure 虚拟网络中的虚拟机可以轻松连接到组织的网络。

应用程序使用的 VM 数可根据你的需要纵向扩展和横向扩展为任意数目。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>在创建 VM 之前需要考虑哪些因素？
在 Azure 中构建应用程序基础结构时，始终要考虑多种[设计注意事项](/azure/architecture/reference-architectures/n-tier/linux-vm)。 在开始之前，必须考虑到 VM 的以下重要方面：

* 应用程序资源的名称
* 资源的存储位置
* VM 的大小
* 可以创建的 VM 数目上限
* VM 运行的操作系统
* VM 在启动后的配置
* VM 所需的相关资源

### <a name="locations"></a>位置
在 Azure 中创建的所有资源分布在世界各地的多个[地理区域](https://azure.microsoft.com/regions/)。 创建 VM 时，区域通常称为“位置”。 位置指定 VM 虚拟硬盘的存储位置。

下表显示了获取可用位置列表的一些方法。

| 方法 | 说明 |
| --- | --- |
| Azure 门户 |创建 VM 时，可从列表中选择位置。 |
| Azure PowerShell |使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 命令。 |
| REST API |使用[列出位置](/rest/api/resources/subscriptions)操作。 |
| Azure CLI |使用 [az account list-locations](/cli/azure/account) 操作。 |

## <a name="availability"></a>可用性
Azure 宣布了行业领先的单实例虚拟机服务级别协议：可用性达到 99.9%（前提是为所有磁盘使用高级存储部署 VM）。  为了使部署符合标准 99.95% 的 VM 服务级别协议，仍需要在可用性集中部署两个或更多个运行工作负荷的 VM。 可用性集可确保 VM 分布在 Azure 数据中心内的多个容错域，并使用不同的维护时段部署到主机。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。

## <a name="vm-size"></a>VM 大小
VM 的[大小](../sizes.md)由所要运行的工作负荷决定。 然后，选择的大小决定了处理能力、内存和存储容量等因素。 Azure 提供各种大小来支持多种类型的用途。

Azure 根据 VM 的大小和操作系统按[小时价格](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)计费。 对于不足一小时的部分，Azure 仅根据使用的分钟数计费。 存储将另行定价和收费。

## <a name="vm-limits"></a>VM 限制
订阅附带默认的[配额限制](../../azure-resource-manager/management/azure-subscription-service-limits.md)，在为项目部署大量 VM 时，这些限制可能会造成影响。 每个订阅的当前限制是每区域 20 个 VM。 可以[开具支持票证](../../azure-portal/supportability/resource-manager-core-quotas-request.md)来请求提高限制

## <a name="managed-disks"></a>托管磁盘

托管磁盘为用户在后台处理 Azure 存储帐户的创建和管理，确保用户无需担心存储帐户的可伸缩性限制。 只需指定磁盘大小和性能层（标准或高级），Azure 就会自动创建和管理磁盘。 在添加磁盘或者扩展和缩减 VM 时，无需考虑所用的存储。 如果要创建新的 VM，请[使用 Azure CLI](quick-create-cli.md) 或 Azure 门户，通过托管 OS 和数据磁盘创建 VM。 如果 VM 具有非托管磁盘，则可以[将 VM 转换为由托管磁盘支持](convert-unmanaged-to-managed-disks.md)。

用户还可以按 Azure 区域在一个存储帐户中管理自定义映像，并使用这些映像在同一订阅中创建数百台 VM。 有关托管磁盘的详细信息，请参阅[托管磁盘概述](../managed-disks-overview.md)。

## <a name="distributions"></a>分发 
Microsoft Azure 支持运行由多家合作伙伴提供和维护的众多热门 Linux 分发版。  可以在 Azure 市场中找到可用的分发版。 Microsoft 积极与各大 Linux 社区合作以便为 [Azure 认可的 Linux 分发版](endorsed-distros.md)列表添加更多成员。

如果首选的 Linux 分发版目前不在库中，可以通过[在 Azure 中创建和上传 Linux VHD](create-upload-generic.md) 来“自带 Linux”VM。

Microsoft 与合作伙伴紧密合作，以确保可用映像进行更新并针对 Azure 运行时进行优化。  有关 Azure 合作伙伴产品/服务的详细信息，请参阅以下链接：

* Azure 上的 Linux - [认可的分发](endorsed-distros.md)
* SUSE - [Azure 市场 - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=suse)
* Red Hat - [Azure 市场 - Red Hat Enterprise Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Red%20Hat%20Enterprise%20Linux)
* Canonical - [Azure 市场 - Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&filters=partners&search=canonical)
* Debian - [Azure 市场 - Debian](https://azuremarketplace.microsoft.com/marketplace/apps?search=Debian&page=1)
* FreeBSD - [Azure 市场 - FreeBSD](https://azuremarketplace.microsoft.com/marketplace/apps?search=freebsd&page=1)
* Flatcar - [Azure 市场 - Flatcar Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Flatcar&page=1)
* RancherOS - [Azure 市场 - RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure 市场 - Azure 上的 Mesosphere DC/OS](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Docker - [Azure 市场 - Docker 映像](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins - [Azure 市场 - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

若要实现适当的 DevOps 区域性，所有基础结构都必须是代码。  当所有基础结构都存在于代码中时，便可以轻松重新创建它。  Azure 可与所有主要自动化工具（如 Ansible、Chef、SaltStack 和 Puppet）配合使用。  Azure 也有自己的自动化工具：

* [Azure 模板](create-ssh-secured-vm-from-template.md)
* [Azure `VMaccess`](../extensions/vmaccess.md)

Azure 在支持它的大多数 Linux 发行版中支持 [cloud-init](https://cloud-init.io/)。  我们正在积极地与我们认可的 Linux 发行版合作伙伴合作，以便在 Azure 市场中提供已启用 cloud-init 的映像。 这些映像可使 cloud-init 部署和配置无缝地应用于 VM 和虚拟机规模集。

* [在 Azure Linux VM 上使用 cloud-init](using-cloud-init.md)

## <a name="storage"></a>存储
* [Microsoft Azure 存储简介](../../storage/common/storage-introduction.md)
* [使用 azure-cli 将磁盘添加到 Linux VM](add-disk.md)
* [如何在 Azure 门户中将数据磁盘附加到 Linux VM](attach-disk-portal.md)

## <a name="networking"></a>网络
* [虚拟网络概述](../../virtual-network/virtual-networks-overview.md)
* [Azure 中的 IP 地址](../../virtual-network/public-ip-addresses.md)
* [在 Azure 中打开 Linux VM 的端口](nsg-quickstart.md)
* [在 Azure 门户中创建完全限定的域名](../create-fqdn.md)


## <a name="data-residency"></a>数据驻留

在 Azure 中，将客户数据存储到一个区域的功能目前仅适用于亚太地区的东南亚区域（新加坡）和巴西地区的巴西南部区域（圣保罗州）。 对于其他所有区域，客户数据存储在以下地域。 有关详细信息，请参阅[信任中心](https://azure.microsoft.com/global-infrastructure/data-residency/)。


## <a name="next-steps"></a>后续步骤

创建第一个 VM！

- [Portal](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
