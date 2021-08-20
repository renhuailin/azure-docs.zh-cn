---
title: 部署 Azure 虚拟桌面入门指南功能 - Azure
description: 介绍如何使用 Azure 门户入门指南功能快速设置 Azure 虚拟桌面的快速入门指南。
author: Heidilohr
ms.topic: quickstart
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 664a63e50b4b0ff1d239317fce653378461a5d4c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798835"
---
# <a name="deploy-azure-virtual-desktop-with-the-getting-started-feature"></a>使用入门指南功能部署 Azure 虚拟桌面

Azure 门户新的入门指南功能可快速轻松地在部署中安装和配置 Azure 虚拟桌面。

## <a name="requirements"></a>要求

需要以下各项才能使用入门指南：

- 一个 Azure Active Directory (AD) 租户
- 一个具有 Azure AD 全局管理员权限的帐户

   >[!NOTE]
   >入门指南功能目前不支持 MSA、B2B 或来宾帐户。

- 一个有效的 Azure 订阅

   >[!NOTE]
   >入门指南功能目前不支持使用多重身份验证的帐户。

- 具有订阅所有者权限的帐户。

如果在包含 Active Directory 域服务 (AD DS) 的环境中使用入门指南功能，还需要满足以下要求：

- AD DS 域管理员凭据
- 必须在订阅上配置 Azure AD Connect，并确保“用户”容器与 Azure AD 同步
- 虚拟机 (VM) 中的域控制器不得具有类型为 Microsoft.Powershell.DSC 的 DSC 扩展

如果要在没有标识提供者的环境中使用入门指南功能，则还需要满足以下额外要求：

