---
title: 在 Azure 虚拟机上安装 SAP HANA | Microsoft Docs
description: 有关在 Azure VM 上安装 SAP HANA 的指南
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 54bfd7c6e691efbd754e9ea2b804e16b3e07ebbe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670972"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>在 Azure 虚拟机上安装 SAP HANA
## <a name="introduction"></a>简介
本指南帮助你参考适当的资源在 Azure 虚拟机中成功部署 HANA。 本指南会将你引领到在 Azure VM 中安装 SAP HANA 之前需要查看的文档资源。 这样，你便可以执行正确的步骤，最终为 Azure VM 中的 SAP HANA 提供受支持的配置。  

> [!NOTE]
> 本指南介绍如何将 SAP HANA 部署到 Azure VM 中。 有关如何将 SAP HANA 部署到 HANA 大型实例的信息，请参阅[如何在 Azure 上安装和配置 SAP HANA（大型实例）](./hana-installation.md)。
 
## <a name="prerequisites"></a>先决条件
本指南还假设你熟悉以下内容：
* SAP HANA 和 SAP NetWeaver 以及如何在本地进行安装。
* 如何在 Azure 上安装和操作 SAP HANA 与 SAP 应用程序实例。
* 以下文档中介绍了相关概念和过程：
   * 规划 Azure 上的 SAP 部署，包括 Azure 虚拟网络规划和 Azure 存储用法。 请参阅 [Azure 虚拟机上的 SAP NetWeaver - 规划和实施指南](./planning-guide.md)
   * Azure 中的部署原则以及部署 VM 的方法。 请参阅[适用于 SAP 的 Azure 虚拟机部署](./deployment-guide.md)
   * [Azure 虚拟机的 SAP HANA 高可用性](./sap-hana-availability-overview.md)中所述的 SAP HANA 高可用性概念

## <a name="step-by-step-before-deploying"></a>部署前的每个步骤
本部分列出了开始在 Azure 虚拟机中安装 SAP HANA 之前需要执行的各个步骤。 步骤的顺序已列出，因此应该按照列出的顺序依次执行：

