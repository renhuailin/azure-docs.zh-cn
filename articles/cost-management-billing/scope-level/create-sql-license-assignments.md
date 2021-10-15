---
title: 为 Azure 混合权益创建 SQL Server 许可证分配
description: 本文介绍如何为 Azure 混合权益创建 SQL Server 许可证分配。
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisri
ms.openlocfilehash: 48b68260e07d6e69e41daeacae631a9415f48a65
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547219"
---
# <a name="create-sql-server-license-assignments-for-azure-hybrid-benefit"></a>为 Azure 混合权益创建 SQL Server 许可证分配

Azure 门户中的新 Azure 混合权益体验支持帐户级别或特定订阅级别的 SQL Server 许可证分配。 在帐户级别创建分配时，Azure 混合权益折扣将自动应用于该帐户中所有订阅中的 SQL 资源，最多为分配中指定的许可证计数。

对于每个许可证分配，将选择一个范围，然后将许可证分配给该范围。 每个范围可以有多个许可证条目。

## <a name="prerequisites"></a>先决条件

必须满足以下先决条件才能创建 SQL Server 许可证分配。

- 你的组织具有受支持的协议类型和受支持的产品/服务。
- 你是有权分配 SQL 许可证的角色成员。
- 你的组织拥有具有软件保障的 SQL Server 核心许可证，或可分配给 Azure 的核心订阅许可证。
- 组织已注册将 Azure SQL VM 自动注册到 SQL IaaS 代理扩展。 若要了解更多信息，请参阅[自动注册到 SQL IaaS 代理扩展](../../azure-sql/virtual-machines/windows/sql-agent-extension-automatic-registration-all-vms.md)。
  > [!IMPORTANT]
  > 如果不满足此先决条件，Azure 将生成有关当前 Azure 混合权益使用情况的不完整数据。 这种情况可能会导致许可证分配不正确，并可能产生不必要的 SQL Server 许可证即用即付费用。

先决条件角色因协议类型不同而有所不同。

