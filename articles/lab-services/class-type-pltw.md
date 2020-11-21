---
title: 在 Azure 实验室服务实验室中设置项目主管
description: 了解如何设置实验室来向项目主管讲授类的方式。
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024613"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>为项目主管设置实验室，方法为类

[项目主管)  (PLTW 的方式 ](https://www.pltw.org/) 是一个非赢利组织，它在 &ndash; 计算机科学、工程和生物医学科学的整个美国提供 PreK 12 课程。  在每个 PLTW 类中，学生使用各种软件应用程序作为其动手学习体验的一部分。  许多软件应用程序都需要快速 CPU，在某些情况下，需要使用 GPU。  本文介绍如何为以下 PLTW 类设置实验室，这些类通常在成绩为 9 12 的学生中提供 &ndash; ：

- **工程设计简介**

    学员会引入工程设计过程，其中包括使用 [Autodesk 发明者计算机辅助设计 (CAD) ](https://www.autodesk.com/products/inventor/new-features) 软件进行3d 建模。

- **工程原则**
    
    学生了解工程机制、结构和材料强度以及自动化。  此类使用诸如 [MD 实体](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf)、 [西点桥设计器](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)和 [美国军队模拟这样的](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf)软件。

- **民事工程和体系结构**

    学生通过使用 [Autodesk Revit](https://www.autodesk.com/products/revit/overview) 体系结构设计软件构建和设计和开发，通过三维建筑 (model.bim) 建模构建信息建模。

- **计算机集成制造**

    学员探索了涉及机器人和自动化的新式制造流程。   在此类中，学生使用 [Autodesk 发明者 CAD](https://www.autodesk.com/products/inventor/new-features) 和 [Autodesk 发明者计算机辅助制造 (CAM) ](https://www.autodesk.com/products/inventor-cam/overview) 软件。 

- **数字电子设备**

    学生通过使用 [国家仪器 Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) 模拟和线路设计软件来研究电子逻辑电路和设备。

- **工程设计和开发**

    学生提供端到端解决方案，通过将其呈现给工程师面板来进行研究、设计和测试。  在此类中，学生使用 [Autodesk 发明者 CAD](https://www.autodesk.com/products/inventor/new-features) 软件。

- **计算机科学基础知识**

    学生被引入了计算概念和工具。  它们从基于块的编程开始，然后使用编码环境（如 [VEXcode V5 块](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf)）迁移到基于文本的编码。

- **计算机科学原则**
    
    学生通过使用[Microsoft Visual Studio 代码开发环境](https://code.visualstudio.com/)，利用[Python](https://www.python.org/)提高其编程专业知识。 

- **计算机科学 A**

    学生通过学习移动应用开发，扩展了此类的编程能力。  在此类中，它们使用[Microsoft Visual Studio 代码开发环境](https://code.visualstudio.com/)来学习[Java](https://www.java.com/) 。  学生还使用模拟器来运行和测试其移动应用代码。  有关如何在 Azure 实验室服务中设置模拟器的信息， [请联系 Azure 实验室服务](mailto:AzLabsCOVIDSupport@microsoft.com)。

有关类软件的完整列表，请前往每个类的 [PLTW 站点](https://www.pltw.org/pltw-software) 。

## <a name="lab-configuration"></a>实验室配置

若要开始设置 PLTW 的实验室，需要一个 Azure 订阅和实验室帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 

获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 有关创建新实验室帐户的详细信息，请参阅 [设置实验室帐户](./tutorial-setup-lab-account.md)。 你还可以使用现有的实验室帐户。

设置实验室帐户后，应为 school 提供的每个 PLTW 类会话创建一个单独的实验室。  还建议为每种类型的 PLTW 类创建单独的映像。  有关如何构建实验室和映像的详细信息，请参阅博客文章 [从物理实验室迁移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)。

### <a name="lab-account-settings"></a>实验室帐户设置

启用实验室帐户设置，如下表所述。 有关如何启用 Azure Marketplace 映像的详细信息，请参阅 [指定可用于实验室创建者的 Azure marketplace 映像](./specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| -------------------- | ----- |
| 市场映像 | 启用 Windows 10 专业版映像，以便在实验室帐户中使用。 |

<br>

### <a name="lab-settings"></a>实验室设置
建议用于 PLTW 类的虚拟机 (VM) 大小取决于你的学生在类中执行的工作负荷类型。  对于前面列出的类，建议使用小型 GPU (可视化) 和大型 VM 大小。 设置 PLTW 类的实验室时，请参阅下表中的指南：

| 实验室设置 | 值和描述 | 类建议 |
| ------------ | ------------------ | --- |
| 虚拟机大小 | **小型 GPU (可视化)**<br>最适合用于框架（如 OpenGL 和 DirectX）的远程可视化、流式处理、游戏和编码。 | 建议为以下 PLTW 类使用此大小：民事工程和体系结构、数字电子设备、计算机集成制造以及工程设计和开发。
| 虚拟机大小 | **大型**<br>最适合于需要更快的 Cpu、更好的本地磁盘性能、大型数据库和大型内存缓存的应用程序。 | 建议为以下 PLTW 类使用此大小：工程设计简介、工程设计原则、计算机科学要点、计算机科学原则和计算机科学 A。 |

<br>

### <a name="license-server"></a>许可证服务器
前面提到的 PLTW 类中使用的大多数软件 *不* 需要访问许可证服务器。  但是，如果你计划对以下软件使用 Autodesk 网络许可模型，则需要访问许可证服务器：
-   Revit
-   发明者
-   发明者凸轮

若要将网络许可用于 Autodesk software， [PLTW 提供了](https://www.pltw.org/pltw-software) 在许可证服务器上安装 Autodesk 网络许可证管理器的详细步骤。  此许可证服务器通常位于本地网络中，或托管在 azure 虚拟网络中 (VM) 的 Azure 虚拟机上。

设置许可证服务器后，需要将虚拟网络与[实验室帐户](./tutorial-setup-lab-account.md)[对等](./how-to-connect-peer-virtual-network.md)。 你需要在创建实验室 *之前* 执行网络对等互连，以便实验室 vm 可以访问许可证服务器，反之亦然。

Autodesk 生成的许可证文件嵌入许可证服务器的 MAC 地址。  如果决定使用 Azure VM 托管许可证服务器，请务必确保许可证服务器的 MAC 地址不会更改。 如果 MAC 地址发生更改，则需要重新生成许可文件。 若要防止 MAC 地址更改，请执行以下操作：

- 为托管许可证服务器的 Azure VM[设置静态专用 IP 和 MAC 地址](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address)。
- 请确保在具有足够 VM 容量的区域或位置中同时设置实验室帐户和许可证服务器的虚拟网络，以便以后无需将这些资源移到新的区域或位置。

有关详细信息，请参阅 [将许可证服务器设置为共享资源](./how-to-create-a-lab-with-shared-resource.md)。

### <a name="template-machine"></a>模板计算机
PLTW 所需的某些安装文件很大。 将文件下载到实验室模板 VM 时，可能需要较长时间才能进行复制。

建议在物理环境中创建 PLTW 映像，而不是将安装文件下载到模板机并在其中安装所有内容。  然后，你可以将自定义图像导入共享映像库，以便可以使用它们来创建实验室。  有关详细信息，请参阅将 [自定义映像上传到共享映像库](./upload-custom-image-shared-image-gallery.md)。

按照此建议进行操作时，请注意设置实验室的主要任务：

1. 在物理环境中，为类创建图像。

    a.  使用 PLTW 的详细步骤下载安装文件并安装所需的软件。

    > [!NOTE]    
    > 安装 Autodesk 应用程序时，安装这些应用程序的计算机需要能够与许可证服务器进行通信。 Autodesk 安装向导将提示你指定托管许可证服务器的计算机的计算机名称。  如果在 Azure VM 上托管许可证服务器，则可能需要等待在实验室模板 VM 上安装 Autodesk，以便安装向导可以访问你的许可证服务器。

    b.  [安装和配置](./how-to-prepare-windows-template.md#install-and-configure-onedrive) 你的学校可能使用的 OneDrive 或其他备份选项。
    
    c.  [安装并配置 Windows 更新](./how-to-prepare-windows-template.md#install-and-configure-updates)。

1.  将自定义映像上传到 [附加到实验室帐户的共享映像库](./how-to-attach-detach-shared-image-gallery.md)。

1.  创建实验室，然后选择在上一步中上传的自定义映像。

1.  创建实验室后，启动并连接到模板 VM，验证映像是否按预期方式工作。

1.  最后，发布模板 VM 以创建学生的 Vm。

## <a name="student-devices"></a>学生设备
学生可以从 Windows 计算机、Mac 和 Chromebook 连接到其实验室 Vm。 有关说明，请参阅：

- [从 Windows 进行连接](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [从 Mac 连接](./connect-virtual-machine-mac-remote-desktop.md)
- [从 Chromebook 连接](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>成本
接下来，我们将介绍 PLTW 类的一个示例成本估算。  此估计不包括运行许可证服务器或使用共享映像库的成本。 假设有25名学生，每位学生的计划类时间为20小时。  对于计划类时间之外的家庭作业或分配，每个学生还额外增加了10个配额小时。  估计的成本如下：

- **大型 VM**

    25名学生 &times; (20 个计划小时 + 10 个配额小时) &times; 70 实验室单位 &times; usd 0.01/小时 = usd 525.00

- **小型 GPU (可视化)**

    25名学生 &times; (20 个计划小时 + 10 个配额小时) &times; 160 实验室单位 &times; usd 0.01/小时 = usd 1200.00

> [!IMPORTANT] 
> 成本估算仅用于示例目的。  有关最新定价信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

> [!NOTE] 
> 许多 PLTW 类使用通过浏览器访问的应用程序，如 MIT App 发明者。  这些基于浏览器的应用程序不需要快速 CPU 或 GPU，你可以从任何具有 internet 连接的设备访问它们。  当学生使用这种类型的应用程序时，我们建议他们在其实际设备上使用浏览器，而不是在其实验室 VM 上使用浏览器。 学生只需将其实验室 VM 用于需要快速 CPU 或 GPU 的应用程序，即可帮助降低成本。

## <a name="next-steps"></a>后续步骤

设置实验室时，请参阅以下文章：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users) 
