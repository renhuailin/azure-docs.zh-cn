---
title: 适用于 Azure 实验室服务的加速实验室设置指南
description: 如果你是实验室创建者，则本指南可帮助你快速设置你的学校的实验室帐户。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 07f0d92ebd926616f1318b430bec2de32f753f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "95021722"
---
# <a name="lab-setup-guide"></a>实验室设置指南

在本指南中，你将了解如何为学校的学生创建实验室。

向学生发布实验室的过程最长可能需要几个小时。 设置时间的长短取决于您要在实验室中创建的虚拟机 (VM) 的数量。 至少需要一天的时间，以确保实验室正常工作，并留出足够的时间来发布学生的 VM。

## <a name="understand-the-lab-requirements-of-your-class"></a>了解课程的实验室要求

设置新实验室之前，应考虑以下问题。

### <a name="what-software-requirements-does-the-class-have"></a>课程有哪些软件要求？

在确定需要在实验室 VM 上安装哪些操作系统，应用程序和工具时，请参考班级的学习目标。 若要设置实验室 VM，你有三个选项：

- 使用 Azure 市场映像：Azure 市场提供数百个映像，可以在创建实验室时使用。 对于某些课程，这些映像中的一个可能已包含课程所需的所有内容。

- 创建新的自定义映像：可以通过使用 Azure 市场映像作为起点来创建自己的自定义映像。 然后，可以通过安装其他软件并进行配置更改来对其进行自定义。

- 使用现有自定义映像：可以重复使用你以前创建的现有自定义映像，或是学校的其他管理员或教职员工创建的映像。 要使用自定义映像，你的管理员需要设置共享映像库。  共享映像库是用于保存自定义映像的存储库。

> [!NOTE]
> 管理员负责启用 Azure 市场映像和自定义映像，以便你可以使用它们。 与 IT 部门协调，以确保启用了所需映像。 你创建的自定义映像会自动启用，以便在你拥有的实验室中使用。

### <a name="what-hardware-requirements-does-the-class-have"></a>课程有哪些硬件要求？

可以选择各种计算大小：

- 嵌套虚拟化规格：使学生能够访问可以承载多个嵌套 VM 的 VM。 例如，您可以对网络班级或道德黑客班级使用该计算大小。

- GPU 规格：可以让学生使用计算密集型的应用程序。 例如，此选项通常用于人工智能和机器学习。

