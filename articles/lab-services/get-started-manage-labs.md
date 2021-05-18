---
title: 开始使用 Azure 实验室服务
description: 本文介绍如何开始使用 Azure 实验室服务。
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165036"
---
# <a name="get-started-with-lab-services"></a>开始使用实验室服务 

Azure 实验室服务向学生和教师提供直接从自己的计算机访问虚拟计算机实验室的权限。

教师需要了解如何向学生/家长教授通过一对一学生发放硬件在指令中使用实验室服务。 这样，学生便能够通过虚拟机 (VM) 来访问其学习计划所需的行业标准软件。 

VM 是充当虚拟计算机的虚拟环境。 VM 具有自己的处理器、内存和存储。 VM 可替代真实计算机，可以为用户提供对操作系统和软件的访问权限，而无需将它们安装在自己的设备上。 Azure 实验室服务提供了一种工具，使学生可访问和导航 VM，并使教职员工可管理其虚拟计算机实验室。 

本文为教职员工提供有关如何访问、管理 Azure 实验室服务以及教授学生/家长利用该服务的信息。

## <a name="key-concepts"></a>关键概念

### <a name="quota-hours"></a>配额小时数

学生可以在计划课程时间内随时访问其 VM，而不会影响其配额小时数。 配额小时数针对整个学期进行设置，确定学生在正常计划课程时间之外可以使用其 VM 的小时数。

每周 8 小时，在星期日重置 - 不累计。

有关详细信息，请参阅[设置配额](how-to-configure-student-usage.md#set-quotas-for-users)。

### <a name="automatic-shut-down"></a>自动关闭

若要帮助降低成本并节省学生配额小时数，可为实验室启用自动关闭。 自动关闭会在一段时间不活动（无鼠标或键盘输入）后关闭 VM。 自动关闭的工作分为两个阶段，首先在一段时间不活动后，学生会从 VM 断开连接。 此时，VM 仍在运行，学生能够进行连接。 在断开连接后又经过一段不活动时间后，VM 会自行关闭。

自动关闭是重要的成本节省工具，但是会使学生在保存其工作和呈现大型项目文件方面面临挑战。 如果学生经常断开连接或 VM 关闭速度太快，请联系 CTE 管理员。 

有关详细信息，请参阅[为实验室帐户配置 VM 自动关闭功能](how-to-configure-lab-accounts.md)。

### <a name="managing-virtual-machines"></a>管理虚拟机

通过管理实验室，教师可以控制实验室容量（供学生使用的 VM 数）以及手动启动、停止或重置 VM 等方面。 教师还可以连接到 VM，以体验学生界面、访问文件并排查软件或 VM 本身的问题。

管理 VM 时要记住的最重要一点是，只要计算机在运行，即使没有用户连接到 VM，也会产生成本。

## <a name="lab-dashboards"></a>实验室仪表板

### <a name="overview"></a>概述

Azure 实验室服务中用于实验室的仪表板提供特定实验室不同方面的快照，包括 VM 信息、已分配和未分配的 VM 数、已注册和未注册的用户数以及有关实验室计划的信息。 

> [!NOTE]
> 虽然仪表板和 [Azure 实验室服务网站](https://labs.azure.com/)的大多数管理方面对教师可见，但特定于角色的权限可能会影响在仪表板中修改特定条件的能力。 如果遇到与特定实验室设置有关的问题，请联系 CTE 管理员。

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="Azure 实验室服务门户":::

### <a name="examine-a-dashboard"></a>检查仪表板

1. 导航并登录 [Azure 实验室服务网站](https://labs.azure.com/)。
1. 选择实验室。
1. 你会在窗口的左侧看到一个“仪表板”。 单击“仪表板”，你会在仪表板中看到一些磁贴。
1. 在“成本和计费”磁贴下，还有用于模板、虚拟机池、用户和计划的磁贴，使你可以修改各个方面并查看有关课堂实验室的更多详细信息。

    * 模板 - 描述模板的创建日期和上次发布日期。 
    * 虚拟机池 - 已分配和未分配的 VM 数。
    * 用户 - 已注册用户数以及已添加到实验室但尚未注册的用户数。
    * 计划 - 显示实验室的即将到来计划事件以及用于查看更多事件的链接。

有关详细信息，请参阅[使用仪表板](use-dashboard.md)。

### <a name="manually-starting-vms"></a>手动启动 VM

1. 在“虚拟机池”页中，可以通过单击页面顶部的“全部启动”按钮来启动实验室中的所有 VM 。

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="启动 VM":::
1. 可以通过单击状态开关来启动单个 VM。 

    该开关在 VM 启动时显示“正在启动”，随后在 VM 启动后显示“正在运行” 。
1. 还可以使用“名称”列左侧的复选框来选择一些 VM。 

    选择了所需 VM 后，请单击屏幕顶部的“启动”按钮。
1. 启动后，可以单击“全部停止”按钮以停止所有 VM。

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="停止 VM":::

### <a name="stopping-and-resetting-vms"></a>停止和重置 VM

* 可以通过单击状态开关来停止单个 VM。
* 还可以通过使用选中框并单击屏幕顶部的“停止”按钮来停止多个 VM。

如果学生在连接到 VM 时遇到问题，或者 VM 出于任何其他原因而需要重置，则可以使用重置函数。
1. 若要重置一个或多个 VM，请使用选中框选中它们，然后单击页面顶部的“重置”按钮。
1. 在弹出窗口中，单击“重置”。

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="重置 VM":::

    > [!NOTE]
    > 打开学生 VM 不会影响学生的配额。 用户配额指定用户在计划课程时间之外可用的实验室小时数。

### <a name="connect-to-vms"></a>连接到 VM

只要学生 VM 已开启，而学生未连接到该 VM，教师便能够连接到它。 通过连接到 VM，你能够访问 VM 上的本地文件，并帮助学生解决问题。

1. 若要连接到学生 VM，请将鼠标悬停在列表中的 VM 上方，然后单击“连接”按钮。 
1. 随后按照适用于 Chromebook、Mac 或 PC 的学生入门指南进行操作

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="连接到学生 VM 按钮":::

## <a name="manage-users-in-a-lab"></a>管理实验室中的用户

教师能够将学生用户添加到实验室，并监视其小时配额。 有关如何通过电子邮件地址或使用电子表格列表来添加用户以及注册用户的详细信息，请参阅[添加和管理实验室用户](how-to-configure-student-usage.md)。

邀请用户或共享链接后，你能够在“用户”页上“状态”列中监视已成功注册的用户 。 

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用在本快速入门中创建的资源，请删除资源。

## <a name="next-steps"></a>后续步骤

[设置实验室帐户](tutorial-setup-lab-account.md)