| 协议类型 | 所需角色 | 受支持的产品/服务 |
| --- | --- | --- |
| 企业协议 | _企业管理员_<p> 若要详细了解如何成为角色成员，请参阅[添加另一个企业管理员](../manage/ea-portal-administration.md#add-a-department-administrator)。 | - MS-AZR-0017P (Microsoft Azure Enterprise)<br>- MS-AZR-USGOV-0017P (Azure Government Enterprise) |
| Microsoft 客户协议 | *计费帐户所有者*<br> *计费帐户参与者* <br> *计费配置文件所有者*<br> *计费配置文件参与者*<br> 若要详细了解如何成为角色成员，请参阅[管理计费角色](../manage/understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。 | MS-AZR-0017G (Microsoft Azure Plan)|
| WebDirect/即用即付 | 不可用 | 无 |
| CSP/合作伙伴引导的客户 | 不可用 | 无 |

> [!NOTE]
> 范围级许可证体验不适用于赞助订阅、MSDN 额度订阅或 MPN 订阅。 开发/测试订阅可免费使用 SQL 软件（MS-AZR-0148P 或 MS-AZR-0023P 产品/服务类型）。

## <a name="create-a-sql-license-assignment"></a>创建 SQL 许可证分配

在下面的过程中，从“成本管理 + 计费”导航到“预留 + 混合权益”。 请勿从 Azure 主页导航到“预留”。 如果这样做，你将没有查看许可证分配体验所需的范围。 

1. 登录到 Azure 门户，导航到“成本管理 + 计费”。  
    :::image type="content" source="./media/create-sql-license-assignments/select-cost-management.png" alt-text="显示在 Azure 门户中导航到“成本管理 + 计费”的屏幕截图。" lightbox="./media/create-sql-license-assignments/select-cost-management.png" :::
 2. 如果你签订了企业协议，请选择计费范围。  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-scope.png" alt-text="显示 EA 计费范围选择的屏幕截图。" lightbox="./media/create-sql-license-assignments/select-billing-scope.png" :::
 3. 如果你签订了 Microsoft 客户协议，请选择计费对象信息。  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-profile.png" alt-text="显示计费对象信息选择的屏幕截图。" lightbox="./media/create-sql-license-assignments/select-billing-profile.png" :::
 4. 在左侧菜单中，选择“预留 + 混合权益”。  
    :::image type="content" source="./media/create-sql-license-assignments/select-reservations.png" alt-text="显示“预留 + 混合权益”选择的屏幕截图。"  :::
 5. 选择“添加”，然后在列表中选择“Azure 混合权益(预览版)”。  
    :::image type="content" source="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" alt-text="显示 Azure 混合权益选择的屏幕截图。" lightbox="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" :::
 6. 在下一个屏幕上，选择“开始分配许可证”。  
    :::image type="content" source="./media/create-sql-license-assignments/get-started-centralized.png" alt-text="显示“添加 SQL 混合权益”选择的屏幕截图" lightbox="./media/create-sql-license-assignments/get-started-centralized.png" :::
 7. 选择一个范围，然后输入要用于每个 SQL Server 版本的许可证计数。 如果没有为特定版本分配任何许可证，请输入零。  
    > [!NOTE]
    > 你负责确定你在范围级别托管许可证体验中输入的条目是准确的，并且将履行许可义务。 将显示许可证使用情况信息，以帮助你进行许可证分配。 但是，由于各种因素，显示的信息可能不完整或不准确。
    >
    > 如果输入的许可证数小于当前使用的许可证数，则会看到一条警告消息，内容为“你输入的许可证数小于当前用于此范围内的 Azure 混合权益的数量。此范围的帐单将增加。”  
    
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition.png" alt-text="显示范围选择和许可证数量的屏幕截图。" lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition.png" :::
 8. （可选）选择“使用情况详细信息”选项卡，以查看在资源范围内启用的当前 Azure 混合权益使用情况。  
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" alt-text="显示“使用情况”选项卡详细信息的屏幕截图。" lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" :::
 9. 选择“添加”。
10. （可选）更改默认许可证分配名称。 查看日期自动设置为提前一年，无法更改。 其用途是提醒你定期查看许可证分配。  
    :::image type="content" source="./media/create-sql-license-assignments/license-assignment-commit.png" alt-text="显示默认许可证分配名称的屏幕截图。" lightbox="./media/create-sql-license-assignments/license-assignment-commit.png" :::
11. 查看选择后，选择“下一步:查看 + 应用”。
12. 选择“通过选择‘应用’证明”选项以确认你有权应用 Azure 混合权益，具有足够的 SQL Server 许可证，并且只要分配了许可证，就会保留这些许可证。  
    :::image type="content" source="./media/create-sql-license-assignments/confirm-apply-attestation.png" alt-text="显示证明选项的屏幕截图。" lightbox="./media/create-sql-license-assignments/confirm-apply-attestation.png" :::
13. 依次选择“应用”、“是”。 
14. 显示已分配许可证的列表。  
    :::image type="content" source="./media/create-sql-license-assignments/assigned-licenses.png" alt-text="显示已分配许可证列表的屏幕截图。" lightbox="./media/create-sql-license-assignments/assigned-licenses.png" ::: 

## <a name="track-assigned-license-use"></a>跟踪分配的许可证使用

导航到“成本管理 + 计费”，然后选择“预留 + 混合权益” 。

将显示所有预留和许可证分配的列表。 如果要在结果中筛选出仅许可证分配，请为“SQL 混合权益”设置筛选器。

:::image type="content" source="./media/create-sql-license-assignments/view-the-assignments.png" alt-text="显示预留和许可证列表的屏幕截图。" lightbox="./media/create-sql-license-assignments/view-the-assignments.png" :::

在“最后一天利用率”或“7 天利用率”下，选择一个可能为空白或零的百分比，以查看分配使用情况历史记录的详细信息。

:::image type="content" source="./media/create-sql-license-assignments/assignment-utilization-view.png" alt-text="显示分配使用情况详细信息的屏幕截图。" lightbox="./media/create-sql-license-assignments/assignment-utilization-view.png" :::

如果许可证分配的使用量为 100%，则该范围内某些资源可能会产生 SQL Server 即用即付费用。 建议再次使用许可证分配体验来查看分配的许可证涵盖的使用量。 之后，请执行与之前相同的过程，包括咨询采购部门或软件资产管理部门、确认有更多许可证可用并分配许可证。

## <a name="changes-after-license-assignment"></a>许可证分配后的更改

创建 SQL 许可证分配后，Azure 门户中的 Azure 混合权益体验会发生变化。

- 为单个 SQL 资源配置的任何现有 Azure 混合权益选择不再适用。 它们将被替换为在订阅或帐户级别创建的 SQL 许可证分配。
- 混合权益选项不会显示在 SQL 资源配置中。
- 以编程方式配置混合权益的应用程序或脚本将继续工作，但设置不会产生任何作用。
- SQL 软件折扣适用于该范围内的 SQL 资源。 范围基于为创建资源的帐户的订阅创建的许可证分配中的许可证数。
- 如果其他资源使用所有许可证，为混合权益配置的特定资源可能无法获得折扣。 但是，最大折扣根据许可证计数应用于范围。 有关如何应用折扣的详细信息，请参阅[什么是 Azure 混合权益的应用范围级别管理？](overview-azure-hybrid-benefit-scope.md)。

## <a name="cancel-a-license-assignment"></a>取消许可证分配

在取消许可证分配之前，请查看许可证情况。 取消许可证分配时，将不再获得这些许可证的折扣。 因此，Azure 帐单可能会增加。 如果取消剩下的最后一个许可证分配，Azure 混合权益管理将还原到单个资源级别。

### <a name="to-cancel-a-license-assignment"></a>取消许可证分配

1. 在预留和许可证分配列表中，选择许可证分配。
1. 在许可证分配详细信息页上，选择“取消”。  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment-symbol.png" alt-text="显示“取消”选项的屏幕截图。" :::
1. 在“取消”页上，查看通知，然后选择“是，取消”。  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment.png" alt-text="显示“取消”页的屏幕截图。" lightbox="./media/create-sql-license-assignments/cancel-assignment.png" :::

## <a name="next-steps"></a>后续步骤

- 查看[范围级别 Azure 混合权益管理常见问题解答](faq-azure-hybrid-benefit-scope.yml)。
- 若要了解如何应用折扣，请参阅[什么是 Azure 混合权益的应用范围级别管理？](overview-azure-hybrid-benefit-scope.md)。