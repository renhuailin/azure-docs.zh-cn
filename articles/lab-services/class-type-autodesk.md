---
title: 使用 Azure 实验室服务设置 Autodesk 的实验室
description: 了解如何设置实验室以通过 Autodesk 教授工程课程。
author: nicolela
ms.topic: article
ms.date: 04/21/2021
ms.author: nicolela
ms.openlocfilehash: 6cc983907349ca8eab0731b18c18d526f2b75ba5
ms.sourcegitcommit: 43be2ce9bf6d1186795609c99b6b8f6bb4676f47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108281113"
---
# <a name="set-up-labs-for-autodesk"></a>设置 Autodesk 实验室

本文介绍如何为工程课程设置 Autodesk Inventor 和 Autodesk Revit 软件：
- [Inventor 计算机辅助设计 (CAD)](https://www.autodesk.com/products/inventor/new-features) 和[计算机辅助制造 (CAM)](https://www.autodesk.com/products/inventor-cam/overview) 提供 3D 建模，用于工程设计。
- [Revit](https://www.autodesk.com/products/revit/overview) 用于 3D 构建信息建模 (BIM) 的体系结构设计。

Autodesk 通常用于大学和 K-12 学校。  例如，在 K-12 中，AutoDesk 包含在[项目引路 (PLTW)](./class-type-pltw.md) 课程中。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要有 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 若要详细了解如何新建实验室帐户，请参阅有关[如何设置实验室帐户](./tutorial-setup-lab-account.md)的教程。 也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

启用实验室帐户设置，如下表所述。 若要详细了解如何启用 Azure 市场映像，请参阅[指定可供实验室创建者使用的 Azure 市场映像](./specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| -------------------- | ----- |
| 市场映像 | 启用 Windows 10 映像，以便在实验室帐户中使用。 |

### <a name="lab-settings"></a>实验室设置
建议的虚拟机 (VM) 大小取决于学生需要完成的工作负载类型。  建议使用小型 GPU（可视化）大小。

| 实验室设置 | 值和描述 |
| ------------ | ------------------ |
| 虚拟机大小 | **小型 GPU (可视化)**<br>最适用于使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏和编码。 | 

> [!NOTE]
> 配置“小型 GPU (可视化)”虚拟机大小可实现高性能的图形体验。 若要详细了解此虚拟机大小，请参阅有关[如何为实验室设置 GPU](./how-to-setup-lab-gpu.md) 的文章。

### <a name="license-server"></a>许可证服务器
如果计划使用 Autodesk 网络许可模型，则需要访问许可证服务器。  有关详细信息，请参阅关于[网络许可证管理](https://knowledge.autodesk.com/customer-service/network-license-administration/network-deployment/preparing-for-deployment/determining-installation-type)的 Autodesk 文章。

为了在 Autodesk 软件中使用网络许可，[Autodesk 提供了详细步骤](https://knowledge.autodesk.com/customer-service/network-license-administration/install-and-configure-network-license)，介绍了如何在许可证服务器上安装 Autodesk Network License Manager。  此许可证服务器通常位于本地网络中，或托管在 Azure 虚拟网络中的 Azure 虚拟机 (VM) 上。

设置许可证服务器后，需要将[虚拟网络](./how-to-connect-peer-virtual-network.md)与[实验室帐户](./tutorial-setup-lab-account.md)进行对等互连。 用户需要在创建实验室之前执行网络对等互连，以便实验室 VM 可以访问许可证服务器，反之亦然。

Autodesk 生成的许可证文件嵌入了许可证服务器的 MAC 地址。  如果决定使用 Azure VM 托管许可证服务器，请务必确保许可证服务器的 MAC 地址不会更改。 如果 MAC 地址发生更改，则需要重新生成许可文件。 如果要防止 MAC 地址更改，请执行以下操作：

- 为托管许可证服务器的 Azure VM [设置静态专用 IP 和 MAC 地址](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address)。
- 请确保在具有足够 VM 容量的区域或位置同时设置实验室帐户和许可证服务器的虚拟网络，以便以后无需将这些资源移到新的区域或位置。

有关详细信息，请参阅[将许可证服务器设置为共享资源](./how-to-create-a-lab-with-shared-resource.md)。

> [!WARNING]
> 在创建实验室之前，不要忘记将实验室帐户的[虚拟网络](./how-to-connect-peer-virtual-network.md)与许可证服务器的虚拟网络进行对等互连。

### <a name="template-machine"></a>模板计算机
本部分中的步骤演示如何设置模板 VM：

1. 启动模板 VM 并连接到计算机。

1. 使用 [AutoDesk 的说明](https://knowledge.autodesk.com/customer-service/download-install/install-software)下载并安装 Inventor 和 Revit。  出现提示时，请指定许可证服务器的计算机名。

1.  最后，发布模板 VM 以创建学生的 VM。

## <a name="cost"></a>成本
接下来介绍此课程的示例成本估算。  此估算中不包括运行许可证服务器的成本。 假设一个班有 25 名学生，每名学生有 20 小时的课堂时间。  除课堂时间外，每名学生还有另外 10 小时的时间来处理家庭作业或作业。  我们选择的虚拟机大小是“小型 GPU (可视化)”，即 160 个实验室单位。

- 25 名学生 &times;（20 小时课堂时间 + 10 小时额外时间）&times; 160 个实验室单位 &times; 0.01 美元/小时 = 1200.00 美元

> [!IMPORTANT] 
> 成本估算仅用于示例。  有关最新定价信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="next-steps"></a>后续步骤

设置实验室时，请参阅以下文章：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users) 
