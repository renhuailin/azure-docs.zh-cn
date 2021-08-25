---
title: 蓝图示例的索引
description: 用于通过 Azure 蓝图部署环境、策略和云采用框架基础的符合性和标准示例的索引。
ms.date: 08/17/2021
ms.topic: sample
ms.openlocfilehash: 22c6e1382fdfbd7cafb423e99239fe2480d3b088
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322127"
---
# <a name="azure-blueprints-samples"></a>Azure 蓝图示例

下表包含 Azure 蓝图示例的链接。 每个示例都可投入实际生产运行，并且现在已可供部署，以便帮助满足你的各种符合性需求。

## <a name="standards-based-blueprint-samples"></a>基于标准的蓝图示例

| 示例 | 说明 |
|---------|---------|
| [澳大利亚政府 ISM PROTECTED](./ism-protected/index.md) | 提供可帮助满足澳大利亚政府 ISM PROTECTED 的规范措施。 |
| [Azure 安全基准](./azure-security-benchmark.md) | 提供可帮助符合 [Azure 安全基准](../../../security/benchmarks/overview.md)的规范措施。 |
| [Azure 安全基准基础](./azure-security-benchmark-foundation/index.md) | 部署和配置 Azure 安全基准基础。 |
| [加拿大联邦 PBMM](./canada-federal-pbmm.md) | 提供防护措施，以便符合加拿大联邦受保护的 B、中等完整性、中等可用性 (PBMM)。 |
| [CIS Microsoft Azure 基础基准检验 v1.3.0](./cis-azure-1-3-0.md) | 提供一组策略以帮助符合 CIS Microsoft Azure 基础基准 v1.3.0 建议。 |
| [CIS Microsoft Azure 基础基准检验 v1.1.0](./cis-azure-1-1-0.md) | 提供一组策略以帮助符合 CIS Microsoft Azure 基础基准 v1.1.0 建议。 |
| [CMMC 级别 3](./cmmc-l3.md) | 提供可帮助符合 CMMC 级别 3 的规范措施。 |
| [DoD 影响级别 4](./dod-impact-level-4/index.md) | 提供了一组策略以帮助符合 DoD 影响级别 4。 |
| [DoD 影响级别 5](./dod-impact-level-5/index.md) | 提供了一组策略以帮助符合 DoD 影响级别 5。 |
| [FedRAMP Moderate](./fedramp-m/index.md) | 提供了一组策略以帮助符合 FedRAMP Moderate。 |
| [FedRAMP High](./fedramp-h/index.md) | 提供了一组策略以帮助符合 FedRAMP High。 |
| [HIPAA HITRUST 9.2](./hipaa-hitrust-9-2.md) | 提供了一组有助于符合 HIPAA HITRUST 的策略。 |
| [IRS 1075 2016 年 9 月](./irs-1075-sept2016.md) | 提供可帮助符合 IRS 1075 的规范措施。|
| [ISO 27001](./iso-27001-2013.md) | 提供可帮助符合 ISO 27001 的规范措施。 |
| [ISO 27001 共享服务](./iso27001-shared/index.md) | 提供了一组符合标准的基础结构模式和策略防护措施，以便帮助通过 ISO 27001 认证。 |
| [ISO 27001 应用服务环境/SQL 数据库工作负荷](./iso27001-ase-sql-workload/index.md) | 为 [ISO 27001 共享服务](./iso27001-shared/index.md)蓝图示例提供了更多基础结构。 |
| [介质](./media/index.md) | 提供了一组策略以帮助符合媒体 MPAA。 |
| [受限于新西兰 ISM](./new-zealand-ism.md) | 分配策略以处理“新西兰信息安全手册”的具体控制。 |
| [NIST SP 800-53 R4](./nist-sp-800-53-r4.md) | 提供可帮助符合 NIST SP 800-53 R4 的规范措施。 |
| [NIST SP 800-171 R2](./nist-sp-800-171-r2.md) | 提供可帮助符合 NIST SP 800-171 R2 的规范措施。 |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | 提供一组策略以帮助用户符合 PCI-DSS v3.2.1。 |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | 帮助用户符合 SWIFT CSP-CSCF v2020。 |
| [英国官方和英国 NHS 监管](./ukofficial-uknhs.md) | 提供了一组符合标准的基础结构模式和策略防护措施，以便帮助用户通过英国官方和英国 NHS 认证。 |
| [CAF 基础](./caf-foundation/index.md) | 提供了一组控制措施，以便帮助你管理云资产，使其与[适用于 Azure 的 Microsoft 云采用框架 (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index) 相符合。 |
| [CAF 迁移登陆区域](./caf-migrate-landing-zone/index.md) | 提供了一组控制措施，以便帮助你安排迁移你的第一个工作负荷并管理你的云资产，使其与[适用于 Azure 的 Microsoft 云采用框架 (CAF)](/azure/architecture/cloud-adoption/migrate/index) 相符合。 |

## <a name="samples-strategy"></a>示例策略

:::image type="complex" source="../media/blueprint-samples-strategy.png" alt-text="一个图表，描述蓝图示例在体系结构复杂性和符合性要求坐标系中的位置。" border="false":::
   描述一个坐标系，其中体系结构复杂性位于 X 轴，符合性要求位于 Y 轴。 随着体系结构复杂性和合规性要求的增加，请采用区域 E 中指定的门户中的标准蓝图示例。对于刚开始使用 Azure 的客户，请使用基于云采用框架 (CAF) 的基础和由区域 A 和 B 指定的登陆区域蓝图。剩下的空间归因于由区域 C、D 和 F 的合作伙伴客户创建的自定义蓝图。:::image-end:::

CAF 基础和 CAF 迁移登陆区域蓝图假定客户正在准备一个现有的干净单一订阅，以便将本地资产和工作负荷迁移到 Azure。
（图中的区域 A 和 B）。

可以基于示例蓝图进行迭代，并查找客户正在应用的自定义模式。 此外，还可以主动处理特定于行业（如金融服务和电子商务）的蓝图（区域 B 的顶端）。 同样，我们设想针对复杂的架构考虑因素（如多个订阅、高可用性、跨区域资源以及对现有订阅和资源实施控制的客户）构建蓝图（区域 C 和 D）。

有一些示例蓝图可满足符合性要求较高、体系结构复杂性较高的客户场景需求（图中的区域 E）。 图中的区域 F 表示将由客户和合作伙伴处理的一种示例蓝图，客户和合作伙伴将应用这些示例蓝图并根据自己的独特需求对每个蓝图进行自定义。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。
