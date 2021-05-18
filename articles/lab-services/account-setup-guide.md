---
title: 适用于 Azure 实验室服务的实验室帐户加速设置指南
description: 本指南可帮助管理员快速设置实验室帐户以在学校中使用。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e1f36b6d0983c10926a790d42edef3736e848a59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669383"
---
# <a name="lab-account-setup-guide"></a>实验室帐户设置指南
如果你是管理员，在设置 Azure 实验室服务环境之前，首先需要在 Azure 订阅中创建一个实验室帐户。 实验室帐户是一个或多个实验室的容器，只需几分钟即可设置好。

本指南包含三个部分：
-  先决条件
-  规划实验室帐户设置
-  设置实验室帐户

## <a name="prerequisites"></a>先决条件
以下几部分概述在设置实验室帐户之前需要执行的操作。


### <a name="access-your-azure-subscription"></a>访问 Azure 订阅
若要创建实验室帐户，你需要访问已为学校设置的 Azure 订阅。 你的学校可能有一个或多个订阅。 你使用订阅来管理所有 Azure 资源和服务（包括实验室帐户）的计费和安全性。  Azure 订阅通常由 IT 部门管理。  有关详细信息，请参阅 [Azure 实验室服务 - 管理员指南](./administrator-guide.md#subscription)中的“订阅”一节。

### <a name="estimate-how-many-vms-and-vm-sizes-you-need"></a>估计需要的 VM 数量和 VM 大小
务必了解学校实验室需要的[虚拟机 (VM) 的数量和大小](./administrator-guide.md#vm-sizing)。 

如需构造实验室和映像方面的指导，请参阅博客文章[从物理实验室迁移到 Azure 实验室服务](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)。

如需构造实验室方面的其他指导，请参阅 [Azure 实验室服务 - 管理员指南](./administrator-guide.md#lab)的“实验室”一节。

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>了解订阅的 VM 限制和区域 VM 的容量
预估实验室的 VM 数量和 VM 大小后，需要执行以下操作：

- 确保 Azure 订阅的容量限制考虑到计划在实验室中使用的 VM 数量和 VM 大小。
- 在 VM 容量够用的区域内创建实验室帐户。

有关详细信息，请参阅 [VM 订阅限制和区域容量](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553)。

### <a name="decide-how-many-lab-accounts-to-create"></a>确定要创建的实验室帐户数量

为快速开始，请在实验室帐户自己的资源组内创建一个实验室帐户。  稍后，你可以根据需要创建其他实验室帐户和资源组。 例如，最终每个部门可能有一个实验室帐户和一个资源组，以便清晰地分离成本。 

有关实验室帐户、资源组和分离成本的详细信息，请参阅：
- [Azure 实验室服务 - 管理员指南](./administrator-guide.md#resource-group)的“资源组”一节
- [Azure 实验室服务 - 管理员指南](./administrator-guide.md#lab-account)的“实验室帐户”一节 
- [Azure 实验室服务成本管理](./cost-management-guide.md)

## <a name="plan-your-lab-account-settings"></a>规划实验室帐户设置

若要规划实验室帐户设置，请思考以下问题。

### <a name="who-should-be-the-owners-and-contributors-of-the-lab-account"></a>谁应该是实验室帐户的“所有者”和“参与者”？

学校的 IT 管理员通常会担任实验室帐户的“所有者”和“参与者”角色。 这些角色负责管理应用于实验室帐户中所有实验室的策略。 创建实验室帐户的人员会自动成为“所有者”。 你可以在与你的订阅关联的 Azure Active Directory (Azure AD) 租户中添加其他“所有者”和“参与者”。 

有关实验室帐户“所有者”和“参与者”角色的详细信息，请参阅 [Azure 实验室服务 - 管理员指南](./administrator-guide.md#manage-identity)的“管理标识”一节。

[!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

实验室用户只会看到一个包含他们在 Azure 实验室服务中各 Azure AD 租户内有权访问的 VM 的列表。

### <a name="who-will-be-allowed-to-create-labs"></a>允许谁创建实验室？

你可以选择让 IT 团队或教职员工来创建实验室。 要创建实验室，可以向这些人员分配实验室帐户中的“实验室创建者”角色。 通常在与学校订阅关联的 Azure AD 租户中分配此角色。 任何创建实验室的人员都会被自动指定为实验室的“所有者”。  

有关“实验室创建者”角色的详细信息，请参阅 [Azure 实验室服务 - 管理员指南](./administrator-guide.md#manage-identity)的“管理标识”一节。

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>谁将有权拥有和管理实验室？

你还可以选择让 IT 和教职员工拥有或管理实验室，而不向他们授予创建实验室的能力。  在这种情况下，订阅的 Azure AD 租户中的用户将被分配现有实验室的“所有者”或“参与者”角色。  

有关实验室“所有者”和“参与者”角色的详细信息，请参阅 [Azure 实验室服务 - 管理员指南](./administrator-guide.md#manage-identity)的“管理标识”一节。

### <a name="do-you-want-to-save-images-and-share-them-across-labs"></a>是否要保存映像并跨实验室共享？

共享映像库是一项可用于保存和共享映像的服务。 对于需要使用同一映像的类，实验室创建者可以创建该映像，然后将其导出到共享映像库。  将映像导出到共享映像库后，可将其用于创建新的实验室。

你可能想在物理环境中创建映像，然后将其导入共享映像库。 有关详细信息，请参阅博客文章[将自定义映像导入共享映像库](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)。

如果你决定使用共享映像库服务，则需要创建一个共享映像库，或将其附加到实验室帐户。 你可以暂时推迟此决定，因为随时都可以将共享映像库附加到实验室帐户。  

有关详细信息，请参阅：
- [Azure 实验室服务 - 管理员指南](./administrator-guide.md#shared-image-gallery)的“共享映像库”一节
- [Azure 实验室服务 - 管理员指南](./administrator-guide.md#pricing)的“定价”一节

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>你的实验室将使用 Azure 市场中的哪些映像？

Azure 市场提供了成百上千个可启用的映像，以便实验室创建者可以使用它们来创建实验室。 一些映像可能包含实验室需要的所有内容。 在其他情况下，你可以从使用映像开始，然后实验室创建者可以通过安装其他应用程序或工具对其进行自定义。

如果不知道需要哪些映像，可以稍后再回来启用它们。 若要查看哪些映像可用，最好的方法是首先创建一个实验室帐户。 这会为你提供访问权限，使你能查看可用映像及其内容的列表。  

有关详细信息，请参阅[指定可供实验室创建者使用的 Azure 市场映像](./specify-marketplace-images.md)。
  
### <a name="do-the-lab-vms-need-access-to-other-azure-or-on-premises-resources"></a>实验室 VM 是否需要访问其他 Azure 或本地资源？

设置实验室帐户时，还可以将实验室帐户与虚拟网络对等互连。  请记住，虚拟网络和实验室帐户必须位于同一个区域中。  若要确定是否需要与虚拟网络建立对等互连，请考虑以下情况：

- **访问许可证服务器**
  
   使用 Azure 市场映像时，操作系统许可证的成本会捆绑到实验室服务的定价中。 但是，不需要为操作系统本身提供许可证。 对于安装的其他软件和应用程序，你需要根据需要提供许可证。  如果要访问许可证服务器：
   - 你可以选择连接到本地许可证服务器。  连接到本地许可证服务器需要进行额外设置。
   - 另一种更快的设置方法是创建在 Azure VM 上托管的许可证服务器。  Azure VM 位于与实验室帐户对等互连的虚拟网络中。

- **访问其他本地资源，如文件共享或数据库**

   你通常创建一个虚拟网络，用于提供通过站点到站点虚拟网络网关对本地资源的访问。 设置此类环境需要额外的时间。

- **访问位于虚拟网络外部的其他 Azure 资源**

   如果需要访问虚拟网络内未受保护的 Azure 资源，可以通过公共 Internet 访问这些资源，而无需执行任何对等互连。

   有关虚拟网络的详细信息，请参阅：
   - [Azure 实验室服务中的体系结构基础知识](./classroom-labs-fundamentals.md#virtual-network)的“虚拟网络”一节
   - [在 Azure 实验室服务中将实验室的网络连接到对等虚拟网络](./how-to-connect-peer-virtual-network.md)
   - [在 Azure 实验室服务中创建具有共享资源的实验室](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>设置实验室帐户

完成规划后，便可以设置实验室帐户了。 你可以应用与设置 [Teams 中的 Azure 实验室服务](./lab-services-within-teams-overview.md)相同的步骤。

1. **设置实验室帐户**。 有关说明，请参阅[创建实验室帐户](./tutorial-setup-lab-account.md#create-a-lab-account)。
   
    有关命名约定的信息，请参阅 [Azure 实验室服务 - 管理员指南](./administrator-guide.md#naming)的“命名”一节。

1. **将用户添加为“实验室创建者”角色**。 有关说明，请参阅[将用户添加为“实验室创建者”角色](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。

1. **连接到对等虚拟网络**。 有关说明，请参阅[将实验室的网络连接到对等虚拟网络](./how-to-connect-peer-virtual-network.md)。

   你可能还需要参阅[配置实验室 VM 地址范围](./how-to-configure-lab-accounts.md)的相关说明。

1. **启用和查看映像**。 有关说明，请参阅[指定可供“实验室创建者”使用的 Azure 市场映像](./specify-marketplace-images.md)。

   若要查看每个 Azure 市场映像的内容，请选择相应的映像名称。 例如，以下屏幕截图显示了 Ubuntu Data Science VM 映像的详细信息：

   ![可在 Azure 市场中查看的映像列表的屏幕截图。](./media/setup-guide/review-marketplace-images.png)

   如果共享映像库已附加到实验室帐户，并且你想启用实验室创建者共享的自定义映像，请完成与以下屏幕截图所示类似的步骤：

   ![共享映像库中已启用的自定义映像列表的屏幕截图。](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>后续步骤

有关设置和管理实验室的详细信息，请参阅：

- [管理实验室帐户](how-to-manage-lab-accounts.md)  
- [实验室设置指南](setup-guide.md)
