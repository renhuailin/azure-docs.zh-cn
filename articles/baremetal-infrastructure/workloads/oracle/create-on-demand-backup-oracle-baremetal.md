---
title: 在 SnapCenter 中创建 Oracle Database 的按需备份
description: 了解如何在 Oracle BareMetal 基础结构上的 SnapCenter 中创建 Oracle Database 的按需备份。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/07/2021
ms.openlocfilehash: 699c070a3eeca2904f9620ca5f12c95c509210ab
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579149"
---
# <a name="create-on-demand-backup-of-your-oracle-database-in-snapcenter"></a>在 SnapCenter 中创建 Oracle Database 的按需备份

本文将逐步介绍如何在 SnapCenter 中创建 Oracle Database 的按需备份。 

[配置 SnapCenter](configure-snapcenter-oracle-baremetal.md) 后，数据文件、控制文件和存档日志会根据你在创建资源组时输入的计划继续备份。 然而，为了进行正常数据库保护，可能还需要按需备份。

## <a name="steps-to-create-an-on-demand-backup"></a>创建按需备份的步骤

1. 在左侧菜单中选择“资源”。 然后，在“视图”旁边的下拉菜单中，选择“资源组” 。 选择与要创建的按需备份对应的资源组名称。

2. 在右上角选择“立即备份”。

3. 验证资源组和保护策略是否适用于按需备份。 如果要验证此备份，请选中“备份后验证”复选框。 选择“备份”。

备份完成后，它将在“资源”下的备份列表中列出。 选择与备份的资源组关联的数据库。 此备份将按照创建保护策略时设置的按需保留策略进行保留。

## <a name="next-steps"></a>后续步骤

了解如何在 SnapCenter 中还原 Oracle Database：

> [!div class="nextstepaction"]
> [还原 Oracle Database](restore-oracle-database-baremetal.md)
