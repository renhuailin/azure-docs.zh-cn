---
title: Azure 实验室服务成本管理指南
description: 了解查看实验室服务成本的各种方法。
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 05613d4e019d5cef18130dfd39b74c1da6b9768c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660431"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure 实验室服务成本管理

对于 Azure 实验室服务，成本管理可划分为两个不同的方面：成本估算和成本分析。 设置实验室时，我们会进行成本估算，以确保实验室的初始结构不会超过预算。 成本分析通常在月底进行，目的是确定下个月要采取的必要措施。

## <a name="estimate-the-lab-costs"></a>估算实验室成本

每个实验室仪表板都有一个“成本和计费”部分，其中列出了该实验室在当月的大致估算成本。 成本估算按照估算的每小时成本汇总了用户数达到上限时的每小时使用情况。 若要获取最准确的估算值，请设置包括[计划](how-to-create-schedules.md)的实验室。 该仪表板将反映估算的成本。

此估算值可能不会显示所有可能的成本。 有少量资源的成本不包括在内：

- 模板准备成本。 创建模板所需的时间可能有很大的不同。 模板的运行成本与实验室每小时总成本是相同的。
- 任何[共享映像库](how-to-use-shared-image-gallery.md)成本，因为库可以在多个实验室之间共享。
- 实验室创建者启动虚拟机 (VM) 时产生的小时成本。

