---
title: 管理警报和智能组状态
description: 管理警报和智能组实例的状态
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: cac926c3c56c7f58e5237b820e0328999ffe2c52
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033565"
---
# <a name="manage-alert-and-smart-group-states"></a>管理警报和智能组状态

Azure Monitor 中的警报现在具有[警报状态和监视条件](./alerts-overview.md)，与此类似，智能组具有[智能组状态](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json)。 现在，在与相应警报或智能组相关联的历史记录中捕获对状态的更改。 本文将指导完成更改状态（包括警报和智能组）的过程。

## <a name="change-the-state-of-an-alert"></a>更改警报状态

1. 可按以下不同方式更改警报的状态： 
    * 在“所有警报”页，单击要更改其状态的警报旁边的复选框，然后单击“更改”状态。   
    ![屏幕截图显示了“所有警报”页，其中的“更改状态”处于选中状态。](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * 可在特定警报实例的“警报详细信息”页中单击“更改状态”   
    ![屏幕截图显示了“警报详细信息”页，其中的“更改警报状态”处于选中状态。](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * 在特定警报实例的“警报详细信息”页的“智能组”窗格中，可单击要更改其状态的警报旁边的复选框，然后单击“更改状态”    
    ![屏幕截图显示了检测信号警报的“警报详细信息”页，其中一些实例带有复选标记。](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * 在“智能组详细信息”页的成员警报列表中，可以单击要更改其状态的警报旁边的复选框，然后单击“更改状态”。   
    ![屏幕截图显示了“智能组详细信息”页，你可以在其中选择要更改其状态的警报。](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. 单击“更改状态”后，随即打开弹出窗口，可在其中选择状态（“新建/已确认/已关闭”）并在必要时输入注释。   
![屏幕截图显示了“详细信息更改警报”对话框。](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. 完成此操作后，状态更改将记录在相应警报的历史记录中。 打开相应的“详细信息”页面并查看历史记录部分即可查看此记录。    
![屏幕截图显示了状态更改的历史记录。](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>更改智能组的状态
1. 可按以下不同方式更改智能组的状态：
    1. 在“智能组”列表页中，可单击要更改其状态的智能组旁边的复选框，然后单击“更改状态”  
    ![屏幕截图显示了智能组的“更改状态”页。](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. 可在“智能组详细信息”页中单击“更改状态”        
    ![屏幕截图显示了已选定“更改智能组状态”的“智能组详细信息”页。](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. 单击“更改状态”后，随即打开弹出窗口，可在其中选择状态（“新建/已确认/已关闭”）并在必要时输入注释。 
![屏幕截图显示了智能组的“更改状态”对话框。](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  更改智能组状态不会更改各个成员警报的状态。

1. 完成此操作后，状态更改将记录在相应智能组的历史记录中。 打开相应的“详细信息”页面并查看历史记录部分即可查看此记录。     
![屏幕截图显示了智能组的更改历史记录。](./media/alerts-managing-alert-states/state-sg-history.jpg)