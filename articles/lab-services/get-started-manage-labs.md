---
title: Azure 实验室服务入门
description: 本文介绍如何开始 Azure 实验室服务。
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165036"
---
# <a name="get-started-with-lab-services"></a>实验室服务入门 

Azure 实验室服务向学生和教师提供直接从自己的计算机访问虚拟计算机实验室的权限。

教师需要了解如何通过一对一学生颁发的硬件来讲授学生/家长如何使用实验室服务。 因此，学生可以通过虚拟机 (VM) 来访问其研究计划所需的行业标准软件。 

VM 是充当虚拟计算机的虚拟环境。 Vm 有自己的处理器、内存和存储空间。 Vm 提供了真实计算机的替代方法，可以为用户提供对操作系统和软件的访问权限，而无需将它们安装在自己的设备上。 Azure 实验室服务为学生提供了一种工具，可用于访问和导航 Vm，并使员工能够管理其虚拟计算机实验室。 

本文介绍有关如何访问、管理和讲授学生/家长以利用 Azure 实验室服务的信息。

## <a name="key-concepts"></a>关键概念

### <a name="quota-hours"></a>配额小时数

学生可以在计划的类时间内随时访问其 Vm，而不会影响其配额时间。 配额时间设置为整个学期，并确定学生可以在计划的类时间之外使用其 VM 的小时数。

每周8小时，在星期日重置-非累计。

有关详细信息，请参阅 [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)。

### <a name="automatic-shut-down"></a>自动关机

为了帮助降低成本和节省学生配额时间，为实验室启用自动关闭。 自动关闭会在一段不活动时间后关闭 Vm (不) 鼠标或键盘输入。 自动关闭工作分为两个阶段，一段时间不活动后，一名学生将从 VM 断开连接。 此时，VM 仍在 **运行** ，并且学生可以连接到。 在断开连接后的另一段时间不活动后，VM 会自行关闭。

自动关闭是一项重要的节省成本的工具，但是对于保存其工作和呈现大型项目文件的学生而言，这确实是一项挑战。 如果你的学生经常断开连接，或者 Vm 关闭速度太快，请联系你的 CTE 管理员。 

有关详细信息，请参阅 [配置实验室帐户的 vm 自动关闭](how-to-configure-lab-accounts.md)。

### <a name="managing-virtual-machines"></a>管理虚拟机

管理实验室允许教师控制实验室容量 (可供学生) 的 Vm 数，并手动启动、停止或重置 Vm。 教师还可以连接到 Vm，体验学生界面、访问文件和排查软件或 VM 本身的问题。

管理 Vm 时要记住的最重要的一点是，无论何时 **运行** 计算机，我们都将产生成本，即使没有人连接到 VM 也是如此。

## <a name="lab-dashboards"></a>实验室仪表板

### <a name="overview"></a>概述

Azure 实验室服务中的 "实验室" 仪表板提供特定实验室的不同方面的快照，包括 VM 信息、已分配和未分配的 Vm 数、已注册和未注册的用户数以及有关实验室计划的信息。 

> [!NOTE]
> 尽管 "仪表板" 和 " [Azure 实验室服务" 网站](https://labs.azure.com/) 的大多数管理方面对于教师都可见，但特定于角色的权限可能会影响你在仪表板中修改特定条件的能力。 如果你的特定实验室安装遇到问题，请联系你的 CTE 管理员。

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="Azure 实验室服务门户":::

### <a name="examine-a-dashboard"></a>检查仪表板

1. 导航并登录到 [Azure 实验室服务网站](https://labs.azure.com/)。
1. 选择实验室。
1. 窗口的左侧会显示一个 **仪表板** 。 单击 " **仪表板** "，你的仪表板中将显示多个磁贴。
1. 在 **费用 & 计费** 磁贴的下方，还提供了用于模板、虚拟机池、用户和计划的磁贴，它们允许你修改各个方面并查看有关教室实验室的更多详细信息。

    * 模板-描述模板的创建日期和最后发布日期。 
    * 虚拟机池-已分配和未分配的 Vm 的数量。
    * 用户-已添加到实验室但未注册的已注册用户和用户的数量。
    * 计划-显示实验室的即将到来的计划事件以及用于查看更多事件的链接。

有关详细信息，请参阅 [使用仪表板](use-dashboard.md)。

### <a name="manually-starting-vms"></a>手动启动 Vm

1. 在 " **虚拟机池** " 页中，可以通过单击页面顶部的 " **全部启动** " 按钮来启动实验室中的所有 vm。

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="启动 Vm":::
1. 可以通过单击状态切换来启动单个 Vm。 

    启动 VM 后，将 **从启动时开始** 切换，然后在 vm 启动后 **运行** 。
1. 还可以使用 " **名称** " 列左侧的检查来选择多个 vm。 

    选择所需的 Vm 后，请单击屏幕顶部的 " **开始** " 按钮。
1. 启动后，可以单击 " **全部停止** " 按钮来停止所有 vm。

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="停止 Vm":::

### <a name="stopping-and-resetting-vms"></a>停止和重置 Vm

* 可以通过单击状态切换来停止单个 Vm。
* 还可以通过使用检查并单击屏幕顶部的 "停止" 按钮来停止多个虚拟机。

如果学生在连接到 VM 时遇到问题，或者出于任何其他原因需要重置 VM，则可以使用 reset 函数。
1. 若要重置一个或多个 Vm，请使用检查来选择它们，然后单击页面顶部的 " **重置** " 按钮。
1. 在弹出窗口中，单击 " **重置**"。

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="重置 VM":::

    > [!NOTE]
    > 启用学生 VM 不会影响学生的配额。 用户配额指定在计划的类时间之外可供用户使用的实验室小时数。

### <a name="connect-to-vms"></a>连接到 Vm

只要启用了学生，教师就可以连接到学生 VM，并且该学生未连接到该 VM。 通过连接到 VM，你将能够访问 VM 上的本地文件，并帮助学生解决问题。

1. 若要连接到 student VM，请将鼠标悬停在虚拟机上的列表中，然后单击 " **连接** " 按钮。 
1. 然后按照适用于 Chromebook、Mac 或电脑的学生入门指南进行操作

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="连接到学生 VM 按钮":::

## <a name="manage-users-in-a-lab"></a>在实验室中管理用户

教师可以将学生用户添加到实验室，并监视其小时配额。 若要详细了解如何通过电子邮件地址添加用户，或通过使用电子表格列表和注册用户，请参阅 [添加和管理实验室用户](how-to-configure-student-usage.md)。

邀请用户或共享链接后，你将能够在 "**状态**" 列的 "**用户**" 页中监视已成功注册的用户。 

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用在本快速入门中创建的资源，请删除这些资源。

## <a name="next-steps"></a>后续步骤

[设置实验室帐户](tutorial-setup-lab-account.md)
