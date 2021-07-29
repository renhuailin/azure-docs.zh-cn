---
title: 使用 Azure Migrate 构建迁移计划
description: 提供有关使用 Azure Migrate 构建迁移计划的指南。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 1c2be47060004d464003c00cbbddb3b58a136e3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871140"
---
# <a name="build-migration-plan-with-azure-migrate"></a>使用 Azure Migrate 构建迁移计划

按照本文操作，使用 [Azure Migrate](migrate-services-overview.md) 构建到 Azure 的迁移计划。 

## <a name="define-cloud-migration-goals"></a>定义云迁移目标

在开始之前，了解并评估迁移到云的[动机](/azure/cloud-adoption-framework/strategy/motivations)有助于产生成功的业务结果。 如[云采用框架](/azure/cloud-adoption-framework)中所述，触发因素和结果有许多种。   

**业务事件** | **迁移结果**
--- | ---
数据中心退出 | Cost 
合并、收购或资产剥离 | 降低供应商/技术复杂性
降低资本支出 | 优化内部操作
终止对任务关键型技术的支持 | 提高业务敏捷性
应对法规遵从性变化 | 为新的技术能力做准备
新数据主权要求 | 扩大规模以满足市场需求
减少中断，提高 IT 稳定性 | 扩大规模以满足地域需求

明确动机有助于确定迁移的战略目标。 下一步是确定和规划适合工作负载的迁移路径。 [Azure Migrate：发现和评估](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)工具可帮助评估本地工作负载，提供相关指导和工具来帮助你进行迁移。

## <a name="understand-your-digital-estate"></a>了解数字资产

首先确定本地基础结构、应用程序和依赖项。 这可以帮助你确定要迁移到 Azure 的工作负载，并收集优化成本预测。 发现和评估工具可帮助你识别你使用的工作负载、工作负载之间的依赖关系以及工作负载优化。

### <a name="workloads-in-use"></a>正在使用中的工作负载

Azure Migrate 使用轻型 Azure Migrate 设备来执行本地 VMware VM、Hyper-V VM、其他虚拟化服务器和物理服务器的无代理发现。 持续发现将收集服务器配置信息、性能元数据以及应用程序数据。 以下是设备从本地服务器收集的内容： 

- 服务器、磁盘和 NIC 元数据。

- 已安装的应用程序、角色和功能。

- 性能数据，包括 CPU 和内存利用率、磁盘 IOPS 和吞吐量。

收集数据后，可以导出应用程序清单列表，以查找在服务器上运行的应用程序和 SQL Server 实例。 可以使用 Azure Migrate: 数据库评估工具了解 SQL Server 就绪情况。

 ![门户上的应用程序清单](./media/concepts-migration-planning/application-inventory-portal.png)

 ![导出应用程序清单](./media/concepts-migration-planning/application-inventory-export.png)

除了使用发现和评估工具发现的数据外，还可以使用配置管理数据库 (CMDB) 数据来构建服务器和数据库资产视图，并了解服务器在业务单元、应用程序所有者、地理位置等中的分布情况。这有助于决定优先迁移哪些工作负载。 

### <a name="dependencies-between-workloads"></a>工作负载之间的依赖关系

