---
title: BareMetal 上的 Oracle 的高可用性和灾难恢复
description: 了解 Azure BareMetal 基础结构上的 Oracle 的高可用性和灾难恢复。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 04/15/2021
ms.openlocfilehash: 3b23774604b5126321d85892647e73118e08260f
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110575608"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>BareMetal 上的 Oracle 的高可用性和灾难恢复

本文介绍高可用性和灾难恢复的基础知识。 随后我们将介绍如何在 BareMetal 基础结构上的 Oracle 环境中实现高可用性和灾难恢复。

## <a name="high-availability-vs-disaster-recovery"></a>高可用性与灾难恢复

高可用性和灾难恢复都可提供覆盖，但针对不同类型的故障。 它们使用 Oracle Database 的不同功能和选项。

高可用性使系统可以克服多个故障，而不会影响应用程序的用户体验。 高度可用系统的常见特征包括：

- 无单一故障点的冗余硬件。
- 从非关键故障（如磁盘驱动器故障或网络电缆故障）中自动恢复。
- 能够回滚硬件和软件更改，而不会对处理产生任何显著影响。
- 满足或超过恢复时间目标 (RTO) 和恢复点目标 (RPO) 这些目标。

Oracle 中用于高可用性的最常见功能是 [Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92)。

灾难恢复可防止无法恢复的局部故障，这会损害主要高可用性策略。 在 Oracle 生态系统中，通过数据库复制（也称为 [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590)）来提供该功能。

## <a name="next-steps"></a>后续步骤

详细了解 Oracle 的高可用性功能：

> [!div class="nextstepaction"]
> [BareMetal 基础结构上的 Oracle 的高可用性功能](high-availability-features.md)
