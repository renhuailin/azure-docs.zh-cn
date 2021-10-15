---
title: Azure VM 上的 SAP 入门 | Microsoft Docs
description: 了解在 Microsoft Azure 中的虚拟机 (VM) 上运行的 SAP 解决方案
services: virtual-machines-sap
ms.service: virtual-machines-sap
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/01/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e875e71a3faaa7314322c170ca09e3b65f5f8ca
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399539"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>使用 Azure 托管和运行 SAP 工作负荷方案

使用 Microsoft Azure 时，能够在可缩放、符合要求且经企业认证的平台上可靠地运行任务关键型 SAP 工作负荷和方案。 可利用 Azure 的可伸缩性、灵活性和低成本特性。 由于 Microsoft 和 SAP 之间扩大了合作关系，因此，可以在 Azure 的各个开发和测试以及生产方案中运行 SAP 应用程序，并且获得完全支持。 从 SAP NetWeaver 到 SAP S/4HANA，Linux 上的 SAP BI 到 Windows 上的 SAP BI，SAP HANA 到 SQL，我们都能满足客户的需求。

除了在 Azure 上托管具有不同 DBMS 的 SAP NetWeaver 方案，还可以在 Azure 上托管其他 SAP 工作负荷方案，例如 SAP BI。 

Azure for SAP HANA 的独特之处在于，它是一款能让 Azure 脱颖而出的产品。 为了支持托管涉及 SAP HANA 的更多内存和 CPU 资源消耗较高的 SAP 方案，Azure 提供了客户专用的裸机硬件。 使用此解决方案，可以运行需要将多达 24 TB（120 TB 横向扩展）内存用于 S/4HANA 或其他 SAP HANA 工作负荷的 SAP HANA 部署。 

在 Azure 中托管 SAP 工作负荷方案可能还会生成标识集成和单一登录的需求。 使用 Azure Active Directory (Azure AD) 连接不同的 SAP 组件和 SAP 软件即服务 (SaaS) 或平台即服务 (PaaS) 产品时，可能会出现这种情况。 “Azure AD SAP 身份集成和单一登录”部分介绍和记录了具有 Azure AD 和 SAP 实体的这类集成和单一登录方案的列表。

## <a name="changes-to-the-sap-workload-section"></a>对 SAP 工作负荷部分的更改
本文末尾列出了对 Azure 上的 SAP 工作负荷部分中文档的更改。 更改日志中的条目将保留大约 180 天。

## <a name="you-want-to-know"></a>你想了解的内容
如果你有特定的问题，我们将在起始页的此部分中为你指出特定的文档或流程。 你想了解：

- 哪些 SAP 软件版本和哪些操作系统版本支持哪些 Azure VM 和 HANA 大型实例单元。 请阅读文档 [Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)，以获取答案和信息查找过程
- Azure VM 和 HANA 大型实例支持哪些 SAP 部署方案。 可在以下文档中找到有关受支持方案的信息：
    - [Azure 虚拟机上的 SAP 工作负荷支持的方案](./sap-planning-supported-configurations.md)
    - [HANA 大型实例支持的方案](./hana-supported-scenario.md)
