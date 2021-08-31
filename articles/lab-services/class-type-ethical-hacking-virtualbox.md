---
title: 使用 Azure 实验室服务在 VirtualBox 上设置道德黑客实验室 | Microsoft Docs
description: 了解如何使用 Azure 实验室服务在 VirtualBox 中设置实验室讲授道德黑客。
ms.topic: article
ms.date: 06/11/2021
ms.openlocfilehash: 49db4562a7d541a4c3409a5956a67e3bd0aadef4
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113040778"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class-with-virtualbox"></a>在 VirtualBox 中设置实验室来讲授道德黑客课程

本文介绍如何设置一个侧重于道德黑客取证方面的课程。 渗透测试是道德黑客社区使用的一种做法，当某人试图获得对系统或网络的访问权限以证明恶意攻击者可能利用的漏洞时，就会进行渗透测试。

在道德黑客课程中，学生可以学习抵御漏洞的新式技术。 每个学生都获得一个主机虚拟机，它包含三个嵌套虚拟机 - 两个是带有 [Seed](https://seedsecuritylabs.org/lab_env.html) 映像的虚拟机，另一个是带有 [Kali Linux](https://www.kali.org/) 映像的虚拟机。 Seed 虚拟机用于开发目的，Kali 虚拟机提供对执行取证任务所需的工具的访问。

本文包含两个主要部分。 第一部分介绍如何创建课堂实验室。 第二部分介绍如何创建启用了嵌套虚拟化并配备所需工具和映像的模板计算机。 在本例中，是已启用了 [VirtualBox](https://www.virtualbox.org/) 来托管映像的计算机上的两个 Seed 映像和一个 Kali Linux 映像。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，你需要具有一个 Azure 订阅才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 在获得 Azure 订阅后，可以在 Azure 实验室服务中创建新实验室帐户，或者也可以使用现有的帐户。 请参阅以下教程来了解如何创建新实验室帐户：[设置实验室帐户的教程](tutorial-setup-lab-account.md)。

按照[此教程](tutorial-setup-classroom-lab.md)创建新实验室并应用以下设置：

| 虚拟机大小 | 映像 |
| -------------------- | ----- |
| 中等(嵌套虚拟化) | Windows Server 2019 Datacenter |
| 中等(嵌套虚拟化) | Windows 10 |

## <a name="template-machine"></a>模板计算机

在创建模板计算机后，请启动并连接到该计算机，以完成以下三个主要任务。

1. 将计算机设置为对嵌套虚拟机使用 [VirtualBox](https://www.virtualbox.org/)。
2. 设置 [Kali](https://www.kali.org/) Linux 映像。 Kali 是一个 Linux 分发版，其中包含用于渗透测试和安全审核的工具。
3. 设置 Seed 映像。 在此示例中，将使用 [Seed](https://seedsecuritylabs.org/lab_env.html) 映像。 此映像是专门为安全培训创建的。

本文的其余部分将介绍完成上述任务的手动步骤。

### <a name="installing-virtualbox"></a>安装 VirtualBox

1. 选择 Windows 主机选项来下载 [VirtualBox 平台包](https://www.virtualbox.org/wiki/Downloads)。
2. 运行安装可执行文件，并使用默认选项完成安装。

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>使用 Kali Linux 映像设置嵌套虚拟机

Kali 是一个 Linux 分发版，其中包含用于渗透测试和安全审核的工具。

1. 从 [Kali Linux VM VirtualBox 映像](https://www.kali.org/get-kali/#kali-virtual-machines)中下载 ova 映像。  建议使用 32 位版本，因为 64 位版本会在加载时出现错误。  记住下载页上注明的默认用户名和密码。
2. 打开 VirtualBox 管理器并[导入 .ova 映像](https://docs.oracle.com/cd/E26217_01/E26796/html/qs-import-vm.html)。  需要审查并接受 Kali 许可协议才能继续。

>[!Note]
>- Kali VM 的 VirtualBox 默认 Ram 为 2 千兆 (2048)，建议根据需求将 Ram 增大到至少 4 千兆 (4096) 或更多。  学员可以在其 VM 上更改此设置。  更改 VirtualBox 中的 RAM 大小不会更改实验室的 VM 大小。
>- 默认情况下，硬盘设置为 80 千兆限制，但会动态分配。  实验室服务计算机限制为 128 千兆硬盘空间，因此请注意不要超过此磁盘大小。
>- Kali 映像启用了 USB 2.0，这需要 [Oracle VM VirtualBox 扩展包](https://www.virtualbox.org/wiki/Downloads)，或在 USB 选项卡下将 USB 控制器设置为 1.0。

### <a name="setup-seed-lab-images"></a>设置 Seed 实验室映像

1. 下载并提取 [SEED 实验室 VM 映像](https://seedsecuritylabs.org/labsetup.html)。
2. 请按照说明[在 VirtualBox 中创建 VM。](https://github.com/seed-labs/seed-labs/blob/master/manuals/vm/seedvm-manual.md)
   如果需要多个 SEED VM 来为每台计算机创建 .iso 副本，则为不同计算机使用相同的 .iso 将导致无法正常工作。

>[!IMPORTANT] 
>在发布模板之前，请确保所有嵌套虚拟机都已关闭电源。  开机会导致意外的副作用，包括破坏虚拟机。

## <a name="cost"></a>成本  

如果要估计此实验室的成本，可以使用以下示例：

如果一个班级有 25 个学生，计划的课堂时间为 20 小时且家庭作业或作业时数配额为 10 小时，则实验室的价格为：

25 个学生 \* (20 + 10) 小时 \* 55 个实验室单位\* 0.01 美元/小时 = 412.50 美元

>[!IMPORTANT]
>成本估算仅用于示例目的。 有关定价的最新详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结论

本文指导你完成了为道德黑客课程创建实验室的步骤。 它包括设置嵌套虚拟化的步骤，用于在主机虚拟机内创建两个虚拟机以进行渗透测试。

## <a name="next-steps"></a>后续步骤

接下来的步骤是设置任何实验室时通用的：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)。
