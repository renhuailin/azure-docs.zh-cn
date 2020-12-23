---
title: 加速了 Azure 实验室服务的实验室帐户设置指南
description: 本指南可帮助管理员快速设置实验室帐户，以便在学校内使用。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: aa3e7b586546b3d87f5c6029b284eeb1402ed171
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659856"
---
# <a name="lab-account-setup-guide"></a>实验室帐户设置指南
若要设置 Azure 实验室服务环境，管理员需要首先在 Azure 订阅中设置一个 **实验室帐户** 。 实验室帐户是实验室的容器，只需几分钟即可完成设置。

本指南包括三个部分：
-  第一部分重点介绍在设置实验室帐户 *之前* 需要完成的先决条件。
-  第二部分提供有关规划实验室帐户设置的指导。
-  第三部分提供设置实验室帐户的分步说明。

## <a name="prerequisites-for-setting-up-your-lab-account"></a>设置实验室帐户的先决条件
本部分概述了在设置实验室帐户之前需要完成的先决条件。

### <a name="obtain-an-azure-subscription"></a>获取 Azure 订阅
若要创建实验室帐户，你需要访问为你的学校设置的 Azure 订阅。 你的学校可能有一个或多个订阅。 使用订阅来管理所有 Azure 资源和服务（包括实验室帐户）的计费和安全性。  Azure 订阅通常由 IT 部门管理。  有关详细信息，请阅读以下主题：
 - [管理员指南-订阅](./administrator-guide.md#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>估算所需 Vm 和 VM 大小的数量
你需要估算 (Vm) 的虚拟机数，以及你的学校需要的 [vm 大小](./administrator-guide.md#vm-sizing) 。  阅读以下博客文章，了解有关如何构造 labs\images. 的指南  此博客文章还将帮助你确定所需的 Vm 数和 VM 大小：
- [从物理实验室迁移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

另请参阅本文，其中介绍了有关如何构建实验室的其他指导：
- [管理员指南-实验室](./administrator-guide.md)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>了解订阅 VM 限制和区域 VM 容量
估计实验室的 vm 数和 VM 大小后，需要执行以下操作：

- 确保 Azure 订阅的容量限制允许 Vm 数和你计划在实验室中使用的 VM 大小。

- 在具有足够的可用 VM 容量的区域内创建实验室帐户。

阅读以下博客文章，了解详细信息： [VM 订阅限制和区域容量](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)。

### <a name="decide-how-many-lab-accounts-to-create"></a>确定要创建的实验室帐户数量

若要快速开始，请在其自己的资源组中创建一个实验室帐户。  稍后，你可以根据需要创建 (和资源组) 的其他实验室帐户。 例如，最终可能有一个实验室帐户和每个部门的资源组，以便清晰地分隔成本。  阅读以下文章，了解有关实验室帐户、资源组和分隔成本的详细信息：
- [管理员指南-资源组](./administrator-guide.md#resource-group)
- [管理员指南-实验室帐户](./administrator-guide.md#lab-account) 
- [Azure 实验室服务的成本管理](./cost-management-guide.md)

## <a name="planning-your-lab-accounts-settings"></a>规划实验室帐户的设置

若要规划实验室帐户的设置，应考虑以下问题。

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>谁应该是实验室帐户的所有者和参与者？

   学校的 IT 管理员通常是实验室帐户的所有者和参与者。 它们负责管理适用于实验室帐户中包含的所有实验室的策略。 创建实验室帐户的人员将自动成为所有者。 你可以从与你的订阅关联的 Azure Active Directory (AD) 租户中添加其他所有者和参与者。 有关实验室帐户所有者和参与者角色的详细信息，请阅读：
   -  [管理员指南-管理标识](./administrator-guide.md#manage-identity)。

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   实验室用户只会看到他们有权在 Azure 实验室服务中跨租户访问的虚拟机列表。

### <a name="who-will-be-allowed-to-create-labs"></a>允许谁创建实验室？

   你可以选择让 IT 和教职员成员创建实验室。 用户创建实验室时，会自动将其指定为实验室的所有者。  若要创建实验室，通常从与) 订阅相关联的 Azure AD 租户 (的用户必须将其分配到实验室帐户中的实验室创建者角色。  有关 Lab Creator 角色的详细信息，请阅读：
   -  [管理员指南-管理标识](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>谁将有权拥有和管理实验室？

   你还可以选择让 IT 和教职员 own\manage 实验室， *无需* 为他们提供创建实验室的能力。  在这种情况下，将为订阅的 Azure AD 租户中的用户分配现有实验室的所有者或参与者。  有关实验室所有者和参与者角色的详细信息，请阅读：
   - [管理员指南-管理标识](./administrator-guide.md#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>是否要保存可跨实验室共享的映像？
共享映像库是一个存储库，可用于保存和共享图像。 对于需要同一映像的类，实验室创建者可以创建映像，然后将其导出到共享图像库。  将映像导出到共享图像库后，可将其用于创建新的实验室。

此外，你可能需要在物理环境中创建映像，然后将其导入共享映像库。  有关此过程的更多详细信息，请阅读以下博客文章： 
- [将自定义图像导入共享映像库](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

如果你决定需要使用共享映像库，则需要创建一个共享映像库，或将其附加到实验室帐户。 此外，你可以将此决定推迟到以后，因为它可以随时附加到实验室帐户。  有关共享映像库的详细信息，请阅读：
- [管理员指南-共享映像库](./administrator-guide.md#shared-image-gallery)
- [管理员指南-共享映像库定价](./administrator-guide.md#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>你的实验室将使用 Azure Marketplace 中的哪些映像？
Azure Marketplace 提供了数百个可启用的映像，使实验室创建者可以使用该映像创建其实验室。 某些映像可能包含实验室已经需要的所有内容。 在其他情况下，你可以使用映像作为起点，然后，实验室创建者可以通过安装其他应用程序或工具对其进行自定义。

如果您不知道所需的映像，可以稍后再回来启用它们。 此外，查看可用映像的最佳方式是先创建实验室帐户。 这会提供访问权限，以便查看可用映像及其内容的列表。  有关 marketplace 映像的详细信息，请阅读：
- [指定可供实验室创建者使用的市场映像](./specify-marketplace-images.md)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>实验室的 Vm 是否需要有权访问其他 Azure 或本地资源？
设置实验室帐户时，还可以将实验室帐户与 (VNet) 的虚拟网络对等互连。  请记住，VNet 和实验室帐户必须位于同一区域。  若要确定是否需要与 VNet 对等，请考虑以下方案：

- **访问授权服务器**
  
   使用 Azure Marketplace 映像时，操作系统许可证的成本会捆绑到实验室服务的定价中。 但是，不需要为操作系统本身提供许可证。 但是，对于安装的其他软件和应用程序，你需要提供相应的许可证。  访问授权服务器：
   - 你可以选择连接到本地授权服务器。  连接到本地授权服务器需要额外的设置。
   - 另一种更快的设置方法是创建在 Azure VM 上托管的授权服务器。  Azure VM 位于与实验室帐户对等的虚拟网络中。

- **访问其他本地资源，如文件共享或数据库**

   创建 VNet 以提供对本地资源的访问，通常通过使用站点到站点虚拟网络网关。 设置此类环境将需要额外的时间。

- **访问位于 VNet 之外的其他 Azure 资源**

   如果需要访问在 VNet 中 *未* 受保护的 Azure 资源，则可以通过公共 internet 访问这些资源，而无需进行任何对等互连。

有关虚拟网络的详细信息，请阅读：
- [体系结构基础知识-虚拟网络](./classroom-labs-fundamentals.md#virtual-network)
- [如何连接到虚拟网络](./how-to-connect-peer-virtual-network.md)
- [如何在 Azure 实验室服务中使用共享资源创建实验室](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>设置实验室帐户

完成规划后，便可以设置实验室帐户。  这些步骤适用于 [使用团队设置实验室 Azure 实验室服务](./lab-services-within-teams-overview.md)。

1. **创建实验室帐户。** 有关说明，请参阅 [创建实验室帐户](./tutorial-setup-lab-account.md#create-a-lab-account) 教程。
   
    有关命名的更多指导，请参阅以下文章：

   - [资源的命名指南](./administrator-guide.md#naming)

2. **将用户添加到实验室创建者角色。** 有关说明，请参阅 [将用户添加到 lab creator 角色](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。


3. **连接到对等虚拟网络。** 有关说明，请参阅 [将实验室网络与对等虚拟网络连接](./how-to-connect-peer-virtual-network.md)。

   你可能还需要参阅 [配置实验室 vm 地址范围的](./how-to-configure-lab-accounts.md)说明。

4. **启用和查看映像。** 有关说明，请参阅 [启用实验室创建者的 Azure Marketplace 映像](./specify-marketplace-images.md)。

   若要查看每个 Azure Marketplace 映像的内容，请选择映像名称。 例如，以下屏幕截图显示了 Ubuntu Data Science VM 映像的详细信息：

   ![查看 Azure Marketplace 映像的屏幕截图](./media/setup-guide/review-marketplace-images.png)

   如果共享映像库附加到实验室帐户，并且想要启用实验室创建者共享的自定义映像，请完成类似的步骤，如以下屏幕截图所示：

   ![在共享映像库中启用自定义映像的屏幕截图](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>后续步骤

设置实验室环境的常见步骤如下：

- [管理实验室帐户](how-to-manage-lab-accounts.md)
- [实验室设置指南](setup-guide.md)