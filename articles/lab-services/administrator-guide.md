---
title: Azure 实验室服务 - 管理员指南 | Microsoft Docs
description: 本指南可帮助管理员使用 Azure 实验室服务创建和管理实验室帐户。
ms.topic: article
ms.date: 10/20/2020
ms.openlocfilehash: 08d2fea719ad67f666ea9da09721dc3f7ab54768
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024630"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure 实验室服务 - 管理员指南
信息技术 (IT) 管理大学云资源的管理员通常负责为学校设置实验室帐户。 设置实验室帐户后，管理员或教师将创建包含在该帐户内的实验室。 本文详细介绍了所涉及的 Azure 资源以及创建这些资源的指南。

![实验室帐户中 Azure 资源的概要视图示意图。](./media/administrator-guide/high-level-view.png)

- 实验室托管在 azure 实验室服务所拥有的 Azure 订阅中。
- 实验室帐户、共享映像库和映像版本都托管在你的订阅中。
- 你可以使实验室帐户和共享映像库处于相同的资源组中。 在此图中，它们处于不同的资源组中。

有关体系结构的详细信息，请参阅 [实验室体系结构基础知识](./classroom-labs-fundamentals.md)。

## <a name="subscription"></a>订阅
你的大学可能有一个或多个 Azure 订阅。 使用订阅来管理在其中使用的所有 Azure 资源和服务（包括实验室帐户）的计费和安全性。

实验室帐户与其订阅之间的关系非常重要，因为：

- 计费通过包含实验室帐户的订阅进行报告。
- 你可以向订阅的 Azure Active Directory 中的用户授予 (Azure AD) 租户访问 Azure 实验室服务。 你可以添加一个用户作为实验室帐户所有者或参与者，或者作为实验室创建者或实验室所有者添加。

实验室及其虚拟机 (Vm) 在 Azure 实验室服务拥有的订阅中进行管理和托管。

## <a name="resource-group"></a>资源组
订阅包含一个或多个资源组。 资源组用于创建在同一解决方案中一起使用的 Azure 资源的逻辑分组。  

创建实验室帐户时，必须配置包含实验室帐户的资源组。 

