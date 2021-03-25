---
title: 将 Azure 实验室服务用于编程马拉松
description: 本文介绍如何通过 Azure 实验室服务创建可在编程马拉松比赛中使用的实验室。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d4648def48557d70547ef7d41d6c3e516671849e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452724"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>将 Azure 实验室服务用于下一次编程马拉松
Azure 实验室服务采用轻量易用的设计，使你可以快速运转新的虚拟机 (VM) 实验室来参与编程马拉松。  使用以下查检表确保你的编程马拉松可以尽量顺利地进行。 此查检表应该由负责创建和管理编程马拉松实验室的 IT 部门或教职人员填写。 

若要将实验室服务用于编程马拉松，请确保至少在编程马拉松开始之前的几日内创建了实验室帐户和实验室。 此外，请遵循以下指导原则：

## <a name="guidance"></a>指南

- **在离参与者最近的区域或位置创建实验室**。 

    为了降低延迟，请在离编程马拉松参与者最近的区域中创建实验室。  如果参与者来自世界各地，则你需要凭借最好的判断创建位置居中的实验室。  或者，根据参与者所在的位置，将编程马拉松划分为使用多个实验室。
- **选择最适合使用需求的计算大小**。

    一般情况下，计算大小越大，虚拟机的执行速度就越快。 但是，为了限制成本，需要根据参与者的需求选择适当的计算大小。 有关可用计算大小的详细信息，请参阅[管理员指南中的 VM 大小信息](administrator-guide.md#vm-sizing)。
- **配置 RDP\SSH 以便与 Linux VM 建立远程桌面连接**。

    如果编程马拉松使用 Linux VM，请确保已启用远程桌面，使参与者能够使用 RDP（远程桌面协议）或 SSH（安全外壳）连接到其 VM。 只需对 Linux VM 执行此步骤，创建实验室时必须启用此步骤。 此外，对于 RDP，可能需要在发布模板 VM 之前，在其上安装并配置 RDP 服务器和 GUI 包。  有关详细信息，请参阅[有关启用适用于 Linux 的远程桌面的操作指南](how-to-enable-remote-desktop-linux.md)。

- **安装和停止 Windows 更新**。 

    如果使用的是 Windows 映像，我们建议先在实验室的[模板 VM](how-to-create-manage-template.md) 上安装最新的 Windows 更新，然后再发布该模板以创建实验室的 VM。 这是为了确保安全性，并防止在编程马拉松期间为了安装更新（这还可能导致 VM 重启）而干扰到参与者。 还可以考虑关闭 Windows 更新以防止将来发生任何中断。 请参阅[有关安装和配置 Windows 更新的操作指南](how-to-prepare-windows-template.md#install-and-configure-updates)。
- **确定学生如何备份其工作内容**。 

    在编程马拉松的持续时间内，将为每个学生分配一个虚拟机。 他们可将工作内容直接保存到该计算机，但我们建议学生备份其工作内容，以便在编程马拉松结束后可以访问这些内容。 例如，可将工作内容保存到 OneDrive、GitHub 等外部位置。 若要使用 OneDrive，可以选择在学生的实验室虚拟机上为学生自动配置 OneDrive。 请参阅[有关安装和配置 OneDrive 的操作指南](how-to-prepare-windows-template.md#install-and-configure-onedrive)。
- **根据参与者人数设置 VM 容量**。 

    确保根据预计的编程马拉松参与者人数设置实验室的虚拟机容量。 发布模板虚拟机时，可能需要花费好几个小时才能在实验室中创建所有计算机。 正因如此，我们建议在编程马拉松开始之前预先创建好这些计算机。 有关详细信息，请参阅[有关更新实验室容量的操作指南](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity)。

- **确定是否限制访问实验室**。 

    将用户添加到实验室时，有一个默认已启用的限制访问选项。 此功能要求你先将所有编程马拉松参与者的电子邮件地址添加到列表，然后，他们才能使用注册链接来注册和访问实验室。 如果在编程马拉松活动开始之前你并不知道有哪些参与者，可以选择禁用限制访问选项，这样，任何人都可以使用注册链接来注册实验室。 有关详细信息，请参阅[有关添加用户的指南](how-to-configure-student-usage.md)。

- **验证计划、配额和自动关机设置**。 

    实验室服务提供多种成本控制措施来限制 VM 的使用。 但是，如果这些设置配置不当，可能会导致实验室的虚拟机意外关机。 为了确保为你的编程马拉松正确配置了这些设置，请验证以下设置：

    **计划**：使用[计划](how-to-create-schedules.md)可以自动控制实验室计算机的启动和关机时间。 默认情况下，在创建新实验室时不会配置任何计划。 但是，应确保设置的实验室计划能够完全满足编程马拉松的需求。  例如，如果编程马拉松在星期六上午 8:00 开始，在星期日下午 5:00 结束，则你可以创建一个在星期六上午 7:30（编程马拉松开始前的 30 分钟）自动启动计算机，在星期日下午 5:00 自动关闭计算机的计划。 或者，你也可以决定完全不使用计划。

    **配额**：[配额](how-to-configure-student-usage.md#set-quotas-for-users)控制参与者在计划时间以外有权访问虚拟机的小时数。 如果参与者在使用计算机时达到了配额，该计算机将自动关闭，并且除非提高了配额，否则参与者无法将其重启。 默认情况下，在创建实验室时，配额设置为 10 小时。 同样，应确保设置的配额能够为编程马拉松留出足够的时间，在未创建计划的情况下，这一点尤为重要。

    **自动关机**：如果已启用，在学生断开其 RDP 会话连接特定的一段时间后，[自动关机](how-to-enable-shutdown-disconnect.md)设置会导致 Windows 虚拟机自动关闭。 默认情况下，此设置处于禁用状态。

- **将防火墙设置配置为允许与实验室 VM 建立连接**。 

    确保学校或组织的防火墙设置允许使用 RDP\SSH 连接到实验室 VM。 有关详细信息，请参阅[有关配置网络防火墙设置的操作指南](how-to-configure-firewall-settings.md)。

- **在参与者的平板电脑、Mac、PC 等设备上安装 RDP\SSH 客户端**。

    编程马拉松参与者必须在其用于连接实验室 VM 的平板电脑或笔记本电脑上安装 RDP 和/或 SSH 客户端。 有不同的 RDP 或 SSH 客户端可供选择，例如：

    - Microsoft 的 **远程桌面连接** 应用，用于建立 RDP 连接。 不同类型的平台（包括 Chromebook 和 [Mac](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162)）都支持远程桌面连接应用。
    - [Putty](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162)，使用 SSH 连接到 Linux VM。
- **验证实验室虚拟机**。 

    发布实验室 VM 后，应验证其配置是否正确。 只需针对一名参与者的实验室虚拟机执行此验证：

    1. 使用 RDP 和/或 SSH 进行连接。
    2. 打开安装的每个附加应用程序和工具，以自定义虚拟机基础映像。
    3. 演练几个基本方案（这些方案在参与者开展的活动中具有代表性），以确保 VM 根据所选的计算大小而言性能足够。

## <a name="on-the-day-of-hackathon"></a>在编程马拉松比赛的那一日
本部分概述在编程马拉松比赛的那一日所要完成的步骤。

1. **启动实验室 VM**。

    实验室计算机最长可能需要 30 分钟才能完成启动，具体取决于所用的 OS。 因此，请务必在编程马拉松开始之前启动计算机，使参与者无需等待。 如果使用了计划，也请确保至少提前 30 分钟自动启动 VM。
2. **邀请学生注册并访问其实验室虚拟机**。 

    为参与者提供以下信息，使其能够访问实验室 VM。 

    - 实验室的注册链接。 
    - 用于连接计算机的凭据。 仅当实验室使用基于 Windows 的映像，并且你已将所有 VM 配置为使用同一密码时，此步骤才适用。
    - 有关参与者如何通过 SSH 和/或 RDP 连接到其计算机的说明。

        有关详细信息，请参阅有关[向用户发送邀请](how-to-configure-student-usage.md#send-invitations-to-users)和[连接到 Linux VM](how-to-use-remote-desktop-linux-student.md) 的操作指南。 

## <a name="next-steps"></a>后续步骤
首先按照以下文章中的说明在实验室中创建一个实验室帐户：[教程：使用 Azure 实验室服务创建实验室帐户](tutorial-setup-lab-account.md)。