1. 并非所有可能的部署方案都在 Azure 上受支持。 因此，应该查看文档 [Azure 虚拟机上的 SAP 工作负载支持的方案](./sap-planning-supported-configurations.md)，确定你打算用于 SAP HANA 部署的方案是否受支持。 如果该方案未列出，则需要假设它尚未经过测试，因此不受支持
2. 假设你大致了解了 SAP HANA 部署的内存要求，接下来需要找到一个适合的 Azure VM。 并非所有已通过 SAP NetWeaver 认证的 VM（如 [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 中所述）都通过了 SAP HANA 认证。 SAP HANA 认证的 Azure VM 的真实信息源是 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)网站。 以“S”开头的单元是 [HANA 大型实例](./hana-overview-architecture.md)单元，而不是 Azure VM。
3. 对于 SUSE Linux 或 Red Hat Linux，不同的 Azure VM 类型具有不同的最低操作系统版本。 在 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)网站上，需要单击 SAP HANA 已认证单元列表中的条目以获取此单元的详细数据。 除了支持的 HANA 工作负载以外，该网站还列出了这些 SAP HANA 单元支持的 OS 版本
4. 对于操作系统版本，需要考虑特定的最低内核版本。 以下 SAP 支持说明中阐述了这些最低版本：
    - [SAP 支持说明 #2814271：SAP HANA 备份在 Azure 上失败，出现校验和错误](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP 支持说明 #2753418：由于计时器回退而导致潜在的性能下降](https://launchpad.support.sap.com/#/notes/2753418)
    - [SAP 支持说明 #2791572：由于 Azure 中的 Hyper-V 缺少 VDSO 支持而导致性能下降](https://launchpad.support.sap.com/#/notes/2791572)
4. 根据所选虚拟机类型支持的 OS 版本，需要检查该操作系统版本是否支持所需的 SAP HANA 版本。 请阅读 [SAP 支持说明 #2235581](https://launchpad.support.sap.com/#/notes/2235581)，其中提供了不同操作系统版本支持的 SAP HANA 版本对照表。
5. 需要查看“SAP 产品可用性对照表”，找到 Azure VM 类型、操作系统版本和 SAP HANA 版本的有效组合。 在“SAP 可用性对照表”中，可以确定你要针对 SAP HANA 数据库运行的 SAP 产品是否受支持。


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>VM 分步部署和来宾 OS 注意事项
在此阶段中，需要完成部署 VM 以安装 HANA 并在安装后最终优化所选操作系统的每个步骤。

1. 从 Azure 库中选择基础映像。 如果你要为 SAP HANA 生成自己的操作系统映像，则需要知道成功安装 SAP HANA 所需的每个不同的包。 否则，建议使用 Azure 映像库中适用于 SAP 或 SAP HANA 的 SUSE 和 Red Hat 映像。 这些映像包含成功安装 HANA 所需的包。 根据你与操作系统提供商签订的支持合同，需要选择一个可在其中自带许可证的映像。 或者，选择一个附带支持义务的 OS 映像
2. 如果选择了一个需要你自带许可证的来宾 OS 映像，则需要将该 OS 映像注册到你的订阅，以便可以下载并应用最新补丁。 此步骤需要访问公共 Internet， 除非你在 Azure 中设置了所需的专用实例（例如 SMT 服务器的专用实例）。
3. 确定 VM 的网络配置。 可以在文档 [Azure 上的 SAP HANA 基础结构配置和操作](./hana-vm-operations.md)中了解详细信息。 请记住，你可向 Azure 中的虚拟网卡分配的网络吞吐量没有配额。 因此，通过不同 vNIC 定向流量仅仅是出于安全考虑因素。 我们相信，你可以在通过多个 vNIC 路由流量所存在的复杂性与出于安全考虑而强制提出的要求之间，做出有利的取舍。
3. 部署并注册 VM 后，将最新补丁应用到操作系统。 可以注册到你自己的订阅。 或者，如果选择了一个附带操作系统支持义务的映像，则 VM 应该已经能够访问补丁。 
4. 为 SAP HANA 应用所需的优化。 以下 SAP 支持说明中列出了这些优化措施：

    - [SAP 支持说明 #2694118 - Azure 上的 Red Hat Enterprise Linux HA 加载项](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP 支持说明 #1984787 - SUSE LINUX Enterprise Server 12：安装说明](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP 支持说明 #2578899 - SUSE Linux Enterprise Server 15：安装说明](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP 支持说明 #2002167 - Red Hat Enterprise Linux 7.x：安装和升级](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP support note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)（SAP 支持说明 #2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置） 
    -  [SAP 支持说明 #2772999 - Red Hat Enterprise Linux 8.x：安装和升级](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP 支持说明 #2777782 - SAP HANA DB：RHEL 8 的建议 OS 设置](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP 支持说明 #2455582 - Linux：运行用 GCC 6.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP 支持说明 #2382421 - 优化 HANA 和 OS 级别的网络配置](https://launchpad.support.sap.com/#/notes/2382421)

1. 选择 SAP HANA 的 Azure 存储类型。 在此步骤中，需要确定 SAP HANA 安装的存储布局。 你将使用附加的 Azure 磁盘或原生的 Azure NFS 共享。 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中阐述了支持的 Azure 存储类型，以及可以使用的不同 Azure 存储类型的组合。 请使用所述的配置作为起点。 对于非生产系统，可以配置较低的吞吐量或 IOPS。 如果是要用于生产目的，可能需要配置稍微高一些的吞吐量和 IOPS。
2. 使用 M 系列或 Mv2 系列 VM 时，请确保为包含 DBMS 事务日志或重做日志的卷配置了 [Azure 写入加速器](../../how-to-enable-write-accelerator.md)。 请注意所述的写入加速器限制。
2. 检查是否已在部署的 VM 上启用了 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。

> [!NOTE]
> 不同 sap-tune 配置文件或说明中所述的命令并非都可以在 Azure 上成功运行。 操控 VM 电源模式的命令通常会返回错误，因为底层 Azure 主机硬件的电源模式不可操控。

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>特定于 Azure 虚拟机的分步准备
Azure 中的一个具体步骤是安装 Azure VM 扩展以用于为 SAP 主机代理传送监视数据。 有关安装此监视扩展的详细信息，请参阅以下文档：

-  [SAP 说明 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) 介绍了如何对 Azure 上的 Linux VM 进行 SAP 增强型监视 
-  [SAP 说明 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) 介绍了有关 Linux 上的 SAPOSCOL 的信息 
-  [SAP 说明 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) 介绍了 Microsoft Azure 上的 SAP 的关键监视指标
-  [SAP NetWeaver 的 Azure 虚拟机部署](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA 安装
部署 Azure 虚拟机且注册并配置操作系统后，可以根据 SAP 安装说明安装 SAP HANA。 若要查看相关文档，可以先访问 SAP 网站 [HANA 资源](https://www.sap.com/products/s4hana-erp.html?btp=9d3e6f82-d8ab-4122-8d2d-bf4971217afd)

对于使用 Azure 高级存储或超级磁盘直接附加磁盘的 SAP HANA 横向扩展配置，请参阅文档 [Azure 上的 SAP HANA 基础结构配置和操作](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out)中的具体信息


## <a name="additional-resources-for-sap-hana-backup"></a>有关 SAP HANA 备份的其他资源
有关如何在 Azure VM 上备份 SAP HANA 数据库的信息，请参阅：
* [Azure 虚拟机上的 SAP HANA 备份指南](./sap-hana-backup-guide.md)
* [文件级别的 SAP HANA Azure 备份](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>后续步骤
阅读文档：

- [Azure 上的 SAP HANA 基础结构配置和操作](./hana-vm-operations.md)
- [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)
