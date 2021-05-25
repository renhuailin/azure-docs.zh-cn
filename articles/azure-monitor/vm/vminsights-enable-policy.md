---
title: 使用 Azure Policy 启用 VM 见解
description: 介绍如何使用 Azure Policy 为多个 Azure 虚拟机或虚拟机规模集启用 VM 见解。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 51baf009543208fbbfe091238d0215a24761641d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031950"
---
# <a name="enable-vm-insights-by-using-azure-policy"></a>使用 Azure Policy 启用 VM 见解
本文介绍如何使用 Azure Policy 为 Azure 虚拟机或连接 Azure Arc（预览版）的混合虚拟机启用 VM 见解。 Azure Policy 允许用户分配策略定义，以便在 Azure 环境中安装所需的 VM 见解代理，并在创建每个虚拟机时自动为 VM 启用监视。 VM 见解提供帮助用户发现环境中不合规的 VM 并进行修正的功能。 请使用该功能，而不要直接使用 Azure Policy。

如果不熟悉 Azure Policy，请通过[使用 Azure Policy 大规模部署 Azure Monitor](../deploy-scale.md) 获取简介。

> [!NOTE]
> 要对 Azure 虚拟机规模集使用 Azure Policy，或直接对 Azure 虚拟机启用 Azure Policy，请参阅[使用 Azure Policy 大规模部署 Azure Monitor](../deploy-scale.md#vm-insights)。

## <a name="prerequisites"></a>先决条件
- [创建和配置 Log Analytics 工作区](./vminsights-configure-workspace.md)。
- 请参阅[支持的操作系统](./vminsights-enable-overview.md#supported-operating-systems)，以确保要启用的虚拟机或虚拟机规模集的操作系统受支持。 


## <a name="vm-insights-initiative"></a>VM 见解计划
VM 见解提供内置策略定义，以便在 Azure 虚拟机上安装 Log Analytics 代理和依赖项代理。 “启用 VM 见解”计划包括其中每项策略定义。 将此计划分配给管理组、订阅或资源组，可在该范围内的任何 Windows 或 Linux Azure 虚拟机上自动安装代理。

## <a name="open-policy-coverage-feature"></a>打开策略覆盖范围功能
要访问“VM 见解策略覆盖范围”，请在 Azure 门户的“Azure Monitor”菜单中找到“虚拟机”。 选择“其他载入选项”，然后在“启用使用策略”下选择“启用”。

[![用于 VM 的 Azure Monitor 的“开始”选项卡](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>创建新分配
如果还没有分配，可通过单击“分配策略”来创建一个新分配。

[![创建分配](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

该页面与在 Azure Policy 中分配计划的页面相同，只不过它使用所选范围以及“启用 VM 见解”计划定义进行硬编码。 可以选择更改“分配名称”，并添加“说明”。 如果要对该范围提供排除项，请选择“排除”。 例如，范围可能是管理组，可以指定从分配中排除该管理组中的某个订阅。

[![分配计划](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

在“参数”页上，选择一个要供分配中所有虚拟机使用的“Log Analytics 工作区”。 如果要为不同的虚拟机指定不同的工作区，则必须创建多个分配，每个分配都有自己的范围。 

   > [!NOTE]
   > 如果工作区超出分配范围，则将 *Log Analytics 参与者* 权限授予策略分配的主体 ID。 如果不这样做，可能会看到部署失败，例如 `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![工作区](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

在单击“创建”创建分配前，可单击“查看 + 创建” 来查看分配的详细信息。 这时请勿创建修正任务，因为很可能需要执行多个修正任务，才能启用现有的虚拟机。 请参阅以下[修正合规性结果](#remediate-compliance-results)。

## <a name="review-compliance"></a>查看合规性
创建分配后，可以在管理组和订阅中查看和管理“启用 VM 见解”计划的覆盖范围。 该信息显示每个管理组或订阅中存在多少个虚拟机，以及其合规性状态。

[![“VM 见解管理策略”页](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


下表提供此视图中的信息说明。

| 函数 | 说明 | 
|----------|-------------| 
| **范围** | 你拥有的或者继承了其访问权限的管理组和订阅，可以通过管理组层次结构向下钻取。|
| **角色** | 用户在该范围内的角色，可能是读者、所有者或参与者。 如果用户有权访问订阅但无权访问其所属的管理组，则此项为空。 此角色决定用户可以查看的数据和可执行的操作 - 分配策略或计划（所有者）、编辑它们或查看合规性。 |
| **VM 总数** | 该范围中的 VM 总数，无论状态如何。 对于管理组，这是指嵌套在订阅或子管理组下的 VM 数之和。 |
| **分配覆盖范围** | 计划覆盖的 VM 百分比。 |
| **分配状态** | 成功 - 该范围内的所有 VM 上都部署了 Log Analytics 和依赖项代理。<br>警告 - 订阅不在管理组下。<br>未启动 -已添加新分配。<br>锁定 - 对于管理组没有足够的特权。<br>空白 -不存在 VM 或未分配策略。 |
| **合规的 VM 数** | 合规的 VM 数，指同时安装了 Log Analytics 代理和依赖项代理的 VM 数。 如果没有分配、范围中没有 VM 或权限不正确，则此值为空。 |
| **遵从性** | 整体合规性是不同合规资源的总和除以所有唯一资源。 |
| **符合性状态** | 合规 - 范围内虚拟机中所有已同时部署 Log Analytics 和依赖项代理的 VM 数，或范围中需要该分配但尚未接受评估的任何新 VM 数。<br>不合规 - 已评估但未启用和可能需要修正的 VM。<br>未启动 -已添加新分配。<br>锁定 - 对于管理组没有足够的特权。<br>空白 - 未分配策略。  |


分配计划时，在分配中选择的范围可以是列出的范围或其子集。 例如，你可能为某个订阅创建了分配（策略范围），但没有为管理组创建分配（覆盖范围）。 这种情况下，“分配覆盖范围”的值表示计划范围内的 VM 数除以覆盖范围内的 VM 数。 另举一例。你可能从策略范围中排除了一些 VM、资源组或订阅。 如果该值为空白，表示策略或计划不存在，或者你没有权限。 “分配状态”下会提供信息。


## <a name="remediate-compliance-results"></a>修正合规性结果
计划在创建或修改后将应用到虚拟机，但不会应用到现有 VM。 如果分配显示并非 100% 合规，请创建修正任务来评估和启用现有 VM，通过选择省略号 (...) 来选择“查看合规性”。

[![查看合规性](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

“合规性”页将列出与指定筛选器匹配的分配，以及它们是否合规。 单击某个分配可查看其详细信息。

[![Azure VM 的策略合规性](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

“计划合规性”页将列出计划中的策略定义，以及每项是否合规。

[![合规性详细信息](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

单击某个策略定义可查看其详细信息。 策略定义显示不合规的方案包括：

* 未部署 Log Analytics 代理或依赖项代理。 创建修正任务进行缓解。
* VM 映像 (OS) 未在策略定义中识别。 部署策略标准仅包含通过已知 Azure VM 映像部署的 VM。 请查看文档，了解 VM OS 是否受支持。
* VM 未登录到指定的 Log Analytics 工作区。 计划范围中的某些 VM 连接到的 Log Analytics 工作区并非策略分配中指定的 Log Analytics 工作区。

[![策略合规性详细信息](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

要创建修正任务来缓解合规性问题，请单击“创建修正任务”。 

[![新建修正任务](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

单击“修正”可创建修正任务，然后单击“修正”可对其启动。 很可能需要创建多个修正任务，每个策略定义各有一个。 不能为计划创建修正任务。

[![显示“监视器 | 虚拟机”的“策略修正”窗格的屏幕截图。](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


修正任务完成后，则 VM 应符合为 VM 见解安装和启用的代理。 

## <a name="next-steps"></a>后续步骤

现在已为虚拟机启用监视，这些信息可供 VM 见解用于分析目的。 

- 要查看已发现的应用程序依赖项，请参阅[查看 VM 见解地图](vminsights-maps.md)。 
- 若要通过 VM 的性能了解瓶颈和整体利用率，请参阅[查看 Azure VM 性能](vminsights-performance.md)。
