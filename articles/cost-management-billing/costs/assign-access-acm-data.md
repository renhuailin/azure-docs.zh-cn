---
title: 分配对 Azure 成本管理数据的访问权限
description: 本文介绍如何分配不同访问范围的 Azure 成本管理数据访问权限。
author: bandersmsft
ms.author: banders
ms.date: 06/27/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: 9ee0f7f854363389f3e6594946411f05b1b8b790
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988690"
---
# <a name="assign-access-to-cost-management-data"></a>分配对成本管理数据的访问权限

具有 Azure Enterprise 协议的用户在 Azure 门户和企业 (EA) 门户中被授予了一些权限的组合，这些权限定义了用户对 Azure 成本管理数据的访问级别。 对于其他 Azure 帐户类型的用户，使用 Azure 基于角色的访问控制 (Azure RBAC) 来定义用户对成本管理数据的访问级别更为简单。 本文介绍如何分配对成本管理数据的访问权限。 为用户分配权限组合后，该用户可以根据其访问范围及其在 Azure 门户中选择的范围查看成本管理中的数据。

用户所选的范围将用于整个成本管理，以提供数据整合并控制对成本信息的访问。 使用范围时，用户不要多选它们。 而应选择一个子范围要汇总到的较大范围，然后筛选到要查看的范围。 理解数据整合很重要，因为有些人不应该访问子范围所属的父范围。

