---
title: 使用 Azure 门户创建 Azure 虚拟桌面主机池 - Azure
description: 如何使用 Azure 门户创建 Azure 虚拟桌面主机池。
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 8e33c165cd249b578bd4cba9ef4f25446287d7ec
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766438"
---
# <a name="tutorial-create-a-host-pool"></a>教程：创建主机池

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md)。 无法通过 Azure 门户来管理使用 Azure 虚拟桌面（经典）创建的任何对象。

主机池是 Azure 虚拟桌面环境中包含一个或多个相同虚拟机 (VM)（也称为“会话主机”）的集合。 每个主机池可以包含一个应用组，用户可以像在物理桌面上一样与该应用组交互。 若要详细了解部署体系结构，请查看 [Azure 虚拟桌面环境](environment-setup.md)。 如果你是一名使用 Azure 虚拟桌面远程应用流式处理的应用开发人员，则你的客户或用户可以像使用物理设备上的本地应用一样使用你的应用。 有关如何以应用开发人员的身份使用 Azure 虚拟桌面的详细信息，请查看 [Azure 虚拟桌面远程应用流式处理](./remote-app-streaming/custom-apps.md)文档。

>[!NOTE]
>如果你是一名使用 Azure 虚拟桌面远程应用流式处理的应用开发人员，并且应用的用户与部署位于同一组织，则你可以使用现有的 Azure 租户来创建主机池。 如果用户位于组织外部，则出于安全原因，需要为每个组织单独创建至少包含一个主机池的 Azure 租户。 若要详细了解我们为确保部署安全而建议遵循的做法，请参阅[体系结构建议](./remote-app-streaming/architecture-recs.md)。

本文将引导你完成通过 Azure 门户为 Azure 虚拟桌面环境创建主机池的设置过程。 此方法提供基于浏览器的用户界面，用于在 Azure 虚拟桌面中创建主机池，在 Azure 订阅中创建包含 VM 的资源组，将这些 VM 加入 Active Directory (AD) 域或 Azure Active Directory (Azure AD) 租户，以及将 VM 注册到 Azure 虚拟桌面。

## <a name="prerequisites"></a>先决条件

有两组不同的要求，具体适用哪组要求取决于你是为组织设置部署的 IT 专业人员还是为客户提供应用程序的应用开发人员。

### <a name="requirements-for-it-professionals"></a>针对 IT 专业人员的要求

需要输入以下参数才能创建主机池：

- VM 映像名称
- VM 配置
- 域和网络属性
- Azure 虚拟桌面主机池属性

还需要了解以下信息：

- 要使用的映像的源所在的位置。 该映像是来自 Azure 库，还是一个自定义映像？
- 你的域加入凭据。

### <a name="requirements-for-app-developers"></a>针对应用开发人员的要求

如果你是使用 Azure 虚拟桌面远程应用流式处理将应用交付给客户的应用开发人员，则需要做好以下准备：

- 如果你打算向最终用户提供组织的应用，请确保该应用已确实准备就绪。 有关详细信息，请参阅[如何使用 Azure 虚拟桌面托管自定义应用](./remote-app-streaming/custom-apps.md)。
- 如果现有的 Azure 库映像选项不满足需求，则你还需要为会话主机 VM 创建自己的自定义映像。 若要详细了解如何创建 VM 映像，请参阅[准备要上传到 Azure 的 Windows VHD 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md)，以及[为 Azure 中的通用化 VM 创建托管映像](../virtual-machines/windows/capture-image-resource.md)。
- 你的域加入凭据。 如果你没有与 Azure 虚拟桌面兼容的标识管理系统，则需要为主机池设置标识管理。 有关详细信息，请参阅[设置托管标识](./remote-app-streaming/identities.md)。

### <a name="final-requirements"></a>最后的要求

最后，请确保已注册 Microsoft.DesktopVirtualization 资源提供程序。 如果还没有注册，请转到“订阅”，选择你的订阅名称，然后选择“资源提供程序” 。 搜索“DesktopVirtualization”，选择“Microsoft.DesktopVirtualization”，然后选择“注册”  。

如果你是创建网络的 IT 专业人员，在使用 Azure 资源管理器模板创建 Azure 虚拟桌面主机池时，可以从 Azure 库、托管映像或非托管映像创建虚拟机。 若要详细了解如何创建 VM 映像，请参阅[准备要上传到 Azure 的 Windows VHD 或 VHDX](../virtual-machines/windows/prepare-for-upload-vhd-image.md)，以及[为 Azure 中的通用化 VM 创建托管映像](../virtual-machines/windows/capture-image-resource.md)。 （如果你是应用开发人员，则不需要考虑此部分。）