完成服务器发现后，可以[分析依赖关系](concepts-dependency-visualization.md)，以直观显示和确定跨服务器依赖项，以及将相互依赖的服务器移动到 Azure 的优化策略。 可视化有助于了解某些服务器是否正在使用，或者是否可以将其取消，而不是迁移。  分析依赖关系有助于确保不会遗留任何内容，并在迁移过程中意外中断。 完成应用程序清单和依赖项分析后，可以创建高可信度的服务器组，并开始对其进行评估。

 ![依赖项映射](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>优化和大小调整

Azure 可以随时间灵活调整云容量，迁移提供了优化分配给服务器的 CPU 和内存资源的机会。 在具有标识的服务器上创建评估有助于了解工作负载性能历史记录。 这对于正确调整 Azure VM SKU 大小和 Azure 中的磁盘推荐至关重要。

## <a name="assess-migration-readiness"></a>评估迁移就绪情况


### <a name="readinesssuitability-analysis"></a>就绪/适用性分析

可以导出评估报告，并按这些类别进行筛选，以了解 Azure 就绪性：

- **Azure 就绪：** 服务器可按原样迁移到 Azure，无需任何更改。 
- **Azure 就绪（但有条件）：** 服务器可迁移到 Azure，但需根据评估中提供的修正指导稍作更改。
- **Azure 未就绪：** 服务器无法按原样迁移到 Azure。 在迁移之前，必须根据修正指南解决问题。 
- **就绪情况未知：** 由于元数据不足，Azure Migrate 无法确定服务器就绪情况。

使用数据库评估，可以评估要迁移到 Azure SQL 数据库或 Azure SQL 托管实例的 SQL Server 数据资产的就绪性。 评估显示每个 SQL Server 实例的迁移就绪状态百分比。 此外，对于每个实例，你可以在 Azure 中看到推荐的目标、可能的迁移阻止程序、中断性变更计数、Azure SQL DB 或 Azure SQL VM 的就绪情况以及兼容性级别。 可以更深入地了解迁移阻止程序的影响，以及修复它们的建议。

 ![数据库评估](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>大小建议

将服务器标记为“Azure 就绪”后，发现和评估将为服务器提供标识 Azure VM SKU 和磁盘类型的大小建议。 可以基于性能历史记录（用于在迁移时优化资源）获取大小建议，或根据本地服务器设置来获取大小建议，而无需性能历史记录。 在数据库评估中，可以查看数据库 SKU、定价层和计算级别的建议。  

### <a name="get-compute-costs"></a>获取计算成本

Azure Migrate 评估中基于性能的大小调整选项可帮助你正确调整 VM 大小，并应将其用作优化 Azure 工作负载的最佳做法。 除了正确调整大小以外，还有其他几个选项可帮助节省 Azure 成本： 

- 预留实例：使用[预留实例 (RI)](https://azure.microsoft.com/pricing/reserved-vm-instances/)，与[即用即付定价](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)相比，可显著降低成本。
- Azure 混合权益：通过 [Azure 混合权益](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)，可以将具有有效软件保障的本地 Windows Server 许可证或 Linux 订阅引入到 Azure，并与预留实例选项结合。
- 企业协议：Azure [企业协议 (EA)](../cost-management-billing/manage/ea-portal-agreements.md) 可节省 Azure 订阅和服务的成本。
- 产品/服务：有多个 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 例如，[即用即付开发/测试](https://azure.microsoft.com/pricing/dev-test/)或 [Enterprise 开发/测试提议](https://azure.microsoft.com/offers/ms-azr-0148p/)，为开发/测试 VM 提供较低的费率
- VM 运行时间：可以查看 Azure VM 每月运行的天数和每天运行的小时数。 关闭不使用的服务器可以降低成本（不适用 RI）。
- 目标区域：可以在不同区域中创建评估，以确定迁移到特定区域是否可能更具成本效益。 

### <a name="visualize-data"></a>可视化数据

你可在门户中查看发现和评估报告（包括 Azure 就绪情况信息和每月成本分布）。 还可以导出评估，并通过其他可视化效果来丰富迁移计划。 可以使用不同的属性组合创建多个评估，并选择最适合你的业务的一组属性。  

 ![“评估”概述](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>评估缺口/阻止程序

当你确定要迁移的应用和工作负载时，请确定这些应用和工作负载的停机时间限制，并查找应用与底层基础结构之间的所有操作依赖关系。 此分析有助于规划满足恢复时间目标 (RTO) 的迁移，并确保最小化到零数据丢失。 在迁移之前，建议查看并缓解任何兼容性问题或不受支持的功能，这可能会阻止服务器/SQL 数据库迁移。 Azure Migrate 发现和评估报告及 Azure Migrate 数据库评估可以帮助解决此情况。 

### <a name="prioritize-workloads"></a>设置工作负载优先级

收集清单信息后，可以确定首先迁移哪些应用和工作负载。 开发“应用和学习”方法，以系统和可控的方式迁移应用，这样你就可以在开始全面迁移之前消除任何障碍。

若要确定迁移顺序的优先级，可以使用一些战略因素，如复杂性、迁移时间、业务紧迫性、生产/非生产性注意事项、符合性、安全要求、应用程序知识等。 

以下是一些建议：

- 确定速效方案优先级：使用评估报告来确定容易实现的目标，包括完全就绪、并且可以毫不费力地迁移到 Azure 的服务器和数据库。 下表汇总了执行此操作的几种方法。

    **State** | **操作**
    --- | ---
    Azure 就绪 VM | 导出评估报告，并筛选出所有状态为“Azure 就绪”的服务器。 这可能是使用 [Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具直接迁移到 Azure 的第一组服务器。
    终止支持的操作系统 | 导出评估报告，并筛选所有运行 Windows Server 2008 R2/Windows Server 2008 的服务器。 这些操作系统的支持即将终止，只有将它们迁移到 Azure，Azure 才会提供为期三年的安全更新。 如果将 Azure 混合权益结合起来并使用 RI，则可能可以节省更多费用。
    SQL Server 迁移 | 使用数据库评估建议，利用 Azure Migrate: 数据库迁移工具迁移可用于 Azure SQL 数据库的数据库。 使用 Azure Migrate: 服务器迁移工具迁移可用于 Azure SQL VM 的数据库。
    终止支持的软件 | 导出应用程序清单，并筛选支持即将终结的任何软件/扩展。 确定要迁移的这些应用程序的优先级。
    **预配过低的服务器** | 导出评估报告，筛选 CPU 使用率 (%) 和内存使用率 (%) 较低的服务器。  迁移到适当大小的 Azure VM，为未充分利用的资源节省成本。
    **预配过高的服务器** | 导出评估报告，筛选 CPU 使用率 (%) 和内存使用率 (%) 较高的服务器。  解决容量限制，防止服务器因负载过重而中断，并通过将这些服务器迁移到 Azure 来提高性能。 在 Azure 中，使用自动缩放功能来满足需求。<br/><br/> 分析评估报告，调查存储限制。 分析磁盘 IOPS 和吞吐量以及建议的磁盘类型。

- 由小到大：首先迁移风险和复杂性最小的应用和工作负载，以建立对迁移策略的信心。 分析 Azure Migrate 评估建议和 CMDB 存储库，查找和迁移可能是试验性迁移候选项的开发/测试工作负载。 开始迁移生产工作负载时，从试验迁移中获得的反馈和经验会很有帮助。  
- 符合：就产品广度和深度而言，Azure 保持着业界最大的合规组合。 使用符合性要求来确定迁移优先级，使应用和工作负载符合你所在国家、地区和行业特定的标准和法规。 对于处理业务关键型流程、持有敏感信息或处于严格监管行业的组织来说，尤其如此。 在这些类型的组织中，标准和法规比比皆是，并且可能经常变化，很难跟上。  

## <a name="finalize-the-migration-plan"></a>完成迁移计划

在完成迁移计划之前，请确保考虑并缓解其他可能的阻止程序，如下所示： 

- 网络要求：评估网络带宽和延迟限制，这可能导致迁移复制速度出现不可预见的延迟和中断。
- 测试/迁移后调整：允许时间缓冲区对已迁移的应用执行性能和用户验收测试，或配置/调整迁移后应用，例如更新数据库连接字符串、配置 Web 服务器、执行转换/清理等。
- 权限：检查建议的 Azure 权限，以及迁移所需的服务器/数据库访问角色和权限。
- 培训：为组织的数字化转型做好准备。 坚实的培训基础对于成功的组织变革至关重要。 在 [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF) 上查看免费培训，包括 Azure 基础知识、解决方案体系结构和安全性课程。 鼓励团队探索  [Azure 认证](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)。  
- 实现支持：如果需要，可以获取实现支持。 许多组织选择外部帮助来支持云迁移。 若要借助个性化协助快速、自信地迁移到 Azure，请考虑使用  [Azure 专家托管服务提供程序](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)或  [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF)。  


创建有效的云迁移计划，其中包括有关要迁移的应用息、应用/数据库可用性、停机时间限制以及迁移里程碑的详细信。 该计划考虑数据复制所需的时间，并包含用于迁移后测试的实际缓冲区和转换活动。 

迁移后测试计划应包括功能、集成、安全和性能测试及用例，以确保已迁移的应用按预期方式工作，并且所有数据库对象和数据关系都成功转移到云中。  

制定迁移路线图，并声明维护时段来迁移应用程序和数据库，将停机时间降至最低，并限制迁移期间潜在的运营和业务影响。  

## <a name="migrate"></a>Migrate

建议在开始全面迁移之前，先在 Azure Migrate 中运行测试迁移。 测试迁移有助于估计所涉及的时间，并调整迁移计划。 它提供了一个机会来发现任何潜在问题，并在全面迁移之前修复它们。

如果已准备好进行迁移，请使用“Azure Migrate：服务器迁移”工具和 Azure 数据迁移服务 (DMS)，以获得具有端到端跟踪的无缝集成迁移体验。

- 利用服务器迁移工具，你可以迁移本地 VM 和服务器或其他私有或公有云（包括 AWS、GCP）中的 VM，并且故障时间几乎为零。
- Azure DMS 是一项完全托管服务，旨在实现从多个数据库源到 Azure 数据平台的无缝迁移，并最大限度地缩短故障时间。  

## <a name="next-steps"></a>后续步骤

- 在 Azure 云采用框架中查看 [云迁移旅程](/azure/architecture/cloud-adoption/getting-started/migrate) 。
- 查看对 Azure Migrate 的[简要概述](migrate-services-overview.md)，观看[入门视频](https://youtu.be/wFfq3YPxYHE)。
- 详细了解如何评估 VM 能否迁移到 [Azure VM](concepts-assessment-calculation.md)。
