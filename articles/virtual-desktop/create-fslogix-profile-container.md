---
title: FSLogix 配置文件容器 NetApp Azure 虚拟桌面 - Azure
description: 如何在 Azure 虚拟桌面中使用 Azure NetApp 文件创建 FSLogix 配置文件容器。
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e4f4a7149f522d11a9a77bdeb9b16a0f848cba77
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776855"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>使用 Azure NetApp 文件和 AD DS 创建配置文件容器

我们建议将 FSLogix 配置文件容器用作 [Azure 虚拟桌面服务](overview.md)的用户配置文件解决方案。 FSLogix 配置文件容器将完整的用户配置文件存储在单个容器中，并设计为在非持久性远程计算环境（如 Azure 虚拟桌面）中漫游配置文件。 登录后，容器将动态附加到使用本地支持的虚拟硬盘 (VHD) 和 Hyper-V 虚拟硬盘 (VHDX) 的计算环境。 这些高级筛选器驱动程序技术使用户配置文件可以立即使用，并完全像本地用户配置文件一样出现在系统中。 要了解有关 FSLogix 配置文件容器的详细信息，请参阅 [FSLogix 配置文件容器和 Azure 文件](fslogix-containers-azure-files.md)。

可以使用 [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)创建 FSLogix 配置文件容器，这是一种易于使用的 Azure 原生平台服务，可帮助客户快速、可靠地为其 Azure 虚拟桌面环境预配企业级 SMB 卷。 要了解有关 Azure NetApp 文件存储的详细信息，请参阅[什么是 Azure NetApp 文件存储？](../azure-netapp-files/azure-netapp-files-introduction.md)

本指南将演示如何在 Azure 虚拟桌面中设置 Azure NetApp 文件帐户和创建 FSLogix 配置文件容器。

本文假定你已经在 Azure 虚拟桌面环境中设置了[主机池](create-host-pools-azure-marketplace.md)并将其分组为一个或多个租户。 要了解如何设置租户，请参阅[在 Azure 虚拟桌面中创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)和[我们的技术社区博客文章](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)。

本指南中的说明专门针对 Azure 虚拟桌面用户。 如果要了解有关如何在 Azure 虚拟桌面之外设置 Azure NetApp 文件和创建 FSLogix 配置文件容器的更常规指导，请参阅[设置 Azure Netapp 文件和创建 NFS 卷快速入门](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)。

>[!NOTE]
>本文不介绍用于保护对 Azure NetApp 文件共享的访问的最佳做法。

