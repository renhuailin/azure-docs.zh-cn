---
title: Microsoft Azure Data Box 自我托管交付 | 关于数据的 Microsoft Docs
description: 描述 Azure Data Box 设备的自我托管交付工作流
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 07/22/2021
ms.author: alkohli
ms.openlocfilehash: cafea36f45dcc063fadc1562428735ee0ac9fe20
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652813"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>在 Azure 门户中对 Azure Data Box 使用自我托管交付功能

本文介绍了 Azure Data Box 设备中下单、提货和交货的自我托管交付任务。 可以使用 Azure 门户管理 Data Box 设备。

> [!NOTE]
> 若要获取有关 Data Box 订单和发货的常见问题的答案，请参阅 [Data Box 常见问题解答](data-box-faq.yml)。

## <a name="prerequisites"></a>先决条件

订购 [Azure Data Box](data-box-deploy-ordered.md) 时，可选择“自我托管交付”选项。 “自我托管交付”功能仅在以下区域提供：

* 美国政府
* 英国
* 欧洲西部
* 日本
* 新加坡
* 韩国
* 印度
* 南非
* 澳大利亚
* 巴西

## <a name="use-self-managed-shipping"></a>使用自我托管交付

在下单购买 Data Box 时，可选择“自我托管交付”选项。

1. 在 Azure Data Box 订单的“联系人详细信息”下，选择“+ 添加送货地址” 。
 
   ![自我托管交付、添加送货地址](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 选择送货类型时，请选择“自我托管交付”选项。 仅当你位于先决条件中所述的受支持的区域时，此选项才可用。

3. 提供送货地址后，需要验证该地址并完成下单。

   ![自我托管交付、验证和添加地址](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. 准备好设备并收到电子邮件通知后，可以安排提货。

   在 Azure Data Box 订单中，转到“概述”，然后选择“安排提货” 。

   ![Data Box 订单、安排提货选项](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. 按照“Azure 提货安排”中的说明进行操作。

   你必须发送电子邮件至 [adbops@microsoft.com](mailto:adbops@microsoft.com)，安排从你所在区域的数据中心来提取设备，然后才可获得授权代码。

   ![Azure 提货安排说明](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

   **有关巴西的说明：** 如果要安排在巴西提取设备，请在电子邮件中包含以下信息。 数据中心在收到入站 `Nota Fiscal` 后会安排取件，可能最长需要 4 个工作日。

   ```
   Subject: Request Azure Data Box Disk pickup for order: <ordername>

   - Order name
   - Company name
   - Company legal name (if different) 
   - CNPJ (Business Tax ID, format: 00.000.000/0000-00) or CPF (Individual Tax ID, format: 000.000.000-00)
   - Address
   - Country 
   - Phone number 
   - Contact name of the person who will pick up the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.)   
   ```

6. 安排好设备提货后，可在“Azure 提货安排”窗格中查看设备授权代码。

   ![查看设备授权代码](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   记下此授权代码。 提取设备的人将需要提供该代码。

   根据安全要求，在安排提货时，需要提供负责提货的人员的姓名和详细信息。 你或联系人员必须携带政府批准的带照片的 ID，我们将在数据中心验证该 ID。

7. 在安排的时间在数据中心提取 Data Box。

   提取设备的人需要提供以下信息：

   * 从 Microsoft Operations 访问数据中心时所需的电子邮件确认的副本。

   * 授权代码。 参考号在提货或交送时是唯一代码，并且在数据中心进行验证。

   * 政府批准的带照片的 ID。 将在数据中心验证此 ID，并且在安排提货时需要提供提取设备的人员的姓名和详细信息。

   > [!NOTE]
   > 如果错过预约的安排，则需要重新安排。

8. 从数据中心提取设备后，你的订单将自动切换到“已提货”状态。

    ![处于“已提货”状态的订单](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

9. 提出设备后，可将数据复制到你所在站点上的 Data Box。 数据复制完成后，可准备交付 Data Box。 有关详细信息，请参阅[准备交付](data-box-deploy-picked-up.md#prepare-to-ship)。

   “准备交付”步骤需要在没有任何关键错误的前提下完成。 否则，在做出必要的修补后需要再次运行此步骤。 “准备交付”步骤成功完成后，可以在设备本地用户界面上查看交货的授权代码。

   > [!NOTE]
   > 请勿通过电子邮件共享授权代码。 仅在数据中心交货时进行验证该代码。

   **有关巴西的说明：** 若要安排在巴西退返设备，请发送电子邮件至 [adbops@microsoft.com](mailto:adbops@microsoft.com)，并提供以下信息：

   ```
   Subject: Request Azure Data Box Disk drop-off for order: <ordername>

   - Order name
   - Contact name of the person who will drop off the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.) 
   - Inbound Nota Fiscal (A copy of the inbound Nota Fiscal will be required at drop-off.)   
   ```

10. 如果你已收到订单交货预约，那么订单在 Azure 门户中应处于“可以在 Azure 数据中心接收”状态。 按照“安排交货”下的说明退回设备。

    ![设备交货说明](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

11. 在你的 ID 和授权代码经过验证，且你将设备交付给数据中心后，订单状态应为“已接收”。

    ![具有“已接收”状态的订单](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

12. 设备被接收后，数据复制将继续。 复制完成后，订单完成。

## <a name="next-steps"></a>后续步骤

* [Azure Data Box 入门](data-box-quickstart-portal.md)
