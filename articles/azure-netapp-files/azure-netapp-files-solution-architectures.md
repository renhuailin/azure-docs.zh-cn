---
title: 使用 Azure NetApp 文件的解决方案体系结构 | Microsoft Docs
description: 提供有关使用 Azure NetApp 文件的解决方案体系结构最佳做法的参考。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: b-juche
ms.openlocfilehash: fc5d979c0c89c6f331e6b48fa1ffba30b5a91eb2
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122970316"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>使用 Azure NetApp 文件的解决方案体系结构
本文提供了使用 Azure NetApp 文件的解决方案体系结构最佳做法的参考，帮助你更好地理解这个体系结构。  

下图总结了 Azure NetApp 文件提供的解决方案体系结构的类别：

![解决方案体系结构的类别](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS 应用和数据库解决方案

本部分提供适用于 Linux OSS 应用程序和数据库的解决方案的参考。 

### <a name="oracle"></a>Oracle

* [使用 Azure NetApp 文件的 Microsoft Azure 上的 Oracle 数据库](https://www.netapp.com/media/17105-tr4780.pdf)
* [Microsoft Azure 上的 Oracle VM 映像及其部署：共享存储配置选项](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Azure NetApp 文件单卷上的 Oracle 数据库性能](performance-oracle-single-volumes.md)
* [将 Azure NetApp 文件与 Oracle Database 配合使用的好处](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>机器学习
*   [Cloudera 机器学习](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)
*   [Azure 中的分布式训练：通道检测 - 解决方案设计](https://www.netapp.com/media/32427-tr-4896-design.pdf)

### <a name="education"></a>教育
* [Azure NetApp 文件 NFS 存储上的 Moodle](https://techcommunity.microsoft.com/t5/azure-architecture-blog/azure-netapp-files-for-nfs-storage-with-moodle/ba-p/2300630)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows 应用和 SQL Server 解决方案

本部分提供有关 Windows 应用程序和 SQL Server 解决方案的参考。

### <a name="file-sharing-and-global-file-caching"></a>文件共享和全局文件缓存

* [生成自己的 Azure NFS？将 Linux 文件共享迁移到云](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [使用 Azure NetApp 文件和 NetApp 全局文件缓存进行全局分布式 Enterprise 文件共享](https://f.hubspotusercontent20.net/hubfs/525875/NA-580-0521-Architecture-Doc-R3.pdf)
* [Azure NetApp 文件的云合规性](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [Azure 上的 SQL Server 部署指南（使用 Azure NetApp 文件）](https://www.netapp.com/pdf.html?item=/media/27154-tr-4888.pdf)
* [使用 Azure NetApp 文件进行 SQL Server 部署的好处](solutions-benefits-azure-netapp-files-sql-server.md)
* [使用 Azure NetApp 文件通过 SMB 部署 SQL Server](https://www.youtube.com/watch?v=x7udfcYbibs)
* [使用 Azure NetApp 文件通过 SMB 部署 SQL Server Always-On 故障转移群集](https://www.youtube.com/watch?v=zuNJ5E07e8Q) 
* [使用 Azure NetApp 文件部署 Always-On 可用性组](https://www.youtube.com/watch?v=y3VQmzzeyvc) 

## <a name="sap-on-azure-solutions"></a>Azure 上的 SAP 解决方案

本部分提供有关 Azure 上的 SAP 解决方案的参考。 

### <a name="generic-sap-and-sap-netweaver"></a>通用 SAP 和 SAP Netweaver 

* [使用 Azure NetApp 文件的 Microsoft Azure 上的 SAP 应用程序](https://www.netapp.com/us/media/tr-4746.pdf)
* [带有适用于 SAP 应用程序的 Azure NetApp 文件的 SUSE Linux Enterprise Server 上 Azure VM 上的 SAP NetWeaver 的高可用性](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [使用适用于 SAP 应用程序的 Azure NetApp 文件实现 Red Hat Enterprise Linux 的 Azure VM 上的 SAP NetWeaver 高可用性](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [使用适用于 SAP 应用程序的 Azure NetApp 文件 (SMB) 实现 Windows 的 Azure VM 上的 SAP NetWeaver 高可用性](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [适用于 SAP 应用程序多 SID 的 Red Hat Enterprise Linux 的 Azure VM 上的 SAP NetWeaver 高可用性指南](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA Azure 虚拟机存储配置](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [适用于 SAP HANA 的 Azure NetApp 文件上的 NFS v4.1 卷](../virtual-machines/workloads/sap/hana-vm-operations-netapp.md)
* [在 Red Hat Enterprise Linux 上使用 Azure NetApp 文件实现 SAP HANA 纵向扩展的高可用性](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [在 SUSE Linux Enterprise Server 上使用 Azure NetApp 文件在 Azure VM 上利用备用节点实现 SAP HANA 的横向扩展](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [在 Red Hat Enterprise Linux 上使用 Azure NetApp 文件在 Azure VM 上利用备用节点实现 SAP HANA 的横向扩展](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)
* [RHEL 上使用 HSR 和 Pacemaker 的 SAP HANA 横向扩展 - Azure 虚拟机](../virtual-machines/workloads/sap/sap-hana-high-availability-scale-out-hsr-rhel.md)
* [Azure 应用程序一致性快照工具 (AzAcSnap)](azacsnap-introduction.md)

### <a name="sap-anydb"></a>SAP AnyDB

* [适用于 SAP 工作负载的 Oracle Azure 虚拟机 DBMS 部署 - Azure 虚拟机](../virtual-machines/workloads/sap/dbms_guide_oracle.md#oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux)
* [使用 Azure NetApp 文件部署 SAP AnyDB (Oracle 19c)](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)
* [使用 Azure NetApp 文件针对 SAP 工作负荷进行 IBM Db2 Azure 虚拟机 DBMS 部署](../virtual-machines/workloads/sap/dbms_guide_ibm.md#using-azure-netapp-files)

### <a name="sap-iq-nls"></a>SAP IQ-NLS

*   [在 SUSE Linux Enterprise Server 上使用 Azure NetApp 文件部署 SAP IQ-NLS HA 解决方案](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)
* [如何在 HA 方案中管理 SAP IQ 许可证](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/how-to-manage-sap-iq-license-in-ha-scenario/ba-p/2052583)

### <a name="sap-tech-community-and-blog-posts"></a>SAP 技术社区和博客文章 

* [Azure NetApp 文件 - 实现 SAP HANA 快速备份](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp 文件 - 从快照备份还原 HANA 数据库](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp 文件 - 通过云同步进行 SAP HANA 卸载备份](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [使用 Azure NetApp 文件加速 SAP HANA 系统复制](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [云卷 ONTAP 和 Azure NetApp 文件：简化 SAP HANA 系统迁移](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)
* [在 HANA N+M 横向扩展体系结构中使 ANF 投资收益最大化的体系结构决策 - 第一部分](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2078737)
* [在 HANA N+M 横向扩展体系结构中使 ANF 投资收益最大化的体系结构决策 - 第二部分](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2117130)
* [在 HANA N+M 横向扩展体系结构中使 ANF 投资收益最大化的体系结构决策 - 第三部分](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2215948)
* [使用 Azure NetApp 文件进行 SAP 布局大小调整和卷整合](https://techcommunity.microsoft.com/t5/sap-on-microsoft/sap-landscape-sizing-and-volume-consolidation-with-anf/m-p/2145572/highlight/true#M14)

## <a name="azure-vmware-solutions"></a>Azure VMware 解决方案

* [将 Azure NetApp 文件与 Azure VMware 解决方案配合使用 - 来宾操作系统装载](../azure-vmware/netapp-files-with-azure-vmware-solution.md)

## <a name="virtual-desktop-infrastructure-solutions"></a>虚拟桌面基础结构解决方案

本部分提供有关虚拟桌面基础结构解决方案的参考。

### <a name="azure-virtual-desktop"></a><a name="windows-virtual-desktop"></a>Azure 虚拟桌面

* [将 Azure NetApp 文件与 Azure 虚拟桌面配合使用的好处](solutions-windows-virtual-desktop.md)
* [Azure 虚拟桌面中 FSLogix 配置文件容器的存储选项](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [使用 Azure NetApp 文件为主机池创建 FSLogix 配置文件容器](../virtual-desktop/create-fslogix-profile-container.md)
* [企业级 Azure 虚拟桌面](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [适用于企业的 Microsoft FSLogix - Azure NetApp 文件最佳做法](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [设置用于 MSIX 应用附加的 Azure NetApp 文件](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

### <a name="citrix"></a>Citrix   

* [通过 Azure NetApp 文件进行 Citrix 配置文件管理的最佳做法指南](https://www.netapp.com/pdf.html?item=/media/55973-tr-4901.pdf)


## <a name="hpc-solutions"></a>HPC 解决方案

本部分提供有关高性能计算 (HPC) 解决方案的参考。 

### <a name="generic-hpc"></a>泛型 HPC

* [Azure NetApp 文件：充分利用云存储](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [使用 Azure Batch 和 Azure NetApp 文件运行 MPI 工作负载](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure CycleCloud：Azure NetApp 文件上的 CycleCloud HPC 环境](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>石油和天然气

* [高性能计算 (HPC)：Azure 中的石油和天然气](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [在 Azure 上运行储层模拟软件](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>电子设计自动化 (EDA)

* [使用 Azure NetApp 文件实现电子设计自动化的好处](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud：将 EDA HPC 实验室与 Azure NetApp 文件配合使用](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [将 Azure 用于半导体行业](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>分析

* [Azure NetApp 文件：在 Microsoft Azure 上将共享文件系统与 SAS 网格配合使用](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [Azure NetApp 文件：在 MS Azure 上将共享文件系统与 SAS 网格配合使用 - RHEL8.3/nconnect 更新](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/722261#M21648)
* [将 Microsoft Azure 与 SAS® 配合使用的最佳做法](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)
* [Azure 上的 SAS 体系结构指南 - Azure 体系结构中心 | Azure NetApp 文件](/azure/architecture/guide/sas/sas-overview#azure-netapp-files-nfs)

## <a name="azure-platform-services-solutions"></a>Azure 平台服务解决方案

本部分提供 Azure 平台服务的解决方案。 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes 服务与 Kubernetes

* [Astra：保护、恢复和管理 Azure NetApp 文件上的 AKS 工作负载](https://cloud.netapp.com/hubfs/Astra%20Azure%20Documentation.pdf) 
* [将 Azure NetApp 文件与 Azure Kubernetes 服务集成](../aks/azure-netapp-files.md)
* [使用 Azure NetApp 文件在 Azure 上实现卓越的 Kubernetes 性能](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Azure NetApp 文件 + Trident = 用于 Kubernetes 的动态和持久存储](https://anfcommunity.com/2021/02/16/azure-netapp-files-trident-dynamic-and-persistent-storage-for-kubernetes/)
* [Trident - 容器的存储业务流程协调程序](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Azure Kubernetes 服务 (AKS) 中的 Magento 电子商务平台](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-batch"></a>Azure Batch

* [使用 Azure Batch 和 Azure NetApp 文件运行 MPI 工作负载](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
