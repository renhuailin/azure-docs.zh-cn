---
title: Microsoft Azure 客户密码箱
description: Microsoft Azure 客户密码箱的技术概述。当 Microsoft 需要访问客户数据时，客户密码箱可用于控制云提供商进行的访问。
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 04/05/2021
ms.openlocfilehash: 80d1e4f39d69f761b801ccec834c0228057e4847
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448519"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Microsoft Azure 客户密码箱

> [!NOTE]
> 若要使用此功能，组织必须具有一个最低级别为“开发人员”的 [Azure 支持计划](https://azure.microsoft.com/support/plans/)。

Microsoft 人员和子处理器执行的大多数操作、支持和故障排除都不需要访问客户数据。 只有在极少数的情况下需要访问，Microsoft Azure 客户密码箱为客户提供查看、同意或拒绝客户数据访问请求的接口。 适用于 Microsoft 工程师为了响应客户发起的支持票证或是出现由 Microsoft 识别的问题时，需要访问客户数据的情况。

本文介绍如何启用客户密码箱，以及如何启动、跟踪密码箱请求和存储这些请求供以后进行审查和审核。

<a name='supported-services-and-scenarios-in-general-availability'></a><a name='supported-services-and-scenarios-in-preview'></a>
## <a name="supported-services-and-scenarios"></a>支持的服务和方案

### <a name="general-availability"></a>正式版
以下服务普遍适用于客户密码箱：

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
- Azure 中的虚拟机（包括远程桌面访问，以及对内存转储和托管磁盘的访问）

### <a name="public-preview"></a>公共预览版
当前客户密码箱预览版支持以下服务：

- Azure 机器学习
- Azure Batch

## <a name="enable-customer-lockbox"></a>启用客户密码箱

现在可以通过[管理模块](https://aka.ms/customerlockbox/administration)中的“客户密码箱”边栏选项卡启用客户密码箱。  

> [!NOTE]
> 若要启用客户密码箱，需要为用户帐户[分配“全局管理员”角色](../../active-directory/roles/manage-roles-portal.md)。

## <a name="workflow"></a>工作流

以下步骤概述了客户密码箱请求的典型工作流。

1. 组织中某人的 Azure 工作负载出现了问题。

2. 此人排查了该问题，但无法予以解决，于是通过 [Azure 门户](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)开具了支持票证。 该票证分配到了某位 Azure 客户支持工程师。

3. Azure 支持工程师在审查服务请求后，确定了解决该问题的后续步骤。

4. 如果支持工程师无法使用标准工具和服务生成的数据排除问题故障，下一步应使用实时 (JIT) 访问服务请求提升权限。 此请求可能来自最初的支持工程师或其他工程师，因为问题已上报到 Azure DevOps 团队。

5. 在 Azure 工程师提交访问请求后，即时服务将在考虑到如下所述因素的前提下评估该请求：
    - 资源的范围
    - 请求者是独立的标识还是使用多重身份验证
    - 权限级别

    基于 JIT 规则，此请求还可能包括内部 Microsoft 审批者做出的审批。 例如，审批者可能是客户支持主管或 DevOps 经理。

6. 当请求需要直接访问客户数据时，将启动客户密码箱请求。 例如，对客户的虚拟机进行远程桌面访问。

    请求现在处于“已通知客户”状态，正在等待客户审批然后授予访问权限。

7. 在客户组织中，具有 Azure 订阅的[“所有者”角色](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)的用户会收到来自 Microsoft 的电子邮件，该邮件告知有待定的访问请求。 对于客户密码箱请求，此人为指定的审批者。

    示例电子邮件：

    ![Azure 客户密码箱 - 电子邮件通知](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. 电子邮件通知中提供了管理模块中“客户密码箱”边栏选项卡的链接。 指定的审批者可以使用此链接登录到 Azure 门户，查看其组织收到的、与客户密码箱相关的任何待定请求：

    ![Azure 客户密码箱 - 登陆页](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   请求将在客户队列中保留四天。 此时间过后，访问请求将自动过期，且不会向 Microsoft 工程师授予任何访问权限。

9. 若要获取待定请求的详细信息，指定的审批者可以从“待定的请求”中选择密码箱请求：

    ![Azure 客户密码箱 - 查看待定的请求](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. 指定的审批者还可以选择“服务请求 ID”查看原始用户创建的支持票证请求。 此信息提供有关 Microsoft 支持部门为何要介入此请求的上下文，以及所报告问题的历史记录。 例如：

    ![Azure 客户密码箱 - 查看支持票证请求](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. 在审查请求后，指定的审批者选择“批准”或“拒绝”： 

    ![Azure 客户密码箱 - 选择“批准”或“拒绝”](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    选择后的结果：
    - **批准**：向 Microsoft 工程师授予访问权限。 授予的访问权限的有效期默认为八个小时。
    - **拒绝**：拒绝 Microsoft 工程师提出的访问权限提升请求，且不执行进一步的操作。

出于审核目的，在此工作流中执行的操作将记录到[客户密码箱请求日志](#auditing-logs)中。

## <a name="auditing-logs"></a>审核日志

客户密码箱日志存储在活动日志中。 在 Azure 门户中，选择“活动日志”可查看与客户密码箱请求相关的审核信息。 可以筛选特定操作，例如：
- **拒绝密码箱请求**
- **创建密码箱请求**
- **批准密码箱请求**
- **密码箱请求过期**

示例：

![Azure 客户密码箱 - 活动日志](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>客户密码箱与 Azure 安全性基准的集成

我们在涵盖客户密码箱适用性的 Azure 安全基准中引入了新的基线控制 ([3.13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios))。 客户现在可以利用该基准来审查服务的客户密码箱适用性。

## <a name="exclusions"></a>排除项

在以下工程支持场景中不会触发客户密码箱请求：

- 不属于标准操作过程的紧急情况。 例如，在意外或不可预测的情况下，需要立即恢复或还原重大服务中断。 此类“中断玻璃”事件很少见，在大多数情况下，不需要访问任何客户数据即可解决。
- Microsoft 工程师在故障排除过程中访问 Azure 平台，无意中接触到客户数据。 例如，Azure 网络团队会执行故障排除，导致在网络设备上捕获数据包。 这种情况很少会导致对有意义数量的客户数据的访问。 客户可以通过使用传输中和静态加密进一步保护其数据。

对数据的外部法律要求也不会触发客户密码箱请求。 有关详细信息，请参阅 Microsoft 信任中心上有关[政府数据请求](https://www.microsoft.com/trust-center/)的讨论。

## <a name="next-steps"></a>后续步骤

客户密码箱适用于具有一个最低级别为“开发人员”的 [Azure 支持计划](https://azure.microsoft.com/support/plans/)的所有客户。 可以通过[管理模块](https://aka.ms/customerlockbox/administration)中的“客户密码箱”边栏选项卡启用客户密码箱。

如果需要 Microsoft 工程师发起客户密码箱请求才能让支持案例得到进一步的处理，则工程师会执行此操作。
