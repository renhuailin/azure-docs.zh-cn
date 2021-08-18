---
title: Azure 实验室服务 - 管理员指南 | Microsoft Docs
description: 本指南可帮助使用 Azure 实验室服务创建和管理实验室帐户的管理员。
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 567a6ad5cd7eae6fb6963dbcdb781fc2d7667f79
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737697"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure 实验室服务 - 管理员指南
管理大学云资源的信息技术 (IT) 管理员通常负责为学校设置实验室帐户。 设置实验室帐户后，管理员或教师会创建帐户中包含的实验室。 本文简要概述了所涉及的 Azure 资源以及创建这些资源的指导。

![实验室帐户中的 Azure 资源的高级视图关系图。](./media/administrator-guide/high-level-view.png)

- 实验室在 Azure 实验室服务拥有的 Azure 订阅中进行托管。
- 实验室帐户、共享映像库和映像版本在订阅中进行托管。
- 你可以使实验室帐户和共享映像库处于相同的资源组中。 在此图中，它们处于不同的资源组中。

有关体系结构的详细信息，请参阅[实验室体系结构基础知识](./classroom-labs-fundamentals.md)。

## <a name="subscription"></a>订阅
你的大学可能具有一个或多个 Azure 订阅。 你可使用订阅为其中使用的所有 Azure 资源和服务（包括实验室帐户）管理计费和安全性。

实验室帐户与其订阅之间的关系非常重要，因为：

- 计费通过包含实验室帐户的订阅进行报告。
- 可以向订阅的 Azure Active Directory (Azure AD) 租户中的用户授予对 Azure 实验室服务的访问权限。 可以将用户添加为实验室帐户“所有者”或“参与者”、“实验室创建者”或实验室“所有者”。

实验室及其虚拟机 (VM) 在 Azure 实验室服务拥有的订阅中进行管理和托管。

## <a name="resource-group"></a>资源组
订阅包含一个或多个资源组。 资源组用于创建在同一解决方案中一起使用的 Azure 资源的逻辑分组。  

创建实验室帐户时，必须配置包含实验室帐户的资源组。 

