---
title: 使用 Azure 实验室服务和 GNS3 设置网络实验室 |Microsoft Docs
description: 了解如何使用 Azure 实验室服务设置实验室，通过 GNS3 讲授网络。
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500508"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>设置实验室来讲授网络类 
本文介绍如何设置一个类，使学生可以使用 [GNS3](https://www.gns3.com/) software 来模拟、配置、测试虚拟和真实网络并对其进行故障排除。 

本文包含两个主要部分。 第一部分介绍如何创建教室实验室。 第二部分介绍如何创建启用了嵌套虚拟化的模板计算机，以及如何安装和配置 GNS3。

## <a name="lab-configuration"></a>实验室配置
若要设置此实验室，你需要具有一个 Azure 订阅才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户或使用现有帐户。 请参阅以下教程，了解如何创建新的实验室帐户： [设置实验室帐户教程](tutorial-setup-lab-account.md)。

按照 [本教程](tutorial-setup-classroom-lab.md) 创建新的实验室，然后应用以下设置：

| 虚拟机大小 | 图像 |
| -------------------- | ----- | 
| 大型(嵌套虚拟化) | Windows 10 专业版，版本1909 |

## <a name="template-machine"></a>模板计算机 

创建模板计算机后，启动计算机并连接到该计算机以完成以下三个主要任务。 
 
1. 为嵌套虚拟化准备模板机。
2. 安装 GNS3。
3. 在 Hyper-v 中创建嵌套的 GNS3 VM。
4. 配置 GNS3 以使用 Windows Hyper-v VM。
5. 添加相应的设备。
6. 发布模板。


### <a name="prepare-template-machine-for-nested-virtualization"></a>准备用于嵌套虚拟化的模板计算机
- 按照 [本文](how-to-enable-nested-virtualization-template-vm.md) 中的说明准备模板虚拟机以进行嵌套虚拟化。 

### <a name="install-gns3"></a>安装 GNS3
- 按照在 [Windows 上安装 GNS3](https://docs.gns3.com/docs/getting-started/installation/windows)的说明进行操作。  请确保在组件对话框中包括安装 **GNS3 VM** ，请参阅下面的。

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

最终，你将看到 GNS3 VM 选择。 请确保选择 " **hyper-v** " 选项。

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  此选项将下载 PowerShell 脚本和 VHD 文件，以便在 Hyper-v 管理器中创建 GNS3 VM。 使用默认值继续安装。 **安装完成后，请不要启动 GNS3**。

### <a name="create-gns3-vm"></a>创建 GNS3 VM
完成设置后，会将一个 zip 文件 **"GNS3.VM.Hyper-V.2.2.17.zip"** 下载到安装文件所在的文件夹中，其中包含驱动器和 PowerShell 脚本以创建 hyper-v vm。
- **全部提取** GNS3.VM.Hyper-V.2.2.17.zip 上。  此操作将解压驱动器和 PowerShell 脚本以创建 VM。
- 通过右键单击该文件，在 "create-vm.ps1" PowerShell 脚本上 **运行 powershell** 。
- 可能会显示执行策略更改请求。 输入 "Y" 以执行该脚本。

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- 脚本完成后，可以确认已在 Hyper-v 管理器中创建了 VM "GNS3 VM"。

### <a name="configure-gns3-to-use-hyper-v-vm"></a>将 GNS3 配置为使用 Hyper-v VM
安装 GNS3 并添加 GNS3 VM 后，请启动 GNS3 将两者链接到一起。  [GNS3 安装向导将自动启动。](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard)  
- 使用 " **从虚拟机运行设备"。** 选项。  使用向导的其余部分的默认值，直到 **找不到 VMware vmrun 工具。** 错误。

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- 选择 **"确定"**，然后 **取消** 向导。
- 若要完成到 hyper-v vm 的连接，请打开 **编辑**  ->  **首选项**  ->  **GNS3 vm** ，并选择 "**启用 GNS3 vm"** 并选择 " **hyper-v** " 选项。
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>添加适当的设备

此时，需要为类添加相应的 [设备。](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace)

### <a name="publish-template"></a>发布模板

现在正确设置模板 VM，并准备好进行发布，有几个要点需要检查。
- 请确保 GNS3 VM 已关闭或关闭。  VM 仍在运行时发布将损坏 VM。
- 关闭 GNS3，在发布和运行时，可能会产生意外的副作用。
- 清理任何安装文件或其他不必要的文件。

## <a name="cost"></a>成本  

如果要估计此实验室的成本，可以使用以下示例： 
 
如果一个班级有 25 个学生，计划的课堂时间为 20 小时且家庭作业或作业时数配额为 10 小时，则实验室的价格为： 

25名学生 * (20 多个) 小时 * 84 实验室单位 * 0.01 美元/小时 = 630 美元。 

**重要提示：** 成本估算仅用于示例目的。  有关定价的最新详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结论
本文逐步介绍如何使用 GNS3 创建用于网络配置的实验室。

## <a name="next-steps"></a>后续步骤
接下来的步骤是设置任何实验室时通用的：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)。