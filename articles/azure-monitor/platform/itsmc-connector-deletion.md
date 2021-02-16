---
title: 删除 ITSM 连接器及其关联的操作
description: 本文提供了有关如何删除 ITSM 连接器以及与其关联的操作组的说明。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 1fbaa48d104642984e604bc66a3aa914fbfed44c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530915"
---
# <a name="deletion-of-unused-itsm-connectors"></a>删除未使用的 ITSM 连接器

删除未使用的连接器的过程包含2个阶段：

1. 删除关联的操作：应删除与 ITSM 连接器关联的所有操作。 应按照顺序执行此操作，而不会有可能会导致订阅中的错误的连接器操作。

2. 删除未使用的 ITSM 连接器。

## <a name="deletion-of-the-associated-actions"></a>删除关联的操作

1. 为了查找操作组，你应该进入监视器选择的 "监视器"  ![ 屏幕截图。](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. 选择  ![ 警报选择的 "警报" 屏幕截图。](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. 选择 "管理操作"  ![ 屏幕截图以选择 "管理操作"。](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. 选择连接到  ![ 连接到 Cherwell 的 ITSM 连接器的 Cherwell 屏幕截图的所有 ITSM 连接器。](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. 删除操作组删除操作组  ![ 屏幕截图。](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>删除未使用的 ITSM 连接器

1. 应在搜索的顶部搜索栏屏幕截图中搜索并选择 "ServiceDesk"，  ![ 并选择 "ServiceDesk"。](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. 选择 "ITSM 连接"，并选择  ![ CHERWELL ITSM 连接器的 Cherwell 连接器屏幕截图。](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. 选择  ![ ITSM 连接器删除的 "删除" 屏幕截图。](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>后续步骤

* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)
