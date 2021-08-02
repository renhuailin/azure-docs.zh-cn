---
title: Azure Bastion 会话监视和管理
description: 了解如何选择正在进行的会话并强制断开其连接或将其删除。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: cherylmc
ms.openlocfilehash: 6b0df7ae98c3718ddfadf9e13354b4f961585c98
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540429"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Azure Bastion 的会话监视和管理

在虚拟网络中预配和部署 Bastion 服务后，就可以使用它无缝地连接到此虚拟网络中的任何 VM。 当用户连接到工作负载时，Azure Bastion 可以用于监视远程会话并采取快速管理操作。 Azure Bastion 会话监视可用于查看哪些用户连接到了哪些虚拟机。 它可显示已连接用户的 IP、连接时长以及连接时间。 会话管理体验可用于选择正在进行的会话并强制断开连接或删除会话，以便将用户与正在进行的会话断开连接。

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>监视远程会话

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 Azure Bastion 资源并从 Azure Bastion 页面选择“会话”。

   ![屏幕截图显示了选中“会话”的 Azure 门户菜单设置。](./media/session-monitoring/sessions.png)
2. 在“会话”页面上，可以在右侧看到正在进行的远程会话。

   ![查看会话](./media/session-monitoring/view-session.png)
3. 选择“刷新”以查看远程会话的更新列表。 选择“刷新”时，Azure Bastion 将获取最新的监视信息并在门户中刷新。

   ![屏幕截图显示了使用 Azure Bastion 的会话，其中突出显示了“刷新”选项。](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>删除或强制断开正在进行的远程会话

你可以选择一组会话并强制断开其连接。 以下步骤说明了如何删除远程会话：

1. 导航到 Azure Bastion 资源并从 Azure Bastion 页面选择“会话”。

   ![屏幕截图显示了 Azure 门户，其中在“设置”下选择了“会话”。](./media/session-monitoring/navigate.png)
2. 选择会话后，可看到远程会话列表。

   ![列出会话](./media/session-monitoring/list.png)
3. 选择特定的远程会话，然后选择会话行右端的省略号，再选择“删除”。

   ![屏幕截图显示了 Azure 门户，其中显示了一个已选择“删除”图标的会话。](./media/session-monitoring/delete.png)
4. 选择“删除”时，远程会话将断开连接，用户可在远程会话中看到“已断开连接”消息。

   ![屏幕截图显示了一条确认你已断开连接的消息。](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>后续步骤

阅读 [Bastion 常见问题解答](bastion-faq.md)。
