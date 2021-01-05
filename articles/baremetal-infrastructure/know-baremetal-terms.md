---
title: 了解 Azure BareMetal 基础结构的条款
description: 了解 Azure BareMetal 基础结构的条款。
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829104"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>了解 BareMetal 基础结构的条款

在本文中，我们将介绍一些重要的 BareMetal 术语。

- **修订**： BareMetal 实例标记有两个不同的标记修订版本。 每个版本在体系结构和邻近性上都不同于 Azure 虚拟主机：
    - **修订版 3** (Rev 3) ：是原始设计。
    - **修订版 4** (Rev 4) ：是一种新的设计，提供与 azure 虚拟机 (VM) 主机之间更近的距离，并降低 azure Vm 和 BareMetal 实例单位之间的延迟。 
    - **修订版 4.2** (Rev 4.2) ：是使用现有 Rev 4 体系结构的最新更名 BareMetal 基础结构。 可以通过 Azure 门户访问和管理 BareMetal 实例。  

- **Stamp**：定义 BareMetal 实例的 Microsoft 内部部署大小。 在部署实例单元之前，必须在数据中心位置部署由计算、网络和存储机架组成的 BareMetal 实例标记。 此类部署称为 BareMetal 实例标记或从修订版本4.2。

- **租户**：在 BareMetal 实例标记中部署的客户隔离到 *租户中。* 租户在网络、存储和计算层中相互隔离。 分配给不同租户的存储和计算单元在 BareMetal 实例的标记级别上无法相互查看或相互通信。 客户可以选择部署到不同的租户中。 即使这样，BareMetal 实例的标记级别上的租户之间也不会进行通信。

## <a name="next-steps"></a>后续步骤
了解如何通过 [Azure 门户](workloads/sap/baremetal-infrastructure-portal.md)确定 BareMetal 实例单位并与其交互。


 