---
title: 删除 ITSM 连接器及其关联的操作
description: 本文提供了有关如何删除 ITSM 连接器及其关联操作组的说明。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: e73510b6c61c58f6f0b2b8067a240214ee35a46c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036480"
---
# <a name="deletion-of-unused-itsm-connectors"></a>删除未使用的 ITSM 连接器

删除未使用的连接器的过程包含 2 个阶段：

1. 删除关联的操作：应删除与 ITSM 连接器关联的所有操作。 这样做是为了避免在没有连接器的情况下执行可能会导致订阅错误的操作。

2. 删除未使用的 ITSM 连接器。

## <a name="deletion-of-the-associated-actions"></a>删除关联的操作

1. 若要查找操作组，应转到“监视器”![监视器选项的屏幕截图。](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. 选择“警报”![警报选项的屏幕截图。](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. 选择“管理操作”![“管理操作”选项的屏幕截图。](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. 选择所有连接到 Cherwell 的 ITSM 连接器![连接到 Cherwell 的 ITSM 连接器的屏幕截图。](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. 删除操作组![删除操作组的屏幕截图。](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>删除未使用的 ITSM 连接器

1. 应在顶部搜索栏中搜索并选择“ServiceDesk”LA ![搜索并选择“ServiceDesk”LA 的屏幕截图。](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. 选择“ITSM 连接”，然后选择 Cherwell 连接器![Cherwell ITSM 连接器的屏幕截图。](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. 选择“删除”![删除 ITSM 连接器的屏幕截图。](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>后续步骤

* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)