>[!NOTE]
>如果你正在寻找有关 Azure 上不同 FSLogix 配置文件容器存储选项的比较资料，请参阅 [FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

## <a name="prerequisites"></a>必备条件

在为主机池创建 FSLogix 配置文件容器之前，必须先执行以下操作：

- 设置和配置 Azure 虚拟桌面
- 预配 Azure 虚拟桌面主机池

## <a name="set-up-your-azure-netapp-files-account"></a>设置 Azure NetApp 文件帐户

首先需要设置 Azure NetApp 文件帐户。

1. 登录到 [Azure 门户](https://portal.azure.com)。 请确保你的帐户具有参与者或管理员权限。

2. 选择搜索栏右侧的 Azure Cloud Shell 图标以打开 Azure Cloud Shell。

3. Azure Cloud Shell 打开后，请选择 PowerShell。

4. 如果这是你第一次使用 Azure Cloud Shell，请在你用于保留 Azure NetApp 文件和 Azure 虚拟桌面的同一订阅中创建存储帐户。

   > [!div class="mx-imgBorder"]
   > ![存储帐户窗口，底部的创建存储按钮以红色突出显示。](media/create-storage-button.png)

5. Azure Cloud Shell 加载后，运行以下两个 cmdlet。

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. 在窗口左侧选择“所有服务”。 在菜单顶部显示的搜索框中输入 Azure NetApp 文件。

   > [!div class="mx-imgBorder"]
   > ![用户在“所有服务”搜索框中输入“Azure NetApp 文件”的屏幕快照。 搜索结果显示“Azure NetApp 文件”资源。](media/azure-netapp-files-search-box.png)


7. 在搜索结果中选择“Azure NetApp 文件”，然后选择“创建”。 

8. 选择“添加”按钮。
9. 当“新建 NetApp 帐户”选项卡打开时，输入以下值：

    - 对于“名称”，输入你的 NetApp 帐户名称。
    - 对于“订阅”，请从下拉菜单中选择您在步骤 4 中设置的存储帐户的订阅。
    - 对于“资源组”，从下拉列表中选择现有资源组，或者通过选择“新建”创建新组 。
    - 对于“位置”，从下拉菜单中选择 NetApp 帐户的区域。 该区域必须与你的会话主机 VM 位于同一区域。

   >[!NOTE]
   >Azure NetApp 文件目前不支持跨区域装载卷。

10. 完成后，选择“创建”以创建 NetApp 帐户。

## <a name="create-a-capacity-pool"></a>创建容量池

接下来，创建新的容量池：

1. 转到“Azure NetApp 文件”菜单，然后选择你的新帐户。
2. 在帐户菜单中，在“存储服务”下选择“容量池”。
3. 选择“添加池”。
4. 当“新建容量池”选项卡打开时，输入以下值：

    - 对于“名称”，输入新容量池的名称。
    - 对于“服务级别”，从下拉菜单中选择所需的值。 对于大多数环境，我们推荐选择“高级”。
       >[!NOTE]
       >高级设置提供可用于高级服务级别的最小吞吐量，即 256 MBps。 对于生产环境，可能需要调整此吞吐量。 最终吞吐量是基于[吞吐量限制](../azure-netapp-files/azure-netapp-files-service-levels.md)中所述的关系。
    - 对于“大小(TiB)”，输入最适合你的需求的容量池大小。 最小大小为 4 TiB。

5. 完成后，选择“确定”。

## <a name="join-an-active-directory-connection"></a>加入 Active Directory 连接

然后，需要加入 Active Directory 连接。

1. 在页面左侧的菜单中选择“Active Directory 连接”，然后选择“加入”按钮以打开“加入Active Directory”页面。  

   > [!div class="mx-imgBorder"]
   > ![“加入 Active Directory 连接”菜单的屏幕截图。](media/active-directory-connections-menu.png)

2. 在“加入 Active Directory”页面中输入以下值以加入连接：

    - 对于“主 DNS”，输入你的环境中可以解析域名的 DNS 服务器的 IP 地址。
    - 对于“域”，输入完全限定的域名 (FQDN)。
    - 对于“SMB 服务器（计算机帐户）前缀”，输入要附加到计算机帐户名称的字符串。
    - 对于“用户名”，输入具有执行域加入权限的帐户的名称。
    - 对于“密码”，输入帐户的密码。

## <a name="create-a-new-volume"></a>创建新卷

接下来，需要创建一个新卷。

1. 选择“卷”，然后选择“添加卷”。 

2. 打开“创建卷”选项卡时，输入以下值：

    - 对于“卷名”，输入新卷的名称。
    - 对于“容量池”，从下拉菜单中选择刚创建的容量池。
    - 对于“配额 (GiB)”，输入适合你的环境的卷大小。
    - 对于“虚拟网络”，从下拉菜单中选择一个与域控制器具有连接性的现有虚拟网络。
    - 在“子网”下，选择“新建” 。 请记住，此子网将委托给 Azure NetApp 文件。

3.  选择“下一步: 协议” **\>\>** 以打开“协议”选项卡并配置卷访问参数。

## <a name="configure-volume-access-parameters"></a>配置卷访问参数

创建卷后，请配置卷访问参数。

1.  选择“SMB”作为协议类型。
2.  在“Active Directory”下拉菜单中的“配置”下面，选择[加入 Active Directory 连接](create-fslogix-profile-container.md#join-an-active-directory-connection) 中最初连接的目录。 请记住，每个订阅的 Active Directory 数量上限为 1 个。
3.  在“共享名”文本框中，输入会话主机池及其用户使用的共享名。

4.  在页面底部选择“查看 + 创建”。 这将打开验证页面。 成功验证卷后，选择“创建”。

5.  此时，新卷将开始部署。 部署完成后，可以使用 Azure NetApp 文件共享。

6.  要查看安装路径，请选择“转到资源”，然后在“概述”选项卡中寻找。

    > [!div class="mx-imgBorder"]
    > ![“概述”屏幕的屏幕快照，红色箭头指向安装路径。](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>在会话主机虚拟机 (VM) 上配置 FSLogix

本节是基于[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。

1. 在会话主机 VM 中仍处于远程状态时，[下载 FSLogix 代理 .zip 文件](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409)。

2. 解压缩下载的文件。

3. 在文件中，转到 **x64** > **Releases**，然后运行 **FSLogixAppsSetup.exe**. 安装菜单将打开。

4.  如果有产品密钥，请在“产品密钥”文本框中输入。

5. 选中“我同意许可证的条款和条件”旁边的复选框。

6. 选择“安装”  。

7. 导航到 **C:\\Program Files\\FSLogix\\Apps** 以确认代理已安装。

8. 从开始菜单中，以管理员身份运行“RegEdit”。

9. 导航到“计算机\\ HKEY_LOCAL_MACHINE\\software\\FSLogix”。

10. 创建名为“配置文件”的密钥。

11.  创建一个名为 **Enabled** 的值，并将 **REG_DWORD** 类型设置为数据值 **1**。

12. 创建一个名为 **VHDLocations** 的值，类型为 **Multi-String**，并将其数据值设置为 Azure NetApp 文件共享的 URI。

13. 创建一个名为 **DeleteLocalProfileWhenVHDShouldApply** 的值，其 DWORD 值为 1，以避免在登录前使用现有本地配置文件出现问题。

     >[!WARNING]
     >创建 DeleteLocalProfileWhenVHDShouldApply 值时请小心。 如果 FSLogix 配置文件系统确定用户应该具有 FSLogix 配置文件，但本地配置文件已存在，则配置文件容器将永久删除本地配置文件。 然后，将用新的 FSLogix 配置文件登录该用户。

## <a name="assign-users-to-session-host"></a>将用户分配到会话主机

1. 以管理员身份打开 POWERSHELL ISE 并登录到 Azure 虚拟桌面。

2. 运行以下 cmdlet：

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. 当系统提示输入凭据时，请在 Azure 虚拟桌面租户上输入具有租户创建者或 RDS 所有者/RDS 参与者角色的用户的凭据。

4. 运行以下 cmdlet 以将用户分配到远程桌面组：

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>确保用户可以访问 Azure NetApp 文件共享

1. 打开 Internet 浏览器，并转到 <https://rdweb.wvd.microsoft.com/arm/webclient>。

2. 使用分配给远程桌面组的用户的凭据来登录。

3. 建立用户会话后，请使用管理帐户登录到 Azure 门户。

4. 打开“Azure NetApp 文件”，选择你的“Azure NetApp 文件”帐户，然后选择“卷”。  “卷”菜单打开后，选择相应的卷。

   > [!div class="mx-imgBorder"]
   > ![先前在 Azure 门户中选择了“卷”按钮时设置的 NetApp 帐户的屏幕快照。](media/netapp-account.png)

5. 转到“概述”选项卡，并确认 FSLogix 配置文件容器正在使用空间。

6. 使用远程桌面直接连接到主机池的任何 VM 部分，然后打开“文件资源管理器”。 然后导航到“安装路径”（在以下示例中，安装路径为 \\\\anf-SMB-3863.gt1107.onmicrosoft.com\\anf-VOL）。

   在此文件夹中，应存在一个配置文件 VHD（或 VHDX），如以下示例中所示。

   > [!div class="mx-imgBorder"]
   > ![装载路径中文件夹内容的屏幕截图。 内部是一个名为“Profile_ssbb”的 VHD 文件。](media/mount-path-folder.png)

## <a name="next-steps"></a>后续步骤

你可以使用 FSLogix 配置文件容器来设置用户配置文件共享。 要了解如何使用新容器创建用户配置文件共享，请参阅[使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。

你还可以创建 Azure 文件共享，以用于存储 FSLogix 配置文件。 要了解详细信息，请参阅[创建具有域控制器的 Azure 文件共享](create-file-share.md)。