- AD 域加入 UPN 不得包含[用户名规则列表不允许](../virtual-machines/windows/faq.yml#what-are-the-username-requirements-when-creating-a-vm-)的任何关键字，且必须使用 Azure AD 订阅中尚不存在的唯一用户名。
- 必须创建新的主机池，以添加使用入门指南功能创建的会话主机。 如果尝试在现有主机池中添加会话主机，则该主机无法正常工作。

## <a name="for-subscriptions-with-azure-ad-ds-or-ad-ds"></a>对于包含 Azure AD DS 或 AD DS 的订阅

下面介绍如何在已包含 Azure AD DS 或 AD DS 的订阅中使用入门指南功能：

1. 打开 [Azure 门户](https://portal.azure.com)。

2. 登录 Azure 并打开“Azure 虚拟桌面管理”，然后选择“入门指南”选项卡。此时将打开入门指南功能登录页。

3. 选择“创建”。

4. 在“基本信息”选项卡中，选择以下值：

    - 对于“订阅”，请转到“订阅的配置方式”，然后选择“现有设置”。

    - 在“位置”中，选择资源的部署位置。

    - 对于“Azure 管理员 UPN”，请输入对 Azure AD 具有管理员权限且对计划使用的订阅具有所有者权限的帐户的完整用户主体名称 (UPN)。

    - 对于“AD 域加入 UPN”，请输入具有计划将 VM 加入域所需权限的帐户的完整 UPN。

    - 对于“标识”，请根据环境选择“Azure AD DS”或“AD DS”。 此处选择的内容将影响 VM 所需的输入。

5. 在“虚拟机”选项卡中，选择以下值：

    - 对于“是否希望用户共享此计算机?”，请根据需要选择以下选项之一：
      - 如果要创建单个会话或个人主机池，请选择“否”。
      - 如果要创建多个会话或共用主机池，请选择“是(多会话)”。 这也会创建一个已加入 Azure AD DS 或 AD DS 的 Azure 文件存储帐户。

    - 对于“映像类型”，请选择 Azure 映像库中的映像、自定义映像或存储 blob 中的 VHD。

    - 对于“VM 大小”，请选择要部署的 VM 所需的大小和 SKU。

    - 对于“VM 数”，请选择要在主机池中预配的 VM 数量。

    - 如果要将现有设置与 AD DS 结合使用，将显示以下选项：

       - 对于“子网”，请在 VNET 中选择一个子网。 选择的子网必须与标识（AD DS 或 Azure AD DS）位于同一位置，或与其对等互连。

       - 对于“域控制器资源组”，请选择 AD DS VM 所在或与之对等互连的资源组。 包含域控制器的资源组必须位于同一订阅中。 入门指南功能目前不支持对等互连订阅。

       - 对于“域控制器虚拟机”，请输入运行部署 AD DS 的 VM 的名称。

    - 如果要打开“选择 Azure AD 用户或用户组”，请选中“分配现有用户”复选框。

    - 如果要创建验证用户帐户以测试部署，请选中“创建验证用户”复选框，并在出现的提示中输入用户名和密码。

       >[!NOTE]
       >入门指南将在“用户”容器中创建验证用户组。 必须确保验证组已同步到 Azure AD。 如果没有同步，请在同步到 Azure AD 的组织单位中预先创建 AVDValidationUsers 组。

## <a name="for-subscriptions-without-azure-ad-ds-or-ad-ds"></a>对于不包含 Azure AD DS 或 AD DS 的订阅

本部分将介绍如何在不包含 Azure AD DS 或 AD DS 的订阅中使用入门指南功能。 为了便于参考，这些订阅有时称为“空”订阅。

若要在不包含 Azure AD DS 或 AD DS 的订阅上部署 Azure 虚拟桌面，请执行以下操作：

1. 打开 [Azure 门户](https://portal.azure.com)。

2. 登录 Azure 并打开“Azure 虚拟桌面管理”，然后选择“入门指南”选项卡。此时将打开入门指南功能登录页。

3. 在“基本信息”选项卡中，选择以下值：

    - 对于“订阅”，请选择要在其中部署 Azure 虚拟桌面的订阅。

    - 对于“订阅的配置方式”，请选择“空订阅”。 “空”订阅无需 Azure AD 或 AD DS 等标识提供者。

    - 对于“资源组前缀”，请输入要创建的资源组的前缀：“-prerequisite”、“-deployment”和“-avd”。

    - 在“位置”中，输入要用于部署的资源位置。

    - 对于“Azure 管理员 UPN”，请输入对 Azure AD 具有管理员权限并对订阅具有所有者权限的帐户的完整 UPN。

    - 对于“AD 域加入 UPN”，请输入将添加到“AAD DC 管理员”组的帐户的完整 UPN。

    >[!NOTE]
    >AD 域加入 UPN 的用户名应为 Azure AD 中尚不存在的唯一名称。 入门指南功能目前不支持使用不包含 Azure AD 或 AD DS 的帐户的现有 Azure AD 用户名。

4. 在“虚拟机”选项卡中，选择以下值：

    - 对于“是否希望用户共享此计算机?”，请根据需要选择以下选项之一：
      - 如果要创建单个会话或个人主机池，请选择“否”。
      - 如果要创建多个会话或共用主机池，请选择“是(多会话)”。 这也会创建一个已加入 Azure AD DS 或 AD DS 的 Azure 文件存储帐户。

    - 对于“映像类型”，请选择 Azure 映像库中的映像、自定义映像或存储 blob 中的 VHD。

    - 对于“VM 大小”，请选择要部署的 VM 所需的大小和 SKU。

    - 对于“VM 数”，请选择要在主机池中预配的 VM 数量。

5. 在“分配”选项卡中，选择“创建验证用户”，然后在“验证用户用户名”和“验证用户密码”字段中输入用户名和密码。 验证用户将用于部署就绪后对其进行测试。

## <a name="clean-up-resources"></a>清理资源

如果在部署后改变主意并想要在不产生额外计费成本的情况下从环境中删除 Azure 虚拟桌面资源，可以按照本部分中的说明安全地删除它们。

如果在包含 Azure AD DS 或 AD DS 的订阅上创建了资源，则该功能将使两个资源组具有前缀“-deployment”和“-avd”。 在 Azure 门户中，请转到“资源组”并删除包含这些前缀的任何资源组以删除部署。

如果在不包含 Azure AD DS 或 AD DS 的订阅上创建了资源，则该功能将使三个资源组具有前缀“-prerequisite”、“-deployment”和“-avd” 。 在 Azure 门户中，请转到“资源组”并删除包含这些前缀的任何资源组以删除部署。

## <a name="next-steps"></a>后续步骤

如果想要更深入了解如何部署 Azure 虚拟桌面，请从查看[使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)教程开始，了解如何手动设置部署。