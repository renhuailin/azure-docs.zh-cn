---
title: 删除未使用的 ITSM 连接器
description: 本文提供了有关如何删除 ITSM 连接器及其关联操作组的说明。
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387932"
---
# <a name="delete-unused-itsm-connectors"></a>删除未使用的 ITSM 连接器

删除未使用的 IT 服务管理 (ITSM) 连接器的过程分为两个阶段。 删除与 ITSM 连接器关联的所有操作，然后删除连接器本身。 首先删除操作，因为没有连接器的操作可能会导致订阅中出现错误。

## <a name="delete-associated-actions"></a>删除关联的操作

1. 在 Azure 门户中选择“监视”。
  
    ![“监视器”选择屏幕截图。](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. 选择“**警报**”。
   
    ![“警报”选择屏幕截图。](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. 选择“管理操作”。
   
    ![“管理操作”选择屏幕截图。](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. 选择与要删除的 ITSM 连接器关联的操作组。 本文使用 Cherwell 连接器的示例。
   
    ![与 Cherwell 连接器关联的操作的屏幕截图。](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. 查看信息，然后选择“删除操作组”。

    ![操作组信息和用于删除组的按钮的屏幕截图。](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>删除连接器

1. 在搜索栏中搜索“servicedesk”。 然后在资源列表中选择“ServiceDesk”。

    ![搜索和选择 ServiceDesk 的屏幕截图。](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. 选择“ITSM 连接”，然后选择 Cherwell 连接器。

    ![Cherwell ITSM 连接器的屏幕截图。](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. 选择“删除” 。

    ![ITSM 连接器的删除按钮的屏幕截图。](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>后续步骤

* [排查 ITSM 连接器中的问题](./itsmc-resync-servicenow.md)
