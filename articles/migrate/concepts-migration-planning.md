---
title: 使用 Azure Migrate 生成迁移计划
description: 提供有关使用 Azure Migrate 生成迁移计划的指南。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: ef916e0e8b32c96382a731d4a307e2b2a98ba1ea
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753852"
---
# <a name="build-migration-plan-with-azure-migrate"></a>使用 Azure Migrate 构建迁移计划

按照本文中的步骤操作，在 Azure 中构建 [Azure Migrate](migrate-services-overview.md)的迁移计划。 

## <a name="define-cloud-migration-goals"></a>定义云迁移目标

在开始之前，了解并评估迁移到云的 [动机](/azure/cloud-adoption-framework/strategy/motivations) 可能会导致成功的业务成果。 如 [云采用框架](/azure/cloud-adoption-framework)中所述，有许多触发器和结果。   

**业务事件** | **迁移结果**
--- | ---
数据中心退出 | 节约成本 
合并、收购或 divestiture | 降低供应商/技术复杂性
资本支出降低 | 优化内部操作
终止对任务关键型技术的支持 | 提高业务灵活性
响应法规遵从性更改 | 为新的技术能力做准备
新数据主权要求 | 根据市场需求进行缩放
中断降低和 IT 稳定性的改进 | 根据地理需求进行缩放

