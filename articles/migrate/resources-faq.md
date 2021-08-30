---
title: Azure Migrate 常见问题解答
description: 获取有关 Azure Migrate 服务常见问题的解答。
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 87dc84b7f73fe95f7ba62dcdef63816753cc233e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735031"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate：常见问题

本文回答了有关 Azure Migrate 的常见问题。 如果在阅读本文后有任何问题，请在 [Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)上发布问题。 你还可以查看下列文章：

- 有关 [Azure Migrate 设备](common-questions-appliance.md)的问题
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题

## <a name="what-is-azure-migrate"></a>什么是 Azure Migrate？

Azure Migrate 提供一个中心，用于跟踪本地应用、工作负载与私有云和公有云 VM 的发现、评估及其到 Azure 的迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方 ISV 产品。 [了解详细信息](migrate-services-overview.md)。

## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate 有什么用途？

使用 Azure Migrate 发现、评估本地基础结构、应用程序和数据并将其迁移到 Azure。 Azure Migrate 支持评估和迁移本地 VMware VM、Hyper-V VM、物理服务器、其他虚拟化 VM、数据库、Web 应用和虚拟桌面。

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate 与 Azure Site Recovery 的区别是什么？

[Azure Migrate](migrate-services-overview.md) 提供了一个集中式中心，用于评估以及迁移到 Azure。

- 借助 Azure Migrate 工具、其他 Azure 服务和第三方工具，可使用 Azure Migrate 提供互操作性和未来的扩展性。
- Azure Migrate 服务器迁移工具专门用于将服务器迁移到 Azure。 此方法针对迁移进行了优化。 不需要了解与迁移没有直接关联的概念和方案。
- 从为 VM 启动复制的 180 天内，不收取迁移的工具使用费用。 这为完成迁移提供了时间。 只需为复制中使用的存储和网络资源以及测试迁移期间使用的计算付费。
- Azure Migrate 支持 Site Recovery 支持的所有迁移方案。 此外，对于 VMware VM，Azure Migrate 提供无代理迁移选项。
- 我们仅优先考虑 Azure Migrate 服务器迁移工具的新迁移功能。 这些功能不针对 Site Recovery。

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) 应仅用于灾难恢复。

Azure Migrate 服务器迁移工具使用某些后端 Site Recovery 功能直接迁移某些本地计算机。

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>我的项目具有 Azure Migrate 以前的经典版体验。 如何开始使用新版本？

Azure Migrate 经典版将于 2024 年 2 月停用。 2024 年 2 月之后，Azure Migrate 经典版将不再受支持，并且经典版项目中的库存元数据也将删除。 无法将以前版本中的项目或组件升级到新版本。 需要[创建新的 Azure Migrate 项目](create-manage-projects.md)并向其[添加评估和迁移工具](./create-manage-projects.md)。 学习教程，了解如何使用可用的评估和迁移工具。 如果已将 Log Analytics 工作区附加到了经典版项目，则可以在删除经典版项目后将其附加到当前版本的项目。

## <a name="whats-the-difference-between-azure-migrate-discovery-and-assessment-and-the-map-toolkit"></a>Azure Migrate：发现和评估与 MAP 工具包之间的区别是什么？

服务器评估提供评估，可帮助进行迁移准备和评估要迁移到 Azure 的工作负载。 [Microsoft 评估和规划 (MAP) 工具包](https://www.microsoft.com/download/details.aspx?id=7826)可帮助完成其他任务，包括更新版本的 Windows 客户端和服务器操作系统的迁移规划以及软件使用情况跟踪。 对于这些情况，继续使用 MAP 工具包。

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>服务器评估与 Site Recovery 部署规划器的区别是什么？

服务器评估是一个迁移规划工具。 Site Recovery 部署规划器是灾难恢复规划工具。

根据要执行的操作选择工具：

- **规划从本地迁移到 Azure**：如果规划将本地服务器迁移到 Azure，请使用服务器评估进行迁移规划。 服务器评估评估本地工作负载，并提供指导和工具来帮助进行迁移。 完成迁移规划后，可以使用 Azure Migrate 服务器迁移等工具将计算机迁移到 Azure。
- **规划到 Azure 的灾难恢复**：如果规划使用 Site Recovery 将灾难恢复设置为从本地到 Azure，请使用 Site Recovery 部署规划器。 部署规划器提供针对本地环境的特定于 Site Recovery 的深入评估，以进行灾难恢复。 它提供与灾难恢复相关的建议，例如复制和故障转移。

## <a name="how-does-server-migration-work-with-site-recovery"></a>如何结合使用服务器迁移与 Site Recovery？

- 如果使用 Azure Migrate 服务器迁移来执行本地 VMware VM 的无代理迁移，则迁移本机于 Azure Migrate，且不使用 Site Recovery。
- 如果使用 Azure Migrate 服务器迁移来执行 VMware VM 的基于代理的迁移，或者如果迁移 Hyper-V VM 或物理服务器，则 Azure Migrate 服务器迁移将使用 Azure Site Recovery 复制引擎。

## <a name="which-geographies-are-supported"></a>支持哪些地理位置？

查看[公有云](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)支持的地理位置。

## <a name="how-do-i-get-started"></a>如何开始？

确定所需的工具，然后将该工具添加到 Azure Migrate 项目。

若要添加 ISV 工具或 Movere：

1. 根据工具策略获取许可证或注册免费试用版即可开始使用该工具。 ISV 工具的许可符合 ISV 或工具的许可模型。
2. 每个工具中都有一个用于连接到 Azure Migrate 的选项。 按照工具说明和文档操作，将该工具与 Azure Migrate 连接起来。

可以在 Azure Migrate 项目中跨 Azure 和其他工具跟踪迁移历程。

## <a name="how-do-i-delete-a-project"></a>如何删除项目？

了解如何[删除项目](how-to-delete-project.md)。

## <a name="next-steps"></a>后续步骤

阅读 [Azure Migrate 概述](migrate-services-overview.md)。