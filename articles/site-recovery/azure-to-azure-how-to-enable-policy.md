---
title: 使用 Azure Policy 为 VM 启用 Azure Site Recovery
description: 了解如何使用 Azure Site Recovery 启用策略支持来保护 VM。
author: rishjai-msft
ms.author: rishjai
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 1c936df9ffb467d732e0c07651e7c6fb31f28b5c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739044"
---
# <a name="using-policy-with-azure-site-recovery-public-preview"></a>将 Policy 和 Azure Site Recovery（公共预览版）结合使用

本文介绍如何使用 Azure Policy 为资源设置 [Azure Site Recovery](./site-recovery-overview.md)。 [Azure Policy](../governance/policy/overview.md) 有助于对 Azure 资源强制执行某些业务规则，并评估提及资源的合规性。

## <a name="disaster-recovery-with-azure-policy"></a>Azure Policy 的灾难恢复
Site Recovery 可帮助你在发生计划内或计划外的区域性故障时保持应用程序的正常运行。 通过 Azure 门户大规模地在计算机上启用 Site Recovery 可能很有难度。 现在，你可以通过门户一起在特定资源组（策略的范围）上启用 Site Recovery。

Azure Policy 解决了这一问题。 为资源组创建灾难恢复策略后，添加到资源组的所有新虚拟机都将自动为其启用 Site Recovery。 此外，对于资源组中已存在的所有虚拟机，你可以通过名为“修正”（详细信息见下方）的进程来启用 Site Recovery。

>[!NOTE]
>此策略的范围应为资源组级。

## <a name="prerequisites"></a>先决条件

- 在[此处](../governance/policy/assign-policy-portal.md)了解如何分配策略。
- 在[此处](./azure-to-azure-architecture.md)详细了解 Azure 到 Azure 灾难恢复的体系结构。
- 查看 Azure Site Recovery 策略支持的支持矩阵：

**方案** | **支持声明**
--- | ---
托管磁盘 | 支持
非托管磁盘  | 不支持
多个磁盘 | 支持
可用性集 | 支持
可用性区域 | 支持
已启用 Azure 磁盘加密 (ADE) 的 VM | 不支持
邻近放置组 (PPG) | 支持
已启用客户管理密钥 (CMK) 的磁盘 | 不支持
存储空间直通 (S2D) 群集 | 不支持
Azure 资源管理器部署模型 | 支持
经典部署模型 | 不支持
区域到区域 DR  | 支持
与 Azure 默认应用的其他策略（如果有）的互操作性 | 支持

>[!NOTE]
>在以下情况下，不会为其启用 Site Recovery。 但是，它们将在资源合规性中表现为不合规：
>1. 如果在策略范围内创建了不受支持的 VM。
>1. 如果 VM 是可用性集和 PPG 的一部分。

## <a name="create-a-policy-assignment"></a>创建策略分配
在本部分中，你将创建策略分配，以便为所有新建资源启用 Azure Site Recovery。
1. 转到“Azure 门户”，然后导航到“Azure Policy” 
1. 选择“Azure Policy”页左侧的“分配”。 分配即为在特定范围内分配策略以供执行。
   :::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assignments.png" alt-text="在“策略概览”页上选择“分配”页的屏幕截图。" border="false":::

1. 在“策略 - 分配”页的顶部选择“分配策略”。
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assign-policy.png" alt-text="在“分配”页上选择“分配策略”的屏幕截图。" border="false":::

1. 在“分配策略”页上，通过依次选择省略号、订阅和资源组，设置范围 。 范围用于确定对其强制执行策略分配的资源或资源组。 然后使用“范围”页底部的“选择”按钮。

1. 通过选择“策略定义”旁边的省略号，启动“策略定义选取器”。 搜索“通过启用复制来对虚拟机配置灾难恢复”，然后选择“策略”。
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-policy-definition.png" alt-text="从“基本信息”页选择“策略定义”的屏幕截图。" border="true":::

1. “分配名称”中自动填充了所选的策略名称，但可以更改它。 如果计划将多个 Azure Site Recovery 策略分配到同一范围，这可能会很有帮助。

1. 选择“下一步”以为策略配置 Azure Site Recovery 属性。

## <a name="configure-target-settings-and-properties"></a>配置目标设置和属性
你正要创建用于启用 Azure Site Recovery 的策略。 让我们立即配置目标设置和属性：
1. 你位于“分配策略”工作流的“参数”部分，如下所示：:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/specify-parameters.png" alt-text="从“参数”页设置参数的屏幕截图" border="true"::: 。
1. 为这些参数选择适当的值：
    - **源区域**：将应用策略的虚拟机的源区域。
    >[!NOTE]
    >此策略将应用于策略范围中属于源区域的所有虚拟机。 源区域中不存在的虚拟机将不会包含在资源合规性中。
    - **目标区域**：要在其中复制源虚拟机数据的位置。 Site Recovery 提供客户可复制到的目标区域列表。 我们建议使用与恢复服务保管库位置相同的位置。
    - **目标资源组**：所有可复制的虚拟机所属的资源组。 默认情况下，Site Recovery 会在目标区域中创建一个新的资源组，
    - **保管库资源组**：恢复服务保管库所在的资源组。
    - **恢复服务保管库**：可保护范围内所有 VM 的保管库。 如果需要，策略可代表你创建新的保管库。
    - **恢复虚拟网络**：选择目标区域中要用于恢复虚拟机的现有虚拟网络。 如果需要，策略也可为你创建新的虚拟网络。
    - **目标可用性区域**：输入虚拟机将进行故障转移的目标区域的可用性区域。
    >[!NOTE]
    >对于区域到区域场景，你需要选择与源区域相同的目标区域，并选择目标可用性区域中的其他可用性区域。     
    >如果资源组中的某些虚拟机已在目标可用性区域中，则不会将该策略应用到这些虚拟机，以防设置区域到区域 DR。
    - **效果**：启用或禁用策略执行。 选择“DeployIfNotExists”以在创建策略后立即启用策略。

1. 选择“下一步”以决定修正任务。

## <a name="remediation-and-other-properties"></a>修正和其他属性
1. Azure Site Recovery 的目标属性已配置。 但是，此策略将仅对策略范围中新创建的虚拟机生效。 在分配策略后，可以通过修正任务将其应用于现有资源。 可以通过选中“创建修正任务”复选框来创建修正任务。

1. Azure Policy 将创建一个[托管标识](../governance/policy/how-to/remediate-resources.md)，该标识将拥有所有者权限，可为范围中的资源启用 Azure Site Recovery。

1. 可以在“非合规消息”选项卡上为策略配置自定义的非合规消息。

1. 选择页面底部的“下一步”或页面顶部的“查看 + 创建”选项卡，转到分配向导的下一部分。

1. 查看选中的选项，然后在页面底部选择“创建”。

## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-test-failover-to-azure.md)如何运行测试故障转移。
