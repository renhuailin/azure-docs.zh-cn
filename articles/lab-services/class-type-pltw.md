---
title: 使用 Azure 实验室服务设置“项目引路”实验室
description: 了解如何设置实验室来讲授“项目引路”课程。
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: 85e8b8d55ed5a91c21c2554ce15e3ef893e30f15
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737687"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>为“项目引路”课程设置实验室

[项目引路 (PLTW)](https://www.pltw.org/)是一个非赢利组织，在美国提供计算机科学、工程和生物医学科学领域的 PreK&ndash;12 课程。  在每节 PLTW 课程中，学生使用各种软件应用程序作为其实践学习体验的一部分。  许多软件应用程序需要快速 CPU，或在某些情况下需要 GPU。  本文介绍如何为以下 PLTW 课程设置实验室，这些课程通常提供给 9&ndash;12 年级的学生：

- **工程设计概论**

    向学生介绍工程设计过程，其中包括使用 [Autodesk Inventor 计算机辅助设计 (CAD)](https://www.autodesk.com/products/inventor/new-features) 软件进行 3D 建模。

- **工程原理**
    
    学生将了解工程机制、结构和材料强度以及自动化。  这节课使用 [MD Solids](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf)、[West Point Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf) 和 [America’s Army simulation](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf) 等软件。

- **土木工程与建筑**

    学生通过使用 [Autodesk Revit](https://www.autodesk.com/products/revit/overview) 建筑设计软件进行 3D 建筑信息建模，学习建筑和场地设计与开发。

- **计算机集成制造**

    学生探索涉及机器人和自动化的现代化制造流程。   在这节课中，学生使用 [Autodesk Inventor CAD](https://www.autodesk.com/products/inventor/new-features) 和 [Autodesk Inventor 计算机辅助制造 (CAM)](https://www.autodesk.com/products/inventor-cam/overview) 软件。 

- **数字电子技术**

    学生通过使用 [National Instrument Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) 模拟和电路设计软件来研究电子逻辑电路和器件。

- **工程设计和开发**

    学生通过向工程师小组提交研究、设计和测试，为端到端解决方案做出贡献。  在这节课中，学生使用 [Autodesk Inventor CAD](https://www.autodesk.com/products/inventor/new-features) 软件。

- **计算机科学基础**

    向学生介绍计算概念和工具。  他们从基于块的编程开始，然后使用编码环境（如 [VEXcode V5 块](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf)）转到基于文本的编码。

- **计算机科学原理**
    
    学生通过使用 [Microsoft Visual Studio 代码开发环境](https://code.visualstudio.com/)，增长 [Python](https://www.python.org/) 编程专业知识。 

- **计算机科学 A**

    在这节课中，学生通过学习移动应用开发，拓展编程能力。  在这节课中，学生通过使用 [Microsoft Visual Studio 代码开发环境](https://code.visualstudio.com/)来学习 [Java](https://www.java.com/)。  学生还使用仿真器来运行和测试其移动应用代码。  若要了解如何在 Azure 实验室服务中设置仿真器，请通过 [Azure 实验室服务论坛](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices)与我们联系，以便了解详细信息。

有关课程软件的完整列表，请转到每节课的 [PLTW 站点](https://www.pltw.org/pltw-software)。

## <a name="lab-configuration"></a>实验室配置

如果要开始设置 PLTW 实验室，需要 Azure 订阅和实验室帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 

获取 Azure 订阅后，即可在 Azure 实验室服务中创建新的实验室帐户。 若要详细了解如何新建实验室帐户，请参阅[设置实验室帐户](./tutorial-setup-lab-account.md)。 也可以使用现有实验室帐户。

设置实验室帐户后，应当为学校提供的每个 PLTW 课时创建一个单独的实验室。  还建议为每种类型的 PLTW 课程创建单独的映像。  有关如何构造实验室和映像的详细信息，请参阅博客文章[从物理实验室迁移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)。

### <a name="lab-account-settings"></a>实验室帐户设置

启用实验室帐户设置，如下表所述。 若要详细了解如何启用 Azure 市场映像，请参阅[指定可供实验室创建者使用的 Azure 市场映像](./specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| -------------------- | ----- |
| 市场映像 | 启用 Windows 10 专业版映像，以便在实验室帐户中使用。 |

<br>

### <a name="lab-settings"></a>实验室设置
建议用于 PLTW 课程的虚拟机 (VM) 的大小取决于学生在课堂中执行的工作负荷类型。  对于前面列出的课程，建议使用小型 GPU（可视化）和大型 VM 大小。 设置 PLTW 课程的实验室时，请参阅下表中的指南：

| 实验室设置 | 值和描述 | 课程建议 |
| ------------ | ------------------ | --- |
| 虚拟机大小 | **小型 GPU (可视化)**<br>最适用于使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏和编码。 | 建议对以下 PLTW 课程使用此大小：土木工程与建筑、数字电子技术、计算机集成制造、工程设计与开发以及工程设计概论。
| 虚拟机大小 | **大型**<br>最适用于需要 CPU 更快、本地磁盘性能更佳、大型数据库和大型内存缓存的应用程序。 | 建议对以下 PLTW 课程使用此大小：工程原理、计算机科学基础、计算机科学原理和计算机科学 A。 |

<br>

### <a name="license-server"></a>许可证服务器
前面提到的 PLTW 课程中使用的大多数软件“不”需要访问许可证服务器。  但是，如果计划对以下软件使用 Autodesk 网络许可模型，则需要访问许可证服务器：
-   Revit
-   Inventor CAD
-   Inventor CAM

如果要将网络许可用于 Autodesk 软件，[PLTW 提供了详细步骤](https://www.pltw.org/pltw-software)，用于在许可证服务器上安装 Autodesk Network License Manager。  此许可证服务器通常位于本地网络中，或托管在 Azure 虚拟网络中的 Azure 虚拟机 (VM) 上。

设置许可证服务器后，需要[将虚拟网络](./how-to-connect-peer-virtual-network.md)与[实验室帐户](./tutorial-setup-lab-account.md)对等。 用户需要在创建实验室“之前”执行网络对等互连，以便实验室 VM 可以访问许可证服务器，反之亦然。

Autodesk 生成的许可证文件嵌入了许可证服务器的 MAC 地址。  如果决定使用 Azure VM 托管许可证服务器，请务必确保许可证服务器的 MAC 地址不会更改。 如果 MAC 地址发生更改，则需要重新生成许可文件。 如果要防止 MAC 地址更改，请执行以下操作：

- 为托管许可证服务器的 Azure VM[ 设置静态专用 IP 和 MAC 地址](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address)。
- 请确保在具有足够 VM 容量的区域或位置同时设置实验室帐户和许可证服务器的虚拟网络，便于以后无需将这些资源移到新的区域或位置。

有关详细信息，请参阅[将许可证服务器设置为共享资源](./how-to-create-a-lab-with-shared-resource.md)。

### <a name="template-machine"></a>模板计算机
PLTW 所需的某些安装文件很大。 将文件下载到实验室模板 VM 时，可能需要较长时间才能进行复制。

建议在物理环境中创建 PLTW 映像，而不是将安装文件下载到模板计算机并在其中安装所有内容。  然后，可以将自定义映像导入共享映像库，以便使用它们来创建实验室。  有关详细信息，请参阅[创建自定义映像的建议方法](approaches-for-custom-image-creation.md)。

按照此建议进行操作时，请注意设置实验室的主要任务：

1. 在物理环境中，为该课程创建映像。

    a.  使用 PLTW 的详细步骤下载安装文件并安装所需的软件。

    > [!NOTE]    
    > 安装 Autodesk 应用程序时，安装这些应用程序的计算机需要能够与许可证服务器进行通信。 Autodesk 安装向导将提示指定托管许可证服务器的计算机的计算机名。  如果要在 Azure VM 上托管许可证服务器，则可能需要等待在实验室模板 VM 上安装 Autodesk，以便安装向导可以访问许可证服务器。

    b.  [安装和配置 OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) 或学校可能使用的其他备份选项。
    
    c.  [安装和配置 Microsoft 更新](./how-to-prepare-windows-template.md#install-and-configure-updates)。

1.  将自定义映像上传到[附加到实验室帐户的共享映像库](./how-to-attach-detach-shared-image-gallery.md)。

1.  创建实验室，然后选择在上一步中上传的自定义映像。

1.  创建实验室后，启动并连接到模板 VM，验证映像是否按预期方式工作。

1.  最后，发布模板 VM 以创建学生的 VM。

> [!NOTE]
> 如果你的学校需要执行内容筛选，例如，为了符合[儿童互联网保护法案 (CIPA)](https://www.fcc.gov/consumers/guides/childrens-internet-protection-act)的要求，你将需要使用第三方软件。  有关详细信息，请阅读有关[使用实验室服务进行内容筛选](./administrator-guide.md#content-filtering)的指南。

## <a name="student-devices"></a>学生设备
学生可以从 Windows 计算机、Mac 和 Chromebook 连接到其实验室 VM。 有关说明，请参阅：

- [从 Windows 进行连接](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [从 Mac 连接](./connect-virtual-machine-mac-remote-desktop.md)
- [从 Chromebook 连接](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>成本
接下来介绍 PLTW 课程的示例成本估算。  此估算不包括运行许可证服务器或使用共享映像库的成本。 假设一个课程有 25 个学生，每个学生有 20 小时的计划课程时间。  对于计划课程时间之外的家庭作业或课后作业，每个学生还额外增加了 10 个配额小时。  估计的成本如下：

- **大型 VM**

    25 个学生 &times;（20 个计划小时 + 10 个配额小时）&times; 70 个实验室单位 &times; 0.01 美元/小时 = 525.00 美元

- **小型 GPU（可视化）**

    25 个学生 &times;（20 个计划小时 + 10 个配额小时）&times; 160 个实验室单位 &times; 0.01 美元/小时 = 1200.00 美元

> [!IMPORTANT] 
> 成本估算仅用于示例。  有关最新定价信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

> [!NOTE] 
> 许多 PLTW 课程使用通过浏览器（如 MIT App Inventor）访问的应用程序。  这些基于浏览器的应用程序不需要快速 CPU 或 GPU，用户可以从具有 Internet 连接的任何设备访问它们。  当学生使用这种类型的应用程序时，建议他们使用物理设备上的浏览器，而不是实验室 VM 上的浏览器。 学生只要将其实验室 VM 仅用于需要快速 CPU 或 GPU 的应用程序，即可帮助降低成本。

## <a name="next-steps"></a>后续步骤

设置实验室时，请参阅以下文章：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users) 
