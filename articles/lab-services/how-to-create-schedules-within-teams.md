---
title: 在 Teams 内创建 Azure 实验室服务计划
description: 了解如何在 Teams 中创建实验室服务计划。
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92042331"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>在 Teams 中创建和管理实验室服务计划

借助日程安排，可以配置课堂实验室，这样实验室中的 VM 就能在指定时间自动启动和关闭。 可以定义一次性日程安排，也可以定义定期日程安排。 下面的过程逐步介绍了如何创建和管理课堂实验室日程安排： 

下面说明计划如何影响实验室虚拟机： 

- 计划中未包含模板虚拟机。 
- 仅启动已分配的虚拟机。 这意味着，如果最终用户（学生）没有认领计算机，则计算机不会在计划的时间启动。 
- 所有虚拟机（无论是否由用户认领）都会按照实验室计划停止。 

> [!IMPORTANT]
> VM 的计划运行时间不计入分配给用户的配额。 配额是指学生在 VM 上花费的计划外时间。 

用户可以在 Teams 中创建、编辑和删除实验室计划，就像在[实验室网站](https://labs.azure.com)中一样。 请参阅有关[创建和管理计划](how-to-create-schedules-within-teams.md)的文章。

## <a name="automatic-shutdown-and-disconnect-settings"></a>自动关闭和断开连接设置

你可以启用多项“自动关闭”成本控制功能，以前摄性方式防止在虚拟机未被主动使用时产生额外成本。 组合使用以下三个自动关闭和断开连接功能可捕获用户意外使虚拟机运行的大多数情况：
 
- 自动断开用户与 OS 认为空闲的虚拟机的连接。
- 用户断开连接时自动关闭虚拟机。
- 自动关闭已启动但用户未连接的虚拟机。

如需更多详细信息，请参阅有关如何[配置实验室的自动关闭设置](how-to-enable-shutdown-disconnect.md)的文章。

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

- [在 Teams 中使用 Azure 实验室服务概述](lab-services-within-teams-overview.md)
- [开始在 Teams 中创建实验室](how-to-get-started-create-lab-within-teams.md)
- [在 Teams 中管理实验室用户列表](how-to-manage-user-lists-within-teams.md)
- [在 Teams 中管理实验室的 VM 池](how-to-manage-vm-pool-within-teams.md)
- [在 Teams 中访问 VM -“学生”视图](how-to-access-vm-for-students-within-teams.md)