识别动机有助于固定战略迁移目标。 下一步是确定和规划为工作负荷定制的迁移路径。 [Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具可帮助你评估本地工作负荷，并提供指导和工具来帮助你进行迁移。

## <a name="understand-your-digital-estate"></a>了解你的数字场地

首先确定本地基础结构、应用程序和依赖项。 这可以帮助你确定要迁移到 Azure 的工作负荷，并收集优化的成本预测。 服务器评估工具可帮助你识别你使用的工作负荷、工作负载之间的依赖关系以及工作负荷优化。

### <a name="workloads-in-use"></a>工作负载正在使用中

Azure Migrate 使用轻型 Azure Migrate 设备来执行本地 VMware Vm、Hyper-v Vm、其他虚拟机和物理服务器的无代理发现。 持续发现将收集计算机配置信息、性能元数据以及应用程序数据。 以下是设备从本地计算机收集的内容： 

- 计算机、磁盘和 NIC 元数据。

- 已安装的应用程序、角色和功能。

- 性能数据，包括 CPU 和内存利用率、磁盘 IOPS 和吞吐量。

收集数据后，可以导出应用程序清单列表以查找应用，并 SQL Server 计算机上运行的实例。 您可以使用 Azure Migrate：数据库评估工具了解 SQL Server 准备情况。

 ![门户上的应用程序清单](./media/concepts-migration-planning/application-inventory-portal.png)

 ![应用程序清单导出](./media/concepts-migration-planning/application-inventory-export.png)

除了使用服务器评估工具发现的数据，您还可以使用您的配置管理数据库 (CMDB) 数据来构建您的服务器和数据库领域的视图，并了解服务器如何跨业务单元、应用程序所有者、地理位置等进行分布。这有助于确定要为迁移划分优先级的工作负荷。 

### <a name="dependencies-between-workloads"></a>工作负载之间的依赖关系

服务器发现后，可以 [分析依赖关系](concepts-dependency-visualization.md)，直观显示和确定跨服务器依赖项，以及将相互依赖的服务器移动到 Azure 的优化策略。 可视化效果有助于了解某些计算机是否正在使用，或者是否可以将其取消，而不是迁移。  分析依赖关系有助于确保不会遗留任何内容，并在迁移过程中意外中断。 完成应用程序清单和依赖项分析后，可以创建高可信度的服务器组，并开始对其进行评估。

 ![依赖项映射](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>优化和大小调整

Azure 可灵活地调整云容量的时间，并使你能够优化分配给服务器的 CPU 和内存资源。 在具有标识的服务器上创建评估有助于了解工作负荷性能历史记录。 这对于正确调整 Azure VM Sku 和 Azure 中的磁盘建议非常重要。

## <a name="assess-migration-readiness"></a>评估迁移就绪情况


### <a name="readinesssuitability-analysis"></a>就绪/适用性分析

可以导出评估报告，并按这些类别进行筛选，以了解 Azure 准备情况：

- **适用于 azure**：计算机可按原样迁移到 Azure，无需任何更改。 
- **面向 azure 的有条件准备**：计算机可以迁移到 azure，但需要进行少量更改，并遵循评估中提供的更正指导。
- **未准备好进行 azure**：无法按原样将计算机迁移到 azure。 在迁移之前，必须根据更正指南解决问题。 
- **准备情况未知**：由于元数据不足，Azure Migrate 无法确定计算机准备情况。

使用数据库评估，可以评估要迁移到 Azure SQL 数据库或 Azure SQL 托管实例的 SQL Server 数据空间。 评估显示每个 SQL server 实例的迁移准备状态百分比。 此外，对于每个实例，你可以在 Azure 中看到推荐的目标、可能的迁移阻止程序、重大更改计数、Azure SQL DB 或 Azure SQL VM 的准备情况以及兼容性级别。 你可以更深入地了解迁移阻止程序的影响，以及修复它们的建议。

 ![数据库评估](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>大小建议

将计算机标记为 "就绪" 后，"服务器评估" 将为计算机提供标识 Azure VM SKU 和磁盘类型的大小调整建议。 你可以基于性能历史记录获取大小建议 (在迁移) 时优化资源，或根据本地计算机设置优化资源，而无需性能历史记录。 在数据库评估中，可以查看数据库 SKU、定价层和计算级别的建议。  

### <a name="get-compute-costs"></a>获取计算成本

Azure Migrate 评估中基于性能的大小调整选项可帮助你使用大小合适的 Vm，并应将其用作优化 Azure 中的工作负荷的最佳实践。 除了正确调整大小以外，还有其他几个选项可帮助节省 Azure 成本： 

- **预订实例**：对于 [ (RI) 的预订实例](https://azure.microsoft.com/pricing/reserved-vm-instances/)，与即 [用即付定价](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)相比，可显著降低成本。
- **Azure 混合权益**：通过 [Azure 混合权益](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)，你可以将具有有效软件保障的本地 Windows Server 许可证或 Linux 订阅引入到 Azure，并与保留实例选项结合。
- **企业协议**： Azure [企业协议 (EA)](../cost-management-billing/manage/ea-portal-agreements.md) 可提供 azure 订阅和服务的节省。
- **产品/服务**：有多个 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 例如， [即用即付开发/测试](https://azure.microsoft.com/pricing/dev-test/)或 [Enterprise 开发/测试提议](https://azure.microsoft.com/offers/ms-azr-0148p/)，为开发/测试 vm 提供较低的费率
- **VM 运行时间**：可以查看每月的天数和 Azure vm 的每日运行时间。 如果计算机未处于使用状态，则无法将其关闭， (不适用于 RIs) 。
- **目标区域**：可以在不同区域中创建评估，以确定迁移到特定区域是否可能更具成本效益。 

### <a name="visualize-data"></a>可视化数据

你可以在门户中查看服务器评估报表 (Azure 准备情况信息和每月成本分布) 。 还可以导出评估，并通过其他可视化功能丰富迁移计划。 你可以使用不同的属性组合创建多个评估，并选择最适合你的企业的一组属性。  

 ![“评估”概述](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>评估缺口/阻止程序

当你确定要迁移的应用和工作负荷时，请确定这些应用和工作负荷的停机限制，并查找你的应用与底层基础结构之间的所有操作依赖关系。 此分析有助于规划满足恢复时间目标 (RTO) 的迁移，并确保最小到零数据丢失。 在迁移之前，建议你查看并缓解任何兼容性问题或不受支持的功能，这可能会阻止服务器/SQL 数据库迁移。 Azure Migrate Server 评估报表和 Azure Migrate 数据库评估可以帮助解决此情况。 

### <a name="prioritize-workloads"></a>确定工作负荷的优先级

收集了清单信息后，可以确定首先迁移哪些应用和工作负荷。 开发以系统且可控的方式迁移应用的 "应用并了解" 方法，使你可以在开始完全规模的迁移之前消除任何瑕疵。

若要确定迁移顺序的优先级，可以使用战略因素，如复杂性、时间迁移、业务紧急性、生产/非生产性注意事项、符合性、安全要求、应用程序知识等。 

以下是一些建议：

- **确定快速 wins 的优先级**：使用评估报告来确定低挂起的水果，包括完全准备就绪的服务器和数据库，并要求最大程度地迁移到 Azure。 下表汇总了执行此操作的几种方法。

    **状态** | **操作**
    --- | ---
    **Azure 就绪 Vm** | 导出评估报告，并筛选出 *适用于 Azure* 的状态为 "就绪" 的所有计算机。 这可能是使用 [Azure Migrate： Server 迁移](migrate-services-overview.md#azure-migrate-server-migration-tool) 工具提升并转移到 Azure 的第一组计算机。
    **支持终止的操作系统** | 导出评估报告，并筛选运行 Windows Server 2008 R2/Windows Server 2008 的所有计算机。 这些操作系统在支持结束时，只有在将 Azure 迁移到 Azure 时，Azure 才提供为期三年的安全更新。 如果你将 Azure 混合权益结合起来并使用 RIs，则节省的费用可能会高得多。
    **SQL Server 迁移** | 使用数据库评估建议，使用 Azure Migrate：数据库迁移工具迁移已可用于 Azure SQL 数据库的数据库。 使用 Azure Migrate：服务器迁移工具迁移适用于 Azure SQL VM 的数据库。
    **支持终止软件** | 导出应用程序清单，并筛选可能会到达支持终止的任何软件/扩展。 确定要迁移的这些应用程序的优先级。
    **预配的计算机** | 导出评估报告，并筛选 CPU 使用率低的计算机 (% ) 和内存使用率 (% ) 。  迁移到适当大小的 Azure VM，为未充分利用的资源节省成本。
    **过度预配的计算机** | 导出评估报告并筛选 CPU 使用率高 (% ) 和内存利用率 (% ) 的计算机。  解决容量限制，防止 overstrained 计算机中断，并通过将这些计算机迁移到 Azure 来提高性能。 在 Azure 中，使用自动缩放功能满足需求。<br/><br/> 分析评估报告，调查存储约束。 分析磁盘 IOPS 和吞吐量以及建议的磁盘类型。

- 从小 **开始，然后去大**：通过移动应用和工作负载来启动，从而降低风险和复杂性，为你的迁移策略构建信心。 分析 Azure Migrate 评估建议和 CMDB 存储库，查找和迁移可能是试验性迁移的候选开发/测试工作负荷。 当你开始迁移生产工作负荷时，试验迁移的反馈和知识会很有帮助。  
- **符合** 性： Azure 在产品/服务的广度和深度方面维持了业界最大的合规性组合。 使用符合性要求来确定迁移的优先级，使应用和工作负荷符合你的国家、地区和行业特定的标准和法律。 对于处理关键业务流程、保存敏感信息，或在大规模管控行业中的组织来说，这种情况尤其如此。 在这些类型的组织中，标准和法规 abound，并可能经常发生变化，因此很难跟上。  

## <a name="finalize-the-migration-plan"></a>完成迁移计划

在完成迁移计划之前，请确保考虑并缓解其他可能的拦截程序，如下所示： 

- **网络要求**：评估网络带宽和延迟限制，这可能会导致迁移复制速度出现不可预见的延迟和中断。
- **测试/迁移后** 调整：允许时间缓冲区对已迁移的应用执行性能和用户验收测试，或配置/调整迁移后的应用，例如更新数据库连接字符串、配置 web 服务器、执行转移/清理等。
- **权限**：检查建议的 Azure 权限、服务器/数据库访问角色以及迁移所需的权限。
- **培训**：为你的组织准备数字转换。 坚实的培训基础对于成功的组织变化非常重要。 查看 [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF)上的免费培训，包括有关 Azure 基础、解决方案体系结构和安全性的课程。 鼓励您的团队探索 [Azure 认证](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF)。  
- **实现支持**：如果需要，可以获取实现支持。 许多组织选择外部帮助来支持云迁移。 若要使用个性化协助快速、自信地迁移到 Azure，请考虑使用 [Azure 专家托管服务提供商](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF)或 [FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF)。  


创建一个有效的云迁移计划，其中包括有关要迁移的应用的详细信息、应用/数据库可用性、停机时间限制以及迁移里程碑。 该计划考虑数据复制将采用的时间，并包含用于迁移后测试的实际缓冲区和切削活动。 

迁移后测试计划应包括功能、集成、安全和性能测试和用例，以确保已迁移的应用按预期方式工作，并且所有数据库对象和数据关系均已成功传输到云中。  

构建迁移路线图，并声明一个维护时段，将应用程序和数据库迁移到最少停机时间，并在迁移过程中限制潜在的运营和业务影响。  

## <a name="migrate"></a>迁移

建议在开始完全规模的迁移之前，在 Azure Migrate 中运行测试迁移。 测试迁移有助于估计所涉及的时间，并调整迁移计划。 它可让你发现任何潜在问题，并在完全迁移之前修复这些问题。

准备好进行迁移时，请使用 Azure Migrate：服务器迁移工具和 Azure 数据迁移服务 (DMS) ，以实现端到端跟踪，实现无缝集成的无缝迁移体验。

- 利用服务器迁移工具，你可以将本地 Vm 和服务器或其他私有或公有云中 (的 Vm （包括 AWS、GCP) ）迁移到大约零停机时间。
- Azure DMS 提供一项完全托管的服务，旨在实现从多个数据库源到 Azure 数据平台的无缝迁移，但停机时间最短。  

## <a name="next-steps"></a>后续步骤

- 在 Azure 云采用框架中调查 [云迁移旅程](/azure/architecture/cloud-adoption/getting-started/migrate)   。
- [快速](migrate-services-overview.md)了解 Azure Migrate，观看入门[视频](https://youtu.be/wFfq3YPxYHE)。
- 了解有关评估要迁移到 [Azure vm](concepts-assessment-calculation.md)的 vm 的详细信息。
