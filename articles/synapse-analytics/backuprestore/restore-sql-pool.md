---
title: 还原现有专用 SQL 池
description: 还原现有专用 SQL 池的操作指南。
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332035"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>还原现有专用 SQL 池

本文介绍如何使用 Azure 门户和 Synapse Studio 在 Azure Synapse Analytics 中还原现有的专用 SQL 池。 本文适用于还原和异地还原。 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>通过 Synapse Studio 还原现有的专用 SQL 池

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 导航到 Synapse 工作区。 
3. 在入门-> 打开 "Synapse Studio" 下，选择 " **打开** "。

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. 在左侧导航窗格中，选择 " **数据** "。
5. 选择 " **管理池** "。 
6. 选择 " **+ 新建** " 以创建新的专用 SQL 池。 
7. 在 "其他设置" 选项卡中，选择要从中还原的还原点。 

    如果要执行异地还原，请选择要恢复的工作区和专用 SQL 池。 

8. 选择“自动还原点”或“用户定义的还原点”。  

    ![还原点](../media/sql-pools/restore-point.PNG)

    如果专用 SQL 池没有任何自动还原点，请在还原前等待几个小时或创建用户定义的还原点。 对于用户定义的还原点，请选择一个现有的，或者创建一个新的。

    如果要还原异地备份，只需选择位于源区域中的工作区，然后选择要还原的专用 SQL 池即可。 

9. 选择“查看 + 创建”。

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>通过 Azure 门户还原现有专用 SQL 池

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 导航到要从中还原的专用 SQL 池。
3. 在“概览”边栏选项卡顶部，选择“还原”。

    ![ 还原概述](../media/sql-pools/restore-sqlpool-01.png)

4. 选择“自动还原点”或“用户定义的还原点”。  

    如果专用 SQL 池没有任何自动还原点，请在还原前等待几个小时或创建用户定义的还原点。 

    如果要执行异地还原，请选择要恢复的工作区和专用 SQL 池。 

5. 选择“查看 + 创建”。

## <a name="next-steps"></a>后续步骤

- [创建还原点](sqlpool-create-restore-point.md)
