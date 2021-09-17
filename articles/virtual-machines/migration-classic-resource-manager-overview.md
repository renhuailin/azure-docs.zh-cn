---
title: 平台支持的从经典部署模型到 Azure 资源管理器部署模型的 IaaS 资源迁移概述
description: 逐步讲解如何对资源进行平台支持的从经典部署模型到 Azure 资源管理器的迁移。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 51191a8b5e2ad377092e2303c2245caa466f0adc
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690313"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>平台支持的从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM

> [!IMPORTANT]
> 目前，大约有 90% 的 IaaS VM 在使用 [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)。 自 2020 年 2 月 28 日起，经典 VM 已弃用，并将于 2023 年 3 月 1 日完全停用。 [详细了解]( https://aka.ms/classicvmretirement)此弃用以及[它对你的影响](classic-vm-deprecation.md#how-does-this-affect-me)。



本文概述平台支持的迁移工具、如何将资源从 Azure 服务管理器 (ASM)（经典）迁移到资源管理器 (ARM) 部署模型，并详细说明如何使用虚拟网络站点到站点网关连接两个在订阅中共存的两个部署模型的资源。 用户可以阅读有关 [Azure Resource Manager 功能和优点](../azure-resource-manager/management/overview.md)的更多内容。 

ASM 支持两种不同的计算产品：Azure 虚拟机（经典）（也称为 IaaS VM），以及 [Azure 云服务（经典）](../cloud-services/index.yml)（也称为 PaaS VM）或 Web/辅助角色。 本文档仅讨论迁移 Azure 虚拟机（经典）。

## <a name="goal-for-migration"></a>迁移目标
Resource Manager 除了可让你通过模板部署复杂的应用程序之外，还可使用 VM 扩展来配置虚拟机，并且纳入了访问管理和标记。 Azure Resource Manager 将虚拟机的可缩放并行部署包含在可用性集内。 新部署模型还针对计算、网络和存储单独提供生命周期管理。 最后，将重点介绍为了默认启用安全性而要在虚拟网络中实施虚拟机的做法。

在 Azure Resource Manager 之下，针对来自经典部署模型的几乎所有功能，均提供计算、网络和存储支持。 要充分利用 Azure Resource Manager 中的新功能，可将现有部署从经典部署模型中迁移出来。

## <a name="supported-resources--configurations-for-migration"></a>迁移支持的资源和配置

### <a name="supported-resources-for-migration"></a>迁移支持的资源
* 虚拟机（带 VM 的云服务）
* [云服务（带 Web/辅助角色）](../cloud-services-extended-support/in-place-migration-overview.md)
* 可用性集
* 存储帐户
* 虚拟网络
* VPN 网关
* [快速路由网关](../expressroute/expressroute-howto-move-arm.md)（仅限在虚拟网络所在的同一订阅中）
* 网络安全组
* 路由表
* 保留 IP

## <a name="supported-configurations-for-migration"></a>迁移支持的配置
迁移过程中支持以下经典 IaaS 资源

| 服务 | 配置 |
| --- | --- |
| Azure AD 域服务 | [包含 Azure AD 域服务的虚拟网络](../active-directory-domain-services/migrate-from-classic-vnet.md) |

## <a name="supported-scopes-of-migration"></a>支持的迁移范围
可通过四种不同的方式完成计算、网络和存储资源的迁移：

* [迁移（不在虚拟网络中的）虚拟机](#migration-of-virtual-machines-not-in-a-virtual-network)
* [迁移（虚拟网络中的）虚拟机](#migration-of-virtual-machines-in-a-virtual-network)
* [存储帐户的迁移](#migration-of-storage-accounts)
* [未附加资源的迁移](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>迁移（不在虚拟网络中的）虚拟机
在 Resource Manager 部署模型中，默认情况下会针对应用程序强制实施安全性。 在 Resource Manager 模型中，所有 VM 都必须位于虚拟网络内。 Azure 平台会在迁移过程中重新启动（`Stop`、`Deallocate`、`Start`）VM。 对于虚拟机将迁移到的虚拟网络，你有两个选项：

* 可以请求平台创建新的虚拟网络，并将虚拟机迁移到新的虚拟网络。
* 可以将虚拟机迁移到 Resource Manager 中的现有虚拟网络。

> [!NOTE]
> 在此迁移范围内，迁移期间可能有一段时间不允许进行管理平面操作和数据平面操作。
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>迁移（虚拟网络中的）虚拟机
对于大多数 VM 配置来说，在经典部署模型和 Resource Manager 部署模型之间迁移的只有元数据。 基础 VM 在相同硬件、相同网络上，使用相同的存储来运行。 在迁移过程中，可能有一段时间不允许进行管理平面操作。 不过，数据平面可继续运行。 也就是说，在 VM（经典）之上运行的应用程序不会在迁移期间造成停机。

目前不支持以下配置。 如果在将来添加支持，可能会造成这一配置中的某些 VM 停机（经历停止、解除分配和重新启动 VM 等操作）。

* 在单个云服务中有一个以上的可用性集。
* 在单个云服务中有一个或多个可用性集和不在可用性集中的 VM。

> [!NOTE]
> 在此迁移范围内，迁移期间可能有一段时间不允许进行管理平面操作。 上述某些配置会造成数据平面停机。
>

### <a name="migration-of-storage-accounts"></a>存储帐户的迁移
为了让迁移顺畅进行，可以在经典存储帐户中部署 Resource Manager VM。 通过此功能，就可以并且应该迁移计算和网络资源，而不必受到存储帐户的约束。 迁移虚拟机和虚拟网络后，需要迁移存储帐户才能完成迁移过程。

如果存储帐户没有任何关联的磁盘或虚拟机数据，并且只有 blob、文件、表和队列，那么到 Azure 资源管理器的迁移可以作为独立的迁移完成，而不需要依赖项。

> [!NOTE]
> Resource Manager 部署模型没有经典映像和磁盘的概念。 迁移存储帐户时，经典映像和磁盘在 Azure 门户中不再可见，但后备 VHD 保留在存储帐户中。

以下屏幕截图演示了如何使用 Azure 门户将经典存储帐户升级到 Azure 资源管理器存储帐户：
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到存储帐户。
3. 在“设置”部分，单击“迁移到 Azure 资源管理器” 。
4. 单击“验证”，确定迁移可行性。
5. 如果验证通过，请单击“准备”，以便创建迁移的存储帐户。
6. 键入“是”对迁移进行确认，然后单击“提交”完成迁移。

    ![验证存储帐户](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![准备存储帐户](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![完成存储帐户迁移](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>未附加资源的迁移
没有关联的磁盘或虚拟机数据的存储帐户可以单独迁移。

未附加到任何虚拟机和虚拟网络的网络安全组、路由表和保留 IP 也可以单独迁移。

<br>

## <a name="unsupported-features-and-configurations"></a>不支持的功能和配置
目前不支持某些功能和配置；以下各节将围绕这些功能和配置介绍我们的建议。

### <a name="unsupported-features"></a>不支持的功能
目前不支持以下功能。 用户可以选择删除这些设置、迁移 VM，并在 Resource Manager 部署模型中重新启用这些设置。

| 资源提供程序 | 功能 | 建议 |
| --- | --- | --- |
| 计算 | 不关联的虚拟机磁盘。 | 迁移存储帐户时，将迁移这些磁盘后面的 VHD blob |
| 计算 | 虚拟机映像。 | 迁移存储帐户时，将迁移这些磁盘后面的 VHD blob |
| 网络 | 终结点 ACL。 | 删除终结点 ACL 并重试迁移。 |
| 网络 | 应用程序网关 | 开始迁移之前请删除应用程序网关，然后在迁移完成后重新创建应用程序网关。 |
| 网络 | 使用 VNet 对等互连的虚拟网络。 | 将虚拟网络迁移到 Resource Manager，并对等互连。 详细了解 [VNet 对等互连](../virtual-network/virtual-network-peering-overview.md)。 |

### <a name="unsupported-configurations"></a>不支持的配置
目前不支持以下配置。

| 服务 | 配置 | 建议 |
| --- | --- | --- |
| Resource Manager |经典资源的基于角色的访问控制 (RBAC) |由于资源的 URI 在迁移后会进行修改，因此建议用户规划需要在迁移后进行的 RBAC 策略更新。 |
| 计算 |与 VM 关联的多个子网 |将子网配置更新为只引用一个子网。 这可能需要从 VM 中删除辅助 NIC（该 NIC 引用另一个子网） ，完成迁移后再将其重新附加。 |
| 计算 |属于虚拟网络，但未分配显式子网的虚拟机 |可以选择性地删除 VM。 |
| 计算 |具有警报、自动缩放策略的虚拟机 |迁移进行下去时，这些设置会删除。 强烈建议用户在进行迁移之前先评估其环境。 或者，也可以在迁移完成之后重新配置警报设置。 |
| 计算 |XML VM 扩展（BGInfo 1.*、Visual Studio 调试器、Web 部署和远程调试） |此操作不受支持。 建议用户在继续迁移之前从虚拟机中删除这些扩展，否则系统会在迁移过程中自动删除它们。 |
| 计算 |使用高级存储启动诊断 |在继续执行迁移之前，为 VM 禁用启动诊断功能。 在迁移完成之后，可以在 Resource Manager 堆栈中重新启用启动诊断。 此外，应删除正用于屏幕截图和串行日志的 blob，以便不会再为这些 blob 付费。 |
| 计算 | 云服务包含一个以上可用性集或多个可用性集。 |目前不支持。 在迁移之前，请将虚拟机移到同一可用性集中。 |
| 计算 | 带 Azure 安全中心扩展的 VM | Azure 安全中心在虚拟机上自动安装扩展，用于监视其安全性并引发警报。 如果在订阅上启用了 Azure 安全中心策略，通常会自动安装这些扩展。 若要迁移虚拟机，则禁用订阅上的安全中心策略，这将从虚拟机删除监视扩展的安全中心。 |
| 计算 | 带备份或快照扩展的 VM | 这些扩展安装在配置有 Azure 备份服务的虚拟机上。 虽然不支持迁移迁移这些 VM，但请遵循[有关从经典部署模型迁移到 Azure 资源管理器部署模型的常见问题](/azure/virtual-machines/migration-classic-resource-manager-faq#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault)中的指导，保留迁移之前已进行的备份。  |
| 计算 | 具有 Azure Site Recovery 扩展的 VM | 这些扩展安装在配置了 Azure Site Recovery 服务的虚拟机上。 虽然可以与 Site Recovery 配合使用来迁移存储，但是当前复制将受到影响。 需要在存储迁移后禁用并启用 VM 复制。 |
| 网络 |包含虚拟机和 Web 角色/辅助角色的虚拟网络 |目前不支持。 在迁移之前，请将 Web/辅助角色移动到其自己的虚拟网络。 一旦迁移经典虚拟网络，就可以将迁移的 Azure 资源管理器虚拟网络与经典虚拟网络对等，从而实现与以前类似的配置。|
| 网络 | 经典 Express Route 线路 |目前不支持。 这些线路需要在开始迁移 IaaS 之前迁移到 Azure 资源管理器。 有关详细信息，请参阅[将 ExpressRoute 线路从经典部署模型转移到资源管理器部署模型](../expressroute/expressroute-move.md)。|
| Azure 应用服务 |包含应用服务环境的虚拟网络 |目前不支持。 |
| Azure HDInsight |包含 HDInsight 服务的虚拟网络 |目前不支持。 |
| Microsoft Dynamics Lifecycle Services |包含由 Dynamics Lifecycle Services 管理的虚拟机的虚拟网络 |目前不支持。 |
| Azure API 管理 |包含 Azure API 管理部署的虚拟网络 |目前不支持。 若要迁移 IaaS VNET，则更改 API 管理部署的 VNET（该部署不会造成停机）。 |

## <a name="next-steps"></a>后续步骤

* [有关平台支持的从经典部署模型到 Azure Resource Manager 部署模型的迁移的技术深入探讨](migration-classic-resource-manager-deep-dive.md)
* [规划从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](migration-classic-resource-manager-plan.md)
* [使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-ps.md)
* [使用 CLI 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-cli.md)
* [用于帮助将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的社区工具](migration-classic-resource-manager-community-tools.md)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md)
* [查看有关将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的最常见问题](migration-classic-resource-manager-faq.yml)
