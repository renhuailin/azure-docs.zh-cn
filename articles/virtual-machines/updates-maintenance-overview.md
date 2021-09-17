---
title: 更新和维护概述
description: 了解 Azure 中的虚拟机可用的更新和维护选项
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/10/2021
ms.reviewer: cynthn
ms.openlocfilehash: 5e2d200e7133074ffc1dc2732cdedb692c1ea30c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698325"
---
# <a name="updates-and-maintenance-overview"></a>更新和维护概述

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

本文概述适用于 Azure 虚拟机 (VM) 的各种更新和维护选项。

## <a name="automatic-os-image-upgrade"></a>自动 OS 映像升级

在规模集中启用[自动 OS 映像升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context)有助于简化更新管理，这样可以安全自动升级规模集中所有实例的 OS 磁盘。

[自动 OS 升级](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context)具有以下特征：

- 配置后，映像发布者发布的最新 OS 映像将自动应用于规模集，无需用户干预。
- 每次发布者发布新映像时，以滚动方式分批升级实例。
- 与应用程序运行状况探测和[应用程序运行状况扩展](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context)集成。
- 适用于所有 VM 大小，同时适用于 Windows 和 Linux 映像。
- 可以随时取消自动升级（也可以手动启动 OS 升级）。
- VM 的 OS 磁盘将被替换为使用最新映像版本创建的新 OS 磁盘。 运行配置的扩展和自定义数据脚本，同时保留永久性数据磁盘。
- 支持[扩展定序](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context)。
- 可在任意大小的规模集上启用自动 OS 映像升级。


## <a name="automatic-vm-guest-patching-preview"></a>自动 VM 来宾修补（预览版）

为 Azure VM 启用[自动 VM 来宾修补](automatic-vm-guest-patching.md)有助于通过安全自动修补虚拟机来简化更新管理，使虚拟机保持符合安全规范。

[自动 VM 来宾修补](automatic-vm-guest-patching.md)具有以下特征：
- 在 VM 上自动下载并应用已分类为“关键”或“安全性”的补丁。 
- 在 VM 所在时区的非高峰期应用补丁。
- 由 Azure 管理补丁业务流程，遵循[可用性优先原则](automatic-vm-guest-patching.md#availability-first-updates)应用补丁。
- 监视虚拟机运行状况（通过平台运行状况信号来确定），以检测修补失败的情况。
- 适用于所有大小的 VM。


## <a name="automatic-extension-upgrade"></a>自动扩展升级

[自动扩展升级](automatic-extension-upgrade.md)适用于 Azure VM 和 Azure 虚拟机规模集。 在 VM 或规模集中启用自动扩展升级后，每当扩展发行商发布了扩展的新版本，该扩展就会自动升级。

 自动扩展升级具有以下特性：
- 支持 Azure VM 和 Azure 虚拟机规模集。
- 在可用性优先部署模型中应用升级。
- 对于虚拟机规模集，在一个批次中升级的规模集虚拟机数不超过总数的 20%。 最小批次大小为一个虚拟机。
- 适用于所有 VM 大小，并且适用于 Windows 和 Linux 扩展。
- 随时可以选择停用自动升级。
- 可在任意大小的虚拟机规模集中启用自动扩展升级。
- 每个受支持的扩展将单独注册，可以选择要自动升级哪些扩展。
- 在所有公有云区域中均受支持。

## <a name="hotpatch"></a>热补丁

[热修补](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context)是在新的 Windows Server Azure Edition 虚拟机 (VM) 上安装更新的一种新方式，安装后无需重新启动。 适用于 Windows Server Azure Edition VM 的热补丁具有以下优势：

- 减少重新启动的次数，降低对工作负载的影响
- 更快地部署更新，因为包更小，安装速度更快，并且可以使用 Azure 更新管理器更轻松地协调补丁安装
- 提供更好的保护，因为热补丁更新包只包括安装速度较快且无需重新启动的 Windows 安全更新


## <a name="azure-update-management"></a>Azure 更新管理

可以使用 [Azure 自动化中的更新管理](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context)，为 Azure、本地环境和其他云环境中的 Windows 和 Linux 虚拟机管理操作系统更新。 可以快速评估所有代理计算机上可用更新的状态，并管理为服务器安装所需更新的过程。

## <a name="maintenance-control"></a>维护控制

使用[维护控制](maintenance-control.md)管理不需要重新启动的平台更新。 Azure 会频繁更新其基础结构，以提高可靠性、性能、安全性或推出新功能。 大多数更新对用户是透明的。 一些敏感的工作负载，如游戏、媒体流和金融交易，甚至不能容忍 VM 冻结或断开连接几秒钟来进行维护。 维护控制允许你等待平台更新并在 35 天滚动时段内应用它们。

维护控制允许你决定何时向独立 VM 和 Azure 专用主机应用更新。

使用[维护控制](maintenance-control.md)可以：
- 将许多更新作为一个更新包进行批处理。
- 等待长达 35 天才应用更新。
- 通过配置维护计划或使用 [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)，可以自动执行平台更新。
- 维护配置跨订阅和资源组生效。


## <a name="scheduled-events"></a>计划事件

计划事件是一个 Azure 元数据服务，可提供应用程序时间用于准备虚拟机 (VM) 维护。 它提供有关即将发生的维护事件的信息（例如重新启动），使应用程序可以为其准备并限制中断。 它可用于 Windows 和 Linux 上的所有 Azure 虚拟机类型（包括 PaaS 和 IaaS）。

有关 Scheduled Events 的信息，请参阅[适用于 Windows VM 的 Scheduled Events](./windows/scheduled-events.md) 和[适用于 Linux 的 Scheduled Events](./linux/scheduled-events.md)

## <a name="next-steps"></a>后续步骤

有关增加应用程序和服务运行时间的更多方法，请查看[可用性和规模](availability.md)文档。
