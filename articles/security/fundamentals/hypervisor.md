---
title: Azure 汽油上的虚拟机监控程序安全性-Azure 安全性
description: Azure 汽油上的虚拟机监控程序安全技术概述。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 766266edd663b75b893a5883e30bb48eed7bbfdf
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696109"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>Azure 汽油上的虚拟机监控程序安全性

Azure 虚拟机监控程序系统基于 Windows Hyper-v。 虚拟机监控程序系统使计算机管理员可以指定具有不同地址空间的来宾分区。 使用单独的地址空间，可以加载在计算机的根分区中执行的操作系统和应用程序并行运行 (主机) 。 主机操作系统 (也称为特权根分区) 可以直接访问系统 (存储控制器、网络 adaptions) 上的所有物理设备和外围设备。 通过向每个来宾分区公开 "虚拟设备"，主机操作系统允许来宾分区共享这些物理设备的使用。 因此，在来宾分区中执行的操作系统有权访问在根分区中执行的虚拟化服务提供的虚拟化外围设备。

Azure 虚拟机监控程序的构建目的是考虑以下安全目标：

| 目标 | 源 |
|--|--|
| 隔离 | 安全策略不要求在 Vm 之间传输信息。 此约束要求 Virtual Machine Manager (VMM) 和硬件中的功能，以便隔离内存、设备、网络和托管资源（如持久化数据）。 |
| VMM 完整性 | 为了实现整体系统完整性，将建立和维护各个虚拟机监控程序组件的完整性。 |
| 平台完整性 | 虚拟机监控程序的完整性取决于它所依赖的硬件和软件的完整性。 尽管虚拟机监控程序无法直接控制平台的完整性，但 Azure 依赖于硬件和固件机制（如 [Cerberus](project-cerberus.md) 芯片）来保护和检测底层平台完整性。 如果平台完整性受到危害，则会阻止 VMM 和来宾运行。 |
| 受限访问 | 只有通过安全连接进行连接的授权管理员才能执行管理功能。 最小特权原则由 Azure 基于角色的访问控制 (Azure RBAC) 机制强制实施。 |
| 审核 | Azure 允许审核功能捕获和保护有关系统上发生的情况的数据，以便以后可以对其进行检查。 |

Microsoft 对 Azure 虚拟机监控程序和虚拟化子系统进行强化的方法可分为以下三个类别。

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>虚拟机监控程序强制实施的强定义安全边界

Azure 虚拟机监控程序强制执行以下两个安全边界：

- 虚拟化的 "来宾" 分区和特权分区 ( "主机" ) 
- 多个来宾
- 本身和主机
- 本身和所有来宾

为虚拟机监控程序安全边界保证机密性、完整性和可用性。 边界防御各种攻击，包括侧通道信息泄露、拒绝服务和特权提升。

虚拟机监控程序安全边界还为网络流量、虚拟设备、存储、计算资源和所有其他 VM 资源提供租户之间的分段。

## <a name="defense-in-depth-exploit-mitigations"></a>深层防御攻击缓解

在极少数情况下，安全边界有一个漏洞，Azure 虚拟机监控程序包括多个缓解层，包括：

- 隔离承载跨虚拟机组件的基于主机的进程
- 基于虚拟化的安全 (VBS) ，确保用户和内核模式组件在安全世界中的完整性
- 多个攻击级别。 缓解措施包括地址空间布局随机化 (ASLR) ，数据执行保护 (DEP) ，任意代码防护，控制流完整性和数据损坏防护
- 在编译器级别自动初始化堆栈变量
- 自动初始化 Hyper-v 所进行的内核堆分配的内核 Api

这些缓解旨在使利用跨 VM 漏洞的攻击成为不可行的。

## <a name="strong-security-assurance-processes"></a>强大的安全保障过程

与虚拟机监控程序相关的攻击面包括软件网络、虚拟设备和所有跨虚拟机表面。 攻击面通过自动生成集成进行跟踪，这会触发定期的安全检查。

所有 VM 攻击面都是威胁建模、代码审查、模糊，并由我们的红色团队测试安全边界违规。 Microsoft 有一个 [bug 奖励计划](https://www.microsoft.com/msrc/bounty-hyper-v) ，该程序针对 Microsoft Hyper-V 的合格产品版本中的相关漏洞支付奖励。

> [!NOTE]
> 了解有关 Hyper-v 中的 [强大安全保障流程](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) 的详细信息。

## <a name="next-steps"></a>后续步骤
若要详细了解如何驱动平台的完整性和安全性，请参阅：

- [固件安全性](firmware.md)
- [安全启动](secure-boot.md)
- [标准启动和主机证明](measured-boot-host-attestation.md)
- [Cerberus 项目](project-cerberus.md)
- [静态加密](encryption-atrest.md)