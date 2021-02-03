---
title: Microsoft Azure Data Box Disk 自我托管交付 | Microsoft Docs
description: 描述 Azure Data Box Disk 设备的自我托管交付工作流
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: f512b4415f4a83e779a8f9bf790ba2806e3b05c5
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526324"
---
# <a name="use-self-managed-shipping-for-azure-data-box-disk-in-the-azure-portal"></a>在 Azure 门户中对 Azure Data Box Disk 使用自我托管交付功能

本文介绍了 Azure Data Box Disk 中下单、提货和交货的自我托管交付任务。 你可使用 Azure 门户来管理 Data Box Disk。

## <a name="prerequisites"></a>先决条件

[订购 Azure Data Box Disk](data-box-disk-deploy-ordered.md) 时，可选择“自我托管交付”选项。 “自我托管交付”功能仅在以下区域中提供：

* 美国政府
* 英国
* 欧洲西部
* 澳大利亚
* 日本
* 新加坡
* 韩国
* 南非
* 印度 (预览) 

## <a name="use-self-managed-shipping"></a>使用自我托管交付

在下单购买 Data Box Disk 时，可选择“自我托管交付”选项。

1. 在 Azure Data Box Disk 订单的“联系人详细信息”下，选择“+添加送货地址” 。

   ![订单向导的屏幕截图，显示 "联系人详细信息" 步骤，其中包含 "添加装运地址" 选项。](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 选择送货类型时，请选择“自我托管交付”选项。 仅当你位于先决条件中所述的受支持的区域时，此选项才可用。

3. 提供寄送地址后，需要对其进行验证并完成订单。

   ![“添加寄送地址”对话框的屏幕截图，其中标注了“寄送方式”选项和“添加寄送地址”选项。](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. 准备好设备并收到电子邮件通知后，可以计划装货。 在 Azure Data Box Disk 订单中，转到“概述”，然后选择“安排提货” 。

   ![将 Data Box 设备排序以便提货](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. 按照“Azure 提货安排”中的说明进行操作。 你必须发送电子邮件至 [adbops@microsoft.com](mailto:adbops@microsoft.com)，安排从你所在区域的数据中心来提取设备，然后才可获得授权代码。

   ![安排提货](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

6. 计划设备拾取后，可以在 **计划 Azure 的计划** 中查看授权代码。

   !["计划选择 Azure" 对话框的屏幕截图，其中包含被称为 "分拣" 文本框的授权代码。](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   记下此授权代码。

   根据安全要求，在制定计划时，需要提供将获得领料的人员的姓名，这是必需的。

   还需要提供有关将在数据中心中进行选择的人员的详细信息。 你或联系点必须携带将在数据中心验证的政府批准的照片 ID。

   提取设备的人员还需要具有授权代码。 授权代码在数据中心中是唯一的，并且在数据中心进行验证。

7. 从数据中心选取设备后，你的订单会自动转移到所 **选** 的状态。

   ![已提货](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

8. 提取设备后，可将数据复制到你所在站点上的 Data Box Disk。 数据复制完成后，可准备寄送 Data Box Disk。

   完成数据复制后，请联系操作来计划下拉的约会。 你需要共享进入数据中心的人员的详细信息，以删除磁盘。 此外，数据中心需要在交货时验证授权代码。 你将在 " **计划" 下拉** Azure 门户中找到用于下拉的授权代码。

   > [!NOTE]
   > 请勿通过电子邮件共享授权代码。 这仅在删除时在数据中心进行验证。

9. 收到用于下拉的约会后，该订单应在 Azure 门户中的 **Azure 数据中心状态下准备就绪** 。

   !["添加送货地址" 对话框的屏幕截图，其中包含 "发货方式" 选项和 "添加发货地址" 选项。](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

10. 验证 ID 和授权代码后，在数据中心内丢弃设备后，应 **收到** 订单状态。

    ![接收完成](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. 设备被接收后，数据复制将继续。 复制完成后，订单完成。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户部署 Azure Data Box Disk](data-box-disk-quickstart-portal.md)
