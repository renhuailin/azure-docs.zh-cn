---
title: 使用 Azure AD DS 启用 SharePoint 用户配置文件服务 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 域服务托管域以支持 SharePoint Server 的配置文件同步
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 10/05/2021
ms.author: justinha
ms.openlocfilehash: 955d37144220ec455f6f95bab2102123b54cd0c1
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129536198"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>配置 Azure Active Directory 域服务以支持 SharePoint Server 的用户配置文件同步

SharePoint Server 包括同步用户配置文件的服务。 此功能允许将用户配置文件存储在一个中心位置，并跨多个 SharePoint 站点和场访问它们。 若要配置 SharePoint Server 用户配置文件服务，必须在 Azure Active Directory 域服务 (Azure AD DS) 托管域中授予适当的权限。 有关更多详细信息，请参阅 [SharePoint Server 中的用户配置文件同步](/SharePoint/administration/user-profile-service-administration)。

本文介绍如何配置 Azure AD DS 以允许 SharePoint Server 用户配置文件同步服务。

## <a name="before-you-begin"></a>准备阶段

需有以下资源和特权才能完成本文：

* 一个有效的 Azure 订阅。
    * 如果你没有 Azure 订阅，请[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 与订阅关联的 Azure Active Directory 租户，可以与本地目录或仅限云的目录同步。
    * 如果需要，请[创建一个 Azure Active Directory 租户][create-azure-ad-tenant]或[将 Azure 订阅关联到你的帐户][associate-azure-ad-tenant]。
* 在 Azure AD 租户中启用并配置 Azure Active Directory 域服务托管域。
    * 如果需要，请完成[创建并配置 Azure Active Directory 域服务托管域][create-azure-ad-ds-instance]的教程。
* 已加入 Azure AD DS 托管域的 Windows Server 管理 VM。
    * 如果需要，请完成[创建管理 VM][tutorial-create-management-vm] 的教程。
* 属于 Azure AD 租户中“Azure AD DC 管理员”组的用户帐户。
* 用于用户配置文件同步服务的 SharePoint 服务帐户名称。 有关配置文件同步帐户的详细信息，请参阅[规划 SharePoint Server 中的管理和服务帐户][sharepoint-service-account]。 若要从 SharePoint 管理中心网站获取配置文件同步帐户名称，请单击“应用程序管理” > “管理服务应用程序” > “用户配置文件服务应用程序”  。 有关详细信息，请参阅[在 SharePoint Server 中使用 SharePoint Active Directory 导入来配置配置文件同步](/SharePoint/administration/configure-profile-synchronization-by-using-sharepoint-active-directory-import)。

## <a name="service-accounts-overview"></a>服务帐户概述

在托管域中，名为“AAD DC服务帐户”的安全组作为“用户”组织单位 (OU) 的一部分存在。 此安全组的成员具有以下委托权限：

- 根 DSE 具有“复制目录更改”特权。
- 对“配置”命名上下文（`cn=configuration` 容器）具有”复制目录更改”特权。

“AAD DC服务帐户”安全组也是内置组“Pre-Windows 2000 Compatible Access”的成员 。

添加到此安全组后，将向 SharePoint Server 用户配置文件同步服务的服务帐户授予所需的特权，使其能够正常工作。

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>启用对 SharePoint Server 用户配置文件同步的支持

SharePoint Server 的服务帐户需要足够的特权，才能将更改复制到目录，并使 SharePoint Server 用户配置文件同步正常工作。 若要提供这些特权，请将用于 SharePoint 用户配置文件同步的服务帐户添加到“AAD DC 服务帐户”组中。

在 Azure AD DS 管理 VM 中，完成以下步骤：

> [!NOTE]
> 要在托管域中编辑组成员身份，必须登录到“AAD DC Administrators”组成员的用户帐户。

1. 在“开始”屏幕中选择“管理工具”。 其中显示了可用管理工具列表，这些工具是在教程[创建管理 VM][tutorial-create-management-vm] 中安装的。
1. 若要管理组成员身份，请从管理工具列表中选择“Active Directory 管理中心”。
1. 在左窗格中，选择托管域，例如 aaddscontoso.com。 显示现有的 OU 和资源的列表。
1. 选择“用户”OU，然后选择“AAD DC服务帐户”安全组 **。
1. 选择“成员”，然后选择“添加...” 。
1. 输入 SharePoint 服务帐户的名称，然后选择“确定”。 在以下示例中，SharePoint 服务帐户的名称为“spadmin”：

    ![将 SharePoint 服务帐户添加到 AAD DC 服务帐户安全组](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)


<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts