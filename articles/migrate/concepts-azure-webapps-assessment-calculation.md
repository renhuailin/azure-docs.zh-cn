---
title: Azure Migrate 发现和评估工具中的 Azure 应用服务评估
description: 了解 Azure Migrate 发现和评估工具中的 Azure 应用服务评估
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: ec83b7a8de449a73dc227941f864ac7cd8495e68
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778227"
---
# <a name="assessment-overview-migrate-to-azure-app-service"></a>评估概述（迁移到 Azure 应用服务）

本文概述使用 [Azure Migrate: 发现和评估工具](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)将本地 ASP.NET Web 应用从 VMware 环境迁移到 Azure 应用服务的评估。

## <a name="whats-an-assessment"></a>什么是评估？
使用发现和评估工具进行评估，是指数据的时间点快照，衡量就绪情况，并提供了将本地服务器、数据库和 Web 应用托管到 Azure 的成本细节。

## <a name="types-of-assessments"></a>评估的类型

通过使用“Azure Migrate: 发现和评估”工具，可以创建四种类型的评估。

**评估类型** | **详细信息**
--- | ---
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 <br/><br/> 使用这种评估类型，可以对 [VMware](how-to-set-up-appliance-vmware.md) 和 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 环境中的本地服务器以及要迁移到 Azure VM 的[物理服务器](how-to-set-up-appliance-physical.md)进行评估。
**Azure SQL** | 将本地 SQL Server 从 VMware 环境迁移到 Azure SQL 数据库或 Azure SQL 托管实例的评估。
**Azure 应用服务** | 评估将运行在 IIS Web 服务器上的本地 ASP.NET Web 应用从 VMware 环境迁移到 Azure 应用服务。
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。 [了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

Azure 应用服务评估提供了一种调整大小标准：

**调整大小标准** | **详细信息** | **数据**
--- | --- | ---
基于配置 | 基于收集的配置数据提出建议的评估 | Azure 应用服务评估只考虑配置数据进行评估计算。 不收集 Web 应用的性能数据。

## <a name="how-do-i-assess-my-on-premises-aspnet-web-apps"></a>如何评估我的本地 ASP.NET Web 应用？

可以使用轻型 Azure Migrate 设备收集的配置数据来评估本地 Web 应用。 该设备发现本地 Web 应用程序并将配置数据发送到 Azure Migrate。 [了解详细信息](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>如何使用设备进行评估？

如果部署 Azure Migrate 设备来发现本地服务器，请执行以下步骤：

1. 设置 Azure 及适合于 Azure Migrate 运行的本地环境。
2. 对于第一次评估，请创建 Azure Migrate 项目。 “Azure Migrate: 发现和评估”工具会默认添加到该项目。
3. 部署轻型 Azure Migrate 设备。 此设备将持续发现本地服务器，并向 Azure Migrate 发送配置和性能数据。 将设备部署为 VM 或物理服务器。 无需在要评估的服务器上安装任何内容。

设备开始发现后，可以将想要评估的服务器（托管 Web 应用）归集到一个组中，然后对评估类型为“Azure 应用服务”的组运行评估。

按照我们的教程评估 [ASP.NET Web 应用](tutorial-assess-webapps.md)，尝试执行这些步骤。

## <a name="what-properties-are-used-to-customize-the-assessment"></a>使用哪些属性来自定义评估？

下面是 Azure 应用服务评估属性包含的内容：

**属性** | **详细信息**
--- | ---
**目标位置** | 要迁移到的 Azure 区域。 Azure 应用服务配置和成本建议基于你指定的位置。
需要隔离 | 如果希望你的 Web 应用在 Azure 数据中心的私有和专用环境中运行，请使用 Dv2 系列 VM，它具有更快的处理器、SSD 存储，并且内存与内核比是标准计划的两倍。
**预留实例** | 指定预留实例，以便在评估的成本估算中将它们考虑在内。<br/><br/> 如果选择了预留实例选项，则不能指定“折扣(%)”。
**产品/服务** | 注册的 [Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 评估会估计该产品/服务的费用。
**货币** | 帐户的计费货币。
**折扣 (%)** | 基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。
**EA 订阅** | 指定将将企业协议 (EA) 订阅用于成本估算。 考虑适用于该订阅的折扣。 <br/><br/> 将预留实例、折扣 (%) 和 VM 运行时间属性保留默认设置。

关于使用 Azure Migrate 创建评估，请[查看最佳做法](best-practices-assessment.md)。

## <a name="calculate-readiness"></a>计算就绪情况

### <a name="azure-app-service-readiness"></a>Azure 应用服务就绪情况

针对 Web 应用的 Azure 应用服务就绪情况基于 Web 应用的本地配置与 Azure 应用服务之间的功能兼容性检查：

1. Azure 应用服务评估会将 Web 应用配置数据视为兼容性问题。
1. 如果未找到兼容性问题，则会将目标部署类型的就绪情况标记为“就绪”。
1. 如果存在不严重的兼容性问题，如不会阻止迁移到特定目标部署类型的降级或不受支持的功能，则就绪情况标记为“就绪(有条件)”（超链接），其中包含“警告”详细信息和建议的修正指导。
1. 如果存在可能会阻止迁移到特定目标部署类型的任何兼容性问题，则就绪情况标记为“未就绪”，其中包含“问题”详细信息和建议的修正指导。
1. 如果发现仍在进行中或 Web 应用存在任何发现问题，则就绪情况标记为“未知”，因为评估无法计算该 Web 应用的就绪情况。

## <a name="calculate-sizing"></a>计算调整大小

### <a name="azure-app-service-sku"></a>Azure 应用服务 SKU

评估基于配置数据确定就绪情况后，它会确定适用于在 Azure 应用服务中运行应用的 Azure 应用服务 SKU。
高级计划适用于生产工作负载，并在专用虚拟机实例上运行。 每个实例均可支持多个应用程序和域。 独立计划在私有专用 Azure 环境中托管应用，对于需要与本地网络安全连接的应用而言，这是理想选择。

> [!NOTE]
> 目前 Azure Migrate 仅建议 I1、P1v2 和 P1v3 SKU。 Azure 应用服务中提供了更多 SKU。 [了解详细信息](https://azure.microsoft.com/pricing/details/app-service/windows/)。

### <a name="azure-app-service-plan"></a>Azure 应用服务计划

在应用服务中，应用始终在[应用服务计划](/azure/app-service/overview-hosting-plans)中运行。 应用服务计划为要运行的 Web 应用定义一组计算资源。 综合来看，计划/SKU 如下表所示。

需要隔离 | 预留实例 | 应用服务计划/SKU
--- | --- | ---
是  | 是 | I1
是  | 否  | I1
否  | 是 | P1v3
否  | 否 | P1v2

### <a name="azure-app-service-cost-details"></a>Azure 应用服务成本详细信息

[应用服务计划](/azure/app-service/overview-hosting-plans)会根据所用的计算资源量产生[费用](https://azure.microsoft.com/pricing/details/app-service/windows/)。 在应用服务中，按应用服务计划而不是 Web 应用付费。 可将一个或多个应用配置为在相同的计算资源中（或相同的应用服务计划中）运行。 放入此应用服务计划的任何应用都在应用服务计划定义的这些计算资源中运行。
为了优化成本，Azure Migrate 评估将多个 Web 应用分配给每个建议的应用服务计划。 分配给每个计划实例的 Web 应用的数量如下表所示。

**应用服务计划** | 每个应用服务计划的 Web 应用数
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

> [!NOTE]
> 随时可以提高和降低应用服务计划。 [了解详细信息](/azure/app-service/overview-hosting-plans#what-if-my-app-needs-more-capabilities-or-features/)。

## <a name="next-steps"></a>后续步骤
- [查阅](best-practices-assessment.md)关于创建评估的最佳做法。 
- 了解如何运行 [Azure 应用服务评估](how-to-create-azure-app-service-assessment.md)。