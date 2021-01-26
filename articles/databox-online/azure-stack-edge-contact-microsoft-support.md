---
title: Azure Stack Edge Pro、Azure Data Box Gateway 的日志支持票证 |Microsoft Docs
description: 了解如何记录与 Azure Stack Edge Pro 或 Data Box Gateway 订单相关的问题的支持请求。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: 411757f4ef4e33ccbc8a69ca2b40598b51c6d13b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790827"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-pro-and-azure-data-box-gateway"></a>为 Azure Stack Edge Pro 和 Azure Data Box Gateway 开支持票证

本文适用于 Azure Stack Edge Pro 和 Azure Data Box Gateway 均由 Azure Stack Edge Pro/Azure Data Box Gateway 服务管理。 如果你的服务遇到任何问题，可以为获取技术支持创建服务请求。 本文介绍：

* 如何创建支持请求。
* 如何从门户内部管理支持请求生命周期。

## <a name="create-a-support-request"></a>创建支持请求

执行以下步骤以创建支持请求：

1. 请按照 Azure Stack Edge Pro 或 Data Box Gateway 顺序进行。 导航到“支持 + 故障排除”部分，然后选择“新建支持请求” 。

2. 在“新建支持请求”的“基本”选项卡上，执行以下步骤：

    1. 从“颁发类型”下拉列表中，选择“技术” 。
    2. 选择“订阅”。
    3. 在“服务”下，检查“我的服务”。  从下拉列表中，选择 " **Azure Stack Edge Pro" 和 "Data Box Gateway**"。
    4. 选择你的“资源”。 这对应于订单的名称。
    5. 简要概述你 **遇到的问题** 。 
    6. 选择“问题类型”。
    7. 根据所选问题类型，选择相应“问题子类型”。
    8. 在完成时选择“下一步:**解决方案 >>”** 。

        ![基础](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. 在“详细信息”选项卡中执行以下步骤：

    1. 提供问题的开始日期和时间。
    2. 提供问题的“说明”。
    3. 在“文件上传”中选择文件夹图标，浏览要上传的任何其他文件。
    4. 选中“共享诊断信息”。
    5. 根据订阅，将自动填充“支持计划”。
    6. 从下拉列表中选择“严重性”。
    7. 指定“首选联系方法”。
    8. 系统会根据订阅计划自动选择“响应时间”。
    9. 提供支持的所需语言。
    10. 在“联系人信息”中提供姓名、电子邮件、电话号码、可选联系人和国家/地区。 Microsoft 支持将使用此信息与你联系，以获得进一步信息、诊断和解决方法。 
    11. 在完成时选择“下一步:**查看 + 创建 >>”** 。

        ![问题](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. 在“查看 + 创建”选项卡上，查看与支持票证相关的信息。 选择“创建”。 

    ![问题2](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    创建支持票证后，支持工程师将尽快与你联系以继续执行请求。

## <a name="get-hardware-support"></a>获取硬件支持

此信息仅适用于 Azure Stack 设备。 报告硬件问题的过程如下：

1. 从 Azure 门户中针对硬件问题打开支持票证。 在“问题类型”下，选择“Azure Stack 硬件”。 在“问题子类型”中选择“硬件故障”。

    ![硬件问题](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    创建支持票证后，支持工程师会尽早与你联系，以便处理请求。

2. 如果 Microsoft 支持部门确定这是硬件问题，则会执行以下操作之一：

    * 发送故障硬件部件的现场更换单元 (FRU)。 目前，电源单元和固态驱动器是唯一受支持的 Fru。
    * 仅在下一工作日内更换 FRU，其他所有部件都需要发送完全系统更换 (FSR)。

3. 如果确定本地时间 (星期一到星期五) 需要一个 FRU 更换，则在下一工作日向现场技术人员派单，以执行 FRU 更换。 完全系统更换通常需要更长的时间，因为部件是从工厂发货的，可能会受到运输和海关延迟的限制。

## <a name="manage-a-support-request"></a>管理支持请求

创建支持请求之后，可以在门户内管理请求的生命周期。

### <a name="to-manage-your-support-requests"></a>管理支持请求

1. 若要访问“帮助和支持”页，请导航到“浏览”>“帮助 + 支持”。

    ![管理支持请求](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. “帮助 + 支持”中会显示“最近的支持请求”表格。 

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. 选择并单击某个支持请求。 可以查看此请求的状态和详细信息。 如果想要跟进此请求，请单击“+ 新建消息”。

## <a name="next-steps"></a>后续步骤

了解如何 [解决与 Azure Stack Edge Pro 相关的问题](azure-stack-edge-troubleshoot.md)。
了解如何[排查 Data Box Gateway 相关问题](../databox-gateway/data-box-gateway-troubleshoot.md)。