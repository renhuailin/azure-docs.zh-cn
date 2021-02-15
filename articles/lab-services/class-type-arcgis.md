---
title: 使用 Azure 实验室服务为 ArcMap\ArcGIS Desktop 设置实验室 |Microsoft Docs
description: 了解如何使用 ArcGIS 为类设置实验室。
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: 8d5356f7fd2661d9743d1058a147a0b20f62850e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373702"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>设置 ArcMap\ArcGIS Desktop 的实验室

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) 是一种 (GIS) 的地理信息系统。  ArcGIS 用于 make\analyze 映射，并处理 [环境系统研究研究所](https://www.esri.com/en-us/home) (ESRI) 提供的地理数据。  尽管 ArcGIS Desktop 包含多个应用程序，但本文介绍了如何使用 ArcMap 设置实验室。  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) 用于创建、编辑和分析2d 地图。

## <a name="lab-configuration"></a>实验室配置

若要开始使用 ArcMap 设置实验室，需要一个 Azure 订阅和实验室帐户。  如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。  有关创建新实验室帐户的详细信息，请参阅 [设置实验室帐户](tutorial-setup-lab-account.md)。  你还可以使用现有的实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

启用实验室帐户设置，如下表所述。  有关如何启用 Azure Marketplace 映像的详细信息，请参阅 [指定可用于实验室创建者的 Azure marketplace 映像](https://docs.microsoft.com/azure/lab-services/specify-marketplace-images)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
|市场映像| 启用 Windows 10 专业版或 Windows 10 专业版 N 映像，以便在实验室帐户中使用。|

### <a name="licensing-server"></a>授权服务器

ArcGIS 桌面产品/服务的一种许可类型是 [并发使用许可证](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm)。  这要求你在许可证服务器上安装 ArcGIS 许可证管理器。  许可证管理器会跟踪可同时运行的软件副本数。  有关如何在服务器上设置许可证管理器的详细信息，请参阅 [许可证管理器指南](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm)。

许可证服务器通常位于本地网络中或托管在 Azure 虚拟网络中的 Azure 虚拟机上。  设置许可证服务器后，需要将虚拟网络与[实验室帐户](https://docs.microsoft.com/azure/lab-services/tutorial-setup-lab-account)[对等](https://docs.microsoft.com/azure/lab-services/how-to-connect-peer-virtual-network)。  你需要在创建实验室之前执行网络对等互连，以便实验室 Vm 可以访问许可证服务器，反之亦然。

有关详细信息，请参阅 [将许可证服务器设置为共享资源](how-to-create-a-lab-with-shared-resource.md)。

### <a name="lab-settings"></a>实验室设置

建议用于 ArcGIS Desktop 的虚拟机 (VM) 大小取决于应用程序、扩展和学生将使用的特定版本。  VM 大小还取决于学员应执行的工作负荷。  请参阅 [ArcGIS Desktop system 要求](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) ，以帮助确定 VM 大小。  确定了可能的 VM 大小后，建议测试学生的工作负荷，以确保有足够的性能。

在本文中，我们建议为版本 [10.7.1 的 ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm)使用 [**中型** VM 大小](administrator-guide.md#vm-sizing)，前提是未使用其他 ArcGIS 桌面扩展。  但是，根据类的需求，可能需要 **大** 甚至 **Small\Medium 的 GPU (可视化)** VM 大小。  例如，ArcGIS Desktop 附带的 [空间分析插件](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) 支持 gpu 以提高性能，但不要求使用 gpu。

| 实验室设置 | 值和描述 |
| ------------ | ------------------ |
|虚拟机大小| **中**。  最适合用于关系数据库、内存中缓存和分析。|  

### <a name="template-machine"></a>模板计算机

本部分中的步骤演示如何设置模板 VM：

1.  启动模板 VM，并使用 RDP 连接到计算机。

2.  按照 ESRI 中的说明下载并安装 ArcGIS 桌面组件。  这些步骤包括为 "并发使用许可" 分配许可证管理员： 
    - [安装和配置 ArcGIS Desktop 简介](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  为学生设置外部备份存储。  学生可以将文件直接保存到其分配的 VM，因为它们所做的所有更改都保存在不同的会话中。  但是，我们建议学生将其工作备份到其 VM 外部的存储中，原因如下：
    - 允许学生在类和实验室结束后访问其工作。  
    - 如果学生将其 VM 恢复到错误状态，则需要 [重置](how-to-set-virtual-machine-passwords.md#reset-vms)其映像。

    对于 ArcGIS，每个学生应在每个工作会话结束时备份以下文件：

    - mxd 文件，用于存储项目的布局信息。
    - 文件 geodatabases，用于存储 ArcGIS 生成的所有数据。
    - 该学生可能使用的任何其他数据，如光栅文件、形状文件、GeoTIFF 等。

    建议使用 OneDrive 进行备份存储。  若要在模板 VM 上设置 OneDrive，请按照 [安装和配置 onedrive](how-to-prepare-windows-template.md#install-and-configure-onedrive)一文中的步骤进行操作。 

4.  最后， [发布](how-to-create-manage-template.md#publish-the-template-vm) 模板 vm 以创建学生的 vm。

### <a name="auto-shutdown-and-disconnect-settings"></a>自动关闭和断开连接设置

实验室的 " [自动关闭" 和 "断开连接" 设置](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) 有助于确保在未使用学生的情况下将其关闭。  应根据你的学生将执行的工作负荷类型来设置这些设置，以便他们的 VM 不会在工作期间关闭。  例如，如果在指定的时间内未检测到鼠标或键盘输入，则 " **当虚拟机处于空闲状态时断开用户连接** " 设置会断开学生与 RDP 会话的连接。  此设置必须为使用鼠标或键盘的学生提供足够的时间，如运行长时间查询或等待呈现。

对于 ArcGIS，建议为以下设置设置以下值：
- 当虚拟机处于空闲状态时断开用户连接
    - 检测到空闲状态30分钟后
- 用户断开连接时关闭虚拟机
    - 用户断开连接后15分钟

## <a name="cost"></a>Cost

我们来介绍此类的可能的成本估算。 此估计不包括运行许可证服务器的成本。 我们将使用一类25名学生。 计划的类时间有20小时。 此外，每个学生在计划的类时间之外为家庭作业或分配获取10小时配额。 选择的虚拟机大小为 " **中**"，即 "42 实验室单位"。

25名学生 \* (20 个计划小时 + 10 个配额小时) \* 42 实验室单位 * 0.01 美元/小时 = 315.00 usd

>[!IMPORTANT]
> 成本估算仅用于示例目的。  有关定价的最新详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。  

## <a name="next-steps"></a>后续步骤

下一步是设置任何实验室的常见步骤。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [向学生发送电子邮件注册链接](how-to-configure-student-usage.md#send-invitations-to-users)