最后但同样重要的是，如果你还没有 Azure 订阅，请务必在按照以下说明开始操作之前[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="begin-the-host-pool-setup-process"></a>开始执行主机池设置过程

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要开始创建新的主机池，请执行以下操作：

1. 在 [https://portal.azure.com](https://portal.azure.com/) 中登录 Azure 门户。
   
   >[!NOTE]
   > 如果要登录 US Gov 门户，请改为转到 [https://portal.azure.us/](https://portal.azure.us/)。
   > 
   >如果你要访问 Azure 中国门户，请转到 [https://portal.azure.cn/](https://portal.azure.cn/)。

2. 在搜索栏中输入“Azure 虚拟桌面”，然后在“服务”下找到并选择“Azure 虚拟桌面” 。

3. 在“Azure 虚拟桌面”概述页中，选择“创建主机池” 。

4. 在“基本信息”选项卡中，在“项目详细信息”下选择正确的订阅。

5. 选择“新建”以创建新的资源组，或者从下拉菜单中选择现有的资源组。

6. 为你的主机池输入一个唯一名称。

7. 在“位置”字段中，从下拉菜单中选择要在其中创建主机池的区域。

   与你选择的区域关联的 Azure 地理位置是此主机池及其相关对象的元数据将存储到的位置。 请确保在要用于存储服务元数据的地理位置选择区域。

     > [!div class="mx-imgBorder"]
     > ![Azure 门户的屏幕截图，其中显示了“位置”字段并选择了“美国东部”位置。 字段旁边的文本指出“将在美国东部存储元数据”。](media/portal-location-field.png)
  
   >[!NOTE]
   > 如果要在美国以外的[受支持区域](data-locations.md)中创建主机池，则需要重新注册资源提供程序。 重新注册后，你应该会在下拉列表中看到其他区域以供选择位置。 参阅[创建主机池](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects)故障排除文章了解如何重新注册。

8. 在“主机池类型”下，选择主机池是“个人”还是“共用”类型 。

    - 如果选择“个人”，请在“分配类型”字段中选择“自动”或“直接”  。

      > [!div class="mx-imgBorder"]
      > ![“分配类型”字段下拉菜单的屏幕截图。 用户已选择“自动”。](media/assignment-type-field.png)

9.  如果选择“共用”，请输入以下信息：

     - 对于“会话数上限”，请输入你要在单个会话主机中进行负载均衡的最大用户数。
     - 对于“负载均衡算法”，请根据使用模式选择“广度优先”或“深度优先”。 若要详细了解其中每个选项的含义，请参阅[主机池负载均衡方法](host-pool-load-balancing.md)。

       > [!div class="mx-imgBorder"]
       > ![“分配类型”字段的屏幕截图，其中已选择“共用”。 用户正在将其光标悬停在负载均衡下拉菜单中的“广度优先”上。](media/pooled-assignment-type.png)

10. 在完成时选择“下一步:虚拟机 >”。

11. 如果你已创建虚拟机并想要在新主机池中使用它们，请选择“否”，然后选择“下一步: 工作区 >”，并跳转到[工作区信息](#workspace-information)部分。 如果要创建新的虚拟机并将其注册到新主机池，请选择“是”。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先为 Azure CLI 准备环境：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

登录后，使用 [az desktopvirtualization hostpool create](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_create) 命令创建新的主机池，可以选择为会话主机创建注册令牌以加入主机池：

```azurecli
az desktopvirtualization hostpool create --name "MyHostPool" \
    --resource-group "MyResourceGroup" \ 
    --location "MyLocation" \
    --host-pool-type "Pooled" \
    --load-balancer-type "BreadthFirst" \
    --max-session-limit 999 \
    --personal-desktop-assignment-type "Automatic"  \
    --registration-info expiration-time="2022-03-22T14:01:54.9571247Z" registration-token-operation="Update" \
    --sso-context "KeyVaultPath" \
    --description "Description of this host pool" \
    --friendly-name "Friendly name of this host pool" \
    --tags tag1="value1" tag2="value2" 
```

如果要创建新的虚拟机并将其注册到新主机池，请继续按照以下部分操作。 如果已创建虚拟机并想要在新主机池中使用它们，请跳转到[工作区信息](#workspace-information)部分。 

---

现在，主机池已创建完成，接下来让我们转到设置过程的下一部分来创建 VM。

## <a name="virtual-machine-details"></a>虚拟机详细信息

现在，我们已完成第一部分，接下来必须设置 VM。

### <a name="portal"></a>[Portal](#tab/azure-portal)

在 Azure 门户主机池设置过程中设置虚拟机：

1. 在“资源组”下，选择要在其中创建虚拟机的资源组。 此资源组可以与用于主机池的资源组不同。

2. 然后，提供一个名称前缀来为设置过程创建的虚拟机命名。 该后缀是 `-` 加上从 0 开始的数字。

3. 选择要在其中创建虚拟机的“虚拟机位置”。 该区域可以与你为主机池选择的区域相同或不同。 请记住，VM 价格因区域而异，VM 位置应尽可能靠近其用户，以最大程度地提高性能。 有关详细信息，请参阅 [Azure 虚拟桌面的数据位置](data-locations.md)。
   
4. 接下来，选择最符合需求的可用性选项。 若要详细了解哪个选项适合你，请参阅 [Azure 中虚拟机的可用性选项](../virtual-machines/availability.md)和[常见问题解答](/azure/virtual-desktop/faq#which-availability-option-is-best-for-me)。
   
   > [!div class="mx-imgBorder"]
   > ![可用性区域下拉菜单的屏幕截图。 已突出显示“可用性区域”选项。](media/availability-zone.png)

5. 接下来，选择需要用来创建虚拟机的映像。 可以选择“库”或“存储 Blob” 。

    - 如果选择了“库”，请从下拉菜单中选择一个建议的映像：

      - Windows 10 企业版多会话版本 1909
      - Windows 10 企业版多会话版本 1909 + Microsoft 365 应用
      - Windows Server 2019 Datacenter
      - Windows 10 企业版多会话版本 2004
      - Windows 10 企业版多会话版本 2004 + Microsoft 365 应用

      如果未看到所需的映像，请选择“查看所有映像”，然后便可以选择库中的另一个映像，或选择 Microsoft 和其他发行商提供的映像。 确保所选的映像是[受支持的 OS 映像](overview.md#supported-virtual-machine-os-images)之一。

      > [!div class="mx-imgBorder"]
      > ![Azure 门户的屏幕截图，其中显示了 Microsoft 提供的映像列表。](media/marketplace-images.png)

      还可以转到“我的项”并选择已上传的自定义映像。

      > [!div class="mx-imgBorder"]
      > ![“我的项”选项卡的屏幕截图。](media/my-items.png)

    - 如果选择“存储 Blob”，则可以使用你自己的、通过 Hyper-V 生成的映像或 Azure VM 上的映像。 只需输入该映像在存储 Blob 中的位置作为 URI 即可。
   
   映像的位置独立于可用性选项，但映像的区域复原能力决定了该映像是否可在可用性区域中使用。 如果在创建映像时选择可用性区域，请确保使用的是已启用区域复原能力的库中的映像。 若要详细了解应使用哪个区域复原能力选项，请参阅[常见问题解答](/azure/virtual-desktop/faq#which-availability-option-is-best-for-me)。

6. 然后，选择要使用的“虚拟机大小”。 可以按原样保留默认大小，也可以选择“更改大小”来更改大小。 如果选择“更改大小”，请在显示的窗口中选择适合你的工作负载的虚拟机大小。 若要详细了解虚拟机大小以及应该选择的大小，请参阅[虚拟机大小调整指南](/windows-server/remote/remote-desktop-services/virtual-machine-recs?context=/azure/virtual-desktop/context/context)。

7. 在“VM 数量”下，提供要为主机池创建的 VM 数量。

    >[!NOTE]
    >在设置主机池时，设置过程最多可以创建 400 个 VM，每个 VM 设置过程将在资源组中创建四个对象。 由于创建过程不会检查订阅配额，因此请确保输入的 VM 数量在资源组和订阅的 Azure VM 与 API 限制范围内。 创建完主机池后，可以添加更多的 VM。

8. 选择希望 VM 使用的 OS 磁盘类型：“标准 SSD”、“高级 SSD”或“标准 HDD”。

9. 在“网络和安全”下，选择要在其中放置所创建的虚拟机的“虚拟网络”和“子网” 。 请确保该虚拟网络可以连接到域控制器，因为你需要将该虚拟网络中的虚拟机加入域。 所选虚拟网络的 DNS 服务器应配置为使用域控制器的 IP。

10. 选择所需的安全组类型：“基本”、“高级”或“无”  。

    如果选择“基本”，则必须选择是否需要打开任何入站端口。 如果选择“是”，请从标准端口列表中选择允许入站连接的端口。

    >[!NOTE]
    >为了提高安全性，我们建议不要打开公共入站端口。

    > [!div class="mx-imgBorder"]
    > ![安全组页的屏幕截图，其中的下拉菜单显示了可用端口列表。](media/available-ports.png)

    如果选择“高级”，请选择已配置的现有网络安全组。

11. 然后，选择是要将虚拟机加入 Active Directory 还是 Azure Active Directory（预览版） 。

    - 对于 Active Directory，请提供要加入域的帐户，然后选择是否要加入特定的域和组织单位。

        - 对于 AD 域加入 UPN，请输入所选虚拟网络的 Active Directory 域管理员的凭据。 使用的帐户不能启用多重身份验证 (MFA)。 加入 Azure Active Directory 域服务 (Azure AD DS) 域时，使用的帐户必须是 Azure AD DC 管理员组的成员，并且帐户密码必须在 Azure AD DS 中有效。

        - 若要指定域，请选择“是”，然后输入要加入的域的名称。 如果需要，还可以通过输入不带引号的完整路径（可分辨名称）来添加虚拟机需要加入到的特定组织单位。 如果你不想指定域，请选择“否”。 VM 将自动加入到与 AD 域加入 UPN 的后缀匹配的域。
  
    - 对于 Azure Active Directory，可以选择“在 Intune 中注册 VM”，使该 VM 在部署后可供管理。

12. 在“虚拟机管理员帐户”下，输入在创建 VM 时要添加的本地管理员帐户的凭据。 可以在已加入 AD 和已加入 Azure AD 的 VM 中，将此帐户用于管理目的。

13. 在完成时选择“下一步:工作区 >”。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az vm create](/cli/azure/vm#az_vm_create) 命令创建新的 Azure 虚拟机：

```azurecli
az vm create --name "MyVMName" \
    --resource-group "MyResourceGroup" \
    --image "MyImage" \
    --generate-ssh-keys
```

有关使用 Azure CLI 创建 Azure 虚拟机的其他信息，请参阅：
- Windows
    - [使用 Azure CLI 创建 Windows VM]( /azure/virtual-machines/windows/quick-create-cli)
    - [教程：使用 Azure CLI 创建和管理 Windows VM](/cli/azure/azure-cli-vm-tutorial)
- Linux
    - [Create a Linux VM using the Azure CLI（使用 Azure CLI 创建 Linux VM）](../virtual-machines/linux/quick-create-cli.md)
    - [教程：使用 Azure CLI 创建和管理 Linux VM]( /azure/virtual-machines/linux/tutorial-manage-vm) 
---

完成这些操作后，我们便可以开始进行设置主机池的下一阶段：将应用组注册到工作区。

## <a name="workspace-information"></a>工作区信息

主机池设置过程默认会创建一个桌面应用程序组。 要使主机池按预期方式工作，需将此应用组发布给用户或用户组，并且必须将此应用组注册到工作区。

>[!NOTE]
>如果你是一名想要发布组织应用的应用开发人员，可将 MSIX 应用动态附加到用户会话，或将应用包添加到自定义 VM 映像。 有关详细信息，请参阅“如何使用 Azure 虚拟桌面为自定义应用提供服务”。

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要将桌面应用组注册到工作区，请执行以下操作：

1. 请选择“是”。

   如果选择“否”，则可以在以后再注册应用组，但我们建议尽快完成工作区注册，使主机池能够正常工作。

2. 接下来，选择是要创建新的工作区，还是从现有工作区进行选择。 只允许将应用组注册到在主机池所在的同一位置中创建的工作区。

3. 或者，可以选择“下一步:标记 >”。

    可以在此处添加标记，以便可以根据元数据将对象分组，为管理员的工作提供方便。

4. 完成操作后，选择“查看 + 创建”。

     >[!NOTE]
     >“查看 + 创建”过程不会检查你的密码是否符合安全标准，也不会检查你的体系结构是否正确，因此，你需要亲自检查密码和体系结构是否存在任何问题。

5. 检查你的部署的相关信息，确保全部正确。 完成操作后，选择“创建”。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az desktopvirtualization workspace create](/cli/azure/desktopvirtualization#az_desktopvirtualization_workspace_create) 命令创建新工作区：

```azurecli
az desktopvirtualization workspace create --name "MyWorkspace" \
    --resource-group "MyResourceGroup" \
    --location "MyLocation" \
    --tags tag1="value1" tag2="value2" \
    --friendly-name "Friendly name of this workspace" \
    --description "Description of this workspace" 
```
---

这会启动部署过程，该过程将创建以下对象：

- 新的主机池。
- 桌面应用组。
- 工作区（如果已选择创建工作区）。
- 如果你选择注册桌面应用组，则注册将会完成。
- 已加入域并已注册到新主机池的虚拟机（如果已选择创建虚拟机）。
- 基于配置的 Azure 资源管理模板的下载链接。

所有操作现已全部完成！

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>运行 Azure 资源管理器模板来预配新的主机池

如果你想要使用自动化过程，请[下载 Azure 资源管理器模板](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)来预配新的主机池。

>[!NOTE]
>如果你使用自动化过程来构建环境，则需要配置 JSON 文件的最新版本。 可在[此处](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list)找到 JSON 文件。

## <a name="next-steps"></a>后续步骤

创建主机池后，可以使用 RemoteApp 程序来填充主机池。 若要详细了解如何在 Azure 虚拟桌面中管理应用，请转到下一篇教程：

> [!div class="nextstepaction"]
> [管理应用组教程](./manage-app-groups.md)
