---
title: Microsoft Azure 客户密码箱
description: Microsoft Azure 的客户密码箱技术概述，在 Microsoft 可能需要访问客户数据时提供对云提供商访问的控制。
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 02/19/2021
ms.openlocfilehash: b39b1d33885de46607cd55acee9919473fab7733
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051423"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure 客户密码箱

> [!NOTE]
> 若要使用此功能，你的组织必须具有最小 **开发人员** 级别的 [Azure 支持计划](https://azure.microsoft.com/support/plans/)。

Microsoft Azure 客户密码箱提供一个界面供客户查看和批准/拒绝客户数据访问请求。 当 Microsoft 工程师需要在支持请求期间访问客户数据时，可以使用此功能。

本文介绍如何启用客户密码箱以及如何启动、跟踪和存储密码箱请求以便以后查看和审核。

<a name='supported-services-and-scenarios-in-general-availability'><a name='supported-services-and-scenarios-in-preview'>
## <a name="supported-services-and-scenarios-general-availability"></a> (公开上市) 支持的服务和方案

以下服务现已正式推出客户密码箱：

- Azure API 管理
- Azure 应用服务
- Azure 认知服务
- Azure 容器注册表
- Azure Database for MySQL
- Azure Databricks
- Azure Data Box
- Azure 数据资源管理器
- Azure 数据工厂
- Azure Database for PostgreSQL
- Azure Functions
- Azure HDInsight
- Azure Kubernetes 服务
- Azure Monitor
- Azure 存储
- Azure SQL 数据库
- Azure 订阅传输
- Azure Synapse Analytics
- Azure 中的虚拟机 (涵盖远程桌面访问、内存转储访问和托管磁盘) 

## <a name="enable-customer-lockbox"></a>启用客户密码箱

你现在可以从 "客户密码箱" 边栏选项卡中的 " [管理" 模块](https://aka.ms/customerlockbox/administration) 启用客户密码箱。  

> [!NOTE]
> 若要启用客户密码箱，需要为用户帐户 [分配全局管理员角色](../../active-directory/roles/manage-roles-portal.md)。

## <a name="workflow"></a>工作流

以下步骤概述了客户密码箱请求的典型工作流。

1. 组织中的某人对其 Azure 工作负荷有问题。

2. 在此人员对问题进行故障排除但无法修复后，他们将从 [Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)中打开支持票证。 票证分配给 Azure 客户支持工程师。

3. Azure 支持工程师会查看服务请求，并确定解决此问题的后续步骤。

4. 如果支持工程师无法通过使用标准工具和遥测对问题进行故障排除，则下一步是通过使用实时 (JIT) access 服务请求提升的权限。 此请求可以来自原始支持工程师或不同工程师，因为此问题已升级到 Azure DevOps 团队。

5. Azure 工程师提交访问请求后，实时服务会评估请求，其中包括以下因素：
    - 资源的范围
    - 请求者是隔离标识还是使用多重身份验证
    - 权限级别

    此请求还可以基于 JIT 规则，包括来自内部 Microsoft 审批者的批准。 例如，审批者可以是客户支持主管或 DevOps 经理。

6. 当请求需要直接访问客户数据时，将启动客户密码箱请求。 例如，对客户的虚拟机的远程桌面访问。

    请求现在处于 **客户通知** 状态，在授予访问权限前等待客户的批准。

7. 在客户组织中，拥有 Azure 订阅的 [所有者角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) 的用户将收到来自 Microsoft 的电子邮件，通知他们有关挂起的访问请求。 对于客户密码箱请求，此人为指定的审批者。

    示例电子邮件：

    ![Azure 客户密码箱-电子邮件通知](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. 电子邮件通知提供指向管理模块中 **客户密码箱** 边栏选项卡的链接。 使用此链接，指定的审批者可以登录到 Azure 门户，查看其组织为客户密码箱所做的任何挂起的请求：

    ![Azure 客户密码箱-登陆页面](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   请求在客户队列中保留四天。 此时间过后，访问请求会自动过期，并且不会向 Microsoft 工程师授予任何访问权限。

9. 若要获取待定请求的详细信息，指定的审批者可以从 **挂起的请求** 中选择密码箱请求：

    ![Azure 客户密码箱-查看挂起的请求](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 指定的审批者还可以选择 **服务请求 ID** ，以查看原始用户创建的支持票证请求。 此信息提供了有关 Microsoft 支持部门的原因以及所报告问题的历史记录的上下文。 例如：

    ![Azure 客户密码箱-查看支持票证请求](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 查看请求后，指定的审批者选择 " **批准** " 或 " **拒绝**"：

    ![Azure 客户密码箱-选择 "批准" 或 "拒绝"](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    作为所选内容的结果：
    - **批准**：向 Microsoft 工程师授予访问权限。 在八小时的默认期限内授予访问权限。
    - **拒绝**： Microsoft 工程师的提升访问请求被拒绝，无需执行其他操作。

出于审核目的，在此工作流中执行的操作记录在 [客户密码箱请求日志](#auditing-logs)中。

## <a name="auditing-logs"></a>审核日志

客户密码箱日志存储在活动日志中。 在 Azure 门户中，选择 " **活动日志** " 以查看与客户密码箱请求相关的审核信息。 可以筛选特定操作，例如：
- **拒绝密码箱请求**
- **创建密码箱请求**
- **批准密码箱请求**
- **密码箱请求过期**

示例：

![Azure 客户密码箱活动日志](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>客户密码箱与 Azure 安全性基准的集成

我们引入了新的基线控件 ([3.13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) Azure 安全基准，其中包含客户密码箱适用性。 客户现在可以利用基准来查看服务客户密码箱适用性。

## <a name="exclusions"></a>排除项

在以下工程支持方案中，不会触发客户密码箱请求：

- Microsoft 工程师需要执行超出标准操作过程范围的活动。 例如，在意外或不可预测的情况下恢复或还原服务。
- Microsoft 工程师在故障排除过程中访问 Azure 平台，并且无意中有权访问客户数据。 例如，Azure 网络团队会执行故障排除，导致在网络设备上捕获数据包。 在这种情况下，如果客户在传输数据时对数据进行加密，则工程师无法读取数据。

## <a name="next-steps"></a>后续步骤

客户密码箱适用于具有最小 **开发人员** 的 [Azure 支持计划](https://azure.microsoft.com/support/plans/)的所有客户。 你可以从 "客户密码箱" 边栏选项卡中的 " [管理" 模块](https://aka.ms/customerlockbox/administration) 启用客户密码箱。

如果需要执行此操作来进行支持案例，则 Microsoft 工程师将发起客户密码箱请求。
