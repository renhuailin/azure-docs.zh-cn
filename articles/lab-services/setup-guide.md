---
title: 适用于 Azure 实验室服务的加速实验室设置指南
description: 如果你是实验室创建者，则本指南可帮助你快速设置你的学校的实验室帐户。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021722"
---
# <a name="lab-setup-guide"></a>实验室设置指南

在本指南中，你将了解如何为你的学校的学生创建实验室。

向学生发布实验室的过程可能需要长达几个小时。 安装时间取决于要在实验室中创建)  (Vm 的虚拟机数量。 至少允许一天，以确保实验室正常工作，并留出足够的时间来发布学生的 Vm。

## <a name="understand-the-lab-requirements-of-your-class"></a>了解课程的实验室要求

设置新实验室之前，应考虑以下问题。

### <a name="what-software-requirements-does-the-class-have"></a>课程有哪些软件要求？

当你决定需要在实验室 Vm 上安装的操作系统、应用程序和工具时，请参考你的类的学习目标。 若要设置实验室 VM，你有三个选项：

- 使用 Azure 市场映像：Azure 市场提供数百个映像，可以在创建实验室时使用。 对于某些课程，这些映像中的一个可能已包含课程所需的所有内容。

- **创建新的自定义映像**：可以通过使用 Azure Marketplace 映像作为起点来创建自己的自定义映像。 然后，可以通过安装其他软件并进行配置更改来对其进行自定义。

- **使用现有的自定义映像**：你可以重复使用之前创建的自定义映像，或者使用你的学校的其他管理员或教职员工创建的映像。 若要使用自定义映像，你的管理员需要设置共享映像库。  共享映像库是用于保存自定义映像的存储库。

> [!NOTE]
> 管理员负责启用 Azure 市场映像和自定义映像，以便你可以使用它们。 与你的 IT 部门协调，以确保已启用所需的映像。 你创建的自定义映像会自动启用，以便在你拥有的实验室中使用。

### <a name="what-hardware-requirements-does-the-class-have"></a>课程有哪些硬件要求？

可以从多种计算大小中进行选择：

- **嵌套虚拟化大小**：允许学生访问可托管多个嵌套 VM 的 VM。 例如，你可能会对网络或道德攻击类使用此计算大小。

- **GPU 大小**：允许学生使用计算机密集型类型的应用程序。 例如，此选项通常与人工智能和机器学习一起使用。

有关选择合适的 VM 大小的指南，请参阅：
- [VM 大小调整](./administrator-guide.md#vm-sizing)
- [从物理实验室迁移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> 由于计算大小的可用性因区域而异，因此您的实验室可能会有更少的大小。 通常，应选择满足需求的最小计算大小。 使用 Azure 实验室服务，可以在以后根据需要设置一个具有更大计算容量的新实验室。

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>课程对外部 Azure 或网络资源有哪些依赖？
实验室 Vm 可能需要访问外部资源，例如数据库、文件共享或授权服务器。  若要允许实验室 Vm 使用外部资源，请与 IT 管理员协调。

> [!NOTE]
> 你应考虑是否可以通过直接在 VM 上提供网络资源来减少实验室对外部资源的依赖。 例如，若要无需从外部数据库读取数据，可以直接在 VM 上安装数据库。  

### <a name="how-will-you-control-costs"></a>你将如何控制成本？
实验室服务使用即用即付定价模型，这意味着只需支付实验室 VM 运行的时间。 若要控制成本，请使用以下任一或全部选项：

- **计划**：使用计划来自动控制实验室 vm 的启动和关闭时间。
- **配额**：使用配额来控制学生在计划时间之外有权访问 VM 的小时数。  当学生使用 VM 并达到配额时，VM 会自动关闭。  如果增加配额，则学生无法重新启动 VM。
- **自动关闭**：当启用 "自动关闭" 设置时，Windows vm 将在学生断开与远程桌面协议 (RDP) 会话之后自动关闭。 默认情况下，此设置处于禁用状态。

有关控制成本的详细信息，请参阅：
- [估算成本](./cost-management-guide.md#estimate-the-lab-costs)
- [管理成本](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>学生如何保存其工作？
在实验室的生存期内，每个单独的学生都被分配一个 VM。 学生可以保存其工作：

- 到 VM。
- 到外部位置，如 OneDrive 或 GitHub。 可以在其实验室 VM 上为学生自动配置 OneDrive。

> [!NOTE]
> 若要确保你的学生能够继续在实验室外访问其保存的工作，并且在类结束后，我们建议将其工作保存到外部存储库中。

### <a name="how-will-students-connect-to-their-vms"></a>学生如何连接到其 Vm？
对于到 Windows Vm 的 RDP 连接，建议学生使用 Microsoft 远程桌面的 [客户端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。 远程桌面客户端支持 Mac、Chromebook 和 Windows 设备。

对于 Linux Vm，学生可以使用安全外壳 (SSH) 或 RDP 协议。 若要让学生使用 RDP 进行连接，你必须安装和配置所需的 RDP 和图形用户界面 (GUI) 包。

### <a name="will-students-also-use-microsoft-teams"></a>学生是否还会使用 Microsoft 团队？
Azure 实验室服务与 Microsoft 团队相集成，使教职员成员可以在团队中创建和管理其实验室。  同样，学生可以在团队中访问其实验室。

有关详细信息，请参阅 [Microsoft 团队中的 Azure 实验室服务](./lab-services-within-teams-overview.md)。

## <a name="set-up-your-lab"></a>设置实验室

了解课程实验室的要求之后，便可以对它进行设置了。 若要了解如何操作，请遵循本部分中的链接。 还提供了有关在团队中设置实验室的说明。

1. **创建实验室**。 参阅以下教程：
    - [创建课堂实验室](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [在团队中创建实验室](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > 如果你的类需要嵌套虚拟化，请参阅 [启用嵌套虚拟化](./how-to-enable-nested-virtualization-template-vm.md)。

1. **自定义映像并发布实验室 vm**。 若要连接到称为模板 VM 的特殊 VM，请参阅：
    - [创建和管理模板 VM](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [使用共享映像库](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > 如果使用的是 Windows，请参阅 [设置 windows 模板 VM](./how-to-prepare-windows-template.md)。 这些说明包括为学生设置 OneDrive 和 Microsoft Office 的步骤。

1. **管理 VM 池和容量**。 可以根据课程需要轻松增加或缩减 VM 容量。 请记住，增加 VM 容量可能需要几个小时，因为正在设置新的虚拟机。 请参阅以下文章：
    - [设置和管理 VM 池](./how-to-set-virtual-machine-passwords.md)
    - [在团队中的实验室服务中管理 VM 池](./how-to-manage-vm-pool-within-teams.md)

1. **添加和管理实验室用户**。 若要将用户添加到实验室，请参阅：
   - [将用户添加到实验室](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [向用户发送邀请](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [管理团队中的实验室服务用户列表](./how-to-manage-user-lists-within-teams.md)

    有关学生可以使用的帐户类型的信息，请参阅 [学生帐户](./how-to-configure-student-usage.md#student-accounts)。
  
1. **设置成本控件**。 若要设置计划、建立配额并启用自动关闭，请参阅以下教程：

   - [设置日程安排](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > VM 可能需要几分钟才能启动，这取决于所安装的操作系统。 为了确保实验室 VM 可以在计划的时间内随时使用，建议提前30分钟开始使用。

   - 为[用户设置配额](./how-to-configure-student-usage.md#set-quotas-for-users)并[为特定用户设置其他配额](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [启用在断开连接时自动关闭](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > 计划和配额不适用于模板 VM，但会应用自动关闭设置。 
        > 
        > 创建实验室时，模板 VM 已创建但未启动。 可以启动模板 VM，连接到该 VM，为实验室安装任何必备软件，然后将其发布。 发布模板 VM 时，如果你没有手动操作，它会自动关闭。 
        > 
        > 模板 Vm 在运行时会产生 *费用* ，因此，请确保在不需要运行模板 vm 时将其关闭。

    - [创建和管理团队中的实验室服务计划](./how-to-create-schedules-within-teams.md) 

1. **使用 "仪表板"**。 有关说明，请参阅 [使用课堂实验室仪表板](./use-dashboard.md)。

    > [!NOTE]
    > "仪表板" 上显示的预估开销是你预计学生实验室使用的最大成本。 例如，不会对学生未使用的配额时数向你收费。 估计成本 *不会* 反映使用模板 VM、共享图像库或实验室创建者启动用户计算机的任何费用。

## <a name="next-steps"></a>后续步骤

在管理实验室的过程中，请参阅以下文章：
- [跟踪教室实验室使用情况](tutorial-track-usage.md)  
- [访问教室实验室](tutorial-connect-virtual-machine-classroom-lab.md)
