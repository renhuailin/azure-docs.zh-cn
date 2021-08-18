---
title: 了解 Azure 中企业协议 (EA) 的管理员角色
description: 了解 Azure 中的企业管理员角色。 可以分配五个不同的管理角色。
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 07/06/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 82e5b16ef4f7892a52ea0ec3d332104cbf99bb68
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113765488"
---
# <a name="managing-azure-enterprise-agreement-roles"></a>管理 Azure 企业协议角色

为协助管理组织的使用情况和开支，签署了企业协议的 Azure 客户可以分配六个不同的管理角色：

- 企业管理员
- 企业管理员（只读）<sup>1</sup>
- EA 买方
- 部门管理员
- 部门管理员（只读）
- 帐户所有者<sup>2</sup>

<sup>1</sup> EA 合同的“收票方”联系人将在此角色下。

<sup>2</sup> 不能在 Azure EA 门户中添加或更改“收票方”联系人，并且将根据在协议级别上设置为“收票方”联系人的用户将其添加到 EA 注册中。 若要更改“收票方”联系人，需要通过合作伙伴/软件顾问向区域运营中心 (ROC) 发出请求。

由注册预配期间设置的第一位注册管理员确定“收票方”联系人帐户的身份验证类型。 当“收票方”联系人作为只读管理员添加到 EA 门户时，将进行 Microsoft 帐户身份验证。 

例如，如果初始身份验证类型设置为“混合”，则 EA 将被添加为 Microsoft 帐户，而“收票方”联系人将具有只读 EA 管理员权限。 如果 EA 管理员未批准现有“收票方”联系人的 Microsoft 帐户授权，EA 管理员可能会删除相关用户，并要求客户将用户重新添加为只读管理员，只在 EA 门户中的注册级别设置工作或学校帐户。

这些角色专用于管理 Azure 企业协议，是在 Azure 用于控制资源访问权限的内置角色的基础上添加的。 有关详细信息，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

## <a name="azure-enterprise-portal-hierarchy"></a>Azure Enterprise 门户层次结构

Azure Enterprise 门户的层次结构包括：

- **Azure Enterprise 门户** - 一个在线管理门户，可帮助你管理 Azure EA 服务的成本。 可以：

  - 创建包含部门、帐户和订阅的 Azure EA 层次结构。
  - 核对所用服务的成本、下载使用情况报表，以及查看价目表。
  - 创建用于注册的 API 密钥。

- “部门”可帮助你将成本细分为逻辑分组。 使用部门可在部门级别设置预算或配额。

- “帐户”是 Azure Enterprise 门户中的一个组织单位。 可以使用帐户来管理订阅和访问报表。

- “订阅”是 Azure Enterprise 门户中的最小单位。 它们是服务管理员管理的 Azure 服务的容器。

下图演示了简单的 Azure EA 层次结构。

