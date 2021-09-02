---
title: 确定所需的子网大小和范围
titleSuffix: Azure SQL Managed Instance
description: 本主题介绍如何计算将在其中部署 Azure SQL 托管实例的子网的大小。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova, srbozovi, wiassaf
ms.date: 06/14/2021
ms.openlocfilehash: e3c789ec59e66189753c8515235b2375aa20e5f1
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687372"
---
# <a name="determine-required-subnet-size-and-range-for-azure-sql-managed-instance"></a>确定 Azure SQL 托管实例所需的子网大小和范围
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 托管实例必须部署在 Azure [虚拟网络](../../virtual-network/virtual-networks-overview.md)中。 可以在虚拟网络子网中部署的托管实例数取决于子网的大小（子网范围）。

创建托管实例时，Azure 会根据预配期间选择的层，分配一定数量的虚拟机。 由于这些虚拟机与子网关联，因此它们需要 IP 地址。 为了确保日常操作和服务维护期间保持高可用性，Azure 可能会分配更多的虚拟机。 子网中所需的 IP 地址数于是大于该子网中的托管实例数。

根据设计，一个托管实例在一个子网中至少需要 32 个 IP 地址。 因此，在定义子网 IP 范围时，可以使用最小的子网掩码，即 /27。 建议为托管实例部署仔细规划子网大小。 在规划期间请考虑以下输入：

- 包含以下实例参数的托管实例的数量：
  - 服务层
  - 硬件代次
  - vCore 数目
  - [维护时段](../database/maintenance-window.md)
- 计划纵向扩展/缩减或更改服务层级

> [!IMPORTANT]
> 具有 16 个 IP 地址（子网掩码 /28）的子网大小允许在其中部署单个托管实例。 该实例应该只用于评估，或者用于不执行缩放操作的开发/测试方案。 

## <a name="determine-subnet-size"></a>确定子网大小

根据将来的实例部署和缩放需求调整子网大小。 可借助以下参数进行计算：

- Azure 使用子网中的 5 个 IP 地址来满足自身的需要。
- 每个虚拟群集分配额外数量的地址。 
- 每个托管实例使用一定数量的地址，具体取决于定价层和硬件代系。
- 每个缩放请求临时分配更多地址数。

> [!IMPORTANT]
> 如果子网中存在任何资源，则无法更改子网地址范围。 也无法将托管实例从一个子网移动到另一个子网。 考虑使用更大的子网，而不使用较小的子网，以防止将来出现问题。

GP = 常规用途；BC = 业务关键；VC = 虚拟群集

| **硬件代次** | **定价层** | **Azure 使用情况** | **VC 使用情况** | **实例使用情况** | **总计** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

在上表中：

- “总计”列显示子网中部署的单个实例使用的地址总数。 
- 在子网中添加更多实例时，该实例使用的地址数会增加。 地址总数因而也会增加。 例如，再添加一个 Gen4 GP 托管实例会将“实例使用情况”值增大至 10，并已用地址的“总计”值增大至 16 。 
- “Azure 使用情况”列中提供的地址在多个虚拟群集之间共享。  
- “VC 使用情况”列中提供的地址在对应虚拟群集中的实例之间共享。

确定子网大小时，还应考虑[维护时段功能](../database/maintenance-window.md)，尤其是在同一子网中部署多个实例时。 在托管实例创建期间或之后为其指定一个维护时段，则意味着必须将其放置在具有相应维护时段的虚拟群集中。 如果子网中没有此类虚拟群集，则必须先创建一个新群集来容纳该实例。

执行更新操作时，通常需要[调整虚拟群集的大小](management-operations-overview.md)。 当出现新的创建或更新请求时，SQL 托管实例服务会与计算平台通信，请求需要添加的新节点。 部署系统会根据计算响应扩展现有虚拟群集或创建新的虚拟群集。 即使操作大多都在同一虚拟群集中完成，也可能会在计算端创建新的虚拟群集。 


## <a name="update-scenarios"></a>更新方案

在执行缩放操作期间，实例临时需要额外的 IP 容量，该容量具体取决于定价层和硬件代系：

| **硬件代次** | **定价层** | **方案** | **更多地址**  |
| --- | --- | --- | --- |
| Gen4<sup>1</sup> | GP 或 BC | 缩放 Vcore 数目 | 5 |
| Gen4<sup>1</sup> | GP 或 BC | 缩放存储 | 5 |
| Gen4 | GP 或 BC | 从 GP 切换到 BC 或从 BC 切换到 GP | 5 |
| Gen4 | GP | 切换到 Gen5 | 9 |
| Gen4 | BC | 切换到 Gen5 | 11 |
| Gen5 | GP | 缩放 Vcore 数目 | 3 |
| Gen5 | GP | 缩放存储 | 0 |
| Gen5 | GP | 正在切换到 BC | 5 |
| Gen5 | BC | 缩放 Vcore 数目 | 5 |
| Gen5 | BC | 缩放存储 | 5 |
| Gen5 | BC | 正在切换到 GP | 3 |

<sup>1</sup> Gen4 硬件正在逐步被淘汰，不能再用于新部署。 将硬件代系从 Gen4 更新为 Gen5 可以利用特定于 Gen5 的功能。
  
## <a name="calculate-the-number-of-ip-addresses"></a>计算 IP 地址数

建议使用以下公式来计算 IP 地址总数。 此公式考虑到了以后在处理创建请求或更新实例期间可能会创建新虚拟群集的情况。 它还考虑到了虚拟群集的维护时段要求。

公式：5 + (a * 12) + (b * 16) + (c * 16)

- a = GP 实例的数量
- b = BC 实例的数量
- c = 不同维护时段配置的数量

解释：
- 5 = Azure 保留的 IP 地址数
- 每个 GP 实例 12 个地址 = 6 个用于虚拟群集、3 个用于托管实例、3 个用于缩放操作
- 每个 BC 实例 16 个地址 = 6 个用于虚拟群集、5 个用于托管实例、5 个用于缩放操作
- 16 个地址作为备份 = 用于新建虚拟群集的情况

示例： 
- 你计划将三个常规用途实例和两个业务关键托管实例部署在同一个子网中。 所有实例都将配置相同的维护时段。 这意味着需要 5 + (3 * 12) + (2 * 16) + (1 * 16) = 89 个 IP 地址。 

  由于 IP 范围定义为 2 的 N 次方，因此子网要求该部署的最小 IP 范围为 128 (2^7) 个地址。 需要保留子网掩码为 /25 的子网。

> [!NOTE]
> 虽然可以在 IP 地址数小于子网计算公式输出值的子网中部署托管实例，但始终应考虑采用较大的子网。 使用较大的子网有助于避免以后由于 IP 地址不足而造成的问题，例如，无法在子网中创建更多实例，或无法缩放现有实例。 

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是 Azure SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 详细了解 [SQL 托管实例中的连接体系结构](connectivity-architecture-overview.md)。
- 了解如何[创建用于部署 SQL 托管实例的虚拟网络](virtual-network-subnet-create-arm-template.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](custom-dns-configure.md)。
