---
title: Microsoft Azure Data Box Disk 自我托管交付 | Microsoft Docs
description: 描述 Azure Data Box Disk 设备的自我托管交付工作流
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 05/08/2021
ms.author: alkohli
ms.openlocfilehash: 6323a077596a7ed13384bfbaace929b270c2d3f5
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2021
ms.locfileid: "109754472"
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
* 印度（预览版）
* 巴西

## <a name="use-self-managed-shipping"></a>使用自我托管交付

在下单购买 Data Box Disk 时，可选择“自我托管交付”选项。

1. 在 Azure Data Box Disk 订单的“联系人详细信息”下，选择“+添加送货地址” 。

   ![“订单”向导的屏幕截图，其中显示了“联系人详细信息”步骤，并标记显示了“添加寄送地址”选项。](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 选择送货类型时，请选择“自我托管交付”选项。 仅当你位于先决条件中所述的受支持的区域时，此选项才可用。

3. 提供送货地址后，需要验证该地址并完成订单。

   ![“添加寄送地址”对话框的屏幕截图，其中标注了“寄送方式”选项和“添加寄送地址”选项。](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. 准备好设备并收到电子邮件通知后，可以安排提货。 在 Azure Data Box Disk 订单中，转到“概述”，然后选择“安排提货” 。

   ![将 Data Box 设备排序以便提货](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-01b.png)

5. 按照“Azure 提货安排”中的说明进行操作。 你必须发送电子邮件至 [adbops@microsoft.com](mailto:adbops@microsoft.com)，安排从你所在区域的数据中心来提取设备，然后才可获得授权代码。

   ![安排提货](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-user-pickup-02c.png)

   **有关巴西的说明：** 如果要安排在巴西提取设备，请在电子邮件中包含以下信息。 数据中心在收到入站 `Nota Fiscal` 后会安排取件，可能最长需要 4 个工作日。

   ```
   Subject: Request Azure Data Box Disk pickup for order: <ordername>

   - Order name
   - Company name
   - Company legal name (if different) 
   - Tax ID 
   - Address 
   - Country 
   - Phone number 
   - Contact name of the person who will pick up the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.)   
   ```

6. 安排好设备提货后，可在“Azure 提货安排”中查看授权代码。

   ![“Azure 提货安排”对话框的屏幕截图，其中标记显示了“提货授权代码”文本框。](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-01b.png)

   记下此授权代码。 提取设备的人将需要该代码。

   根据安全要求，在安排提货时，需要提供负责提货的人员的姓名和详细信息。 你或联系人员必须携带政府批准的带照片的 ID，我们将在数据中心验证该 ID。

7. 在安排的时间在数据中心提取 Data Box Disk。

   提取设备的人需要提供以下信息：

   * 从 Microsoft Operations 访问数据中心时所需的电子邮件确认的副本。

   * 授权代码。 参考号在提货或交送时是唯一代码，并且在数据中心进行验证。

   * 政府批准的带照片的 ID。 将在数据中心验证此 ID，并且在安排提货时需要提供提取设备的人员的姓名和详细信息。

   > [!NOTE]
   > 如果错过预约的安排，则需要重新安排。

8. 从数据中心提出设备后，你的订单将自动切换到“已提货”状态。

   ![已提货](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-ready-disk-01b.png)

9. 提取设备后，可将数据复制到你所在站点上的 Data Box Disk。 数据复制完成后，可准备寄送 Data Box Disk。

   完成数据复制后，请联系操作部门以安排交送预约。 你需要分享要前往数据中心交送磁盘的人员的详细信息。 此外，数据中心需要在交货时验证授权代码。 你将在 Azure 门户中的“安排交送”下找到用于交送的授权代码。

   > [!NOTE]
   > 请勿通过电子邮件共享授权代码。 仅在数据中心交送时验证该代码。

   **有关巴西的说明：** 若要安排在巴西退返设备，请发送电子邮件至 [adbops@microsoft.com](mailto:adbops@microsoft.com)，并提供以下信息：

   ```
   Subject: Request Azure Data Box Disk drop-off for order: <ordername>

   - Order name
   - Contact name of the person who will drop off the Data Box Disk (A government-issued photo ID will be required to validate the contact’s identity upon arrival.) 
   - Inbound Nota Fiscal (A copy of the inbound Nota Fiscal will be required at drop-off.)   
   ```

10. 收到交送预约后，订单在 Azure 门户中应处于“可以在 Azure 数据中心接收”状态。

    ![“添加寄送地址”对话框的屏幕截图，其中标记显示了“寄送方式”选项和“添加寄送地址”选项。](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-authcode-dropoff-02b.png)

11. 验证了 ID 和授权代码，且在数据中心交送设备后，订单状态应为“已接收”。

    ![接收完成](media\data-box-disk-portal-customer-managed-shipping\data-box-disk-received-01a.png)

11. 设备被接收后，数据复制将继续。 复制完成后，订单完成。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户部署 Azure Data Box Disk](data-box-disk-quickstart-portal.md)
