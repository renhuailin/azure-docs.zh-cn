---
title: Azure 机群上的虚拟机监控程序安全性 - Azure 安全性
description: Azure 机群上的虚拟机监控程序安全性技术概述。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: e8da9ae3d8f8c13bf52c5c0a2ea61b38f316a8b2
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895627"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Azure 机群上的虚拟机监控程序安全性

Azure 虚拟机监控程序系统基于 Windows Hyper-V。 借助虚拟机监控程序系统，计算机管理员可以指定具有单独地址空间的来宾分区。 单独的地址空间使你可以加载与在计算机的根分区中执行的（主机）操作系统并行运行的操作系统和应用程序。 主机 OS（也称为特权根分区）可以直接访问系统上的所有物理设备和外设（存储控制器、网络适配器）。 通过将“虚拟设备”公开给每个来宾分区，主机 OS 允许来宾分区共用这些物理设备。 因此，在来宾分区中执行的操作系统可以访问由在根分区中执行的虚拟化服务所提供的虚拟化外围设备。

构建 Azure 虚拟机监控程序时要牢记以下安全目标：

| 目标 | 源 |
|--|--|
| 隔离 | 安全策略禁止在 VM 之间传输信息。 此约束需要 Virtual Machine Manager (VMM) 和硬件的功能来隔离内存、设备、网络和受管理资源（如持久化数据）。 |
| VMM 完整性 | 若要实现总体系统完整性，需要构建单个虚拟机监控程序组件的完整性并对其进行维护。 |
| 平台完整性 | 虚拟机监控程序的完整性取决于它所依赖的硬件和软件的完整性。 虽然虚拟机监控程序无法直接控制平台的完整性，但 Azure 依靠硬件和固件机制（例如 [Cerberus](project-cerberus.md) 芯片）来保护和检测基础平台的完整性。 如果平台完整性受到损害，则会阻止 VMM 和来宾运行。 |
| 受限访问 | 只有通过安全连接进行连接的授权管理员才能执行管理功能。 最小特权原则由 Azure 基于角色的访问控制 (Azure RBAC) 机制强制实施。 |
| 审核 | 借助 Azure，审核功能可以捕获和保护有关系统上发生的情况的数据，以便以后可以对其进行检查。 |

Microsoft 强化 Azure 虚拟机监控程序和虚拟化子系统的方法可以分为以下三类。

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>由虚拟机监控程序强制实施的严格定义的安全边界

Azure 虚拟机监控程序在以下各项之间强制实施多个安全边界：

- 虚拟化的“来宾”分区和特权分区（“主机”）
- 多个来宾
- 本身和主机
- 本身和所有来宾

为虚拟机监控程序安全边界确保了机密性、完整性和可用性。 边界可以抵御各种攻击，包括旁道信息泄漏、拒绝服务和特权提升。

虚拟机监控程序安全边界还为网络流量、虚拟设备、存储、计算资源和所有其他 VM 资源提供了租户之间的分段。

## <a name="defense-in-depth-exploit-mitigations"></a>深层防御攻击缓解

如果安全边界存在漏洞，Azure 虚拟机监控程序包括多个缓解层，包括：

- 隔离跨 VM 组件托管的基于主机的进程
- 基于虚拟化的安全性 (VBS)，用于确保安全环境中用户和内核模式组件的完整性
- 多个级别的漏洞缓解。 缓解包括地址空间布局随机化 (ASLR)、数据执行保护 (DEP)、任意代码防护、控制流完整性和数据损坏防护
- 在编译器级别自动初始化堆栈变量
- 内核 API 自动对由 Hyper-V 分配的内核堆进行零初始化

这些缓解旨在使利用跨 VM 漏洞的攻击变得不可行。

## <a name="strong-security-assurance-processes"></a>强大的安全保障过程

与虚拟机监控程序有关的攻击面包括软件网络、虚拟设备和所有跨 VM 的面。 通过自动构建集成来跟踪攻击面，这会触发定期的安全检查。

针对安全边界违规行为，我们的 RED 团队对所有 VM 攻击面进行了威胁建模、代码评审、模糊处理和测试。 Microsoft 有一个 [bug 赏金计划](https://www.microsoft.com/msrc/bounty-hyper-v)，用于奖励发现 Microsoft Hyper-V 合格产品版本中的相关漏洞的人员。

> [!NOTE]
> 详细了解 Hyper-v 中的[强大安全保障流程](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes)。

## <a name="next-steps"></a>后续步骤
若要详细了解为提高平台完整性和安全性而做的工作，请参阅：

- [固件安全性](firmware.md)
- [平台代码完整性](code-integrity.md)
- [安全启动](secure-boot.md)
- [测量启动和主机证明](measured-boot-host-attestation.md)
- [Cerberus 项目](project-cerberus.md)
- [静态加密](encryption-atrest.md)