观看视频 [Cost Management controlling access](https://www.youtube.com/watch?v=_uQzQ9puPyM)（成本管理控制访问），了解如何通过 Azure 基于角色的访问控制 (Azure RBAC) 分配查看成本和费用的访问权限。 若要观看其他视频，请访问[成本管理 YouTube 频道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>成本管理范围

成本管理支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。 帐户的类型确定可用范围。

### <a name="azure-ea-subscription-scopes"></a>Azure EA 订阅范围

若要查看 Azure EA 订阅的成本数据，用户必须至少具有以下一个或多个范围的读取权限。

| **范围** | **定义位置** | **查看数据所需的访问权限** | **先决条件 EA 设置** | **将数据合并到** |
| --- | --- | --- | --- | --- |
| 计费帐户<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | 企业管理员 | 无 | 企业协议中的所有订阅 |
| 部门 | [https://ea.azure.com](https://ea.azure.com/) | 部门管理员 | **DA 视图费用** 已启用 | 属于一个合约帐户的所有订阅，该帐户已关联到部门 |
| 合约帐户<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | 帐户所有者 | **AO 视图费用** 已启用 | 注册帐户的所有订阅 |
| 管理组 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理读取者（或参与者） | **AO 视图费用** 已启用 | 管理组下的所有订阅 |
| 订阅 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理读取者（或参与者） | **AO 视图费用** 已启用 | 订阅中的所有资源/资源组 |
| 资源组 | [https://portal.azure.com](https://portal.azure.com/) | 成本管理读取者（或参与者） | **AO 视图费用** 已启用 | 资源组中的所有资源 |

<sup>1</sup> 计费帐户还称为“企业协议”或“合约”。

<sup>2</sup> 合约帐户还称为“帐户所有者”。


## <a name="other-azure-account-scopes"></a>其他 Azure 帐户范围

若要查看其他 Azure 订阅的成本数据，用户必须至少具有以下一个或多个范围的读取权限：

- 管理组
- 订阅
- 资源组

在合作伙伴将客户加入 Microsoft 客户协议后，将有各种范围可用。 在 CSP 合作伙伴为其 CSP 客户启用成本管理功能后，客户即可使用该功能。 有关详细信息，请参阅[面向合作伙伴的 Azure 成本管理入门](get-started-partners.md)。

## <a name="enable-access-to-costs-in-the-azure-portal"></a>启用对 Azure 门户中的成本的访问

部门范围要求将“部门管理员可以查看费用”（“DA 查看费用”）选项设置为“打开”。   请在 Azure 门户或 EA 门户中配置该选项。 其他所有范围要求将“帐户所有者可以查看费用”（“AO 查看费用”）选项设置为“打开”。  

若要在 Azure 门户中启用选项：

1. 使用企业管理员帐户通过 https://portal.azure.com 登录到 Azure 门户。
1. 选择“成本管理 + 计费”菜单项。 
1. 选择“计费范围”查看可用计费范围和计费帐户的列表。 
1. 从可用计费帐户列表中选择你的 **计费帐户**。
1. 在“设置”下选择“策略”菜单项，然后配置设置。    
    ![显示“查看费用”选项的计费范围策略](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

启用视图费用选项后，大多数范围还需要 Azure 门户中的 Azure 基于角色的访问控制 (Azure RBAC) 权限配置。

## <a name="enable-access-to-costs-in-the-ea-portal"></a>启动对 EA 门户中的成本的访问

部门范围需要在 EA 门户中启用“DA 视图费用”选项   。 请在 Azure 门户或 EA 门户中配置该选项。 所有其他范围需要在 EA 门户中启用“AO 视图费用”选项   。

若要在 EA 门户中启用选项：

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”  。
3. 为想提供访问权限的成本管理范围启用“DA 视图费用”和“AO 视图费用”的费用选项   。  
    ![显示 DA 和 AO 视图费用选项的“合约”选项卡](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

启用视图费用选项后，大多数范围还需要 Azure 门户中的 Azure 基于角色的访问控制 (Azure RBAC) 权限配置。

## <a name="enterprise-administrator-role"></a>企业管理员角色

默认情况下，企业管理员可以访问计费帐户（企业协议/注册）和所有其他范围（子范围）。 企业管理员为其他用户分配对这些范围的访问权限。 考虑到业务连续性，最好始终保持有两位用户具有企业管理员访问权限。 以下部分提供示例来演示企业管理员如何为其他用户分配对这些范围的访问权限。

## <a name="assign-billing-account-scope-access"></a>分配计费帐户范围的访问权限

要访问计费帐户范围，需要在 EA 门户中具备企业管理员权限。 企业管理员可以查看整个 EA 注册或多个注册的成本。 对于计费帐户范围，在 Azure 门户中不需要任何操作。

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”  。
3. 在“合约”选项卡中选择要管理的合约  。  
    ![在 EA 门户中选择注册](./media/assign-access-acm-data/ea-portal.png)
4. 选择“+ 添加管理员”。 
5. 在“添加管理员”框中选择身份验证类型并键入用户的电子邮件地址。
6. 如果要向用户授予对成本和使用情况数据的只读访问权限，请在“只读”下选择“是”   。  否则请选择“否”  。
7. 选择“添加”以创建帐户。   
    ![在“添加管理员”框中显示的示例信息](./media/assign-access-acm-data/add-admin.png)

可能在 30 分钟以后，该新用户才能访问成本管理中的数据。

### <a name="assign-department-scope-access"></a>分配部门范围访问权限

若要访问部门范围，需要在 EA 门户中具备部门管理员（DA 视图费用）访问权限。 部门管理员可以查看与一个或多个部门关联的成本和使用情况数据。 部门数据包含属于关联至该部门的合约帐户的所有订阅。 在 Azure 门户中不需要任何操作。

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”  。
3. 在“合约”选项卡中选择要管理的合约  。
4. 选择“部门”选项卡，然后选择“添加管理员”。  
5. 在“添加部门管理员”框中选择身份验证类型并键入用户的电子邮件地址。
6. 如果要向用户授予对成本和使用情况数据的只读访问权限，请在“只读”下选择“是”   。  否则请选择“否”  。
7. 选择要授予其部门管理权限的部门。
8. 选择“添加”以创建帐户。   
    ![在“添加部门管理员”框中输入所需的信息](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>分配合约帐户范围的访问权限

若要访问合约范围，需要在 EA 门户中具备帐户所有者（AO 视图费用）访问权限。 帐户所有者可以查看与从该合约帐户创建的订阅关联的成本和使用情况数据。 在 Azure 门户中不需要任何操作。

1. 使用企业管理员帐户在 [https://ea.azure.com](https://ea.azure.com) 登录到 EA 门户。
2. 在左窗格中选择“管理”  。
3. 在“合约”选项卡中选择要管理的合约  。
4. 选择“帐户”选项卡，然后选择“添加帐户”。  
5. 在“添加帐户”框中，选择帐户要关联到的“部门”，或将其保留为未分配状态  。
6. 选择身份验证类型并键入帐户名。
7. 键入用户的电子邮件地址，然后根据需要键入成本中心。
8. 选择“添加”以创建帐户。   
    ![在注册帐户的“添加帐户”框中输入所需信息](./media/assign-access-acm-data/add-account.png)

完成上述步骤后，用户帐户将成为企业门户中的合约帐户并且可以创建订阅。 用户可以访问他们创建的订阅的成本和使用情况数据。

## <a name="assign-management-group-scope-access"></a>分配管理组范围的访问权限

若要查看管理组范围，至少需要成本管理读取者（或读取者）权限。 可以在 Azure 门户中配置对管理组的访问权限。 你必须至少对管理组具有“用户访问权限管理员”（或“所有者”）权限才能为其他人启用访问权限。 此外对于 Azure EA 帐户，还必须在 EA 门户中启用“AO 视图费用”设置  。


- 向管理组范围内的用户分配成本管理读取器（或读取者）角色。  
     有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

## <a name="assign-subscription-scope-access"></a>分配订阅范围的访问权限

若要访问订阅，至少需要成本管理读取者（或读取者）权限。 可以在 Azure 门户中配置对订阅的访问权限。 你必须至少对订阅具有“用户访问权限管理员”（或“所有者”）权限才能为其他人启用访问权限。 此外对于 Azure EA 帐户，还必须在 EA 门户中启用“AO 视图费用”设置  。

- 向订阅范围内的用户分配成本管理读取器（或读取者）角色。  
     有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

## <a name="assign-resource-group-scope-access"></a>分配资源组范围的访问权限

若要访问资源组，至少需要成本管理读取者（或读取者）权限。 可以在 Azure 门户中配置对资源组的访问权限。 你必须至少对资源组具有“用户访问权限管理员”（或“所有者”）权限才能为其他人启用访问权限。 此外对于 Azure EA 帐户，还必须在 EA 门户中启用“AO 视图费用”设置  。


- 向资源组范围内的用户分配成本管理读取器（或读取者）角色。  
     有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

## <a name="cross-tenant-authentication-issues"></a>跨租户身份验证问题

目前，Azure 成本管理对跨租户身份验证提供有限的支持。 在某些情况下，当你尝试跨租户进行身份验证时，可能会在成本分析中收到“访问被拒绝”  错误。 如果你为另一租户的订阅配置 Azure 基于角色的访问控制 (Azure RBAC)，然后尝试查看成本数据，则可能会出现此问题。

*若要解决此问题，请执行以下操作*：在配置跨租户 Azure RBAC 后，请等待一小时。 然后，尝试在两个租户中查看成本分析中的成本或者向用户授予成本管理访问权限。  


## <a name="next-steps"></a>后续步骤

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
