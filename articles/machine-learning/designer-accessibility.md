---
title: 使用设计器中的辅助功能
titleSuffix: Azure Machine Learning
description: 了解设计器中提供的键盘快捷方式和屏幕阅读器辅助功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.custom: designer
ms.openlocfilehash: 86cb5260a59f864658fbb7ac1c1da2d943c6253e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "90893417"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>通过键盘使用 Azure 机器学习设计器

了解如何通过键盘和屏幕阅读器来使用 Azure 机器学习设计器。 有关在 Azure 门户中随处可用的键盘快捷方式的列表，请参阅 [Azure 门户中的键盘快捷方式](../azure-portal/azure-portal-keyboard-shortcuts.md)

此工作流使用[讲述人](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator)和 [JAWS](https://www.freedomscientific.com/products/software/jaws/) 进行了测试，但它应该可以用于其他标准屏幕阅读器。

## <a name="navigate-the-pipeline-graph"></a>导航管道图

管道图以嵌套列表的形式进行组织。 外部列表是模块列表，它描述管道图中的所有模块。 内部列表是连接列表，它描述某特定模块的所有连接。  

1. 在模块列表中，请使用箭头键来切换模块。
1. 使用 Tab 键来打开目标模块的连接列表。
1. 使用箭头键在模块的连接端口之间切换。
1. 使用“G”键转到目标模块。

## <a name="edit-the-pipeline-graph"></a>编辑管道图

### <a name="add-a-module-to-the-graph"></a>将模块添加到该图

1. 使用 Ctrl + F6 将焦点从画布切换到模块树。
1. 使用标准树视图控件在模块树中查找所需模块。

### <a name="edit-a-module"></a>编辑模块

若要将一个模块连接到另一个模块，请执行以下操作：

1. 在以模块列表中的某个模块为目标时，使用 Ctrl + Shift + H 打开连接帮助程序。
1. 编辑该模块的连接端口。

若要调整模块属性，请执行以下操作：

1. 在以某个模块为目标时，使用 Ctrl + Shift + E 打开模块属性。
1. 编辑模块属性。

## <a name="navigation-shortcuts"></a>导航快捷方式

| 击键 | 描述 |
|-|-|
| Ctrl + F6 | 在画布和模块树之间切换焦点 |
| Ctrl + F1   | 在将焦点放在模块树中某个节点上时打开信息卡 |
| Ctrl + Shift + H | 在焦点位于某个节点上时打开连接帮助程序 |
| Ctrl + Shift + E | 当焦点位于某个节点上时打开模块属性 |
| Ctrl + G | 如果管道运行失败，则将焦点移到第一个失败的节点 |

## <a name="action-shortcuts"></a>操作快捷方式

请使用以下带有访问键的快捷方式。 有关访问键的详细信息，请参阅 https://en.wikipedia.org/wiki/Access_key 。

| 击键 | 操作 |
|-|-|
| 访问键 + R | 运行 |
| 访问键 + P | 发布 |
| 访问键 + C | 克隆 |
| 访问键 + D | 部署 |
| 访问键 + I | 创建/更新推理管道 |
| 访问键 + B | 创建/更新批推理管道 |
| 访问键 + K | 打开“创建推理管道”下拉列表 |
| 访问键 + U | 打开“更新推理管道”下拉列表 |
| 访问键 + M | 打开“更多(...)”下拉列表 |

## <a name="next-steps"></a>后续步骤

- [启用高对比度或更改主题](../azure-portal/set-preferences.md#choose-a-theme-or-enable-high-contrast)
- [Microsoft 的辅助功能相关工具](https://www.microsoft.com/accessibility)
