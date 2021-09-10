---
title: 概念 - Azure VMware 解决方案中的运行命令
description: 了解如何使用 Azure VMware 解决方案中的运行命令。
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 28cbf76dd035ce9b04909a61e3001063aa151162
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310665"
---
# <a name="run-commands-in-azure-vmware-solution"></a>Azure VMware 解决方案中的运行命令

在 Azure VMware 解决方案中，你将使用 CloudAdmin 角色获取 vCenter 访问权限。 可以在 Azure VMware 解决方案私有云 vCenter 上[查看授予 Azure VMware 解决方案 CloudAdmin 角色的特权](concepts-identity.md#view-the-vcenter-privileges)。 运行命令是 PowerShell cmdlet 的集合，你在 vCenter 上使用这些 cmdlet 执行某些操作时，需要提升的权限。 

Azure VMware 解决方案支持以下操作：

- [安装和卸载 JetStream DR 解决方案](deploy-disaster-recovery-using-jetstream.md)

- [配置外部标识源](configure-identity-source-vcenter.md)

- [查看和编辑存储策略](configure-storage-policy.md) 


>[!NOTE]
>运行命令按提交的顺序一次执行一个。

## <a name="view-the-status-of-an-execution"></a>查看执行的状态

可以查看已执行的任何运行命令的状态，包括输出、错误、警告和信息。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“运行命令” > “运行执行状态”。

   可以按执行名称、包名称、包版本、命令名称、开始时间、结束时间和状态进行排序。  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="显示“运行执行状态”选项卡的屏幕截图。" lightbox="media/run-command/run-execution-status.png":::

1. 选择要查看的执行。

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="显示运行执行示例的屏幕截图。":::

   可以查看有关执行的更多详细信息，包括输出、错误、警告和信息。

   - 详细信息 - 执行详细信息的摘要，如名称、状态、包和运行的命令名称。 

   - 输出 - 成功结束 cmdlet 的执行时显示的消息。 并非所有 cmdlet 都有输出。

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="显示运行执行输出的屏幕截图。":::

   - 错误 - 停止了 cmdlet 执行的终止异常。    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="显示在执行过程中检测到的错误的屏幕截图。":::

   - 警告 - 在 cmdlet 的执行过程中发生的非终止异常。 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="显示在执行过程中检测到的警告的屏幕截图。":::

   - 信息 - 在 cmdlet 的执行过程中显示的进度消息。 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="显示 cmdlet 在运行时的总体实时进度的屏幕截图。":::



## <a name="cancel-or-delete-a-job"></a>取消或删除作业



### <a name="method-1"></a>方法 1

>[!NOTE]
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

- [配置存储策略](configure-storage-policy.md) - 部署到 vSAN 数据存储的每个 VM 都分配有至少一个 VM 存储策略。 可以在 VM 的初始部署中或在执行其他 VM 操作（例如克隆或迁移）时分配 VM 存储策略。

- [为 vCenter 配置外部标识源](configure-identity-source-vcenter.md) - vCenter 具有一个名为 cloudadmin 的内置本地用户，并为该用户分配了 CloudAdmin 角色。 本地 cloudadmin 用户用于在 Active Directory (AD) 中设置用户。 借助运行命令功能，可以为 vCenter 配置通过 LDAP 或 LDAPS 的 Active Directory 作为外部标识源。

- [使用 JetStream 部署灾难恢复](deploy-disaster-recovery-using-jetstream.md) - 将数据直接存储到 vSAN 中的恢复群集。 数据是通过在 vSphere 中运行的 I/O 筛选器捕获的。 基础数据存储可以是 VMFS、VSAN、vVol 或任何 HCI 平台。 