![简单 Azure EA 层次结构示意图](./media/understand-ea-roles/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>企业用户角色

以下管理用户角色是企业注册的一部分：

- 企业管理员
- EA 买方
- 部门管理员
- 帐户所有者
- 服务管理员
- 通知联系人

角色用于在两个不同的门户中完成任务。 可以使用 [Azure Enterprise 门户](https://ea.azure.com)来管理计费和成本，使用 [Azure 门户](https://portal.azure.com)来管理 Azure 服务。

用户角色与用户帐户相关联。 为了验证用户真实性，每个用户必须具有有效的工作、学校或 Microsoft 帐户。 请确保每个帐户与某个实际受监视的电子邮件地址相关联。 帐户通知将发送到该电子邮件地址。

设置用户时，可将多个帐户分配到企业管理员角色。 但是，只能将一个帐户分配到帐户所有者角色。 此外，可向单个帐户同时分配企业管理员和帐户所有者角色。

### <a name="enterprise-administrator"></a>企业管理员

具有此角色的用户拥有最高级别的访问权限。 他们可以：

- 管理帐户和帐户所有者。
- 管理其他企业管理员。
- 管理部门管理员。
- 管理通知联系人。
- 购买 Azure 服务，包括预留项。
- 查看所有帐户的使用情况。
- 查看所有帐户的未开单费用。
- 查看并管理企业协议适用的所有预留订单和预留。
  - 企业管理员（只读）可以查看预留订单和预留。 他们不能对其进行管理。

可以在一个企业注册中分配多个企业管理员。 可以向企业管理员授予只读访问权限。 这些管理员全部继承部门管理员角色。

### <a name="ea-purchaser"></a>EA 买方

具有此角色的用户有权购买 Azure 服务，但无权管理帐户。 他们可以：

- 购买 Azure 服务，包括预留项。
- 查看所有帐户的使用情况。
- 查看所有帐户的未开单费用。
- 查看并管理企业协议适用的所有预留订单和预留。

目前仅对基于 SPN 的访问启用 EA 买方角色。 若要了解如何将角色分配给服务主体名称，请参阅[将角色分配给 Azure 企业协议服务主体名称](assign-roles-azure-service-principals.md)。

### <a name="department-administrator"></a>部门管理员

具有此角色的用户可以：

- 创建和管理部门。
- 创建新的帐户所有者。
- 查看他们所管理的部门的使用情况详细信息。
- 查看成本（如果拥有所需的权限）。

可为每个企业注册分配多个部门管理员。

编辑或新建部门管理员时，可以向部门管理员授予只读访问权限。 将只读选项设置为“是”。

### <a name="account-owner"></a>帐户所有者

具有此角色的用户可以：

- 创建和管理订阅。
- 管理服务管理员。
- 查看订阅的使用情况。

每个帐户需要唯一的工作、学校或 Microsoft 帐户。 有关 Azure Enterprise 门户管理角色的详细信息，请参阅[了解 Azure 中的 Azure 企业协议管理角色](understand-ea-roles.md)。

### <a name="service-administrator"></a>服务管理员

服务管理员角色有权在 Azure 门户中管理服务，以及将用户分配到共同管理员角色。

### <a name="notification-contact"></a>通知联系人

通知联系人接收注册相关的使用情况通知。

以下部分介绍每个角色的限制和功能。

## <a name="user-limit-for-admin-roles"></a>管理员角色的用户限制

|角色| 用户限制|
|---|---|
|企业管理员|无限制|
|企业管理员（只读）|无限制|
| 分配给 SPN 的 EA 买方 | 无限制 |
|部门管理员|无限制|
|部门管理员（只读）|无限制|
|帐户所有者|每个帐户 1 个<sup>3</sup>|

<sup>3</sup> 每个帐户需要唯一的 Microsoft 帐户，或者工作或学校帐户。

## <a name="organization-structure-and-permissions-by-role"></a>组织结构和权限（按角色）

|任务| 企业管理员|企业管理员（只读）| EA 买方 | 部门管理员|部门管理员（只读）|帐户所有者| Partner|
|---|---|---|---|---|---|---|---|
|查看企业管理员|✔|✔| ✔|✘|✘|✘|✔|
|添加或删除企业管理员|✔|✘|✘|✘|✘|✘|✘|
|查看通知联系人<sup>4</sup> |✔|✔|✔|✘|✘|✘|✔|
|添加或删除通知联系人<sup>4</sup> |✔|✘|✘|✘|✘|✘|✘|
|创建并管理部门 |✔|✘|✘|✘|✘|✘|✘|
|查看部门管理员|✔|✔|✔|✔|✔|✘|✔|
|添加或删除部门管理员|✔|✘|✘|✔|✘|✘|✘|
|查看注册的帐户 |✔|✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|将帐户添加到注册并更改帐户所有者|✔|✘|✘|✔<sup>5</sup>|✘|✘|✘|
|购买预留项|✔|✘|✔|✘|✘|✘|✘|
|创建并管理订阅和订阅权限|✘|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> 将会向通知联系人发送有关 Azure 企业协议的电子邮件通信。
- <sup>5</sup> 任务局限于部门中的帐户。

## <a name="add-a-new-enterprise-administrator"></a>添加新的企业管理员

企业管理员拥有管理 Azure EA 注册的大部分特权。 设置 EA 协议时，会创建初始的 Azure EA 管理员。 但是，你随时可以添加或删除新管理员。 新管理员只能由现有的管理员添加。 有关添加更多企业管理员的详细信息，请参阅[创建另一个企业管理员](ea-portal-administration.md#create-another-enterprise-administrator)。有关计费配置文件角色和任务的详细信息，请参阅[计费配置文件角色和任务](understand-mca-roles.md#billing-profile-roles-and-tasks)。

## <a name="update-account-owner-state-from-pending-to-active"></a>将帐户所有者状态从“挂起”更新为“活动”

首次将新的帐户所有者 (AO) 添加到 Azure EA 注册时，其状态将显示为“挂起”。 当新帐户所有者收到激活欢迎电子邮件时，他们可以登录以激活其帐户。 激活帐户后，其帐户状态将从“挂起”更新为“可用” 。 帐户所有者需要阅读“警告”消息，然后选择“继续”。 系统可能会提示新用户输入其名字和姓氏来创建商务帐户。 如果出现此提示，他们必须添加所需的信息以继续，然后其帐户将会激活。

## <a name="add-a-department-admin"></a>添加部门管理员

Azure EA 管理员创建部门后，Azure 企业管理员可以添加部门管理员并将每个管理员关联到某个部门。 部门管理员可以创建新帐户。 需要添加新帐户才能创建 Azure EA 订阅。

有关添加部门管理员的详细信息，请参阅[创建 Azure EA 部门管理员](ea-portal-administration.md#add-a-department-administrator)。

## <a name="usage-and-costs-access-by-role"></a>按角色访问使用情况和成本

|任务| 企业管理员|企业管理员（只读）|EA 买方|部门管理员|部门管理员（只读） |帐户所有者| Partner|
|---|---|---|---|---|---|---|---|
|查看额度余额（包括 Azure 预付款）|✔|✔|✔|✘|✘|✘|✔|
|查看部门支出配额|✔|✔|✔|✘|✘|✘|✔|
|设置部门支出配额|✔|✘|✘|✘|✘|✘|✘|
|查看组织的 EA 价目表|✔|✔|✔|✘|✘|✘|✔|
|查看使用情况和成本详细信息|✔|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|管理 Azure 门户中的资源|✘|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> 要求企业管理员在企业门户中启用“DA 视图费用”策略。 然后，部门管理员就可以查看部门的成本详细信息。
- <sup>7</sup> 要求企业管理员在企业门户中启用“AO 视图费用”策略。 然后，帐户所有者就可以查看帐户的成本详细信息。

## <a name="see-pricing-for-different-user-roles"></a>请参阅不同用户角色的定价

可能会在 Azure 门户中看到不同的定价，具体取决于你的管理角色，以及企业管理员设置的视图费用策略。 企业门户中有下述两个策略，这两个策略影响你在 Azure 门户中看到的定价：

- DA 视图费用
- AO 视图费用

若要了解如何设置这些策略，请参阅[管理 Azure 账单信息的访问权限](manage-billing-access.md)。

下表显示了企业协议管理角色之间的关系、视图费用策略、Azure 门户中的 Azure 角色，以及在 Azure 门户中看到的定价。 企业管理员始终可以查看基于组织的 EA 定价的使用情况详细信息。 但是，部门管理员和帐户所有者看到的定价视图并不相同，具体取决于视图费用策略以及二者的 Azure 角色。 下表中列出的部门管理员角色是指部门管理员角色和部门管理员（只读）角色。

|企业协议管理员角色|角色的视图费用策略|Azure 角色|定价视图|
|---|---|---|---|
|帐户所有者或部门管理员|✔ 已启用|所有者|组织的 EA 定价|
|帐户所有者或部门管理员|✘ 已禁用|所有者|无定价|
|帐户所有者或部门管理员|✔ 已启用 |none|无定价|
|帐户所有者或部门管理员|✘ 已禁用 |none|无定价|
|无|不适用 |所有者|无定价|

在企业门户中设置企业管理员角色和视图费用策略。 Azure 角色可以在 Azure 门户中更新。 有关详细信息，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>后续步骤

- [管理对 Azure 账单信息的访问权限](manage-billing-access.md)
- [使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)
- [Azure 内置角色](../../role-based-access-control/built-in-roles.md)
