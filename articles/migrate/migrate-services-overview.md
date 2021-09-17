---
title: 关于 Azure Migrate
description: 了解 Azure Migrate 服务。
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: b67239a973f3ba435aeeb7021d8d4f2faea9c98f
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015631"
---
# <a name="about-azure-migrate"></a>关于 Azure Migrate

本文简要概述了 Azure Migrate 服务。

Azure Migrate 提供了一个集中化中心，用于评估本地服务器、基础结构、应用程序和数据并将其迁移到 Azure。 它提供以下内容：

- **统一迁移平台**：用于启动、运行和跟踪到 Azure 的迁移的单一门户。
- **工具范围**：用于评估和迁移的一系列工具。 Azure Migrate 工具包括 Azure Migrate: 发现和评估以及 Azure Migrate: 服务器迁移。 Azure Migrate 还集成了其他 Azure 服务和工具，以及独立软件供应商 (ISV) 产品/服务。
- **评估和迁移**：在 Azure Migrate 中心内，可以评估和迁移以下项目：
    - **服务器、数据库和 Web 应用**：评估包含 Web 应用和 SQL Server 实例的本地服务器，并将其迁移到 Azure 虚拟机或 Azure VMware 解决方案 (AVS)（预览版）。
    - **数据库**：评估本地数据库，并将其迁移到 Azure SQL 数据库或 SQL 托管实例。
    - **Web 应用程序**：评估本地 Web 应用程序，并将其迁移到 Azure 应用服务。
    - **虚拟桌面**：评估本地虚拟桌面基础结构 (VDI) 并将其迁移到 Azure 中的 Windows 虚拟桌面。
    - **Data**：使用 Azure Data Box 产品快速且经济高效地将大量数据迁移到 Azure。

## <a name="integrated-tools"></a>集成工具

Azure Migrate 中心包含以下工具：

