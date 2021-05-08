---
title: 配置 Azure Data Box 密码箱
description: 了解如何使用 Azure Data Box 客户密码箱。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124905"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>使用 Azure Data Box 客户密码箱（预览版）

Azure Data Box 用于将客户数据传入和传出 Azure。 有些情况下，Microsoft 支持部门可能需要在支持请求期间访问客户数据。 可以使用客户密码箱作为界面来评审和批准或拒绝这些数据访问请求。 

本文介绍如何针对 Data Box 导入和导出订单来启动和跟踪客户密码箱请求。 本文适用于 Azure Data Box 设备和 Azure Data Box Heavy 设备。 

## <a name="devops-workflow-for-data-access"></a>用于数据访问的 DevOps 工作流

Microsoft 的支持和 Data Box 运营团队通常不会访问客户数据。 他们尝试使用标准工具和遥测来解决问题。 <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

如果问题无法解决，并且需要 Microsoft 支持部门调查或修复数据，他们将通过实时 (JIT) 门户请求提升的访问权限。 JIP 门户验证权限级别，提供多重身份验证，同时还包括来自内部 Microsoft 审批者的批准。 例如，审批者可以是 DevOps 经理。 

提升的访问权限请求经 JIT 门户批准后，如果启用了密码箱，Microsoft 还需要你明确的同意才能访问数据。 访问权限通过门户中的客户密码箱服务来请求和跟踪。 

如果尚未启用密码箱，则不需要你的同意即可访问数据。


## <a name="prerequisites-for-access-request"></a>访问请求的先决条件

在开始之前，请确保：

1. 你已按照以下文章中的说明创建了 Azure Data Box 订单：
    1. [教程：订购 Azure Data Box](data-box-deploy-ordered.md)，用于导入订单。
    1. [教程：订购 Azure Data Box](data-box-deploy-export-ordered.md)，用于导出订单。

2. 你已配置 Data Box 客户密码箱。 这是一项可以选择使用的服务。 

    1. 客户密码箱当前提供 Data Box 服务预览版。 若要为你的组织启用 Data Box 客户密码箱，请注册 [Azure 公共预览版的客户密码箱](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu)。
    2. 客户密码箱自动适用于具有一个最低级别为开发人员的Azure 支持计划的所有客户。 <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. 已为此问题开启服务请求或支持票证。 有关支持票证的信息，请参阅 [提交 Data Box 服务请求](data-box-disk-contact-microsoft-support.md)。


## <a name="track-approve-request-via-lockbox"></a>通过密码箱跟踪、批准请求

若要跟踪和批准访问客户数据的请求，请执行以下步骤：

1. Microsoft 检测到在 Azure 数据中心上传或下载数据时出现问题。 例如，在“数据复制”阶段，Data Box 订单暂停。 

    支持工程师通过技术支持会话连接到 Data Box，并尝试通过使用标准工具和遥测来排查问题。 如果 Data Box 磁盘已锁定且共享不可访问，则支持工程师会创建一个密码箱请求。 
 
2. 创建请求时，通常会向订阅管理员发送通知，但你也可以配置通知组。 

3. 你可以在 Azure 门户中查看需要批准的密码箱请求。 

    ![Azure 门户中的请求](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    若要从门户批准密码箱请求，可以选择“批准”。

    ![批准请求](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    请求批准后，会解锁设备磁盘，并可在技术支持会话中访问共享。

4. 支持工程师解决上传的问题，然后禁用技术支持会话。

问题解决后，数据复制作业进度完成。


## <a name="next-steps"></a>后续步骤

- [Microsoft Azure 客户密码箱](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->