> [!div class="mx-imgBorder"]
> ![显示仪表板中成本估算的屏幕截图。](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>分析上个月的使用情况

成本分析用于检查上个月的使用情况，以帮助确定对实验室做出的任何调整。 可以在[订阅成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md)中找到以往成本的细分。 可以在 Azure 门户上的搜索框中输入“订阅”，然后选择“订阅”选项 。

> [!div class="mx-imgBorder"]
> ![显示搜索框和“订阅”选项的屏幕截图。](./media/cost-management-guide/subscription-search.png)

选择要检查的特定订阅。

> [!div class="mx-imgBorder"]
> ![显示如何选择订阅的屏幕截图。](./media/cost-management-guide/subscription-select.png)

在“成本管理”下的左侧窗格中选择“成本分析” 。

> [!div class="mx-imgBorder"]
> ![显示图表上的订阅成本分析的屏幕截图。](./media/cost-management-guide/subscription-cost-analysis.png)

在此仪表板中可以进行深入的成本分析，包括按计划以不同的文件类型导出。 有关详细信息，请参阅[成本管理 + 计费概述](../cost-management-billing/cost-management-billing-overview.md)。

可按资源类型进行筛选。 使用 `microsoft.labservices/labaccounts` 只会显示与实验室服务关联的成本。

## <a name="understand-the-usage"></a>了解使用情况

以下屏幕截图是成本分析的一个示例。

> [!div class="mx-imgBorder"]
> ![显示订阅的示例成本分析的屏幕截图。](./media/cost-management-guide/cost-analysis.png)

默认会提供六列：“资源”、“资源类型”、“位置”、“资源组名称”、“标记”和“成本”     。 “资源”列包含有关实验室帐户、实验室名称和 VM 的信息。 显示实验室帐户、实验室名称和默认值（第二行和第三行）的行是实验室的成本。 在显示实验室帐户、实验室名称、默认值和 VM 名称的行中，可以看到已用 VM 的成本。

在此示例中，添加第一行和第二行（都以 aaalab/ dockerlab 开头）可以得到“aaalab”实验室帐户中实验室“dockerlab”的总成本。

若要获得映像库的总成本，请将资源类型更改为 `Microsoft.Compute/Galleries`。 共享映像库可能不会显示在成本中，具体取决于该库的存储位置。

> [!NOTE]
> 共享映像库已连接到实验室帐户。 这意味着，多个实验室可以使用同一个映像。

## <a name="separate-the-costs"></a>区分成本

某些大学已使用实验室帐户和资源组作为区分班级的方式。 每个班级具有自身的实验室帐户和资源组。

在成本分析窗格中，使用班级的相应资源组名称添加一个基于资源组名称的筛选器。 然后，只会显示该班级的成本。 这样，在你查看成本时，将会看到不同班级的更清晰成本描绘。 可以使用成本分析的[按计划导出](../cost-management-billing/costs/tutorial-export-acm-data.md)功能，在单独的文件中下载每个班级的成本。

## <a name="manage-costs"></a>管理成本

根据班级的类型，可以通过多种方式管理成本，以减少未由任何学生使用的 VM 实例仍在运行的情况。

### <a name="automatic-shutdown-settings-for-cost-control"></a>用于成本控制的自动关闭设置

使用自动关闭功能可以防止实验室中浪费 VM 使用时间。 以下设置可以解决用户意外使其虚拟机保持运行的大多数情况：

> [!div class="mx-imgBorder"]
> ![显示三项自动关闭设置的屏幕截图。](./media/cost-management-guide/auto-shutdown-disconnect.png)

可以在实验室帐户级别和实验室级别配置这些设置。 如果在实验室帐户级别启用这些设置，则它们将应用到实验室帐户中的所有实验室。 对于所有新的实验室帐户，默认已启用这些设置。

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>自动断开用户与 OS 认为空闲的虚拟机的连接

> [!NOTE]
> 此设置仅适用于 Windows 虚拟机。

启用“当虚拟机空闲时断开用户连接”设置后，如果 Windows OS 认为会话处于空闲状态，则用户将从实验室中的所有计算机（包括模板虚拟机）断开连接。 [Windows OS 对空闲的定义](/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state)运用了两个准则：

- 用户不在场：没有键盘或鼠标输入活动。
- 未消耗资源：所有处理器和所有磁盘在一定百分比的时间内处于空闲状态。

用户在断开连接之前，会在 VM 中看到如下所示的消息：

> [!div class="mx-imgBorder"]
> ![该屏幕截图显示了一条警告消息，其中指出会话空闲时间已超过限制，将断开连接。](./media/cost-management-guide/idle-timer-expired.png)

用户断开连接后，虚拟机仍会保持运行。 如果用户通过登录重新连接到该虚拟机，在断开连接之前已打开的窗口或文件或者未保存的工作仍保留在其中。 在此状态下，由于虚拟机正在运行，因此仍会将它视为活动状态，从而继续计收其成本。

若要自动关闭已断开连接的空闲 Windows 虚拟机，请结合使用“当虚拟机空闲时断开用户连接”和“用户断开连接时关闭虚拟机”设置 。

例如，如果配置了如下所示的设置：

- 当虚拟机空闲时断开用户连接：检测到空闲状态后的 15 分钟。
- 用户断开连接时关闭虚拟机：用户断开连接后的 5 分钟。

在用户停止使用 20 分钟后，Windows 虚拟机将自动关闭。

> [!div class="mx-imgBorder"]
> ![演示用于自动关闭 VM 的设置组合的插图。](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>用户断开连接时自动关闭虚拟机

“用户断开连接时关闭虚拟机”设置支持 Windows 和 Linux 虚拟机。 如果已打开此设置，出现在以下情况时将自动关机：

- 对于 Windows：远程桌面 (RDP) 连接已断开。
- 对于 Linux：SSH 连接已断开。

> [!IMPORTANT]
> 仅支持 [Linux 的特定发行版和版本](../virtual-machines/extensions/diagnostics-linux.md#supported-linux-distributions)。  [Data Science Virtual Machine - Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 映像不支持关闭设置。

可以指定虚拟机在自动关闭之前应等待用户重新连接的时间。

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>自动关闭已启动但用户未连接的虚拟机

在实验室中，用户可能会启动一个虚拟机，但从不连接到该虚拟机。 例如：

- 实验室中的某个计划为班级会话启动了所有虚拟机，但某些学生并未显示，因为他们未连接到其计算机。
- 用户启动一个虚拟机，但忘记连接到该虚拟机。

“用户未连接时关闭虚拟机”设置解决了这种情况，它可以自动关闭虚拟机。

有关如何配置和启用在断开连接时自动关闭 VM 的信息，请参阅以下文章：

- [为实验室帐户配置 VM 自动关闭](how-to-configure-lab-accounts.md)
- [为实验室配置 VM 自动关闭](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>计划的时间与配额时间

了解[计划的时间](classroom-labs-concepts.md#schedules)和[配额时间](classroom-labs-concepts.md#quota)有助于更好地根据教授和学生的需求配置实验室。

计划的时间是规定的一段时间，在此时间内所有学生 VM 均已启动并可连接。 当所有学生都有自己的 VM，并在一天的规定时间（例如课堂时间）接受教授的授课时，通常使用计划的时间。 其缺点是所有学生 VM 都会启动，即使某个学生未登录到其 VM，也会累积成本。

配额时间是分配给每位学生，由其自行决断使用的时间（通常在学生独立学习时使用）。 除非学生启动 VM，否则 VM 不会保持启动状态。

实验室可以使用配额时间或计划的时间，或者结合使用两者。 如果某个班级不需要计划的时间，仅使用配额时间可以最有效地利用 VM。

### <a name="scheduled-event-stop-only"></a>计划事件：仅停止

在计划中，可以添加一个“仅停止”事件类型，以在特定时间停止所有计算机。 某些实验室所有者已将“仅停止”事件设置在每日午夜，以便在学生忘记关闭其所用 VM 时降低成本和配额用量。 这种类型的事件的缺点是，即使某个学生正在使用 VM，也会关闭所有 VM。

### <a name="other-costs-related-to-labs"></a>与实验室相关的其他成本

某些成本不会累计到实验室服务，但可以关联到实验室服务。 可以将共享映像库连接到实验室，但该库不会显示在实验室服务成本中，但它确实产生了成本。 为了帮助将总成本保持较低水平，应该删除库中所有未使用的映像，因为映像会产生固有的存储成本。

实验室可以通过虚拟网络连接到其他 Azure 资源。 删除实验室后，应删除该虚拟网络和其他资源。

## <a name="conclusion"></a>结束语

但愿你已通过本文中的信息更好地了解了可帮助降低使用成本的工具。
