---
title: 概念 - Azure VMware 解决方案中的运行命令（预览版）
description: 了解如何使用 Azure VMware 解决方案中的运行命令。
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: a4121f8479b22eb1c0666a1e7d7a23ece4fb3d20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668842"
---
# <a name="run-command-in-azure-vmware-solution-preview"></a>Azure VMware 解决方案中的运行命令（预览版）

在 Azure VMware 解决方案中，vCenter 具有名为 cloudadmin 的内置本地用户，并为该用户分配了 CloudAdmin 角色。 CloudAdmin 角色具有与其他 VMware 云解决方案和本地部署中不同的 vCenter [特权](concepts-identity.md#view-the-vcenter-privileges)。 通过“运行命令”功能（预览版），可执行通常需要通过 PowerShell cmdlet 集合获取提升的权限的操作。 

Azure VMware 解决方案支持以下操作：

- [配置外部标识源](configure-identity-source-vcenter.md)

- [查看和设置存储策略](configure-storage-policy.md) 

- [使用 JetStream 部署灾难恢复](deploy-disaster-recovery-using-jetstream.md)


>[!NOTE]
>运行命令按提交的顺序一次执行一个。

## <a name="view-the-status-of-an-execution"></a>查看执行的状态

可查看已执行的任何运行命令的状态，包括 cmdlet 的输出、错误、警告和信息日志。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“运行命令” > “运行执行状态”。

   可选择列，按不同的列进行排序。  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="显示“运行执行状态”选项卡的屏幕截图。" lightbox="media/run-command/run-execution-status.png":::

1. 选择要查看的执行。 这会打开一个窗格，其中有执行详细信息，还有其他选项卡显示 cmdlet 生成的不同输出类型。

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="显示运行执行示例的屏幕截图。":::

   可以查看有关执行的更多详细信息，包括输出、错误、警告和信息。

   - **详细信息** - 执行详细信息的摘要，例如名称、状态、包、cmdlet 名称和命令失败时显示的错误。 

   - **输出** -cmdlet 输出的消息。 可能包含操作的进度或结果。 并非所有 cmdlet 都有输出。

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="显示运行执行输出的屏幕截图。":::

   - **错误** - 在执行 cmdlet 的过程中生成的错误消息。 这是对细节窗格中的终止错误消息的补充。    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="显示在执行过程中检测到的错误的屏幕截图。":::

   - **警告** -执行期间生成的警告消息。 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="显示在执行过程中检测到的警告的屏幕截图。":::

   - **信息** - 进度和 cmdlet 执行期间诊断生成的消息。 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="显示 cmdlet 在运行时的总体实时进度的屏幕截图。":::



## <a name="cancel-or-delete-a-job"></a>取消或删除作业



### <a name="method-1"></a>方法 1

此方法会尝试取消执行，然后在完成时删除它。

>[!IMPORTANT]
>方法 1 是不可逆的。

1. 选择“运行命令” > “运行执行状态”，然后选择要取消的作业。

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-1.png" alt-text="显示如何取消和删除运行命令的屏幕截图。":::

2. 选择“是”以取消并删除所有用户的作业。



### <a name="method-2"></a>方法 2

1. 选择“运行命令” > “包” > “运行执行状态”。

2. 选择要取消和删除的作业的“更多(...)”。

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-2.png" alt-text="显示如何使用省略号取消和删除运行命令的屏幕截图。":::

3. 选择“是”以取消并删除所有用户的作业。



## <a name="next-steps"></a>后续步骤

现在，你已经了解了运行命令的概念，可以使用运行命令功能来执行以下操作：

- [配置存储策略](configure-storage-policy.md) - 部署到 vSAN 数据存储的每个虚拟机 (VM) 都分配有一个 vSAN 存储策略。 可在 VM 的初始部署中或在执行其他 VM 操作（例如克隆或迁移）时分配 vSAN 存储策略。

- [为 vCenter 配置外部标识源（运行命令）](configure-identity-source-vcenter.md)- 为 vCenter 配置 Active Directory over LDAP/LDAPS，使能够将外部标识源用作 Active Directory。 然后，可将来自外部标识源的组添加到 CloudAdmin 角色。

- [使用 JetStream 部署灾难恢复](deploy-disaster-recovery-using-jetstream.md) - 将数据直接存储到 vSAN 中的恢复群集。 数据是通过在 vSphere 中运行的 I/O 筛选器捕获的。 基础数据存储可以是 VMFS、VSAN、vVol 或任何 HCI 平台。 