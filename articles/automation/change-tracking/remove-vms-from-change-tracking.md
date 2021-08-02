---
title: 从 Azure 自动化更改跟踪和库存中删除 VM
description: 本文介绍如何从更改跟踪和库存中删除 VM。
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 05/26/2021
ms.openlocfilehash: 3a39294c2ecfe7b26cb3ef3d65c11cbcd665d220
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110654118"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>从“更改跟踪和清单”中删除 VM

完成对环境中 VM 更改的跟踪后，可以停止使用[更改跟踪和清单](overview.md)功能来管理它们。 若要停止管理 VM，需在关联到自动化帐户的 Log Analytics 工作区中编辑已保存的搜索查询 `MicrosoftDefaultComputerGroup`。

## <a name="sign-into-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="to-remove-your-vms"></a>删除 VM

1. 在 Azure 门户中，从 Azure 门户的顶部导航栏启动 Cloud Shell。 如果不熟悉 Azure Cloud Shell，请参阅 [Azure Cloud Shell 概述](../../cloud-shell/overview.md)。

2. 使用以下命令识别希望从管理中删除的计算机的 UUID。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. 在 Azure 门户中，导航到 Log Analytics 工作区。 从列表中选择你的工作区。

4. 在 Log Analytics 工作区中，从左侧菜单选择“计算机组”。

5. 在右侧窗格的“计算机组”中，默认情况下会显示“已保存组”选项卡 。

6. 在表中，单击 MicrosoftDefaultComputerGroup 项（其“旧类别”值为“ChangeTracking”）右侧的“运行查询”图标   。

7. 在查询编辑器中，查看该查询并找到 VM 的 UUID。 删除 VM 的 UUID，并对要删除的任何其他 VM 重复这些步骤。

   > [!NOTE]
   > 为了加强保护，请确保在进行编辑之前复制查询。 这样一来，如果出现问题，就可以将查询还原。

   如果要从原始查询开始，并且要重新添加支持清理或维护活动的计算机，请复制以下查询：

   ```kusto
   Heartbeat
   | where Computer in~ ("") or VMUUID in~ ("")
   | distinct Computer
   ```

8. 编辑完保存的搜索以后，请从顶栏中选择“保存”>“另存为函数”，再次对其进行保存。 出现提示时，请指定以下项：

    * **名称**：ChangeTracking__MicrosoftDefaultComputerGroup
    * 选择“另存为计算机组”
    * **旧类别**：ChangeTracking

>[!NOTE]
>取消注册后，系统仍会显示这些计算机，因为我们会报告在过去 24 小时内评估的所有计算机。 删除计算机后，需要等待 24 小时，系统才不会再次列出这些计算机。

## <a name="next-steps"></a>后续步骤

要重新启用此功能，请参阅[从自动化帐户启用更改跟踪和清单](enable-from-automation-account.md)、[通过浏览 Azure 门户启用更改跟踪和清单](enable-from-portal.md)、[从 runbook 启用更改跟踪和清单](enable-from-runbook.md)或者[从 Azure VM 启用更改跟踪和清单](enable-from-vm.md)。