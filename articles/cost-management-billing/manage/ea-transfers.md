---
title: Azure Enterprise 传输
description: 介绍 Azure EA 传输
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: cc8d65ec4b714bbb98036c5ed3deabd4b75d3c98
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751984"
---
# <a name="azure-enterprise-transfers"></a>Azure Enterprise 传输

本文概述了企业传输。

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>将企业帐户转移到新注册

帐户转移将帐户所有者从一个注册移动到另一个注册。 帐户所有者下的所有相关订阅都将移动到目标注册。 如果你有多个有效注册，但只想移动选定的帐户所有者，请使用帐户转移。

此部分仅供参考，因为企业管理员无法执行该操作。 需要支持请求才能将企业帐户转移到新注册。

将企业帐户转移到新注册时，请记住以下几点：

- 仅转移请求中指定的帐户。 如果选择所有帐户，会转移所有这些帐户。
- 源注册的状态将保留为活动或已扩展。 在该注册过期之前，可以继续使用它。

### <a name="prerequisites"></a>先决条件

请求帐户转移时，请提供以下信息：

- 要转移的帐户的目标注册编号、帐户名和帐户所有者电子邮件
- 对于源注册，请提供要转移的注册编号和帐户

转移帐户之前要注意的其他要点：

- 目标和源注册需要经过 EA 管理员的批准
- 如果帐户转移不符合要求，请考虑进行注册转移。
- 帐户转移与特定帐户相关的所有服务和订阅。
- 转移完成后，转移的帐户在源注册下显示为非活动状态，在目标注册下显示为活动状态。
- 帐户显示与源注册的有效转移日期相对应的终止日期，并将其显示为目标注册的起始日期。
- 在有效转移日期之前该帐户发生的任何使用情况均保留在源注册中。

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>将企业注册转移到新注册

在以下情况下，应考虑注册转移：

- 当前注册的预付款期限已到期。
- 注册处于到期/延期状态，且新协议正在商定中。
- 你有多个注册，并希望合并单个注册下的所有帐户和账单。

