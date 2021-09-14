---
title: 在 Azure VMware 解决方案上部署 Zerto 灾难恢复（初始可用性）
description: 了解如何为本地 VMware 或 Azure VMware 解决方案虚拟机实现 Zerto 灾难恢复。
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 9e919f8fa22965bdd7a170db2c19bd921de030cd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123475274"
---
# <a name="deploy-zerto-disaster-recovery-on-azure-vmware-solution-initial-availability"></a>在 Azure VMware 解决方案上部署 Zerto 灾难恢复（初始可用性）

本文介绍如何为本地 VMware 或基于 Azure VMware 解决方案的虚拟机 (VM) 实现灾难恢复 (DR)。 本文中的解决方案使用 [Zerto 灾难恢复](https://www.zerto.com/solutions/use-cases/disaster-recovery/)。 Zerto 的实例会被部署在受保护站点和恢复站点上。 

Zerto 旨在最大限度地减少发生灾难时 VM 的停机时间。 Zerto 的平台以连续数据保护 (CDP) 为基础构建，可实现最小或接近零数据丢失。 它为许多业务关键型和任务关键型企业应用程序提供所需的保护级别。 Zerto 还自动执行和协调故障转移和故障回复，确保在灾难中最大限度地减少停机时间。 总体而言，Zerto 通过自动化功能简化了管理，确保了快速且高度可预测的恢复时间。


## <a name="core-components-of-the-zerto-platform"></a>Zerto 平台的核心组件

| 组件 | 说明 |
| --- | --- |
| Zerto Virtual Manager (ZVM)   | 作为安装在 Windows VM 上的 Windows 服务实现的 Zerto 管理应用程序。 私有云管理员安装和管理 Windows VM。 ZVM 启用第 0 天和第 2 天 DR 配置。 例如，配置主站点和灾难恢复站点、保护 VM、恢复 VM 等。 但是，它不处理受保护客户 VM 的复制数据。     |
| 虚拟复制设备 (vRA)   | 用于处理从源到复制目标的数据复制的 Linux VM。 每个 ESXi 主机均安装有一个 vRA 实例，可提供与私有云的主机一起增长和缩减的真正的缩放体系结构。 VRA 管理到受保护的 VM 的和从受保护的 VM 到其本地或远程目标的数据复制，并将数据存储在日志中。    |
| Zerto ESXi 主机驱动程序   | 安装在为 Zerto DR 配置的各个 VMware ESXi 主机上。 主机驱动程序会截获 vSphere VM 的 IO，并将复制数据发送到该主机所选的 vRA。 然后，vRA 负责将 VM 的数据复制到一个或多个 DR 目标。    |
| Zerto 云设备 (ZCA)   | 仅当使用 Zerto 将 vSphere VM 恢复为 Azure 本机 IaaS VM 时，才使用 Windows VM。 ZCA 由以下各项组成：<ul><li>ZVM：承载 UI 并与 Azure 的本机 API 集成以进行管理和协调的 Windows 服务。</li><li>VRA：将数据从或复制到 Azure 的 Windows 服务。</li></ul>ZCA 使用其部署所在的平台进行本机集成，使你能够在 Microsoft Azure 的存储帐户中使用 Azure Blob 存储。 因此，它可确保在每个平台上实现最经济高效的部署。   |
| 虚拟保护组 (VPG)   | 在 ZVM 上创建的 VM 的逻辑组。 Zerto 允许在 VPG 上配置 DR、备份和移动性策略。 利用此机制，可以将一组一致的策略应用于一组 VM。  |


若要详细了解 Zerto 平台体系结构，请参阅 [Zerto 平台体系结构指南](https://www.zerto.com/wp-content/uploads/2021/07/Zerto-Platform-Architecture-Guide.pdf)。 


## <a name="supported-zerto-scenarios"></a>支持的 Zerto 方案

可以在以下三种方案中结合使用 Zerto 和 Azure VMware 解决方案： 

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>方案 1：本地 VMware 到 Azure VMware 解决方案 DR

在此方案中，主站点是基于本地 vSphere 的环境。 灾难恢复站点是 Azure VMware 解决方案私有云。 

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-1.png" alt-text="显示 Azure VMware 解决方案上的 Zerto 灾难恢复解决方案的方案 1 的示意图。" border="false":::



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-cloud-dr"></a>方案 2：Azure VMware 解决方案到 Azure VMware 解决方案云 DR

在此方案中，主站点是一个 Azure 区域中的 Azure VMware 解决方案私有云。 灾难恢复站点是另一个 Azure 区域中的 Azure VMware 解决方案私有云。

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2.png" alt-text="显示 Azure VMware 解决方案上的 Zerto DR 解决方案的方案 2 的示意图。" border="false":::


### <a name="scenario-3-azure-vmware-solution-to-iaas-vms-cloud-dr"></a>方案 3：Azure VMware 解决方案到 IaaS VM 云 DR

在此方案中，主站点是一个 Azure 区域中的 Azure VMware 解决方案私有云。 在灾难发生时使用 Azure Blob 和 Azure IaaS（基于 Hyper-V 的）VM。

:::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-3.png" alt-text="显示 Azure VMware 解决方案上的 Zerto DR 解决方案的方案 3 的示意图。" border="false":::



## <a name="prerequisites"></a>必备条件

### <a name="on-premises-vmware-to-azure-vmware-solution-dr"></a>本地 VMware 到 Azure VMware 解决方案 DR

- 部署为次要区域的 Azure VMware 解决方案私有云。

- 本地和 Azure VMware 解决方案之间的 VPN 或 ExpressRoute 连接。



### <a name="azure-vmware-solution-to-azure-vmware-solution-cloud-dr"></a>Azure VMware 解决方案到 Azure VMware 解决方案云 DR

- Azure VMware 解决方案私有云必须部署在主要和次要区域中。

   :::image type="content" source="media/zerto-disaster-recovery/zerto-disaster-recovery-scenario-2a-prerequisite.png" alt-text="显示 Azure VMware 解决方案上 Zerto DR 解决方案的方案 2 的第一个必备条件的示意图。":::
 
- 源和目标 Azure VMware 解决方案私有云之间的连接，如 ExpressRoute Global Reach。



### <a name="azure-vmware-solution-iaas-vms-cloud-dr"></a>Azure VMware 解决方案 IaaS VM 云 DR

- 基于 ExpressRoute 的网络连接，从 Azure VMware 解决方案到用于灾难恢复的 vNET。   

- 对于其余先决条件，请遵循 [Zerto 虚拟复制 Azure 企业指南](http://s3.amazonaws.com/zertodownload_docs/Latest/Zerto%20Virtual%20Replication%20Azure%20Enterprise%20Guidelines.pdf)。



## <a name="install-zerto-on-azure-vmware-solution"></a>在 Azure VMware 解决方案上安装 Zerto

目前，Azure VMware 解决方案上的 Zerto DR 处于初始可用性 (IA) 阶段。 在 IA 阶段，必须与 Microsoft 联系以请求并获得 IA 支持。

若要请求对 Azure VMware 解决方案上的 Zerto 进行 IA 支持，请向 zertoonavs@microsoft.com 发送电子邮件请求。 在 IA 阶段，Azure VMware 解决方案仅支持手动安装和载入 Zerto。 但是，Microsoft 将与你合作，以确保你可以在私有云上手动安装 Zerto。

>[!NOTE]
>正式发布的 Azure VMware 解决方案将支持自助安装以及 Azure VMware 解决方案上 Zerto 的第 2 天操作。


## <a name="configure-zerto-for-disaster-recovery"></a>为灾难恢复配置 Zerto

若要为本地 VMware 到 Azure VMware 解决方案 DR 和 Azure VMware 解决方案到 Azure VMware 解决方案云 DR 方案配置 Zerto，请参阅 [Zerto Virtual Manager 管理指南 vSphere 环境](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/Zerto%20Virtual%20Manager%20vSphere%20Administration%20Guide.pdf?cb=1629311409)。


有关详细信息，请参阅 [Zerto 技术文档](https://www.zerto.com/myzerto/technical-documentation/)。 此外，还可以下载[适用于 Zerto Software PDF 文档包的 v8.5 搜索工具](https://s3.amazonaws.com/zertodownload_docs/8.5_Latest/SEARCH_TOOL.zip?cb=1629311409)的所有 Zerto 指南部分。



## <a name="ongoing-management-of-zerto"></a>Zerto 的持续管理

- 缩放 Azure VMware 解决方案私有云操作时，可能需要添加新的 Azure VMware 解决方案主机以用于 Zerto 保护，或者将 Zerto DR 配置到新的 Azure VMware 解决方案 vSphere 群集。 在这两种情况下，若要为新主机添加 Zerto vRA，则将需要在初始可用性阶段向 Azure VMware 解决方案团队提出支持请求。 可以通过 Azure 门户提出对这些第 2 天配置的[支持请求](https://rc.portal.azure.com/#create/Microsoft.Support)。   

   :::image type="content" source="media/zerto-disaster-recovery/support-request-zerto-disaster-recovery.png" alt-text="显示对第 2 天 Zerto DR 配置的支持请求的屏幕截图。":::

- 由于 Zerto-vSphere 集成的性质（使用 vSphere DRS 硬性相关性），预计群集的向下缩放操作会失败。 Azure VMware 解决方案 SRE 团队将在 IA 阶段对这些故障进行脱机更正。

- 在 GA 阶段，上述所有操作都将以自动自助服务方式启用。


## <a name="faqs"></a>常见问题

### <a name="can-i-use-a-pre-existing-zerto-product-license-on-azure-vmware-solution"></a>能否在 Azure VMware 解决方案上使用预先存在的 Zerto 产品许可证？

可对 Azure VMware 解决方案环境重复使用预先存在的 Zerto 产品许可证。 如果需要新的 Zerto 许可证，请通过发送电子邮件至 sales@zerto.com 从 Zerto 获取新许可证。 

### <a name="how-is-zerto-supported"></a>如何支持 Zerto？

Zerto 灾难恢复是由 Zerto 销售并支持的解决方案。 对于 Zerto 的任何支持问题，请始终联系 [Zerto 支持](https://www.zerto.com/company/support-and-service/support/)。

Zerto 和 Microsoft 支持团队将根据需要相互交流，以排查 Azure VMware 解决方案上的 Zerto 问题。

