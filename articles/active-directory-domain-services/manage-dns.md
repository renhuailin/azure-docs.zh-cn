---
title: 管理 Azure AD 域服务的 DNS | Microsoft Docs
description: 了解如何安装 DNS 服务器工具来管理 DNS，以及如何为 Azure Active Directory 域服务托管域创建条件转发器。
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 06/18/2021
ms.author: justinha
ms.openlocfilehash: 935abb1a1eae362e3036c323da35aa5fd7b9b018
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707963"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>管理 DNS 并在 Azure Active Directory 域服务托管域中创建条件转发器

Azure AD DS 包括为托管域提供名称解析的域名系统 (DNS) 服务器。 此 DNS 服务器包含使服务能够运行的关键组件的内置 DNS 记录和更新。

运行你自己的应用程序和服务时，你可能需要为未加入域的计算机创建 DNS 记录、为负载均衡器配置虚拟 IP 地址，或者设置外部 DNS 转发器。 属于“AAD DC 管理员”组的用户将被授予对 Azure AD DS 托管域的 DNS 管理权限，并且可以创建和编辑自定义 DNS 记录。

在混合环境中，在其他 DNS 命名空间（如本地 AD DS 环境）中配置的 DNS 区域和记录不会同步到托管域。 若要解析其他 DNS 命名空间中的命名资源，请创建并使用指向环境中的现有 DNS 服务器的条件转发器。

本文介绍了如何安装 DNS 服务器工具，然后使用 DNS 控制台来管理记录，并在 Azure AD DS 中创建条件转发器。

>[!NOTE]
>不支持创建或更改服务器级 DNS 转发器，并且会导致 Azure AD DS 托管域出现问题。

## <a name="before-you-begin"></a>开始之前

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]的教程。
* 从 Azure AD DS 虚拟网络到承载其他 DNS 命名空间的位置的连接。
    * 可以通过 [Azure ExpressRoute][expressroute] 或 [Azure VPN 网关][vpn-gateway]连接来提供此连接。
* 已加入托管域的 Windows Server 管理 VM。
    * 如果需要，请完成[创建 Windows Server VM 并将其加入到托管域][create-join-windows-vm]的教程。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。

## <a name="install-dns-server-tools"></a>安装 DNS 服务器工具

若要在托管域中创建和修改 DNS 记录，需要安装 DNS 服务器工具。 这些工具可以作为 Windows Server 中的一项功能进行安装。 有关如何在 Windows 客户端上安装管理工具的详细信息，请参阅安装[远程服务器管理工具 (RSAT)][install-rsat]。

1. 登录到管理 VM。 有关如何使用 Azure 门户进行连接的步骤，请参阅[连接到 Windows Server VM][connect-windows-server-vm]。
1. 如果在登录 VM 时服务器管理器默认情况下未打开，请选择“开始”菜单，然后选择“服务器管理器”。
1. 在“服务器管理器”窗口的“仪表板”窗格中，选择“添加角色和功能”。
1. 在“添加角色和功能向导”的“准备工作”页上，选择“下一步”。
1. 对于“安装类型”，请保留选中“基于角色或基于功能的安装”选项，然后选择“下一步”。
1. 在“服务器选择”页上，从服务器池中选择当前的 VM（例如 *myvm.aaddscontoso.com*），然后选择“下一步”。
1. 在“服务器角色”页上，单击“下一步”。
1. 在“功能”页上，依次展开“远程服务器管理工具”节点和“角色管理工具”节点。 从角色管理工具列表中选择“DNS 服务器工具”功能。

    ![从可用的角色管理工具列表中选择安装 DNS 服务器工具](./media/manage-dns/install-dns-tools.png)

1. 在“确认”页上选择“安装”。  安装 DNS 服务器工具可能需要一两分钟。
1. 功能安装完成后，选择“关闭”退出“添加角色和功能”向导。 

## <a name="open-the-dns-management-console-to-administer-dns"></a>打开 DNS 管理控制台来管理 DNS

安装 DNS 服务器工具后，你可以管理托管域上的 DNS 记录。

> [!NOTE]
> 若要管理托管域中的 DNS，必须登录到属于“AAD DC 管理员”组成员的用户帐户。

1. 在“开始”屏幕中选择“管理工具”。 此时会显示可用的管理工具列表，包括上一部分中安装的 **DNS**。 选择“DNS”以启动 DNS 管理控制台。
1. 在“连接到 DNS 服务器”对话框中，选择“以下计算机”，并输入托管域的 DNS 域名（例如aaddscontoso.com）：

    ![在 DNS 控制台中连接到托管域](./media/manage-dns/connect-dns-server.png)

1. DNS 控制台会连接到指定的托管域。 展开“正向查找区域”或“反向查找区域”以创建所需的 DNS 条目，或根据需要编辑现有记录。

    ![DNS 控制台 - 管理域](./media/manage-dns/dns-manager.png)

> [!WARNING]
> 使用 DNS 服务器工具来管理记录时，请确保不要删除或修改 Azure AD DS 使用的内置 DNS 记录。 内置 DNS 记录包括域 DNS 记录、名称服务器记录，以及其他用于 DC 定位的记录。 如果修改这些记录，虚拟网络上的域服务会中断。

## <a name="create-conditional-forwarders"></a>创建条件转发器

Azure AD DS DNS 区域应该只包含托管域本身的区域和记录。 请勿在托管域中创建其他区域来解析其他 DNS 命名空间中的命名资源。 而应使用托管域中的条件转发器告知 DNS 服务器要前往哪里来解析这些资源的地址。

条件转发器是 DNS 服务器中的一个配置选项，允许你定义要将查询转发到的 DNS 域，例如 contoso.com。 系统会将 DNS 查询转发到为该域配置的 DNS，而不是由本地 DNS 服务器来尝试解析对该域中的记录进行的查询。 此配置可确保返回正确的 DNS 记录，因为你不会创建一个在托管域中包含重复记录的本地 DNS 区域来反映这些资源。

若要在托管域中创建条件转发器，请完成以下步骤：

1. 选择你的 DNS 区域，例如 aaddscontoso.com。
1. 选择“条件转发器”，然后右键单击并选择“新建条件转发器...”
1. 输入你的其他 **DNS 域**，例如 contoso.com，然后输入该命名空间的 DNS 服务器的 IP 地址，如以下示例所示：

    ![为 DNS 服务器添加并配置条件转发器](./media/manage-dns/create-conditional-forwarder.png)

1. 选中“在 Active Directory 中存储此条件转发器，并按如下方式复制它”复选框，然后选择“此域中的所有 DNS 服务器”选项，如以下示例所示：

    ![DNS 控制台 - 选择“此域中的所有 DNS 服务器”](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > 如果条件转发器存储在林中而不是域中，则条件转发器会失败。

1. 若要创建条件转发器，请选择“确定”。

现在，连接到托管域的 VM 的其他命名空间中的资源的名称解析应能正确进行。 针对条件转发器中配置的 DNS 域的查询会被传递到相关的 DNS 服务器。

## <a name="next-steps"></a>后续步骤

有关管理 DNS 的详细信息，请参阅 [Technet 上的“DNS 工具”一文](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11))。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh