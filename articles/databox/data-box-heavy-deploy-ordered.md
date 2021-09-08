---
title: Azure Data Box Heavy 订购教程 | Microsoft Docs
description: 在本教程中，了解 Azure Data Box Heavy（允许将本地数据导入到 Azure 中的混合解决方案）以及如何订购 Data Box Heavy。
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: alkohli
ms.localizationpriority: high
ms.custom: contperf-fy22q1
ms.openlocfilehash: d5334314626d29dc9e3047bc382d41fcfa318a1d
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469511"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>教程：订购 Azure Data Box Heavy


Azure Data Box Heavy 是一个混合解决方案，可以快速、方便、可靠地将本地数据导入 Azure。 请先将数据传输到 Microsoft 提供的 770 TB（大致可用容量）存储设备，然后将设备寄回。 然后，此数据将上传到 Azure。

本教程介绍如何订购 Azure Data Box Heavy。 本教程的介绍内容包括：

> [!div class="checklist"]
> * Data Box Heavy 先决条件
> * 订购 Data Box Heavy
> * 跟踪订单
> * 取消订单

## <a name="prerequisites"></a>先决条件

请先完成下述适用于 Data Box 服务和设备的配置先决条件，然后部署设备。

### <a name="for-installation-site"></a>安装场地

在开始之前，请确保：

- 设备可以通过标准的门廊和入口通道。 但是，请确保设备可以通过所有入口通道。 设备规格：宽度26 英寸，长度48 英寸，高度28 英寸。
- 如果在第一层以外的楼层安装设备，必须能够通过升降机或活动梯来接触设备。 设备重约 500 磅。
- 务必在数据中心内的平坦场地上操作，该场地靠近可用的网络连接，并能够放得下 Data Box Heavy 大小的设备。

### <a name="for-service"></a>对于服务

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>对于设备

在开始之前，请确保：
- 设备已打开包装。
- 应该将一个主机连接到数据中心网络。 Data Box Heavy 将从此计算机复制数据。 主机必须按照 [Azure Data Box Heavy 系统要求](data-box-system-requirements.md)中的说明运行支持的操作系统。
- 需要使用一台配备 RJ-45 线缆的笔记本电脑连接到本地 UI 并配置设备。 使用笔记本电脑配置设备的每个节点一次。
- 数据中心需要有高速网络。 强烈建议你至少建立一个 10 GbE 连接。
- 需要为每个设备节点使用一条 40 Gbps 或 10 Gbps 线缆。 选择与 [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) 网络接口兼容的线缆：

    - 40 Gbps 线缆的设备端需是 QSFP+。
    - 对于 10 Gbps 线缆，需要使用 SFP+ 线缆，其一端插入 10 G 交换机，插入设备的另一端配备 QSFP+ 转 SFP+ 适配器（或 QSA 适配器）。
    - 设备附带了电源线。

## <a name="order-data-box-heavy"></a>订购 Data Box Heavy

[!INCLUDE [order-data-box-via-portal](../../includes/data-box-order-portal.md)]

## <a name="track-the-order"></a>跟踪订单

下单后，可以从 Azure 门户跟踪订单状态。 转到你的 Data Box Heavy 订单，然后转到“概览”来查看状态。 门户中会显示订单处于“已订购”状态。

如果设备缺货，你会收到通知。 如果设备有货，Microsoft 会确定要发货的设备，并准备发货。 在设备准备期间，会执行以下操作：

- 将为与设备关联的每个存储帐户创建 SMB 共享。
- 将为每个共享生成访问凭据（例如用户名和密码）。
- 也会生成用于解锁设备的设备密码。
- 锁定 Data Box Heavy 的目的是在任何时候防止对设备进行未经授权的访问。

设备准备完成后，门户会显示订单处于“已处理”状态。

![处理的 Data Box Heavy 订单](media/data-box-overview/data-box-order-status-processed.png)

然后，Microsoft 会安排区域承运人发运设备。 设备发货后，你会收到跟踪号。 门户会显示订单处于“已发运”状态。

![派遣的 Data Box Heavy 订单](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>取消订单

若要取消此订单，请在 Azure 门户中转到“概览”，然后在命令栏中单击“取消”。 

下单后，只要订单状态尚未标记为“已处理”，就可以随时取消订单。
 
若要删除已取消的订单，请转到“概况”，然后在命令栏中单击“删除”。 

## <a name="next-steps"></a>后续步骤

本教程介绍了 Azure Data Box Heavy 主题，例如：

> [!div class="checklist"]
> * 先决条件
> * 订购 Data Box Heavy
> * 跟踪订单
> * 取消订单

请继续学习下一教程，了解如何设置 Data Box Heavy。

> [!div class="nextstepaction"]
> [设置 Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
