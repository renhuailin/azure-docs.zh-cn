---
title: 了解 Azure BareMetal 基础结构的条款
description: 了解 Azure BareMetal 基础结构的条款。
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: fd7a39854c86f728ef152f8e7d858157e1ad26f4
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861920"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>了解 BareMetal 基础结构的条款

在本文中，我们将介绍一些重要的 BareMetal 术语。

- **修订**：存在称为修订版 3 (Rev 3) 的原始标记修订，以及 BareMetal 实例标记的两个不同的戳记修订版本。 每个 stamp 在体系结构和邻近性上不同于 Azure 虚拟主机：
    - **修订版 4** (Rev 4) ：一种较新的设计，更接近 azure 虚拟机 (VM) 主机，并降低 azure Vm 和 BareMetal 实例单位之间的延迟。 
    - **修订版 4.2** (Rev 4.2) ：最新的更名 BareMetal 基础结构，使用现有的 Rev 4 体系结构。 修订版4更接近于 Azure 虚拟机 (VM) 主机。 它显著改进了在修订版4戳记或 rows 中部署的 Azure Vm 和 BareMetal 实例单位之间的网络延迟。 可以通过 Azure 门户访问和管理 BareMetal 实例。    

- **Stamp**：定义 BareMetal 实例的 Microsoft 内部部署大小。 在部署实例单元之前，必须在数据中心位置部署包含计算、网络和存储机架的 BareMetal 实例标记。 此类部署称为 BareMetal 实例标记或从修订版本4.2。

- **租户**：在 BareMetal 实例标记中部署的客户隔离到 *租户中。* 租户在网络、存储和计算层中相互隔离。 分配给不同租户的存储和计算单元在 BareMetal 实例的标记级别上无法相互查看或相互通信。 客户可以选择部署到不同的租户中。 即使这样，BareMetal 实例的标记级别上的租户之间也不会进行通信。

## <a name="next-steps"></a>后续步骤
了解有关 [BareMetal 基础结构](workloads/sap/baremetal-overview-architecture.md) 的详细信息，或者如何 [识别 BareMetal 实例单元并与之交互](workloads/sap/baremetal-infrastructure-portal.md)。 