创建[共享映像库](#shared-image-gallery)时，也需要资源组。 你可以将实验室帐户和共享映像库放在同一个资源组中，也可以放在两个不同的资源组中。 如果计划跨各种解决方案共享映像库，则可能需要采用第二种方法。

创建实验室帐户时，可以同时自动创建并附加共享映像库。  此选项会导致在单独的资源组中创建实验室帐户和共享映像库。 遵照[在创建实验室帐户时配置共享映像库](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation)教程中所述的步骤执行操作时，你将看到此行为。 本文开头的图像使用此配置。 

建议提前投入时间来规划资源组的结构，因为实验室帐户或共享映像库的资源组在创建后无法更改。 如果需要更改这些资源的资源组，则需要删除并重新创建实验室帐户或共享映像库。

## <a name="lab-account"></a>实验室帐户

实验室帐户充当一个或多个实验室的容器。 开始使用 Azure 实验室服务时，最常见的情况是只有一个实验室帐户。 随着实验室使用的扩展，以后可以选择创建更多实验室帐户。

以下列表突出显示了多个实验室帐户可能十分有益的方案：

- **跨实验室管理不同策略要求**

    设置实验室帐户时，会设置应用于实验室帐户下所有实验室的策略，例如：
    - 具有实验室可以访问的共享资源的 Azure 虚拟网络。 例如，你可能有一组实验室需要访问虚拟网络中的共享数据集。
    - 实验室可用于创建 VM 的虚拟机映像。 例如，你可能有一组实验室需要访问 [Data Science VM for Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Azure 市场映像。

    如果每个实验室都有独特的策略要求，则创建单独的实验室帐户来单独管理每个实验室可能会有好处。

- **为每个实验室帐户分配单独的预算**
  
    你可能需要更清晰的分配预算，而不是通过单个实验室帐户报告所有实验室成本。 例如，可以为大学的数学系、计算机科学系等创建单独的实验室帐户，以便在各院系之间分配预算。  随后可以使用 [Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)查看每个单独实验室帐户的成本。

- **将试点实验室与活动或生产实验室分隔**
  
    你可能会遇到这样的情况：你想要在不影响活动或生产实验室的情况下，针对某个实验室帐户试点策略更改。 在这种类型的情况下，为进行试点而创建单独实验室帐户允许隔离更改。 

## <a name="lab"></a>实验室

实验室包含各自分配给单个学生的 VM。  一般而言，可以：

- 每个课程有一个实验室。
- 为要使用的每学期、每季度或其他学年制创建一组新的实验室。 对于需要使用同一映像的课程，应使用[共享映像库](#shared-image-gallery)。 这样，你就可以跨实验室和学期重用映像。

确定如何构造实验室时，请注意以下几点：

- **使用已发布的相同映像部署实验室中的所有 VM**

    因此，如果你有一个需要同时发布不同实验室映像的课程，则必须为每个映像创建单独的实验室。
  
- **使用配额在实验室级别进行设置，应用于实验室中的所有用户**

    如果要为用户设置不同的配额，必须创建单独的实验室。 但是，设置配额之后，可以将更多时数添加到特定用户。
  
- 启动或关闭计划在实验室级别进行设置，应用于实验室中的所有 VM

    类似于配额设置，如果需要为用户设置不同的计划，则需要为每个计划创建单独的实验室。

默认情况下，每个实验室都具有自己的虚拟网络。  如果启用了虚拟网络对等互连，则每个实验室的子网都将与指定虚拟网络对等互连。

## <a name="shared-image-gallery"></a>共享映像库

共享映像库附加到实验室帐户，用作存储映像的中央存储库。 当教师选择从实验室模板 VM 中导出时，映像会保存在该库中。 教师每次更改模板 VM 和导出时，就会在库中创建新的映像定义和/或版本。  

当教师创建新的实验室时，他们可以从共享映像库发布映像版本。 尽管该库存储了多个版本的映像，但教师只能在实验室创建过程中选择最新版本。  依次根据版本最高的 MajorVersion、MinorVersion 和 Patch 选择最新版本。  有关版本控制的更多信息，请参阅 [映像版本](../virtual-machines/shared-image-galleries.md#image-versions)。

共享映像库服务是一种可选资源，仅从少量实验室开始时，可能不会立即需要该资源。 但是，在扩展到其他实验室时，共享映像库提供了诸多好处：

- **可以保存和管理模板 VM 映像的各个版本**

    创建自定义映像，或是对来自 Azure 市场库的映像进行更改（软件、配置等）时很有用。  例如，教师常常需要安装不同的软件或工具。 可以将不同版本的模板 VM 映像导出到共享映像库，而不是要求学生手动安装这些必备组件。 然后，你可以在创建新实验室时使用这些映像版本。

- **可以跨实验室共享和重用模板 VM 映像**

    可以保存并重用映像，这样便不必在每次创建新的实验室时都从头开始配置映像。 例如，如果多个课程需要使用相同的映像，则可以创建一次并将其导出到共享映像库，这样便可以跨实验室共享映像。

- 可以从实验室外的其他环境上传自己的自定义映像

    可以 [从实验室的上下文之外的其他环境上传自定义映像](how-to-attach-detach-shared-image-gallery.md)。  例如，可以从自己的物理实验室环境或从 Azure VM 将映像上传到共享映像库。  将映像导入库后，可以使用映像创建实验室。

如果要对共享映像进行逻辑分组，可以执行以下任一操作：

- 创建多个共享映像库。 每个实验室帐户只能连接到一个共享映像库，因此此选项还要求创建多个实验室帐户。
- 使用由多个实验室帐户共享的单个共享映像库。 在这种情况下，每个实验室帐户只能启用适用于该帐户实验室的映像。

## <a name="naming"></a>命名

在刚开始使用 Azure 实验室服务时，建议为资源组、实验室帐户、实验室和共享映像库建立命名约定。 尽管建立的命名约定对于组织的需求是独有的，不过下表概述了一般准则：

| 资源类型 | 角色 | 建议的模式 | 示例 |
| ------------- | ---- | ----------------- | -------- | 
| 资源组 | 包含一个或多个实验室帐户以及一个或多个共享映像库 | \<organization short name\>-\<environment\>-rg<ul><li>“组织短名称”标识资源组支持的组织的名称。</li><li>“环境”标识资源的环境，如“试点”或“生产” 。</li><li>“Rg”表示“资源组”资源类型。</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| 实验室帐户 | 包含一个或多个实验室 | \<organization short name\>-\<environment\>-la<ul><li>“组织短名称”标识资源组支持的组织的名称。</li><li>“环境”标识资源的环境，如“试点”或“生产” 。</li><li>“La”表示“实验室帐户”资源类型。</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| 实验室 | 包含一个或多个 VM |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>“课程名称”标识实验室支持的课程的名称。</li><li>“时间范围”标识在其中提供课程的时间范围。</li>“教师标识符”标识拥有实验室的教师。</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| 共享映像库 | 包含一个或多个 VM 映像版本 | \<organization short name\>库 | contosouniversitylabsgallery |

有关命名其他 Azure 资源的详细信息，请参阅“[Azure 资源的命名约定](/azure/architecture/best-practices/naming-conventions)”。

## <a name="regionslocations"></a>区域\位置

设置 Azure 实验室服务的资源时，需要提供将托管资源的数据中心的区域或位置。 以下各部分介绍了区域或位置可能如何影响与设置实验室相关的每个资源。

### <a name="resource-group"></a>资源组

区域指定存储有关资源组的信息的数据中心。 资源组中包含的 Azure 资源可以处于与其父组不同的区域中。

### <a name="lab-account"></a>实验室帐户

实验室帐户的位置指示资源所存在的区域。  

### <a name="lab"></a>实验室    

实验室所存在的位置因以下因素而异：

  - 实验室帐户与虚拟网络对等互连
  
    当实验室帐户与虚拟网络位于同一区域时，可以[将实验室帐户与虚拟网络对等互连](./how-to-connect-peer-virtual-network.md)。  实验室帐户与虚拟网络对等互连时，会在实验室帐户和虚拟网络所在的同一个区域中自动创建实验室。

    > [!NOTE]
    > 实验室帐户与虚拟网络对等互连时，会禁用“允许实验室创建者选取实验室位置”设置。 有关详细信息，请参阅[允许实验室创建者选取实验室的位置](./allow-lab-creator-pick-lab-location.md)。
    
  - 未与虚拟网络对等互连 ***并且不允许实验室创建者选取实验室位置***
  
    如果没有虚拟网络与实验室帐户对等互连并且[不允许实验室创建者选取实验室位置](./allow-lab-creator-pick-lab-location.md)，则会在具有可用 VM 容量的区域中自动创建实验室。  具体而言，Azure 实验室服务会在[与实验室帐户处于同一地域的区域](https://azure.microsoft.com/global-infrastructure/regions)中查找可用性。

  - 未与虚拟网络对等互连 ***并且允许实验室创建者选取实验室位置***
       
    如果没有与虚拟网络对等互连，并且[允许实验室创建者选取实验室位置](./allow-lab-creator-pick-lab-location.md)，则实验室创建者可以选择的位置取决于可用容量。

> [!NOTE]
> 如果要帮助确保区域有足够的 VM 容量，请务必在创建实验室时先通过实验室帐户请求容量。

一般规则是将资源的区域设置为最靠近其用户的区域。 对于实验室，这意味着创建最靠近学生的实验室。 对于学生遍布世界各地的在线课程，请根据自己的最佳判断来创建位于中心位置的实验室。 也可以根据学生的区域将一个课程拆分到多个实验室。

## <a name="vm-sizing"></a>VM 大小调整

当管理员或实验室创建者创建实验室时，他们可以根据课堂的需求从各种 VM 大小中进行选择。 请记住，大小可用性取决于实验室帐户所在的区域。

在下表中，请注意，多个 VM 大小映射到多个 VM 系列。  根据容量可用性，实验室服务可能会使用针对 VM 大小列出的任何 VM 系列。  例如，“小型”VM 大小映射到使用 [Standard_A2_v2](../virtual-machines/av2-series.md) 或 [Standard_A2](../virtual-machines/sizes-previous-gen.md#a-series) VM 系列。  选择“小型”作为实验室的 VM 大小时，实验室服务将首先尝试使用“Standard_A2_v2”系列。   但是，当没有足够的可用容量时，实验室服务将改为使用“Standard_A2”系列。  定价由 VM 大小确定，无论实验室服务对该特定大小使用哪个 VM 系列，定价都是相同的。 有关每个 VM 大小的定价的详细信息，请参阅 [实验室服务定价指南](https://azure.microsoft.com/pricing/details/lab-services/)。


| 大小 | 最小规格 | 系列 | 建议用途 |
| ---- | ----- | ------ | ------------- |
| 小型| <ul><li>双核&nbsp;</li><li>3.5 千兆字节 (GB) RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md)、[Standard_A2](../virtual-machines/sizes-previous-gen.md#a-series) | 最适用于命令行、打开 Web 浏览器、低流量 Web 服务器、中小型数据库。 |
| 中型 | <ul><li>4&nbsp;核</li><li>7&nbsp;GB&nbsp;RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md)、[Standard_A3](../virtual-machines/sizes-previous-gen.md#a-series) | 最适用于关系数据库、内存中缓存和分析。 |
| 中等(嵌套虚拟化) | <ul><li>4&nbsp;核</li><li>16&nbsp;GB&nbsp;RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md#dsv3-series) | 最适用于关系数据库、内存中缓存和分析。  此大小还支持嵌套虚拟化。
| 大 | <ul><li>8&nbsp;核</li><li>16&nbsp;GB&nbsp;RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md)、[Standard_A7](../virtual-machines/sizes-previous-gen.md#a-series) | 最适用于需要 CPU 更快、本地磁盘性能更佳、大型数据库、大型内存缓存的应用程序。 |
| 大型（嵌套虚拟化） | <ul><li>8&nbsp;核</li><li>32&nbsp;GB&nbsp;RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md#dsv3-series) | 最适用于需要 CPU 更快、本地磁盘性能更佳、大型数据库、大型内存缓存的应用程序。  此大小还支持嵌套虚拟化。 |
| 小型 GPU（可视化） | <ul><li>6&nbsp;核</li><li>56&nbsp;GB&nbsp;RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | 最适用于使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏和编码。 |
| 小型 GPU (计算) | <ul><li>6&nbsp;核</li><li>56&nbsp;GB&nbsp;RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md)、[Standard_NC6s_v3](../virtual-machines/ncv3-series.md) |最适用于计算机密集型应用程序，如 AI 和深度学习。 |
| 中等 GPU（可视化） | <ul><li>12&nbsp;核</li><li>112&nbsp;GB&nbsp;RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md)、[Standard_NV12s_v3](../virtual-machines/nvv3-series.md)、[Standard_NV12s_v2](../virtual-machines/sizes-previous-gen.md#nvv2-series)  | 最适用于使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏和编码。 |

## <a name="manage-identity"></a>管理标识

通过使用 [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 来访问实验室帐户和实验室，你可以分配以下角色：

- 实验室帐户“所有者”

    系统会为创建实验室帐户的管理员自动分配实验室帐户的“所有者”角色。 “所有者”角色可以：
     - 更改实验室帐户设置。
     - 向其他管理员授予作为“所有者”或“参与者”访问实验室帐户的权限。
     - 向教师授予作为“创建者”、“所有者”或“参与者”访问实验室的权限。
     - 在实验室帐户中创建和管理所有实验室。

- 实验室帐户“参与者”

    分配有“参与者”角色的管理员可以：
    - 更改实验室帐户设置。
    - 在实验室帐户中创建和管理所有实验室。

    但是，“参与者”无法向其他用户授予访问实验室帐户或实验室的权限。

- **实验室创建者**

    如果要在实验室帐户中创建实验室，教师必须是“实验室创建者”角色的成员。  系统会将创建实验室的教师自动添加为实验室“所有者”。 有关详细信息，请参阅“[将用户添加到实验室创建者角色](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)”。 

- 实验室“所有者”或“参与者” 
  
    拥有实验室“所有者”或“参与者”角色的教师可以查看和更改实验室的设置。 此人员还必须是实验室帐户“读者”角色的成员。

    实验室“所有者”和“参与者”角色的主要区别在于，只有“所有者”才能向其他用户授予管理实验室的访问权限。 “参与者”无法向其他用户授予管理实验室的访问权限。

- 共享映像库

    将共享映像库附加到实验室帐户时，会自动向实验室帐户“所有者”、参与者”、“实验室创建者”、实验室“所有者”和实验室“参与者”授予在库中查看和保存映像的访问权限。

分配角色时，可以遵循以下提示：

   - 通常，只有管理员才应该是实验室帐户“所有者”或“参与者”角色的成员。 实验室帐户可能有多个“所有者”或“参与者”。
   - 如果要让教师能够创建新的实验室并管理他们创建的实验室，只需为其分配“实验室创建者”角色。
   - 如果要让教师能够管理特定实验室，但不能创建新实验室，可以针对其将管理的每个实验室为其分配“所有者”或“参与者”角色。 例如，你可能要允许一个教授和一个助教共同拥有一个实验室。 有关详细信息，请参阅“[将所有者添加到实验室](./how-to-add-user-lab-owner.md)”。

## <a name="content-filtering"></a>内容筛选

您的学校可能需要进行内容筛选，以防止学生访问不当的网站。  例如，为了遵守 [儿童互联网保护法 (CIPA)](https://www.fcc.gov/consumers/guides/childrens-internet-protection-act)。  实验室服务不提供对内容筛选的内置支持。

学校通常考虑两种内容筛选方法：
- 配置防火墙来在网络级别筛选内容。
- 直接在执行内容筛选的每台计算机上安装第三方软件。

实验室服务目前不支持第一种方法。  实验室服务在 Microsoft 托管 Azure 订阅中托管每个实验室的虚拟网络。  因此，无法访问基础虚拟网络来执行网络级别的内容筛选。  有关实验室服务体系结构的详细信息，请参阅文章 [体系结构基础知识](./classroom-labs-fundamentals.md)。

相反，我们建议采用第二种方法，即在每个实验室模板 VM 上安装第三方软件。  作为此解决方案的一部分，要突出几个要点：
- 如果打算使用 [自动关闭设置](./cost-management-guide.md#automatic-shutdown-settings-for-cost-control)，则需要使用第三方软件取消阻止几个 Azure 主机名。  自动关闭设置使用必须能够与实验室服务通信的诊断扩展。  否则，将无法为实验室启用自动关闭设置。
- 可能还想要每个学生都在其 VM 上使用非管理员帐户，以使他们无法卸载内容筛选软件。  默认情况下，实验室服务创建一个管理员帐户，每个学生都使用该帐户登录到其 VM。  可以使用专用映像添加非管理员帐户，但存在一些已知的限制。

如果学校需要进行内容筛选，请通过 [Azure 实验室服务论坛](https://techcommunity.microsoft.com/t5/azure-lab-services/bd-p/AzureLabServices) 与我们联系以获取详细信息。

## <a name="endpoint-management"></a>终结点管理

许多终结点管理工具（如 [Microsoft Endpoint Manager](https://techcommunity.microsoft.com/t5/azure-lab-services/configuration-manager-azure-lab-services/ba-p/1754407)）要求 Windows VM 具有唯一的计算机安全标识符 (SID)。  使用 SysPrep 创建“通用化”映像通常可确保每个 Windows 计算机在 VM 从映像启动时生成新的唯一计算机 SID。

使用实验室服务，即使使用“通用化”映像创建实验室，模板 VM 和学生 VM 也全部将具有相同的计算机 SID。  VM 具有相同的 SID，因为模板 VM 的映像在发布以创建学生 VM 时处于“通用化”状态。

例如，Azure 市场映像已通用化。  如果从 Win 10 市场映像创建实验室并发布模板 VM，实验室内的所有学生 VM 将具有与模板 VM 相同的计算机 SID。  可以使用 [PsGetSid](/sysinternals/downloads/psgetsid) 等工具验证计算机 SID。

如果打算使用终结点管理工具或类似软件，我们建议使用实验室 VM 进行测试，以确保它在计算机 SID 相同时正常工作。  

## <a name="pricing"></a>定价

### <a name="azure-lab-services"></a>Azure 实验室服务

如果要了解定价，请参阅“[Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)”。

### <a name="shared-image-gallery"></a>共享的映像库

如果你计划使用共享映像库存储和管理映像版本，则还需要考虑共享映像库服务的定价。 

创建共享映像库并将它附加到实验室帐户是免费的。 在将映像版本保存到该库之前，不会产生成本。 通常，使用共享映像库的价格几乎可忽略不计，但了解其计算方式非常重要，因为它不包括在 Azure 实验室服务的定价中。  

#### <a name="storage-charges"></a>存储费用

为了存储映像版本，共享映像库默认使用标准硬盘驱动器 (HDD) 托管磁盘。  将共享映像库与实验室服务一同使用时，建议使用 HDD 托管磁盘。  所使用的 HDD 托管磁盘的大小取决于所存储的映像版本大小。  实验室服务最大支持 128 GB 的映像和磁盘大小。  如果要了解定价，请参阅“[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)”。

#### <a name="replication-and-network-egress-charges"></a>复制和网络流出量费用

使用实验室模板 VM 保存映像版本时，Azure 实验室服务首先将它存储在源区域中，然后自动将源映像版本复制到一个或多个目标区域。 

必须注意的一点是，Azure 实验室服务会自动将源映像版本复制到实验室所在的[地域中的所有目标区域](https://azure.microsoft.com/global-infrastructure/regions/)。 例如，如果实验室处于美国地域，则映像版本会复制到存在于美国的所有八个区域。

将映像版本从源区域复制到其他目标区域时，会产生网络流出量费用。 计费量基于映像数据最初从源区域进行出站传输时的映像版本大小。  有关定价详细信息，请参阅“[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)”。

对于[教育解决方案](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3)客户，可能会免于支付流出量费用。 如果要了解详细信息，请联系你的帐户管理员。 

有关详细信息，请参阅 [教育机构计划](https://azure.microsoft.com/pricing/details/bandwidth/)页 FAQ 部分中的“对于学术客户有哪些数据传输计划，我如何获得资格？”。

#### <a name="pricing-example"></a>定价示例

让我们看一个示例，了解将模板 VM 映像保存到共享映像库的成本。 假设以下情形：

- 你有一个自定义 VM 映像。
- 你要保存该映像的两个版本。
- 你的实验室处于美国（共有八个区域）。
- 每个映像版本的大小为 32 GB；因此，HDD 托管磁盘价格是每月 1.54 美元。

每月总成本估算为：

* 映像数 &times; 版本数 &times; 副本数 &times; 托管磁盘价格 = 每月总成本

在此示例中，成本为：

* 1 个自定义映像 (32 GB) &times; 2 个版本 &times; 8 个美国区域 &times; 1.54 美元 = 24.64 美元/月

> [!NOTE]
> 上述计算仅用于示例目的。 它包含与使用共享映像库关联的存储成本，不包括流出量成本。 有关存储的实际定价，请参阅“[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)”。

#### <a name="cost-management"></a>成本管理

实验室帐户管理员可通过定期从库中删除不需要的映像版本来管理成本，这十分重要。 

请勿删除到特定区域的复制来降低成本，虽然此选项存在于共享映像库中。 复制更改可能会对 Azure 实验室服务从共享映像库中保存的映像发布 VM 的能力产生负面影响。

## <a name="next-steps"></a>后续步骤

有关设置和管理实验室的详细信息，请参阅：

- [实验室帐户设置指南](account-setup-guide.md)  
- [实验室设置指南](setup-guide.md)  
- [实验室成本管理](cost-management-guide.md)  
- [使用 Teams 中的 Azure 实验室服务](lab-services-within-teams-overview.md)