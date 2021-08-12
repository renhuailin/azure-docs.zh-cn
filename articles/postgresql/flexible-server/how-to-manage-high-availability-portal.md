---
title: 管理区域冗余高可用性 - Azure 门户 - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 灵活服务器中启用或禁用区域冗余高可用性。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/07/2021
ms.openlocfilehash: 5a7b8c2f76abc9dd41894280e7cf610d0cae85ea
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111554854"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>在灵活服务器中管理区域冗余高可用性

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本文介绍在灵活服务器中如何启用或禁用区域冗余高可用性配置。

高可用性功能预配在不同区域中物理分隔主副本和备用副本。 有关详细信息，请参阅[高可用性概念文档](./concepts-high-availability.md)。 可选择在灵活服务器创建时或创建后启用高可用性。 本页提供了如何启用或禁用高可用性的指南。 此操作不会更改其他设置，包括 VNET 配置、防火墙设置和备份保留。 同样，启用和禁用高可用性是一个联机操作，并且不会影响应用程序的连接和操作。

## <a name="pre-requisites"></a>先决条件

只有在支持多个区域的区域中才提供区域冗余高可用性。 

## <a name="enable-high-availability-during-server-creation"></a>在服务器创建过程中启用高可用性

本部分详细介绍了 HA 相关的字段。 创建灵活服务器时，可以按照以下步骤部署高可用性。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择“灵活服务器”，然后单击“创建”。  有关如何填写“订阅”、“资源组”、“服务器名称”、“区域”和其他字段等详细信息的详细信息，请参阅服务器创建的操作方法文档   。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="查看订阅和区域":::

2.  选择你的可用性区域。 如果希望将应用程序与数据库并置在相同的可用性区域中以缩短延迟，这非常有用。 如果希望灵活服务器可部署在任意可用性区域上，请选择“无首选项”。
    ![AZ 选择]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="可用性区域选择":::  

3.  在“可用性”选项中，单击“区域冗余高可用性”复选框。

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="“高可用性”复选框":::

4.  如果要更改默认计算和存储，请单击“配置服务器”。
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="配置服务器 - 计算+存储":::  

5.  如果选中“高可用性”选项，将无法选择可突发层。 可选择“常规用途”或“内存优化”计算层 。 然后，可从下拉列表中选择所选的计算大小。

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="计算层选择":::  


6.  使用滑动条选择“存储大小”（单位为 GiB），然后选择 7 天到 35 天之间的“备份保持期” 。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="存储备份"::: 

7. 单击“保存”  。 

## <a name="enable-high-availability-post-server-creation"></a>在服务器创建后启用高可用性

按照以下步骤，为现有的灵活服务器启用高可用性。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择现有的 PostgreSQL 灵活服务器。

2.  在“灵活服务器”页面上，单击左面板中的“高可用性”以打开“高可用性”页面。
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="左面板选择"::: 

3.  单击“区域冗余高可用性”复选框以启用该选项，然后单击“保存”以保存更改  。

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="启用高可用性"::: 

4.  此时将显示一个确认对话框，指出在启用高可用性后，由于额外的服务器和存储部署，你的成本将增加。

5.  单击“启用 HA”按钮以启用高可用性。

6.  此时将显示一条通知，指出高可用性部署正在进行中。

## <a name="disable-high-availability"></a>禁用高可用性

按照以下步骤，为配置有区域冗余的灵活服务器禁用高可用性。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择现有的 Azure Database for PostgreSQL 灵活服务器。

2.  在“灵活服务器”页面上，单击前面板中的“高可用性”以打开“高可用性”页面。
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="左面板选择"::: 

3.  单击“区域冗余高可用性”复选框以禁用该选项 。 然后，单击“保存”以保存更改。

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="禁用高可用性"::: 

4.  此时将显示一个确认对话框，你可以在其中确认禁用高可用性。

5.  单击“禁用 HA”按钮，以禁用高可用性。

6.  此时将显示一条通知，指出正在解除高可用性部署。

## <a name="forced-failover"></a>强制故障转移

按照以下步骤，强制将主灵活服务器故障转移到备用灵活服务器。 这样会使主服务器立即停机，并触发故障转移至备用服务器。 这适用于测试工作负载的计划外停机故障转移时间等情况。

1.  在 [Azure 门户](https://portal.azure.com/)中，选择已启用高可用性功能的现有灵活服务器。
2.  在“灵活服务器”页面上，单击前面板中的“高可用性”以打开“高可用性”页面。
3.  检查“主可用性区域”和“备用可用性区域”
4.  单击“强制故障转移”以启动手动故障转移过程。 在故障转移完成之前，弹出窗口会通知潜在故障时间。 阅读消息，并单击“确定”。
5.  系统将显示一条通知，指出正在进行故障转移。
6.  在故障转移到备用服务器后，系统将弹出一条通知。
7.  检查新的“主可用性区域”和“备用可用性区域”。
    
    :::image type="content" source="./media/how-to-manage-high-availability-portal/ha-forced-failover.png" alt-text="按需强制故障转移"::: 

>[!IMPORTANT] 
> * 请不要一个紧接一个地执行故障转移。 在两次故障转移之间等待至少 15-20 分钟，这样还有助于充分构建新备用服务器。
>
> * 门户上报告的整个端到端操作时间可能比应用程序的实际故障时间长。 请从应用程序的角度衡量故障时间。 

## <a name="planned-failover"></a>计划的故障转移

按照以下步骤，执行从主服务器到备用灵活服务器的计划的故障转移。 此操作需先准备备用服务器再执行故障转移。 这样可最大程度地减少故障时间，因为在某些情况（比如在故障转移事件后，要将主副本移回到首选的可用性区域）下，此操作可将故障正常转移到备用服务器。
1.  在 [Azure 门户](https://portal.azure.com/)中，选择已启用高可用性功能的现有灵活服务器。
2.  在“灵活服务器”页面上，单击前面板中的“高可用性”以打开“高可用性”页面。
3.  检查“主可用性区域”和“备用可用性区域”
4.  单击“计划的故障转移”以启动手动故障转移过程。 弹出窗口会通知相关进程。 阅读消息，并单击“确定”。
5.  系统将显示一条通知，指出正在进行故障转移。
6.  在故障转移到备用服务器后，系统将弹出一条通知。
7.  检查新的“主可用性区域”和“备用可用性区域”。
        :::image type="content" source="./media/how-to-manage-high-availability-portal/ha-planned-failover.png" alt-text="按需计划的故障转移"::: 

>[!IMPORTANT] 
>
> * 请不要一个紧接一个地执行故障转移。 在两次故障转移之间等待至少 15-20 分钟，这样还有助于充分构建新备用服务器。
>
> * 建议在少量活动期间执行计划的故障转移。
>
> * 整个端到端操作时间可能比应用程序的实际故障时间长。 请从应用程序的角度衡量故障时间。


## <a name="next-steps"></a>后续步骤

-   了解[业务连续性](./concepts-business-continuity.md)
-   了解[区域冗余高可用性](./concepts-high-availability.md)