此部分仅供参考，因为企业管理员无法执行该操作。 除非注册有资格进行[自动注册转移](#auto-enrollment-transfer)，否则需要发出支持请求才能将企业注册转移到新的注册。

请求将整个企业注册转移到某个注册时，将发生以下操作：

- 转移的使用情况最多可能需要 72 小时，才能反映在新注册中。
- 如果在转移的注册上启用了 DA 或 AO 视图费用，则必须在新注册中启用这些费用。
- 如果使用 API 报表或 Power BI，请在新注册下生成新的 API 密钥。
- 所有 Azure 服务、订阅、帐户、部门和整个注册结构（包括所有 EA 部门管理员）将转移到新的目标注册。
- 注册状态将设置为“已转移”。 转移的注册仅可用于历史使用情况报告。
- 无法将角色或订阅添加到处于已转移状态的注册。 “已转移”状态可防止注册进一步产生使用费。
- 协议中任何剩余的 Azure 预付款余额都将丢失（包括将来的款项）。
-    如果作为转移来源的注册中购买了预留实例 (RI)，系统仍会将 RI 购买费保留在源注册中，但会转移所有 RI 权益以供在新注册中使用。
-    不会向新注册转移旧注册中已产生的市场一次性购买费和任何固定包月费用。 将传输基于使用量的市场费用。

### <a name="effective-transfer-date"></a>转移生效日期

转移生效日期可以是目标注册的开始日期当日或之后的某个日期。

源注册的使用费根据 Azure 预付款或超额计收。 在转移生效日期之后发生的使用费将转移到新注册并收费。

### <a name="prerequisites"></a>先决条件

请求注册转移时，请提供以下信息：

- 对于源注册，请提供注册编号。
- 对于目标注册，请提供要转移到的注册编号。
- 注册转移生效日期可以是目标注册开始日期当日或之后的某个日期 所选日期不能影响任何已开具的超额发票的使用。

转移注册之前要注意的其他要点：

- 需要经过目标和源注册 EA 管理员的批准。
- 如果注册转移不符合要求，请考虑进行帐户转移。
- 源注册状态将更新为“已转移”，并仅用于历史使用情况报告。
- 注册转移期间不会发生停机。
- 使用情况最多可能需要 24 - 48 小时，才能反映在目标注册中。
- 部门管理员或帐户所有者的成本视图设置不会存续。
  - 如果之前已经启用，则必须为目标注册也启用设置。
- 必须为目标注册重新生成源注册中使用的任何 API 密钥。
- 如果源注册和目标注册位于不同的云实例上，转移将会失败。 Azure 支持只能在同一云实例中转移。
- 适用于预留情形（已预留示例）：
  - 不同货币之间的注册或帐户转移会影响每月购买预留项。
  - 每当在注册转移期间或之后更改货币时，为源注册设置的每月已购预留项将取消。 此为有意操作，但仅会影响每月购买预留项。
  - 可能需要使用本地或新货币的新注册重新购买源注册中取消的每月预留项。


### <a name="auto-enrollment-transfer"></a>自动注册转移

即使未提交支持票证来请求注册转移，你也可能会看到注册处于“已转移”状态。 “已转移”状态是在自动注册转移过程中生成的。 为了在续订期间进行自动注册转移过程，新协议必须包含以下项：

- 先前的注册号（必须位于 EA 门户）
- 先前的注册号的到期日期是新协议生效开始日期的前一天
- 新协议具有已开票的 Azure 预付款订单，订单上的日期不超过当前日期
- 新注册在 EA 门户中创建

如果在 EA 门户中，先前注册和新注册的使用数据相同，则无需创建转移支持票证。

### <a name="azure-prepayment"></a>Azure 预付款

Azure 预付款不可在注册之间转移。 Azure 预付款余额在契约上关联到订购服务的注册。 在帐户或注册转移过程中，不会转移 Azure 预付款。

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>帐户和注册转移不影响任何服务

帐户或注册转移期间不会发生停机。 如果已提供所有必备信息，则转移可以在请求的同一日完成。

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>将 Enterprise 订阅转换为即用即付订阅

若要将 Enterprise 订阅转换为采用即用即付费率的单个订阅，必须在 Azure Enterprise 门户中创建新的支持请求。 若要创建支持请求，请在“帮助和支持”区域中选择“+ 新建支持请求”。 

## <a name="change-azure-subscription-or-account-ownership"></a>更改 Azure 订阅或帐户所有权

Azure EA 门户可将一个帐户所有者的订阅转移到另一个帐户所有者。 有关详细信息，请参阅[更改 Azure 订阅或帐户所有权](ea-portal-administration.md#change-azure-subscription-or-account-ownership)。

## <a name="subscription-transfer-effects"></a>订阅转移的影响

将 Azure 订阅转移到同一 Azure Active Directory 租户中的某个帐户后，以前拥有 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 权限，可管理资源的所有用户、组和服务主体将保留其访问权限。

若要查看哪些用户对订阅拥有 RBAC 访问权限：

1. 在 Azure 门户中，打开 **订阅**。
2. 选择要查看的订阅，然后选择“访问控制(IAM)”。
3. 选择“角色分配”。 角色分配页将列出对订阅拥有 RBAC 访问权限的所有用户。

如果将订阅转移到了不同 Azure AD 租户中的某个帐户，则以前拥有 [RBAC](../../role-based-access-control/overview.md) 权限，可管理资源的所有用户、组和服务主体将失去其访问权限。 尽管失去了 RBAC 访问权限，但他们可以通过安全机制来访问订阅，这些机制包括：

- 用于向用户授予订阅资源管理权限的管理证书。 有关详细信息，请参阅[创建并上传 Azure 的管理证书](../../cloud-services/cloud-services-certs-create.md)。
- 存储空间等服务的访问密钥。 有关详细信息，请参阅 [Azure 存储帐户概述](../../storage/common/storage-account-overview.md)。
- Azure 虚拟机等服务的远程访问凭据。

如果接收方需要限制对其 Azure 资源的访问，他们应考虑更新与服务关联的任何机密。 可通过以下步骤更新大多数资源：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在“中心”菜单上，选择“所有资源”。
3. 选择资源。
4. 在资源页上，选择“设置”以查看和更新现有的机密。

## <a name="next-steps"></a>后续步骤

- 在排查 Azure EA 门户问题时如需帮助，请参阅[排查 Azure EA 门户访问问题](ea-portal-troubleshoot.md)。