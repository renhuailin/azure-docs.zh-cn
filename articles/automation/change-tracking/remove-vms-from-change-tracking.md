---
title: 从 Azure 自动化更改跟踪和库存中删除 VM
description: 本文介绍如何从更改跟踪和库存中删除 VM。
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b79fa22d3203504e63161aba03b32830d74d016
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131269"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>从“更改跟踪和清单”中删除 VM

跟踪环境中 Vm 的更改后，可以停止通过 [更改跟踪和清单](overview.md) 功能对它们进行管理。 若要停止管理 VM，需在关联到自动化帐户的 Log Analytics 工作区中编辑已保存的搜索查询 `MicrosoftDefaultComputerGroup`。

## <a name="sign-into-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="to-remove-your-vms"></a>删除 VM

1. 在 Azure 门户中，从 Azure 门户的顶部导航开始 **Cloud Shell** 。 如果不熟悉 Azure Cloud Shell，请参阅 [Azure Cloud Shell 概述](../../cloud-shell/overview.md)。

2. 使用以下命令识别希望从管理中删除的计算机的 UUID。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. 在 Azure 门户中，导航到 Log Analytics 工作区。 从列表中选择你的工作区。

4. 在 Log Analytics 工作区中，选择“日志”，然后从顶部的操作菜单中选择“查询资源管理器”。

5. 从右侧窗格中的查询资源管理器展开“已保存的查询\更新”，然后选择已保存的搜索查询 `MicrosoftDefaultComputerGroup` 进行编辑。

6. 在查询编辑器中，查看该查询并找到 VM 的 UUID。 删除 VM 的 UUID，并对要删除的任何其他 VM 重复这些步骤。

7. 完成编辑操作后，通过从顶部栏中选择“保存”来保存搜索。

>[!NOTE]
>取消注册后，系统仍会显示这些计算机，因为我们会报告在过去 24 小时内评估的所有计算机。 删除计算机后，需要等待 24 小时，系统才不会再次列出这些计算机。

## <a name="next-steps"></a>后续步骤

若要重新启用此功能，请参阅 [从自动化帐户启用更改跟踪和清单](enable-from-automation-account.md)， [通过浏览 Azure 门户启用更改跟踪和清单](enable-from-portal.md)， [启用从 runbook 进行更改跟踪和清点](enable-from-runbook.md)，或者 [从 Azure VM 启用更改跟踪和清单](enable-from-vm.md)。