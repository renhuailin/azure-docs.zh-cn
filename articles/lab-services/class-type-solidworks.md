---
title: 使用 Azure 实验室服务设置 SOLIDWORKS 实验室以用于工程目的 | Microsoft Docs
description: 了解如何使用 SOLIDWORKS 设置用于工程课程的实验室。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 3a7e8c47977f0518a3a3e9f8a6fd2e57454e1c42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626098"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>使用 SOLIDWORKS 设置用于工程课程的实验室

[SOLIDWORKS](https://www.solidworks.com/) 提供 3D 计算机辅助设计 (CAD) 环境，可对实体对象进行建模，并用于不同类型的工程领域。  借助 SOLIDWORKS，工程师可以轻松地创建、直观显示、模拟和记录自己的设计。

大学中常用的许可选项是 SOLIDWORKS 的网络许可。   在此选项下，许可服务器管理一个许可证池，其中的所有许可证供用户共享。  这种类型的许可证有时称为“浮动”许可证，因为许可证数量只要足够满足并发用户人数即可。  当某个用户完成了 SOLIDWORKS 的使用后，许可证将重新回到集中管理的许可证池中，以便其他用户重复使用。

本文介绍了如何设置一个使用 SOLIDWORKS 2019 和网络许可的课程。

## <a name="license-server"></a>许可证服务器

SOLIDWORKS 网络许可模式要求许可证服务器上安装并激活 SolidNetWork License Manager。  此许可证服务器通常位于本地网络或 Azure 中的专用网络上。  有关如何在服务器上设置 SolidNetWork License Manager 的详细信息，请参阅 SOLIDWORKS 安装指南中的[安装和激活许可证管理器](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm)。  在进行此项设置时，请记住所使用的 **端口号** 和 [**序列号**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm)，因为在后续步骤中将需要提供这些信息。

设置好许可证服务器后，需要将[虚拟网络 (VNet)](./how-to-connect-peer-virtual-network.md) 对等互连到[实验室帐户](./tutorial-setup-lab-account.md)。  必须先完成网络对等互连后再创建实验室，以便于实验室虚拟机可以访问许可证服务器，反之亦然。

> [!NOTE]
> 应验证防火墙上的适当端口是否已打开，以允许实验室虚拟机与许可证服务器之间的通信。  例如，请参阅有关[为 Windows 防火墙修改许可证管理器计算机端口](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm)的说明，其中显示了如何将入站和出站规则添加到许可证服务器的防火墙。  可能还需要打开实验室虚拟机的端口。  有关此操作的详细信息，请遵循有关[实验室防火墙设置](./how-to-configure-firewall-settings.md)的文章中的相应步骤，包括如何获取实验室的公共 IP 地址。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要拥有 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 若要详细了解如何新建实验室帐户，请参阅有关[如何设置实验室帐户](./tutorial-setup-lab-account.md)的教程。 也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中所述的设置。 若要详细了解如何启用市场映像，请参阅有关[如何指定可供实验室创建者使用的市场映像](./specify-marketplace-images.md)的文章。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
|市场映像| 启用 Windows 10 专业版映像，以便在实验室帐户中使用。|

> [!NOTE]
> 除 Windows 10 外，SOLIDWORKS 还支持其他版本的 Windows。  有关详细信息，请参阅 [SOLIDWORKS 系统要求](https://www.solidworks.com/sw/support/SystemRequirements.html)。

### <a name="lab-settings"></a>实验室设置

设置课堂实验室时，请使用下表中的设置。 若要详细了解如何创建课堂实验室，请参阅设置课堂实验室教程。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| 小型 GPU（可视化）。  此 VM 最适合使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏和编码。|  
|虚拟机映像| Windows 10 专业版|

> [!NOTE]
> 配置“小型 GPU (可视化)”虚拟机大小可实现高性能的图形体验。  若要详细了解此虚拟机大小，请参阅有关[如何为实验室设置 GPU](./how-to-setup-lab-gpu.md) 的文章。

> [!WARNING]
> 在创建实验室 **之前**，不要忘记将实验室帐户的[虚拟网络对等互连](./how-to-connect-peer-virtual-network.md)到许可证服务器的虚拟网络。

## <a name="template-virtual-machine-configuration"></a>模板虚拟机配置

本部分中的步骤说明了如何通过下载 SOLIDWORKS 安装文件并安装客户端软件来设置模板虚拟机：

1. 启动模板虚拟机，并使用 RDP 连接到计算机。

1. 下载 SOLIDWORKS 客户端软件的安装文件。 有两个下载选项：
   - 从 [SOLIDWORKS 客户门户](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F)下载。
   - 从服务器上的目录下载。  如果使用此选项，需要确保可以从模板虚拟机访问服务器。  例如，此服务器所处的虚拟网络可能就是与实验室帐户对等互连的同一虚拟网络。
  
    有关详细信息，请参阅 SOLIDWORKS 安装指南中的[在 SOLIDWORKS 中的单独计算机上安装](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0)。

1. 安装文件下载完成后，使用 SOLIDWORKS Installation Manager 安装客户端软件。 有关详细信息，请参阅 SOLIDWORKS 安装指南中的[安装许可证客户端](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm)。

    > [!NOTE]
    > 在“添加服务器”对话框中，按提示输入许可证服务器所使用的 **端口号**，以及许可证服务器的名称或 IP 地址。

## <a name="cost"></a>Cost

我们来介绍此课程可能的估算成本。 此估算中不包括运行许可证服务器的成本。 课程将提供给 25 名学生。 计划的课程时长为 20 小时。 此外，在计划的课程时间之外还为每名学生提供 10 小时配额，用于完成家庭作业或课外作业。 我们选择的虚拟机大小是“小型 GPU (可视化)”，即 160 个实验室单位。

25 名学生（计划的 20 小时 + 配额的 10 小时）160 个实验室单位 * 每小时 0.01 美元 = 1200.00 美元\*\*

>[!IMPORTANT]
> 成本估算仅用于示例目的。  有关定价的最新详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。  

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室时通用的。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)