- 不同的 Azure 区域提供哪些 Azure 服务、Azure VM 类型和 Azure 存储服务，请查看站点[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/) 
- 除 Windows 和 Pacemaker 以外，是否支持第三方 HA 框架？ 请查看 [SAP 支持说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 的底部部分
- 哪种 Azure 存储最适合我的方案？ 请参阅[适用于 SAP 工作负荷的 Azure 存储类型](./planning-guide-storage.md)
- SAP 是否支持 Oracle Enterprise Linux 中的 Red Hat 内核？ 请参阅 SAP [SAP 支持说明 #1565179](https://launchpad.support.sap.com/#/notes/1565179)
- 为什么 Azure [Da(s)v4](../../dav4-dasv4-series.md)/[Ea(s)](../../eav4-easv4-series.md) VM 系列未通过 SAP HANA 认证？ Azure Das/Eas VM 系列基于 AMD 处理器驱动的硬件。 SAP HANA 不支持 AMD 处理器，甚至不适用于虚拟化方案
- 尽管运行的是最新的 Linux 内核，为什么使用 SAP HANA 仍会收到消息：‘未设置 RDTSCP 指令的 cpu 标志或 constant_tsc 或 nonstop_tsc 的 cpu 标志，或者 current_clocksource 和 available_clocksource 未正确配置’。 有关答案，请查看 [SAP 支持说明 #2791572](https://launchpad.support.sap.com/#/notes/2791572)
- 在哪里可以找到用于在 Azure 上部署 SAP Fiori 的体系结构？ 请查看博客 [Azure 上的 SAP：面向 Internet 的 SAP Fiori 应用的应用程序网关 Web 应用程序防火墙 (WAF) v2 设置](https://blogs.sap.com/2020/12/03/sap-on-azure-application-gateway-web-application-firewall-waf-v2-setup-for-internet-facing-sap-fiori-apps/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA（大型实例）

可通过阅读一系列文档来了解 Azure 上的 SAP HANA（大型实例），简称 HANA 大型实例。 有关 HANA 大型实例的信息，请先阅读文档 [Azure 上的 SAP HANA（大型实例）概述和体系结构](./hana-overview-architecture.md)，然后阅读 HANA 大型实例部分中的相关文档



## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA
文档的此部分介绍 SAP HANA 的不同方面。 作为先决条件，应熟悉 Azure 的主体服务，它们提供 Azure IaaS 的基本服务。 因此，你需要了解 Azure 计算、存储和网络。 与 SAP NetWeaver 相关的 [Azure 规划指南](./planning-guide.md)介绍了许多这类主题。 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 虚拟机上部署的 SAP NetWeaver
本部分列出了有关 Azure 上的 SAP NetWeaver、SAP LaMa 和 Business One 的计划和部署文档。 该文档重点介绍了 Azure 中 SAP 工作负荷下的非 HANA 数据库的基本知识和使用情况。 有关高可用性的文档和文章也是了解 Azure 中 SAP HANA 高可用性的基础

## <a name="sap-netweaver-and-s4hana-high-availability"></a>SAP NetWeaver 和 S/4HANA 高可用性
SAP 应用程序层和 DBMS 的高可用性记录在从文档 [Sap NetWeaver 的 Azure 虚拟机高可用性](./sap-high-availability-guide-start.md)开始的详细信息中



## <a name="integrate-azure-ad-with-sap-services"></a>将 Azure AD 与 SAP 服务集成
在本节中，可以找到有关如何向大多数 SAP SaaS 和 PaaS 服务、NetWeaver 和 Fiori 配置 SSO 的信息 



## <a name="documentation-on-integration-of-azure-services-into-sap-components"></a>有关将 Azure 服务集成到 SAP 组件的文档
在本节中，可以找到有关 Microsoft Power BI 集成到 SAP 数据源中以及 Azure 数据工厂集成到 SAP BW 中的文档。

## <a name="change-log"></a>更改日志
- 2021 年 10 月 1 日：在目录中添加了链接，该链接指向新的适用于 SAP HANA 备份的 Azure 备份体系结构文档。 在[适用于 SAP 工作负载的 Azure 虚拟机 Oracle DBMS 部署](./dbms_guide_oracle.md)中添加了链接，该链接指向适用于 Oracle DBMS 的 Azure 备份服务
- 2021 年 9 月 24 日：更改了[在 SLES 上的 Azure VM 中通过 Pacemaker 对 HSR 进行 SAP HANA 横向扩展](./sap-hana-high-availability-scale-out-hsr-suse.md)、[在 RHEL 上通过 ANF 纵向扩展 SAP HANA 的 HA](./sap-hana-high-availability-netapp-files-red-hat.md) 和[在 RHEL 上的 Azure VM 中通过 Pacemaker 对 HSR 进行 SAP HANA 横向扩展](./sap-hana-high-availability-scale-out-hsr-rhel.md)中的内容，以调整为 HANA srHook 创建 sudoers 文件的说明
- 2021 年 9 月 15 日：在[适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](./dbms_guide_sapase.md)中为 SAP ASE 引入新 HADR 配置
- 2021 年 9 月 8 日：将手动 QoS 容量池添加到[适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)中
- 2021 年 8 月 26 日：更改了[在 Azure 中的 RHEL 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 和[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 中的内容，以更正 Azure Fence 代理的角色定义 JSON
- 2021 年 8 月 17 日：更改了[适用于 SAP 工作负载的 IBM Db2 Azure 虚拟机 DBMS 部署](./dbms_guide_ibm.md)、[适用于 SAP 工作负载的 Azure 存储类型](./planning-guide-storage.md)和 [Azure 虚拟机支持的方案中的 SAP 工作负载](./sap-planning-supported-configurations.md)中的内容，以引入使用 ANF 上托管的 NFS 卷的 IBM Db2 支持
- 2021 年 8 月 2 日：更改了 [SLES for SAP Applications 上 Azure VM 中的 SAP NW 的 HA](./high-availability-guide-suse.md)、[SLES 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-suse-netapp-files.md)、[RHEL for SAP Applications 上 Azure VM 中的 SAP NW 的 HA](./high-availability-guide-rhel.md) 和 [RHEL 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-rhel-netapp-files.md) 中的内容，以阐明模拟排队服务器故障的测试方案的行为 (ENSA1/ENSA2)     
- 2021 年 8 月 11 日：更改了 [RHEL for SAP Applications 上 Azure VM 中的 SAP NW 的 HA](./high-availability-guide-rhel.md)、[RHEL 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-rhel-netapp-files.md) 和 [RHEL 上 Azure VM 中的 SAP NW 的 HA 多 SID 指南](./high-availability-guide-rhel-multi-sid.md)中的内容，以调整群集资源粘性、迁移阈值和顺序约束  
- 2021 年 8 月 11 日：发布了[使用 Azure 上的 SAP IQ 的 SAP BW 近线存储 (NLS) 实施指南](./sap-iq-deployment-guide.md) 
- 2021 年 7 月 29 日：在[使用用于 SAP 应用程序的 Azure NetApp 文件 (SMB) 实现 Windows 上的 Azure VM 上 SAP NetWeaver 的高可用性](./high-availability-guide-windows-netapp-files-smb.md)和[使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上对 SAP ASCS/SCS 实例进行聚类分析](./sap-high-availability-guide-wsfc-shared-disk.md)中为 ASCS/SCS 和 DBMS 引入了合并的双节点 Windows 群集 
- 2021 年 7 月 26 日：更改了[在 Azure 中的 RHEL 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 和[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 中的内容，以便在介绍 Azure Fence 代理设置的部分中将角色分配说明替换为 RBAC 文档的链接
- 2021 年 8 月 2 日：更改了 [RHEL for SAP Applications 上 Azure VM 中的 SAP NW 的 HA](./high-availability-guide-rhel.md)、[RHEL 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-rhel-netapp-files.md) 和 [RHEL 上 Azure VM 中的 SAP NW 的 HA 多 SID 指南](./high-availability-guide-rhel-multi-sid.md)中的内容，以便为 ASCS 群集资源（仅限 ENSA2）删除 `failure-timeout`
- 2021 年 7 月 16 日：重构了 Azure 上的 SAP 文档目录 (TOC) 以简化导航
- 2021 年 7 月 2 日：更改了[在 HANA 大型实例上备份和还原 SAP HANA](./hana-backup-restore.md) 中的内容，以删除 azacsnap 工具的重复内容，以及备份和还原 HANA 大型实例
- 2021 年 7 月 2 日：更改了[在 Azure 中的 RHEL 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 中的内容，以添加如何在两节点 Pacemaker 群集中避免隔离竞争的信息，以及说明如何在将可选 STONITH 配置与 `fence_kdump` 结合使用时减少故障转移延迟的 KB 链接
- 2020 年 12 月 21 日：在[对 HLI 可用的 SKU](./hana-available-skus.md) 中添加了新认证的 HANA 大型实例 SKU 
- 2021 年 6 月 30 日：更改了[使用 WSFC 和 Azure NetApp 文件 (SMB) 的 SAP ASCS/SCS 的 HA 指南](./high-availability-guide-windows-netapp-files-smb.md)中的内容，以便为建议的 SAP 配置文件参数添加节 
- 2020 年 6 月 29 日：更改了[在 Azure 中的 RHEL 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 中的内容，以添加带有 fence_kdump 的可选 stonith 配置
- 2021 年 6 月 28 日：更改了[使用 WSFC 和 Azure NetApp 文件 (SMB) 的 SAP ASCS/SCS 的 HA 指南](./high-availability-guide-windows-netapp-files-smb.md)中的内容，以添加一个声明，指出 SMB 服务器（计算机帐户）前缀不应超过 8 个字符，以避免遇到 SAP 主机名长度限制
- 2020 年 6 月 17 日：更改了 [RHEL 上 Azure VM 上的 SAP HANA 的高可用性](./sap-hana-high-availability-rhel.md)中的内容，以从 HANA 资源创建命令 (RHEL 8.x) 中删除元关键字
- 2021 年 6 月 9 日：在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中更正了 VM SKU 的名称，M192---_v2
- 2021 年 5 月 26 日：更改了[在 SLES 上的 Azure VM 中通过 Pacemaker 使用 HSR 进行 SAP HANA 横向扩展](./sap-hana-high-availability-scale-out-hsr-suse.md)、[在 RHEL 上通过 ANF 纵向扩展 SAP HANA 的 HA](./sap-hana-high-availability-netapp-files-red-hat.md) 和[在 RHEL 上的 Azure VM 中通过 Pacemaker 使用 HSR 横向扩展 SAP HANA](./sap-hana-high-availability-scale-out-hsr-rhel.md) 中的内容，添加配置以准备 OS，以便在 ANF 上运行 HANA  
- 2021 年 5 月 13 日，更改了[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 中的内容，以阐明资源代理 azure-events 的运作方式 
- 2021 年 4 月 30 日：更改了[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 中的内容，以在包 python3-azure-mgmt-compute (SLES 15) 的版本中包含有关与 Azure 隔离代理不兼容的更改的警告  
- 2021 年 4 月 27 日：更改了[使用 WSFC 和文件共享的 SAP ASCS/SCS 实例](./sap-high-availability-guide-wsfc-file-share.md)中的内容，以在先决条件部分中增加指向重要 SAP 说明的链接
- 2021 年 4 月 27 日：在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中，向 HANA 存储配置增加了新的 Msv2、Mdsv2 VM
- 2021 年 4 月 27 日：在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中增加了跨所有 HSR 配置的 VM 在 HANA 系统复制中使用相同存储类型的要求
- 2021 年 4 月 27 日：在[适用于 SAP 工作负载的 Azure 存储类型](./planning-guide-storage.md)中，增加了跨所有 DBMS 高可用性复制配置的 VM 在 DBMS 复制场景中使用相同存储类型的要求
- 2021 年 4 月 23 日：在[用于 Linux on Azure 的 SAP BusinessObjects BI 平台部署指南](businessobjects-deployment-guide-linux.md)中，增加了配置 Azure Database for MySQL 专用链接的部分，以及一些细微更改
- 2021 年 4 月 22 日：发布了适用于 Windows on Azure 的 SAP BusinessObjects BI 平台文档 - [用于 Windows on Azure 的 SAP BusinessObjects BI 平台部署指南](businessobjects-deployment-guide-windows.md)
- 2021 年 4 月 21 日：在文章 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中，增加了启用高级存储磁盘的读取缓存时 M32ts 和 M32ls 上的 HCMT/HWCCT 存储测试可能达不到 HANA KPI 的原因
- 2021 年 4 月 20 日：在[适用于 SAP 工作负载的 IBM Db2 Azure 虚拟机 DBMS 部署](./dbms_guide_ibm.md)一文中阐明了具有不同 Azure 块存储的 IBM Db2 的存储块大小
- 2021 年 4 月 12 日：更改了 [SLES 上 Azure VM 中的 SAP HANA 的 HA](./sap-hana-high-availability.md)、[RHEL 上 Azure VM 中的 SAP HANA 的 HA](./sap-hana-high-availability-rhel.md) 和[在 RHEL 上通过 ANF 向上扩展 SAP HANA 的 HA](./sap-hana-high-availability-netapp-files-red-hat.md) 中的内容，以增加 SAP HANA 系统复制 Python 挂钩的配置说明  
- 2021 年 4 月 12 日：将 SAP HANA 的备份文档替换为[使用 Azure 备份服务备份/还原 SAP HANA](../../../backup/sap-hana-db-about.md)的文档 
- 2021 年 4 月 12 日：发布了[在 SLES 上的 Azure VM 中通过 Pacemaker 使用 HSR 进行 SAP HANA 横向扩展](./sap-hana-high-availability-scale-out-hsr-suse.md)配置指南
- 2021 年 4 月 7 日：在[用于 SAP NetWeaver 的 SQL Server Azure 虚拟机 DBMS 部署](./dbms_guide_sqlserver.md)中阐明了对 SQL Server 多实例和多数据库的支持
- 2021 年 4 月 7 日：在[用于 SAP NetWeaver 的 Azure 虚拟机规划和实施](./planning-guide.md)中增加了有关辅助 IP 地址的信息
- 2021 年 4 月 7 日：在[适用于 SAP 工作负载的 Azure 存储类型](./planning-guide-storage.md)中增加了在 ANF 上对 Oracle DBMS 的支持
- 2021 年 3 月 17 日：更改了 [SLES 上 Azure VM 中的 SAP HANA 的 HA](./sap-hana-high-availability.md)、[RHEL 上 Azure VM 中的 SAP HANA 的 HA](./sap-hana-high-availability-rhel.md) 和[在 RHEL 上通过 ANF 向上扩展 SAP HANA 的 HA](./sap-hana-high-availability-netapp-files-red-hat.md) 中的内容，以增加有关 Pacemaker 群集中已启用 HANA 活动/读取的系统复制的说明
- 2021 年 3 月 15 日：更改了[使用 WSFC 和文件共享的 SAP ASCS/SCS 实例](./sap-high-availability-guide-wsfc-file-share.md)、[安装使用 WSFC 和文件共享的 SAP ASCS/SCS 实例](./sap-high-availability-installation-wsfc-file-share.md)和[使用 WSFC 和文件共享的 SAP ASCS/SCS 多 SID](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 中的内容，以阐明 SAP ASCS/SCS 实例和 SOFS 共享必须部署在不同的群集中
- 2021 年 3 月 3 日：更改了[使用 WSFC 和 Azure NetApp 文件 (SMB) 的 SAP ASCS/SCS 的 HA 指南](./high-availability-guide-windows-netapp-files-smb.md)中的内容，以添加警告声明：在安装 SAP 系统的过程中，运行 SWPM 的用户需要提升的权限
- 2021 年 2 月 11 日：更改了 [Red Hat Enterprise Linux Server 上 Azure VM 中的 IBM Db2 LUW 的高可用性](./high-availability-guide-rhel-ibm-db2-luw.md)中的内容，以修改 RHEL 8.x 的 pacemaker 群集命令
- 2021 年 2 月 3 日：更改了[在 Azure 中的 RHEL 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 中的内容，以更新 stonith create 命令中的 pcmk_host_map
- 2021 年 2 月 3 日：更改了[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 中的内容，以在 stonith create 命令中添加 pcmk_host_map 
- 2021 年 2 月 3 日：在文章 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中增加了有关 SUSE 的 I/O 计划程序设置的详细信息
- 2021 年 2 月 1 日：更改了[在 RHEL 上通过 ANF 进行 SAP HANA 向上扩展的 HA](./sap-hana-high-availability-netapp-files-red-hat.md)、[在 RHEL 上的 Azure VM 中通过 Pacemaker 使用 HSR 进行 SAP HANA 横向扩展](./sap-hana-high-availability-scale-out-hsr-rhel.md)、[在 SLES 上的 Azure VM 中通过 ANF 使用备用节点进行 SAP HANA 横向扩展](./sap-hana-scale-out-standby-netapp-files-suse.md) 和[在 RHEL 上的 Azure VM 中通过 ANF 使用备用节点进行 SAP HANA横向扩展](./sap-hana-scale-out-standby-netapp-files-rhel.md) 中的内容，以增加指向[适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)的链接
- 2021 年 1 月 23 日：在文章 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)和[适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)中引入 HANA 数据卷分区功能，将其作为可在不使用磁盘卷管理器的情况下，在不同的 Azure 磁盘或 NFS 共享上针对 HANA 数据文件进行 I/O 条带化操作的功能
- 2021 年 1 月 18 日：在[适用于 SAP 工作负载的 Azure 虚拟机 Oracle DBMS 部署](./dbms_guide_oracle.md)中增加了适用于 Oracle 的基于 Azure NetApp 文件的 NFS 支持，并调整了[适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)文档的表格中的小数
- 2021 年 1 月 11 日：略微更改了 [RHEL for SAP Applications 上 Azure VM 中的 SAP NW 的 HA](./high-availability-guide-rhel.md)、[RHEL 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-rhel-netapp-files.md) 和 [RHEL 上 Azure VM 中的 SAP NW 的 HA 多 SID 指南](./high-availability-guide-rhel-multi-sid.md)中的内容，以调整命令使其既适用于 RHEL8 和 RHEL7，也适用于 ENSA1 和 ENSA2
- 2021 年 1 月 5 日：更改了[在 SLES 上的 Azure VM 中通过 ANF 使用备用节点进行 SAP HANA 横向扩展](./sap-hana-scale-out-standby-netapp-files-suse.md)和[在 RHEL 上的 Azure VM 中通过 ANF 使用备用节点进行 SAP HANA 横向扩展](./sap-hana-scale-out-standby-netapp-files-rhel.md)中的内容，修订了推荐的配置以允许 SAP 主机代理管理本地端口范围  
- 2021 年 1 月 4 日：向[什么是 Azure 上的 SAP HANA（大型实例）](./hana-overview-architecture.md)中增加了 HLI 支持的新 Azure 区域
- 2020 年 12 月 29 日：向[使用 Azure 可用性区域的 SAP 工作负载配置](./sap-ha-availability-zones.md)中增加了特定 Azure 区域的体系结构建议
-  2020 年 12 月 21 日：向[对 HLI 可用的 SKU](./hana-available-skus.md) 中增加了HANA 大型实例 SKU 的新认证
- 2020 年 12 月 12 日：向 [Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md#oracle-dbms-support)中增加了指向用于阐明 SAP 支持 Oracle Enterprise Linux 的详细信息的 SAP 说明的指针
- 2020 年 11 月 26 日：修改了 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)和[适用于 SAP 工作负载的 Azure 存储类型](./planning-guide-storage.md)以适应更改的单个 [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)
- 2020 年 11 月 5 日：在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中更改了有关 HANA 支持的文件系统类型的新 SAP 说明的链接 
- 2020 年 10 月 26 日：在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中更改了某些 Azure 高级存储配置表格，以阐明预配吞吐量与突发吞吐量
- 2020 年 10 月 22 日：更改了 [SLES for SAP Applications 上 Azure VM 中的 SAP NW 的 HA](./high-availability-guide-suse.md)、[SLES 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-suse-netapp-files.md)、[RHEL for SAP Applications 上 Azure VM 中的 SAP NW 的 HA](./high-availability-guide-rhel.md) 和 [RHEL 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-rhel-netapp-files.md) 中的内容，以调整针对 net.ipv4.tcp_keepalive_time 的建议  
- 2020 年 10 月 16 日：更改了 [SLES 上 Azure VM 中通过 Pacemaker 实现 IBM Db2 LUW 的 HA](./dbms-guide-ha-ibm.md)、[RHEL for SAP Applications 上 Azure VM 中的 SAP NW 的 HA](./high-availability-guide-rhel.md)、[RHEL 上 Azure VM 中的 IBM Db2 LUW 的 HA](./high-availability-guide-rhel-ibm-db2-luw.md)、[RHEL 上 Azure VM 中的 SAP NW 的 HA 多 SID 指南](./high-availability-guide-rhel-multi-sid.md)、[RHEL 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-rhel-netapp-files.md)、[SLES for SAP Applications上 Azure VM 中的 SAP NW 的 HA](./high-availability-guide-suse.md)、[SLES 上 Azure VM 中的 SAP NNW 的 HA 多 SID 指南](./high-availability-guide-suse-multi-sid.md)、[SLES for SAP Applications 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-suse-netapp-files.md)、[SLES 中 Azure VM 上的 NFS 的 HA](./high-availability-guide-suse-nfs.md)、[SLES 上 Azure VM 中的 SAP HANA 的 HA](./sap-hana-high-availability.md)、[在 RHEL 上通过 ANF 进行 SAP HANA 向上扩展的 HA](./sap-hana-high-availability-netapp-files-red-hat.md)、[RHEL 上 Azure VM 中的 SAP HANA 的 HA](./sap-hana-high-availability-rhel.md)、[在 RHEL 上的 Azure VM 中通过 Pacemaker 使用 HSR 进行 SAP HANA 横向扩展](./sap-hana-high-availability-scale-out-hsr-rhel.md)、[准备使用 WSFC 和共享磁盘实现 SAP ASCS/SCS 的 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-shared-disk.md)、[使用 WSFC 和 Azure 共享磁盘实现 SAP ASCS/SCS 的多 SID HA 指南](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md)和[使用 WSFC 和共享磁盘实现 SAP ASCS/SCS 的多 SID HA 指南](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)中的内容，以增加有关负载均衡方案中辅助 IP 不支持浮动 IP 的声明 
- 2020 年 10 月 16 日：在 [HANA 大型实例上 SAP HANA 的备份和还原](./hana-backup-restore.md)中增加用于控制 HANA 大型实例的存储快照的文档
- 2020 年 10 月 15 日：发布了 Azure 上的 SAP BusinessObjects BI 平台文档 - [Azure 上的 SAP BusinessObjects BI 平台计划和实现指南](businessobjects-deployment-guide.md)和 [Azure 上的 SAP BusinessObjects BI 平台部署指南](businessobjects-deployment-guide-linux.md)
- 2020 年 10 月 5 日：发布了[在 RHEL 上的 Azure VM 中通过 Pacemaker 使用 HSR 进行 SAP HANA 横向扩展](./sap-hana-high-availability-scale-out-hsr-rhel.md)配置指南
- 2020 年 9 月 30 日：更改了 [RHEL 上 Azure VM 中的 SAP HANA 的高可用性](./sap-hana-high-availability-rhel.md)、[在 RHEL 上通过 ANF 向上扩展 SAP HANA 的 HA](./sap-hana-high-availability-netapp-files-red-hat.md) 和[在 Azure 中的 RHEL 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 中的内容，以适应 RHEL 8.1 的说明
- 2020 年 9 月 29 日：在文章[用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](./sap-proximity-placement-scenarios.md)中对 PPG 的使用做出更明确的限制和建议 
- 2020 年 9 月 28 日：在[适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](./hana-vm-operations-netapp.md)中增加了 SAP HANA 使用 Azure NetApp 文件的新存储操作指南
- 2020 年 9 月 23 日：在[对 HLI 可用的 SKU](./hana-available-skus.md) 中增加了新的 HLI 认证 SKU 
- 2020 年 9 月 20 日：更改了文档[部署适用于 SAP 工作负载的 Azure 虚拟机 DBMS 的注意事项](./dbms_guide_general.md)、[适用于 SAP NetWeaver 的 SQL Server Azure 虚拟机 DBMS 部署](./dbms_guide_sqlserver.md)、[适用于 SAP 工作负载的 Azure 虚拟机 Oracle DBMS 部署](./dbms_guide_oracle.md)、[适用于 SAP 工作负载的 IBM Db2 Azure 虚拟机 DBMS 部署](./dbms_guide_ibm.md)中的内容，以适应有关将 DBMS 二进制文件和 SAP 二进制文件隔离到不同的 Azure 磁盘的新配置建议。 还有，将超高性能磁盘建议添加到不同的指南。
- 2020 年 9 月 8 日：更改 [SLES 上 Azure VM 中 SAP HANA 的高可用性](./sap-hana-high-availability.md)中的内容，以阐明 stonith 定义
- 2020 年 9 月 3 日：更改了 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中的内容，以适应超高性能磁盘的最小 2 IOPS/GB 容量
- 2020 年 9 月 2 日：更改了[对 HLI 可用的 SKU](./hana-available-skus.md) 中的内容，以在哪些 SKU 经过 HANA 认证的问题上获得更大的透明度
- 2020 年 8 月 25 日：更改 [SLES 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-suse-netapp-files.md) 中的内容，以修复拼写错误
- 2020 年 8 月 25 日：更改[使用 WSFC 和共享磁盘实现 SAP ASCS/SCS 的 HA 指南](./sap-high-availability-guide-wsfc-shared-disk.md)、[准备使用 WSFC 和共享磁盘实现 SAP ASCS/SCS 的 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-shared-disk.md)和[使用 WSFC 和共享磁盘安装 SAP NW HA](./sap-high-availability-guide-wsfc-shared-disk.md) 中的内容，以引入使用 Azure 共享磁盘和文档 SAP ERS2 体系结构的选项
- 2020 年 8 月 25 日：发布[使用 WSFC 和 Azure 共享磁盘实现 SAP ASCS/SCS 的多 SID HA 指南](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md)
- 2020 年 8 月 25 日：由于使用 WFC 和共享磁盘实现 SAP ASCS/SCS 的 HA 指南中的内容更新和重构，而更改[使用 WSFC 和 Azure NetApp 文件 (SMB) 的 SAP ASCS/SCS 的 HA 指南](./high-availability-guide-windows-netapp-files-smb.md)、[准备使用 WSFC 和文件共享实现 SAP ASCS/SCS 的 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-file-share.md)、[使用 WSFC 和共享磁盘实现 SAP ASCS/SCS 的多 SID HA 指南](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)和[使用 WSFC 和 SOFS 文件共享实现 SAP ASCS/SCS 的多 SID HA 指南](./sap-ascs-ha-multi-sid-wsfc-file-share.md)中的内容 
- 2020 年 8 月 21 日：将新的操作系统版本添加到[适用于 HANA 大型实例的兼容操作系统](./os-compatibility-matrix-hana-large-instance.md)中，用作类型 I 和 II HLI 单元的操作系统
- 2020 年 8 月 18 日：发布[在 RHEL 上通过 ANF 向上扩展 SAP HANA 的 HA](./sap-hana-high-availability-netapp-files-red-hat.md)
- 2020 年 8 月 17 日：在文章 [SAP NetWeaver 的 Azure 虚拟机计划和实施指南](./planning-guide.md)中添加有关使用 Azure Site Recovery 将 SAP NetWeaver 系统从本地迁移到 Azure 的信息
- 2020 年 8 月 14 日：在文章[适用于 SAP 工作负荷的 IBM Db2 Azure 虚拟机 DBMS 部署](./dbms_guide_ibm.md)中添加对 Db2 的磁盘配置建议
- 2020 年 8 月 11 日：将 RHEL 7.6 添加到[适用于 HANA 大型实例的兼容操作系统](./os-compatibility-matrix-hana-large-instance.md)中，用作类型 I HLI 单元的操作系统
- 2020 年 8 月 10 日：在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中引入具有成本意识的 SAP HANA 存储配置，以及对 [Azure 上的 SAP 工作负荷：计划和部署清单](./sap-deployment-checklist.md)进行部分更新
- 2020 年 8 月 4 日：更改[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 和[在 Azure 中的 RHEL 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 中的内容，以强调可靠名称解析对 Pacemaker 群集的重要性
- 2020 年 8 月 4 日：更改 [WFCS 上采用文件共享的 SAP NW 的 HA](./sap-high-availability-installation-wsfc-file-share.md)、[WFCS 上采用共享磁盘的 SAP NW 的 HA](./sap-high-availability-installation-wsfc-shared-disk.md)、[Azure VM 中的 SAP NW 的 HA](./high-availability-guide.md)、[SLES 上 Azure VM 中的 SAP NW 的 HA](./high-availability-guide-suse.md)、[SLES 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-suse-netapp-files.md)、[SLES 上 Azure VM 中的 SAP NW 的 HA 多 SID 指南](./high-availability-guide-suse-multi-sid.md)、[RHEL 上 Azure VM 中的 SAP NetWeaver 的高可用性](./high-availability-guide-rhel.md)、[RHEL 上 Azure VM 中通过 ANF 实现 SAP NW 的 HA](./high-availability-guide-rhel-netapp-files.md) 和 [RHEL 上 Azure VM 中的 SAP NW 的 HA 多 SID 指南](./high-availability-guide-rhel-multi-sid.md)中的内容，以阐明参数 `enque/encni/set_so_keepalive` 的使用
- 2020 年 7 月 23 日：添加[使用 Azure 预留节省 SAP HANA 大型实例](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md)文章，解释购买 SAP HANA 大型实例预留之前需注意的事项以及如何进行购买
- 2020 年 7 月 16 日：在[部署指南](deployment-guide.md)中介绍如何使用 Azure PowerShell 来安装适用于 SAP 的新 VM 扩展
- 2020 年 7 月 4 日：发布了 [Azure Monitor for SAP Solutions（预览版）](./monitor-sap-on-azure.md)
- 2020 年 7 月 1 日：在文档 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中，根据 Azure 高级存储突发功能建议成本更低的存储配置 
- 2020 年 6 月 24 日：更改[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 中的内容，以发布新改进的 Azure 隔离代理，并基于 Azure 隔离代理为设备提供更具弹性的 STONITH 配置 
- 2020 年 6 月 24 日：更改[在 Azure 中的 RHEL 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 中的内容，以发布更具弹性的 STONITH 配置
- 2020 年 6 月 23 日：更改[适用于 SAP NetWeaver 的 Azure 虚拟机计划和实施](./planning-guide.md)指南，和介绍[适用于 SAP 工作负荷的 Azure 存储类型](./planning-guide-storage.md)指南
- 2020 年 6 月 22 日：将适用于 SAP 的新 VM 扩展的安装步骤添加到[部署指南](deployment-guide.md)
- 2020 年 6 月 16 日：更改 [SAP HA 方案中使用 Azure 标准 ILB 的 VM 的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)中的内容，以添加指向 SUSE 公有云基础结构 101 文档的链接 
- 2020 年 6 月 10 日：将新的 HLI SKU 添加到[对 HLI 可用的 SKU](./hana-available-skus.md) 和 [SAP HANA（大型实例）存储体系结构](./hana-storage-architecture.md)
- 2020 年 5 月 21 日：更改[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 和[在 Azure 中的 RHEL 上设置 Pacemaker](./high-availability-guide-rhel-pacemaker.md) 中的内容，以添加指向 [SAP HA 方案中使用 Azure 标准 ILB 的 VM 的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)的链接  
- 2020 年 5 月 19 日：在 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中添加一条重要消息，指示在将 LVM 用于与 HANA 相关的卷时，不要使用根卷组
- 2020 年 5 月 19 日：在 [HANA 大型实例的兼容操作系统](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance)中添加适用于 HANA 大型实例类型 II 的新支持操作系统
- 2020 年 5 月 12 日：更改 [SAP HA 方案中使用 Azure 标准 ILB 的 VM 的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)中的内容，以更新链接并添加有关第三方防火墙配置的信息
- 2020 年 5 月 11 日：更改了 [SLES 上 Azure VM 中的 SAP HANA 的高可用性](./sap-hana-high-availability.md)中的内容，以将 `netcat` 资源的资源粘性设置为 0，因为这样可以简化故障转移 
- 2020 年 5 月 5 日：更改[适用于 SAP NetWeaver 的 Azure 虚拟机计划和实施](./planning-guide.md)中的内容，以表明 Gen2 部署可用于 Mv1 VM 系列
- 2020 年 4 月 24 日：更改[在 SLES 上 Azure VM 中通过 ANF 使用备用节点横向扩展 SAP HANA](./sap-hana-scale-out-standby-netapp-files-suse.md)、[在 RHEL 上 Azure VM 中通过 ANF 使用备用节点横向扩展 SAP HANA](./sap-hana-scale-out-standby-netapp-files-rhel.md)、[在 SLES 上 Azure VM 中通过 ANF 实现 SAP NetWeaver 的高可用性](./high-availability-guide-suse-netapp-files.md)和[在 RHEL 上 Azure VM 中通过 ANF 实现 SAP NetWeaver 的高可用性](./high-availability-guide-rhel-netapp-files.md)中的内容，以添加系统自动为 ANF 卷分配 IP 地址的说明
- 2020 年 4 月 22 日：更改 [SLES 上 Azure VM 中的 SAP HANA 的高可用性](./sap-hana-high-availability.md)中的内容，以从指令中删除元属性 `is-managed`，因为它与使群集进入或退出维护模式相冲突
- 2020 年 4 月 21 日：在文章 [Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)和[在 Microsoft Azure 上运行的 SAP 认证和配置](./sap-certifications.md)中，添加了 SQL Azure DB 作为 SAP (Hybris) Commerce Platform 1811 和更高版本支持的 DBMS
- 2020 年 4 月 16 日：在文章 [Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)和[在 Microsoft Azure 上运行的 SAP 认证和配置](./sap-certifications.md)中，添加了 SAP HANA 作为 SAP (Hybris) Commerce Platform 支持的 DBMS
- 2020 年 4 月 13 日：在[适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署](./dbms_guide_sapase.md)中更正为确切的 SAP ASE 版本号
- 2020 年 4 月 7 日：更改[在 Azure 中的 SLES 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 中的内容，以阐明 cloud-netconfig-azure 指令
- 2020 年 4 月 6 日：更改[在 SLES 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](./sap-hana-scale-out-standby-netapp-files-suse.md) 和[在 RHEL 上的 Azure VM 中通过 Azure NetApp 文件使用备用节点横向扩展 SAP HANA](./sap-hana-scale-out-standby-netapp-files-rhel.md) 中的内容，以删除对 NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf)（由 [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf) 代替）的引用
- 2020 年 3 月 31 日：更改 [SLES 上 Azure VM 中的 SAP HANA 的高可用性](./sap-hana-high-availability.md)和 [RHEL 上 Azure VM 中的 SAP HANA 的高可用性](./sap-hana-high-availability-rhel.md)中的内容，以添加有关如何在创建带区卷时指定条带大小的说明
- 2020 年 3 月 27 日：更改[在 SLES for SAP Applications 上的 Azure VM 中通过 ANF 实现 SAP NW 的高可用性](./high-availability-guide-suse-netapp-files.md)中的内容，以根据 NetApp TR-4746 调整文件系统装载选项（删除同步装载选项）
- 2020 年 3 月 26 日：更改 [SLES 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](./high-availability-guide-suse-multi-sid.md)中的内容，以添加对 NetApp TR-4746 的引用
- 2020 年 3 月 26 日：更改 [SLES for SAP Applications 上 Azure VM 中的 SAP NetWeaver 的高可用性](./high-availability-guide-suse.md)、[在 SLES for SAP Applications 上的 Azure VM 中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](./high-availability-guide-suse-netapp-files.md)、[SLES 上 Azure VM 中的 NFS 的高可用性](./high-availability-guide-suse-nfs.md)、[RHEL 上 Azure VM 中的 SAP NetWeaver 的高可用性多 SID 指南](./high-availability-guide-suse-multi-sid.md)、[RHEL for SAP Applications 上 Azure VM 中的 SAP NetWeaver 的高可用性](./high-availability-guide-rhel.md)和[在 RHEL for SAP Applications 上的 Azure VM 中通过 Azure NetApp 文件实现 SAP NetWeaver 的高可用性](./high-availability-guide-rhel-netapp-files.md)中的内容，以更新图表并阐述有关 Azure 负载均衡器后端池创建的说明
- 2020 年 3 月 19 日：进行重大修订，将文档[快速入门：在 Azure 虚拟机上手动安装单实例 SAP HANA](./hana-get-started.md) 修改为[在 Azure 虚拟机上安装 SAP HANA](./hana-get-started.md)
- 2020 年 3 月 17 日：更改[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](./high-availability-guide-suse-pacemaker.md) 中的内容，以删除不再需要的 SBD 配置设置
- 2020 年 3 月 16 日：在 [Azure 部署支持的 SAP 软件](./sap-supported-product-on-azure.md)中阐明 SAP HANA IaaS 认证平台中的列认证方案
- 2020/03/11：更改 [Azure 虚拟机上的 SAP 工作负荷支持的方案](./sap-planning-supported-configurations.md)中的内容，以阐明每个 DBMS 实例多个数据库支持
- 2020 年 3 月 11 日：更改[适用于 SAP NetWeaver 的 Azure 虚拟机计划和实施](./planning-guide.md)中的内容，以介绍第 1 代和第 2 代 VM
- 2020 年 3 月 10 日：更改 [SAP HANA Azure 虚拟机存储配置](./hana-vm-operations-storage.md)中的内容，以阐明 ANF 的实际现有吞吐量限制
