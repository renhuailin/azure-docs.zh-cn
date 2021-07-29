---
title: 使用标记管理 Azure NetApp 文件计费 | Microsoft Docs
description: 介绍如何使用标记来管理 Azure NetApp 文件计费。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/06/2021
ms.author: b-juche
ms.openlocfilehash: b0efd8c580b0d4f1ae94d4dfc655bb351e084a67
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2021
ms.locfileid: "109480848"
---
# <a name="manage-billing-by-using-capacity-pool-billing-tags"></a>使用容量池计费标记来管理计费

标记是可用于对资源进行分类和查看合并计费的名称和值对。 可将同一标记应用于多个资源和资源组。  有关标记的详细信息，请参阅[使用标记来组织 Azure 资源和管理层次结构](../azure-resource-manager/management/tag-resources.md)。  

标记可以帮助你管理 Azure NetApp 文件计费和开支。 例如，你的公司可能只有一个 Azure 订阅，但有多个部门使用 Azure 资源并产生开支。 可以在容量池级别使用部门名称来标记资源。 相应的标记将显示在帐单中，以帮助你查看每个部门产生的开支。   

计费标记是在容量池级别而不是卷级别分配的。

## <a name="steps"></a>步骤

1. 若要在容量池中添加或编辑标记，请转到该容量池并选择“标记” 。   

2. 填写“名称”和“值”对 。  单击“应用”。

    > [!IMPORTANT] 
    > 标记数据将全局复制。 因此，请不要使用可能损害资源安全性的标记名称或值。 例如，不要使用包含个人信息或敏感信息的标记名称。 

      ![显示容量池的“标记”窗口的快照。](../media/azure-netapp-files/billing-tags-capacity-pool.png)

3. 可以使用 [Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)门户显示和下载有关标记的资源的信息： 
    1. 单击“成本分析”，然后选择“按资源列出的成本”视图 。    
      [ ![显示 Azure 成本管理的“成本分析”的屏幕截图](../media/azure-netapp-files/cost-analysis.png) ](../media/azure-netapp-files/cost-analysis.png#lightbox)  

    2. 若要下载发票，请选择“发票”，然后选择“下载”按钮 。   
      [ ![显示 Azure 成本管理的“发票”的屏幕截图](../media/azure-netapp-files/azure-cost-invoices.png) ](../media/azure-netapp-files/azure-cost-invoices.png#lightbox)  

    1. 在出现的“下载”窗口中，下载使用情况详细信息。 下载的 `csv` 文件包含相应资源的容量池计费标记。   
       ![显示 Azure 成本管理的“下载”窗口的快照。](../media/azure-netapp-files/invoice-download.png)   

       [ ![显示已下载的电子表格的屏幕截图。](../media/azure-netapp-files/spreadsheet-download.png) ](../media/azure-netapp-files/spreadsheet-download.png#lightbox)

## <a name="next-steps"></a>后续步骤

[Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md) 