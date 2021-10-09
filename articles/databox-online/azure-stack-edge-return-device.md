---
title: Azure Stack Edge 设备退回
description: 了解如何擦除数据并退回 Azure Stack Edge 设备，然后删除与设备相关的资源。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/15/2021
ms.author: alkohli
ms.openlocfilehash: 3fa49fdab111c60c9f73dad5703c39dbc33e1472
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128554198"
---
# <a name="return-your-azure-stack-edge-device"></a>退回 Azure Stack Edge 设备

[!INCLUDE [applies-to-pro-fpga](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

本文介绍如何擦除数据，然后退回 Azure Stack Edge 设备。 退回设备后，还可以删除与设备关联的资源。

在本文中，学习如何：

> [!div class="checklist"]
>
> * 擦除设备上数据磁盘中的数据
> * 在 Azure 门户中开始设备退回
> * 打包设备并安排取件
> * 删除 Azure 门户中的资源

## <a name="erase-data-from-the-device"></a>清除设备上的数据

若要擦除设备上数据磁盘中的数据，需要重置设备。

重置之前，请根据需要创建设备上本地数据的副本。 可将设备中的数据复制到 Azure 存储容器。 

即使在设备重置之前，你也可以启动设备退回。

你可以在本地 Web UI 或 PowerShell 中重置设备。 有关 PowerShell 说明，请参阅[重置设备](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)。

[!INCLUDE [Reset data from the device](../../includes/azure-stack-edge-device-reset.md)]

> [!NOTE]
> - 如果要更换或升级为新设备，建议仅在收到新设备后才重置自己的设备。
> - 设备重置仅删除设备上的所有本地数据。 云中的数据不会被删除，并且会[收费](https://azure.microsoft.com/pricing/details/storage/)。 此数据需要使用云存储空间管理工具（如 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）单独删除。

## <a name="initiate-device-return"></a>启动设备退回

若要开始退回过程，请执行以下步骤。

1. 在 Azure 门户中转到 Azure Stack Edge 资源。 在“概述”中，转到右窗格中的命令栏并选择“退回设备” 。 

    ![退回设备 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. 在“退回设备”边栏选项卡的“基本详细信息”下 ：

    1. 提供设备的序列号。 若要获取设备序列号，请转到设备的本地 Web UI，然后转到“概述”。  
    
       ![设备序列号 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. 输入服务标记号。 服务标记号是具有五个或更多字符的标识符，这是设备所独有的。 服务标记位于设备的右下角（当你面对设备时）。 取出信息标记（这是一个滑出标签面板）。 此面板包含系统信息，如服务标记、NIC、MAC 地址等。 
    
       ![服务标记号 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. 从下拉列表中，选择退回的原因。

       ![退回设备 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. 在“送货详细信息”下：

    1. 提供你的姓名、公司名称和完整公司地址。 输入工作电话，包括区号和电子邮件 ID 以接收通知。
    2. 如果需要退货箱，可以发送请求。 对于问题“需要一个空箱进行退货”，回答“是” 。

    ![退回设备 3](media/azure-stack-edge-return-device/return-device-3.png)

4. 查看“隐私条款”，选择说明你已查看并同意隐私条款的复选框。

5. 选择“启动退回”。

    ![退回设备 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. 捕获设备退回详细信息后，可通过电子邮件通知 Azure Stack Edge 运营团队。 你可使用电子邮件应用程序（假设已安装并配置电子邮件应用程序）。 还可复制数据来创建和发送电子邮件。

    ![退回设备 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Azure Stack Edge 运营团队收到电子邮件后，他们将向你发送反向装运标签。 当你收到此标签时，你可以与承运商一起安排设备取件。 

## <a name="pack-the-device"></a>打包设备

要打包设备，请执行以下步骤。

1. 关闭设备。 在本地 Web UI 中，转到“维护”>“电源设置”。
2. 选择“关闭”。 出现确认提示时，请单击“是”以继续。 有关详细信息，请参阅[管理电源](../databox-online/azure-stack-edge-manage-access-power-connectivity-mode.md#manage-power)。
3. 拔下电源线，并拔下设备的所有网络电缆。
4. 按照以下说明认真准备发货包：
    1. 使用从 Azure 那里请求的包装箱，或者使用最初的带泡沫包装的包装箱。 
    1. 将底部的泡沫片放到箱子里。
    1. 将设备放在泡沫顶部，注意让它紧贴泡沫。
    1. 将顶部的泡沫片放到箱子里。
    1. 将电源线放到配件托盘里，将导轨放在泡沫片顶部。
    1. 将包装箱密封，将从 Azure 那里收到的发货标签贴到包裹上。
    
    > [!IMPORTANT]
    > 如果未遵循正确的退回货物准备指南，设备可能会损坏，我们可能会收取损坏设备费用。 查看[产品服务条款](https://www.microsoft.com/licensing/product-licensing/products)和[有关设备丢失或损坏的 FAQ](https://azure.microsoft.com/pricing/details/databox/edge/)。
 


## <a name="schedule-a-pickup"></a>安排取件

若要安排取件，请执行以下步骤。

1. 安排区域快递公司取件。 如果在美国退回设备，则快递公司可能是 UPS 或 FedEx。 安排 UPS 取件：

    1. 给本地 UPS 打电话（特定于国家/地区的免费电话号码）。
    2. 在通话中，引用反向装运跟踪号码，如打印的标签上所示。
    3. 如果未引用跟踪号码，UPS 将在取件时要求你支付额外费用。

    如果不安排取件，也可以在最近的卸货位置放置 Azure Stack Edge。

## <a name="delete-the-resource"></a>删除资源

在 Azure 数据中心接收到设备后，会检查设备是否损坏或是否有任何篡改迹象。

- 如果设备到达时完好无损，则该资源的计费指示器将停止。 Azure Stack Edge 运营团队将与你联系，确认设备已退回。 然后，你可以在 Azure 门户中删除与该设备关联的资源。
- 如果设备到达时严重损坏，可能会收取损坏费用。 有关详细信息，请参阅[有关设备丢失或损坏的 FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) 和[产品服务条款](https://www.microsoft.com/licensing/product-licensing/products)。  


可以在以下时段从 Azure 门户删除设备：

- 在你下订单之后但在 Microsoft 准备好设备之前。
- 将设备退回 Microsoft 并且 Azure Stack Edge 运营团队已致电确认设备已退回之后。 直到退回的设备通过 Azure 数据中心的物理检查，运营团队才会致电。

如果你已针对其他订阅或位置激活了设备，Microsoft 将在一个工作日内将你的订单转移到新的订阅或位置。 移动订单后，可以删除此资源。


执行以下步骤以删除 Azure 门户中的设备和资源。

1. 在 Azure 门户中，转到资源，然后转到“概述”。 在命令栏中，选择“删除”。

    ![选择“删除”](media/azure-stack-edge-return-device/delete-resource-1.png)

2. 在“删除设备”边栏选项卡中，键入要删除的设备的名称，然后选择“删除” 。

    ![确认删除](media/azure-stack-edge-return-device/delete-resource-2.png)

设备和关联的资源成功删除后，你会收到通知。


## <a name="next-steps"></a>后续步骤

- 了解如何[更换 Azure Stack Edge 设备](azure-stack-edge-replace-device.md)。