有关选择合适的 VM 大小的指南，请参阅：
- [VM 大小调整](./administrator-guide.md#vm-sizing)
- [从物理实验室迁移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> 由于计算大小的可用性因区域而异，因此你的实验室可以使用的计算大小数量可能会减少。 通常，应选择适合需求的最小计算大小。 借助 Azure 实验室服务，可以在以后需要时设置具有更大计算大小的新实验室。

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>课程对外部 Azure 或网络资源有哪些依赖？
实验室 VM 可能需要访问外部资源，例如数据库、文件共享或授权服务器。  要允许实验室 VM 使用外部资源，请与 IT 管理员协调。

> [!NOTE]
> 应考虑是否可以通过直接在 VM 上提供网络资源，减少实验室对外部资源的依赖。 例如，若要无需从外部数据库读取数据，可以直接在 VM 上安装数据库。  

### <a name="how-will-you-control-costs"></a>你将如何控制成本？
实验室服务使用即用即付定价模型，这意味着你需要为实验室 VM 的运行时间付款。 要控制成本，请使用以下任一或全部选项：

- 计划：使用计划来自动控制何时启动和关闭实验室 VM。
- 配额：使用配额可以控制学生在计划时间之外访问虚拟机的小时数。  如果学生正在使用 VM，此时达到了时间配额，VM 会自动关闭。  除非你增加配额，否则学生无法重新启动 VM。
- 自动关闭：启用自动关闭设置后，学生断开与远程桌面协议 (RDP) 会话的连接后，Windows VM 将自动关闭。 默认情况下，此设置处于禁用状态。

有关控制成本的详细信息，请参阅：
- [估算成本](./cost-management-guide.md#estimate-the-lab-costs)
- [管理成本](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>学生如何保存其工作？
在实验室的生存期内，每个学生都被分配一个 VM。 学生可以将其工作保存到以下位置：

- 保存到 VM。
- 保存到外部位置，如 OneDrive 或 GitHub。 可以在其实验室 VM 上为学生自动配置 OneDrive。

> [!NOTE]
> 为确保学生在实验室外部以及课程结束之后可继续访问其保存的工作，建议学生将其工作保存到外部存储库。

### <a name="how-will-students-connect-to-their-vms"></a>学生如何连接到其 VM？
通过 RDP 连接到 Windows VM 时，建议学生使用 [Microsoft 远程桌面客户端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)。 远程桌面客户端支持 Mac、Chromebook 和 Windows 设备。

对于 Linux VM，学生可以使用安全外壳 (SSH) 或 RDP 协议。 要让学生使用 RDP 进行连接，必须安装并配置必要的 RDP 和图形用户界面 (GUI) 包。

### <a name="will-students-also-use-microsoft-teams"></a>学生是否还会使用 Microsoft Teams？
Azure 实验室服务与 Microsoft Teams 集成，使教职员成员可以在 Teams 中创建和管理其实验室。  同样，学生可以在 Teams 中访问其实验室。

有关详细信息，请参阅 [Microsoft Teams 中的 Azure 实验室服务](./lab-services-within-teams-overview.md)。

## <a name="set-up-your-lab"></a>设置实验室

了解课程实验室的要求之后，便可以对它进行设置了。 要了解如何操作，请点击本部分中的链接。 还提供了有关在 Teams 中设置实验室的说明。

1. 创建实验室。 参阅以下教程：
    - [创建课堂实验室](./tutorial-setup-classroom-lab.md#create-a-classroom-lab)
    - [在 Teams 创建实验室](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > 如果班级需要嵌套虚拟化，请参阅[启用嵌套虚拟化](./how-to-enable-nested-virtualization-template-vm.md)。

1. 自定义映像并发布实验室 VM。 要连接到称为模板 VM 的特殊 VM，请参阅：
    - [创建和管理模板 VM](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [使用共享映像库](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > 如果使用的是 Windows，还请参阅[设置 Windows 模板 VM](./how-to-prepare-windows-template.md)。 这些说明包括为学生设置 OneDrive 和 Office 的步骤。

1. 管理 VM 池和容量。 可以根据课程需要轻松增加或缩减 VM 容量。 请记住，增加 VM 容量可能需要数小时，因为将要设置新的 VM。 请参阅以下文章：
    - [设置和管理 VM 池](./how-to-set-virtual-machine-passwords.md)
    - [在 Teams 中的实验室服务中管理 VM 池](./how-to-manage-vm-pool-within-teams.md)

1. 添加和管理实验室用户。 要向实验室中添加用户，请参阅：
   - [将用户添加到实验室](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [向用户发送邀请](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [在 Teams 中管理实验室服务用户](./how-to-manage-user-lists-within-teams.md)

    有关学生可以使用的帐户类型的信息，请参阅[学生帐户](./how-to-configure-student-usage.md#student-accounts)。
  
1. 设置成本控制。 要设置计划、建立配额并启用自动关闭，请参阅以下教程：

   - [设置日程安排](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > VM 可能需要几分钟才能启动，具体取决于所安装的操作系统。 要确保实验室 VM 在计划时间内可随时使用，建议提前 30 分钟启动。

   - [为用户设置配额](./how-to-configure-student-usage.md#set-quotas-for-users)和[为特定用户设置额外配额](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [启用在断开连接时自动关闭](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > 计划和配额不适用于模板 VM，但自动关闭设置适用于模板 VM。 
        > 
        > 创建实验室时，将会创建模板 VM ，但不会启动。 可以启动模板 VM，连接到模板 VM，并为实验室安装任何必备软件，然后发布模板 VM。 发布模板 VM 时，如果你没有手动关闭模板 VM，则会自动关闭。 
        > 
        > 模板 VM 在运行时会产生 *成本*，因此，请确保在不需要运行时将其关闭。

    - [在 Teams 中创建和管理实验室服务计划](./how-to-create-schedules-within-teams.md) 

1. 使用仪表板。 有关说明，请参阅[使用课堂实验室仪表板](./use-dashboard.md)。

    > [!NOTE]
    > 仪表板上显示的估计成本是针对学生使用实验室所预计的最大成本。 例如，不会对学生未使用的配额时数向你收费。 估计成本不会反映使用模板 VM、共享图像库或实验室创建者启动用户计算机的任何费用。

## <a name="next-steps"></a>后续步骤

管理实验室过程中，请参阅以下文章：
- [跟踪课堂实验室使用情况](tutorial-track-usage.md)  
- [访问教室实验室](tutorial-connect-virtual-machine-classroom-lab.md)