创建 [共享映像库](#shared-image-gallery)时，还需要资源组。 你可以将实验室帐户和共享映像库放置在同一资源组中，也可以放在两个不同的资源组中。 如果计划跨各种解决方案共享映像库，则可能需要采用这种第二种方法。

创建实验室帐户时，可以同时自动创建并附加共享映像库。  此选项会导致在单独的资源组中创建实验室帐户和共享映像库。 按照 [实验室帐户创建时配置共享映像库](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation) 教程中所述的步骤，你将看到此行为。 本文开头的图像使用此配置。 

建议你提前投入时间来规划资源组的结构，因为创建实验室帐户或共享映像库资源组后， *不* 能对其进行更改。 如果需要更改这些资源的资源组，则需要删除并重新创建实验室帐户或共享映像库。

## <a name="lab-account"></a>实验室帐户

实验室帐户充当一个或多个实验室的容器。 当你开始使用 Azure 实验室服务时，最常见的方法是使用一个实验室帐户。 随着实验室使用量的增加，可以选择稍后创建更多实验室帐户。

以下列表突出显示了多个实验室帐户可能有用的方案：

- **跨实验室管理不同的策略要求**

    设置实验室帐户时，将设置适用于实验室帐户下 *所有* 实验室的策略，例如：
    - 包含实验室可以访问的共享资源的 Azure 虚拟网络。 例如，你可能有一组实验室需要访问虚拟网络中的共享数据集。
    - 实验室可用于创建 Vm 的虚拟机映像。 例如，你可能有一组需要访问 Linux Azure Marketplace 映像的 [Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 实验室。

    如果每个实验室都有独特的策略要求，则为单独管理每个实验室创建单独的实验室帐户可能会有好处。

- **为每个实验室帐户分配单独的预算**
  
    你可能需要更清晰的分配预算，而不是通过单个实验室帐户报告所有实验室成本。 例如，可以为大学的数学系、计算机科学部门等创建单独的实验室帐户，以便在各部门之间分配预算。  然后，你可以使用 [Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md)查看每个实验室帐户的成本。

- **将试验实验室与活动或生产实验室隔离**
  
    在某些情况下，你可能希望对实验室帐户执行策略更改，而不会影响活动或生产实验室。 在这种类型的情况下，为进行试点而创建单独实验室帐户允许隔离更改。 

## <a name="lab"></a>实验室

实验室包含每个虚拟机分配给一名学生。  一般而言，可以：

- 每个类都有一个实验室。
- 为每个要使用的学期、季度或其他学术系统创建一组新的实验室。 对于需要使用同一映像的类，应使用 [共享映像库](#shared-image-gallery)。 这样，你就可以跨实验室和学术周期重复使用映像。

确定如何构建实验室的结构时，请注意以下几点：

- **实验室中的所有 Vm 都部署了已发布的相同映像**

    因此，如果你有一个需要同时发布不同实验室映像的类，则必须为每个映像创建一个单独的实验室。
  
- **使用配额在实验室级别设置，适用于实验室中的所有用户**

    若要为用户设置不同的配额，必须创建单独的实验室。 但是，在设置配额后，可以为特定用户增加更多的时间。
  
- 启动或关闭计划在实验室级别进行设置，应用于实验室中的所有 VM

    类似于配额设置，如果需要为用户设置不同的计划，则需要为每个计划创建一个单独的实验室。

默认情况下，每个实验室都有其自己的虚拟网络。  如果已启用虚拟网络对等互连，则每个实验室都将具有其自己的子网对等互连与指定的虚拟网络。

## <a name="shared-image-gallery"></a>共享映像库

共享映像库附加到实验室帐户，用作存储映像的中央存储库。 当教师从实验室模板 VM 选择导出时，该图像将保存在库中。 每次教师对模板 VM 进行更改并将其导出时，会保存新版本的映像，并保留以前的版本。

当教师创建新的实验室时，可以从共享映像库中发布映像版本。 尽管库存储了多个版本的映像，但教师只能在创建实验室的过程中选择最新版本。

共享映像库服务是一种可选资源，如果仅从几个实验室开始，你可能不需要此资源。 但是，在扩展到其他实验室时，共享映像库提供了许多有用的好处：

- **你可以保存和管理模板 VM 映像的版本**

    对于从公共 Azure Marketplace 库中的映像创建自定义映像或更改 (软件、配置等) ，这很有用。  例如，教师通常需要安装不同的软件或工具。 可以将不同版本的模板 VM 映像导出到共享映像库，而不是要求学生自行手动安装这些必备组件。 然后，你可以在创建新实验室时使用这些映像版本。

- **可以跨实验室共享和重复使用模板 VM 映像**

    你可以保存和重复使用映像，这样就不必在每次创建新实验室时从头开始进行配置。 例如，如果多个类需要使用相同的映像，则可以创建一次，然后将其导出到共享的映像库，以便可以跨实验室共享。

- **通过自动复制确保映像可用性**

    将图像从实验室保存到共享映像库时，会自动将其复制到 [同一地域内](https://azure.microsoft.com/global-infrastructure/regions/)的其他区域。 如果某个区域发生服务中断，则将该映像发布到实验室不受影响，因为可以使用另一个区域中的映像副本。  从多个副本发布 VM 还可以帮助提高性能。

若要对共享映像进行逻辑分组，可以执行以下任一操作：

- 创建多个共享映像库。 每个实验室帐户只能连接到一个共享映像库，因此此选项还要求你创建多个实验室帐户。
- 使用由多个实验室帐户共享的单个共享映像库。 在这种情况下，每个实验室帐户只能启用适用于该帐户中的实验室的映像。

## <a name="naming"></a>命名

在 Azure 实验室服务入门中，我们建议你为资源组、实验室帐户、实验室和共享映像库建立命名约定。 尽管你建立的命名约定对于组织的需求是唯一的，但下表提供了一般准则：

| 资源类型 | 角色 | 建议的模式 | 示例 |
| ------------- | ---- | ----------------- | -------- | 
| 资源组 | 包含一个或多个实验室帐户以及一个或多个共享映像库 | \<organization short name\>-\<environment\>-rg<ul><li>**组织短名称** 标识资源组支持的组织的名称。</li><li>**环境** 标识资源的环境，如 *试点* 或 *生产* 环境。</li><li>**Rg** 代表资源类型 *资源组*。</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| 实验室帐户 | 包含一个或多个实验室 | \<organization short name\>-\<environment\>-la<ul><li>**组织短名称** 标识资源组支持的组织的名称。</li><li>**环境** 标识资源的环境，如 *试点* 或 *生产* 环境。</li><li>**La** 代表资源类型 *实验室帐户*。</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| 实验室 | 包含一个或多个 VM |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>**类名称** 标识实验室支持的类的名称。</li><li>“时间范围”标识在其中提供课程的时间范围。</li>**教师标识符** 标识拥有实验室的教师。</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| 共享映像库 | 包含一个或多个 VM 映像版本 | \<organization short name\>图库 | contosouniversitylabsgallery |

有关命名其他 Azure 资源的详细信息，请参阅 [Azure 资源的命名约定](/azure/architecture/best-practices/naming-conventions)。

## <a name="regionslocations"></a>区域\位置

设置 Azure 实验室服务资源时，需要提供将托管资源的数据中心的区域或位置。 以下各节介绍了区域或位置可能如何影响与设置实验室相关的每个资源。

### <a name="resource-group"></a>资源组

区域指定有关资源组信息的存储数据中心。 资源组中包含的 Azure 资源可以与其父组中的不同区域。

### <a name="lab-account"></a>实验室帐户

实验室帐户的位置指示资源所在的区域。  

### <a name="lab"></a>实验室

实验室存在的位置因以下因素而异：

  - **实验室帐户与虚拟网络对等互连**
  
    当某个实验室帐户位于同一区域中时，可以将该 [帐户与虚拟网络对等](./how-to-connect-peer-virtual-network.md) 。  当实验室帐户与虚拟网络对等互连时，会在实验室帐户和虚拟网络所在的同一区域中自动创建实验室。

    > [!NOTE]
    > 当实验室帐户与虚拟网络对等互连时，将禁用 " **允许实验室创建者选取实验室位置** " 设置。 有关详细信息，请参阅[允许实验室创建者选取实验室的位置](./allow-lab-creator-pick-lab-location.md)。
    
  - **不对等互连虚拟网络 *，并且* 不允许实验室创建者选取实验室位置**
  
    如果 *没有* 与实验室帐户对等互连的虚拟网络，并且 [*不允许* 实验室创建者选取实验室位置](./allow-lab-creator-pick-lab-location.md)，则会在具有可用 VM 容量的区域中自动创建实验室。  具体而言，Azure 实验室服务会在[与实验室帐户处于同一地域的区域](https://azure.microsoft.com/global-infrastructure/regions)中查找可用性。

  - **没有对等互连虚拟网络 *，并且* 实验室创建者不允许选取实验室位置**
       
    如果 *没有* 对等互连虚拟网络，并且 [实验室创建者 *允许* 选取实验室位置](./allow-lab-creator-pick-lab-location.md)，则实验室创建者可以选择的位置取决于可用容量。

> [!NOTE]
> 若要帮助确保区域具有足够的 VM 容量，请在创建实验室时首先通过实验室帐户请求容量，这一点很重要。

一般规则是将资源的区域设置为离其用户最近的区域。 对于实验室，这意味着创建与学生最接近的实验室。 对于学生遍布世界各地的在线课程，请使用最佳判断来创建一个集中定位的实验室。 也可以根据学生的区域将一个类拆分为多个实验室。

## <a name="vm-sizing"></a>VM 大小调整

当管理员或实验室创建者创建实验室时，他们可以选择各种 VM 大小，具体取决于其教室的需求。 请记住，计算大小的可用性取决于实验室帐户所在的区域。

| 大小 | 规格 | 系列 | 建议用途 |
| ---- | ----- | ------ | ------------- |
| 小型| <ul><li>2 &nbsp; 核心</li><li>3.5 gb (GB) RAM</li> | [Standard_A2_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 最适用于命令行，打开 web 浏览器，低流量 web 服务器，小型到中型数据库。 |
| 中型 | <ul><li>4 &nbsp; 核</li><li>7 &nbsp; GB &nbsp; RAM</li> | [Standard_A4_v2](../virtual-machines/av2-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 最适合用于关系数据库、内存中缓存和分析。 |
| 中型 (嵌套虚拟化)  | <ul><li>4 &nbsp; 核</li><li>16 &nbsp; GB &nbsp; RAM</li></ul> | [Standard_D4s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | 最适合用于关系数据库、内存中缓存和分析。
| 大型 | <ul><li>8 &nbsp; 核</li><li>16 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_A8_v2](../virtual-machines/av2-series.md) | 最适合于需要更快的 Cpu、更好的本地磁盘性能、大型数据库、大型内存缓存的应用程序。  此大小还支持嵌套虚拟化。 |
| 大型 (嵌套虚拟化)  | <ul><li>8 &nbsp; 核</li><li>32 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json#dsv3-series) | 最适合于需要更快的 Cpu、更好的本地磁盘性能、大型数据库、大型内存缓存的应用程序。 |
| 小型 GPU (可视化)  | <ul><li>6 &nbsp; 核</li><li>56 &nbsp; GB &nbsp; RAM</li>  | [Standard_NV6](../virtual-machines/nv-series.md) | 最适合使用 OpenGL 和 DirectX 之类的框架进行远程可视化、流式处理、游戏和编码。 |
| 小型 GPU (计算) | <ul><li>6 &nbsp; 核</li><li>56 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NC6](../virtual-machines/nc-series.md) |最适用于计算机密集型应用程序，如 AI 和深度学习。 |
| 中等 GPU (可视化)  | <ul><li>12 &nbsp; 核</li><li>112 &nbsp; GB &nbsp; RAM</li></ul>  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) | 最适合使用 OpenGL 和 DirectX 之类的框架进行远程可视化、流式处理、游戏和编码。 |

## <a name="manage-identity"></a>管理标识

通过使用 [Azure 基于角色的访问控制 (RBAC) ](../role-based-access-control/overview.md) 来访问实验室帐户和实验室，你可以分配以下角色：

- 实验室帐户 **所有者**

    系统会自动为创建实验室帐户的管理员分配实验室帐户所有者角色。 所有者角色可以：
     - 更改实验室帐户设置。
     - 向其他管理员授予作为所有者或参与者的实验室帐户的访问权限。
     - 为创建者、所有者或参与者授予教师对实验室的访问权限。
     - 在实验室帐户中创建和管理所有实验室。

- 实验室帐户 **参与者**

    分配有 "参与者" 角色的管理员可以：
    - 更改实验室帐户设置。
    - 在实验室帐户中创建和管理所有实验室。

    但是，参与者 *无法* 向其他用户授予对实验室帐户或实验室的访问权限。

- **实验室创建者**

    若要在实验室帐户中创建实验室，教师必须是实验室创建者角色的成员。  创建实验室的教师会自动作为实验室所有者添加。 有关详细信息，请参阅 [将用户添加到 Lab Creator 角色](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。 

- 实验室 **所有者** 或 **参与者**
  
    "实验室所有者" 或 "参与者" 角色中的教师可以查看和更改实验室的设置。 此人员还必须是实验室帐户读者角色的成员。

    实验室所有者和参与者角色之间的主要区别在于，只有所有者才能向其他用户授予管理实验室的权限。 参与者 *无法* 向其他用户授予管理实验室的权限。

- 共享映像库

    将共享映像库附加到实验室帐户时，会自动向实验室帐户所有者和参与者和实验室创建者、实验室所有者和实验室参与者授予访问权限，以便在库中查看和保存图像。

分配角色时，可以遵循以下提示：

   - 通常，只有管理员才应该是实验室帐户所有者或参与者角色的成员。 实验室帐户可能有多个所有者或参与者。
   - 若要使教师能够创建新的实验室并管理他们创建的实验室，只需向其分配 "实验室创建者" 角色。
   - 若要为教师提供管理特定实验室的功能，但 *不* 允许创建新的实验室，请为他们要管理的每个实验室分配所有者或参与者角色。 例如，你可能想要允许教授和教授助手共同拥有实验室。 有关详细信息，请参阅 [将所有者添加到实验室](./how-to-add-user-lab-owner.md)。

## <a name="pricing"></a>定价

### <a name="azure-lab-services"></a>Azure 实验室服务

若要了解定价，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。


### <a name="shared-image-gallery"></a>共享的映像库

如果打算使用共享映像库来存储和管理映像版本，还需要考虑共享映像库服务的定价。 

创建共享映像库并将它附加到实验室帐户是免费的。 在将图像版本保存到库中之前，不会产生任何费用。 使用共享映像库的定价通常相当简单，但请务必了解它是如何计算的，因为它不包括在 Azure 实验室服务的定价中。  

#### <a name="storage-charges"></a>存储费用

为了存储映像版本，共享映像库使用标准硬盘驱动器 (HDD) 托管磁盘。 所使用的 HDD 托管磁盘的大小取决于所存储的映像版本大小。 若要了解定价，请参阅 [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。

#### <a name="replication-and-network-egress-charges"></a>复制和网络流出量费用

使用实验室模板 VM 保存映像版本时，Azure 实验室服务首先将其存储在源区域中，然后自动将源映像版本复制到一个或多个目标区域。 

请务必注意，Azure 实验室服务会自动将源映像版本复制到实验室所在 [地域内的所有目标区域](https://azure.microsoft.com/global-infrastructure/regions/) 。 例如，如果你的实验室在美国地理位置，则会将映像版本复制到美国每个区域中的八个区域。

将映像版本从源区域复制到其他目标区域时，会产生网络流出量费用。 计费量基于映像数据最初从源区域进行出站传输时的映像版本大小。  有关定价详细信息，请参阅 [带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。

对于 [教育解决方案](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) 客户，可能会停征出口费用。 若要了解详细信息，请与帐户管理员联系。 

有关详细信息，请参阅 "学术客户存在哪些数据传输计划" 和 "我如何资格"。 " [教育机构计划](https://azure.microsoft.com/pricing/details/bandwidth/) " 页的 "常见问题解答" 部分。

#### <a name="pricing-example"></a>定价示例

让我们看一下将模板 VM 映像保存到共享映像库的成本的示例。 假设以下情形：

- 你有一个自定义 VM 映像。
- 你要保存该映像的两个版本。
- 你的实验室在美国，共8个区域。
- 每个映像版本的大小为 32 GB；因此，HDD 托管磁盘价格是每月 1.54 美元。

每月总成本估算为：

* *映像数 &times; &times; 副本托管磁盘价格的版本数 &times; = 每月总成本*

在此示例中，成本为：

* 1个自定义映像 (32 GB) &times; 2 个版本 &times; 8 个美国区域 &times; $1.54 = $24.64/月

> [!NOTE]
> 上述计算仅用于示例目的。 它包含与使用共享映像库关联的存储成本， *不包括出口* 费用。 有关存储的实际定价，请参阅 [托管磁盘定价](https://azure.microsoft.com/en-us/pricing/details/managed-disks/)。

#### <a name="cost-management"></a>成本管理

通常，实验室帐户管理员可以通过从库中定期删除不需要的映像版本来管理成本。 

如果共享映像库中存在此选项，请不要删除到特定区域的复制，从而降低成本。 复制更改可能会对 Azure 实验室服务从共享映像库中保存的映像发布 Vm 的能力产生负面影响。

## <a name="next-steps"></a>后续步骤

有关设置和管理实验室的详细信息，请参阅：

- [实验室帐户设置指南](account-setup-guide.md)  
- [实验室设置指南](setup-guide.md)  
- [实验室成本管理](cost-management-guide.md)  
- [在团队中使用 Azure 实验室服务](lab-services-within-teams-overview.md)
