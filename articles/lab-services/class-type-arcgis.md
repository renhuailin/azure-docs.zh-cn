---
title: 使用 Azure 实验室服务为 ArcMap\ArcGIS Desktop 设置实验室 | Microsoft Docs
description: 了解如何使用 ArcGIS 为课程设置实验室。
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: 530597a72b19afa1e80b5c7640b105d86479b1c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740097"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>为 ArcMap\ArcGIS Desktop 设置实验室

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) 是一种地理信息系统 (GIS) 类型。  ArcGIS 用于创建\分析地图，并处理由 [Environmental Systems Research Institute](https://www.esri.com/en-us/home) (ESRI) 提供的地理数据。  虽然 ArcGIS Desktop 包含多个应用程序，但本文演示如何使用 ArcMap 设置实验室。  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) 用于创建、编辑和分析2D 地图。

## <a name="lab-configuration"></a>实验室配置

若要开始使用 ArcMap 设置实验室，需要 Azure 订阅和实验室帐户。  如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

获取 Azure 订阅后，即可在 Azure 实验室服务中创建新的实验室帐户。  若要详细了解如何新建实验室帐户，请参阅[设置实验室帐户](tutorial-setup-lab-account.md)。  也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

启用实验室帐户设置，如下表所述。  若要详细了解如何启用 Azure 市场映像，请参阅[指定可供实验室创建者使用的 Azure 市场映像](./specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
|市场映像| 启用 Windows 10 专业版或 Windows 10 专业版 N 映像，以便在实验室帐户中使用。|

### <a name="licensing-server"></a>授权服务器

ArcGIS Desktop 提供的一种许可类型是[并发使用许可证](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm)。  这要求在许可证服务器上安装 ArcGIS 许可证管理器。  许可证管理器会跟踪可同时运行的软件副本数。  有关如何在服务器上设置许可证管理器的详细信息，请参阅[许可证管理器指南](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm)。

此许可证服务器通常位于本地网络中，或托管在 Azure 虚拟网络中的 Azure 虚拟机上。  设置许可证服务器后，需要[将虚拟网络](./how-to-connect-peer-virtual-network.md)与[实验室帐户](./tutorial-setup-lab-account.md)对等。  用户需要在创建实验室“之前”执行网络对等互连，以便实验室 VM 可以访问许可证服务器，反之亦然。

有关详细信息，请参阅[将许可证服务器设置为共享资源](how-to-create-a-lab-with-shared-resource.md)。

### <a name="lab-settings"></a>实验室设置

建议用于 ArcGIS Desktop 的虚拟机 (VM) 大小取决于学生将使用的应用程序、扩展和特定版本。  VM 大小还取决于学生预计执行的工作负载。  请参阅 [ArcGIS Desktop 系统要求](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm)以帮助确定 VM 大小。  确定了潜在 VM 大小后，建议测试学生的工作负载，以确保有足够的性能。

在本文中，建议将[中等 VM 大小](administrator-guide.md#vm-sizing)用于版本 [10.7.1 的 ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm)，前提是未使用其他 ArcGIS Desktop 扩展。  但是，根据课程需求，可能需要大型甚至是小型\中等 GPU（可视化）VM 大小 。  例如，ArcGIS Desktop 附带的[空间分析扩展](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm)支持 GPU 以增强性能，但不要求使用 GPU。

| 实验室设置 | 值和描述 |
| ------------ | ------------------ |
|虚拟机大小| **中**。  最适用于关系数据库、内存中缓存和分析。|  

### <a name="template-machine"></a>模板计算机

本部分中的步骤演示如何设置模板 VM：

1.  启动模板 VM 并使用 RDP 连接到计算机。

2.  按照 ESRI 提供的说明下载并安装 ArcGIS Desktop 组件。  这些步骤包括为并发使用许可分配许可证管理器： 
    - [安装和配置 ArcGIS Desktop 简介](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  为学生设置外部备份存储。  学生可以将文件直接保存到其分配的 VM，因为进行的所有更改都会在会话间保存。  但是，建议学生将其工作备份到其 VM 外部的存储中，原因如下：
    - 使学生可以在课程和实验室结束后访问其工作。  
    - 以防学生使其 VM 进入错误状态，其映像需要[重置](how-to-set-virtual-machine-passwords.md#reset-vms)。

    对于 ArcGIS，每个学生都应在每个工作会话结束时备份以下文件：

    - mxd 文件，用于存储项目的布局信息。
    - 文件地理数据库，用于存储 ArcGIS 生成的所有数据。
    - 学生可能使用的任何其他数据，如光栅文件、形状文件、GeoTIFF 等。

    建议使用 OneDrive 进行备份存储。  若要在模板 VM 上设置 OneDrive，请按照[安装和配置 OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive) 一文中的步骤进行操作。 

4.  最后，[发布](how-to-create-manage-template.md#publish-the-template-vm)模板 VM 以创建学生的 VM。

### <a name="auto-shutdown-and-disconnect-settings"></a>自动关闭和断开连接设置

实验室的[自动关闭和断开连接设置](cost-management-guide.md#automatic-shutdown-settings-for-cost-control)有助于确保学生的 VM 在未使用时关闭。  应根据学生将执行的工作负载类型设置这些设置，以便其 VM 不会在工作期间关闭。  例如，“当虚拟机空闲时断开用户连接”设置会在指定时间量内未检测到鼠标或键盘输入之后断开学生与 RDP 会话的连接。  此设置必须允许在学生不主动使用鼠标或键盘的情况下有足够的时间处理工作负载，例如用于运行长时间查询或等待呈现。

对于 ArcGIS，建议为这些设置设置以下值：
- 当虚拟机空闲时断开用户连接
    - 检测到空闲状态后 30 分钟
- 用户断开连接时关闭虚拟机
    - 用户断开连接后 15 分钟

## <a name="cost"></a>成本

我们来介绍此课程可能的估算成本。 此估算中不包括运行许可证服务器的成本。 课程将提供给 25 名学生。 课程时间计划为 20 小时。 此外，在计划的课程时间之外还为每名学生提供 10 小时配额，用于完成家庭作业或课外作业。 我们所选的虚拟机大小为“中等”，包含“42 个实验室单位”。

25 名学生 \*（计划的 20 小时 + 配额的 10 小时）\* 42 个实验室单位 * 每小时 0.01 美元 = 315.00 美元

>[!IMPORTANT]
> 成本估算仅用于示例目的。  有关定价的最新详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。  

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室时通用的。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)