**工具** | **评估和迁移** | **详细信息**
--- | --- | ---
**Azure Migrate: 发现和评估** | 发现和评估服务器，包括 SQL 和 Web 应用 | 在准备迁移到 Azure 时，发现并评估在 VMware、Hyper-V 和物理服务器上运行的本地服务器。
**Azure Migrate:服务器迁移** | 迁移服务器 | 将 VMware VM、Hyper-V VM、物理服务器、其他虚拟服务器和公有云 VM 迁移到 Azure。
**Data Migration Assistant** | 评估 SQL Server 数据库，以便迁移到 Azure SQL 数据库、Azure SQL 托管实例或运行 SQL Server 的 Azure VM。 | 数据迁移助手是一种用于评估 SQL Server 的独立工具。 它可以帮助找出阻止迁移的潜在问题。 它可以识别不受支持的功能、迁移后可从中受益的新功能，以及正确的数据库迁移路径。 [了解详细信息](/sql/dma/dma-overview)。
**Azure 数据库迁移服务** | 将本地数据库迁移到运行 SQL Server、Azure SQL 数据库或 SQL 托管实例的 Azure VM | [详细了解](../dms/dms-overview.md)数据库迁移服务。
**Movere** | 评估服务器 | [详细了解](#movere) Movere。
**Web 应用迁移助手** | 评估本地 Web 应用并将其迁移到 Azure。 |  Azure 应用服务迁移助手是一种独立工具，用于评估用于迁移到 Azure 应用服务的本地网站。<br/><br/> 使用迁移助手将 .NET 和 PHP Web 应用迁移到 Azure。 [详细了解](https://appmigration.microsoft.com/) Azure 应用服务迁移助手。
**Azure Data Box** | 迁移脱机数据 | 使用 Azure Data Box 产品将大量脱机数据迁移到 Azure。 [了解详细信息](../databox/index.yml)。

> [!NOTE]
> 如果使用的是 Azure 政府版，则外部集成工具和 ISV 产品/服务无法将数据发送到 Azure Migrate。 你可以单独使用工具。

## <a name="isv-integration"></a>ISV 集成

Azure Migrate 集成了多个 ISV 产品/服务。 

**ISV**    | **功能**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | 迁移服务器。
[Cloudamize](https://www.cloudamize.com/platform) | 评估服务器。
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | 评估和迁移服务器。
[Device42](https://docs.device42.com/) | 评估服务器。
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | 评估 VDI。
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | 迁移服务器。
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | 评估服务器。
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | 评估服务器和数据库。
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | 迁移服务器。

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Azure Migrate: 发现和评估工具

“Azure Migrate: 发现和评估”工具可发现并评估要迁移到 Azure 的本地 VMware VM、Hyper-V VM 和物理服务器。

该工具的作用如下：

- **Azure 迁移就绪性**：评估本地服务器、SQL Server 和 Web 应用是否已准备好迁移到 Azure。
- **Azure 大小调整**：估算迁移后的 Azure VMs/Azure SQL 配置大小/Azure VMware Solution 节点数量。
- **Azure 成本估算**：估算在 Azure 中运行本地服务器的成本。
- **依赖关系分析**：确定跨服务器依赖关系和优化策略，以便将相互依赖的服务器移动到 Azure。 详细了解使用[依赖关系分析](concepts-dependency-visualization.md)进行的发现和评估。

发现和评估使用本地部署的轻型 [Azure Migrate 设备](migrate-appliance.md)。

- 该设备在 VM 或物理服务器上运行。 可以使用下载的模板轻松安装它。
- 设备会发现本地服务器。 它还持续向 Azure Migrate 发送服务器元数据和性能数据。
- 设备发现是无代理的。 无需在已发现的服务器上安装任何组件。
- 完成设备发现后，可将发现的服务器收集到组中，然后对每个组运行评估。

## <a name="azure-migrate-server-migration-tool"></a>“Azure Migrate:服务器迁移工具

Azure Migrate:服务器迁移工具可帮助将服务器迁移到 Azure：

**迁移** | **详细信息**
--- | ---
本地 VMware VM | 可以使用无代理或基于代理的迁移将 VM 迁移到 Azure。<br/><br/> 对于无代理迁移，服务器迁移使用发现和评估工具用于发现和评估服务器的相同设备。<br/><br/> 对于基于代理的迁移，服务器迁移使用复制设备。
本地 Hyper-V VM | 将 VM 迁移到 Azure。<br/><br/> 服务器迁移使用 Hyper-V 主机上安装的提供程序代理进行迁移。
托管在其他云上的本地物理服务器或服务器 | 可以将物理服务器迁移到 Azure。 还可迁移其他虚拟化服务器以及其他公有云中的 VM，方法是将其视为物理服务器以进行迁移。 服务器迁移使用复制设备进行迁移。

## <a name="selecting-assessment-and-migration-tools"></a>选择评估和迁移工具

在 Azure Migrate 中心内，选择用于评估或迁移的工具，并将其添加到某个项目。 如果添加 ISV 工具或 Movere：

- 若要开始使用该工具，请根据工具说明获取许可证或注册免费试用版。 每个 ISV 或工具都会指定工具许可。
- 每个工具都有一个用于连接到 Azure Migrate 的选项。 按照工具说明进行连接。
- 可以在项目中跨所有工具跟踪迁移。

## <a name="movere"></a>Movere

Movere 是一个软件即服务 (SaaS) 平台。 它可以准确呈现整个 IT 环境在一天内的情况，从而增强商业智能。 组织和企业会发展、变革以及进行数字化优化。 在这种情况下，Movere 可让企业自信地洞察和控制其环境，不管使用哪个平台和应用程序，也不管在哪个地理位置运营。

Microsoft 已[收购](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/)了 Movere，后者不再作为单独的产品/服务进行销售。 可以通过 Microsoft 解决方案评估和 Microsoft 云经济计划获取 Movere。 [详细了解](https://www.movere.io) Movere。

此外，我们建议考虑 Azure Migrate - 内置的迁移服务。 Azure Migrate 提供了一个集中化中心来简化云迁移。 该中心为工作负荷（例如物理和虚拟服务器、数据库及应用程序）提供全面的支持。 可以借助端到端的可见性轻松跟踪整个发现、评估和迁移过程的进度。

Azure Migrate 内置了 Azure 与合作伙伴的 ISV 工具，可提供丰富的功能，包括：

- 发现虚拟服务器和物理服务器。
- 基于性能的大小调整。
- 成本规划。
- 基于导入的评估。
- 对无代理应用程序进行依赖关系分析。

如果在入门的过程中需要专家帮助，请联系 Microsoft 训练有素的 [Azure 专家托管服务提供商](https://azure.microsoft.com/partners)来获取相关指导。 请查看 [Azure Migrate 网站](https://azure.microsoft.com/services/azure-migrate/)。

## <a name="azure-migrate-versions"></a>Azure Migrate 版本

有两个版本的 Azure Migrate 服务。

- **当前版本**：使用此版本创建项目，发现本地服务器，并安排评估和迁移。 [详细了解](whats-new.md)此版本中的新增功能。
- **以前版本**：以前的 Azure Migrate 版本（亦称为 Azure Migrate 经典版）只支持评估 VMware 上运行的本地服务器。 Azure Migrate 经典版将于 2024 年 2 月停用。 2024 年 2 月之后，Azure Migrate 经典版将不再受支持，并且经典版项目中的库存元数据也将删除。 无法将以前版本中的项目或组件升级到新版本。 需要[创建新的项目](create-manage-projects.md)并向其[添加评估和迁移工具](./create-manage-projects.md)。 学习教程，了解如何使用可用的评估和迁移工具。 如果已将 Log Analytics 工作区附加到了经典版项目，则可以在删除经典版项目后将其附加到当前版本的项目。

    若要在 Azure 门户中访问现有项目，请搜索并选择“Azure Migrate”。 “Azure Migrate”仪表板有一个通知，还有一个用于访问旧项目的链接。

## <a name="next-steps"></a>后续步骤

- 尝试根据我们的教程评估 [VMware VM](./tutorial-discover-vmware.md)、[Hyper-V VM](./tutorial-discover-hyper-v.md) 或[物理服务器](./tutorial-discover-physical.md)。
- [查看常见问题解答](resources-faq.md)（关于 Azure Migrate）。