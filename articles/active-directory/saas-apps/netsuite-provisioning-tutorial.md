---
title: 教程：使用 Azure Active Directory 为 NetSuite OneWorld 配置自动用户预配 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 NetSuite OneWorld 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b1c03bafd6d97dd6a60defee00d4efe854315631
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648078"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>教程：为 NetSuite 配置自动用户预配

本教程旨在介绍为从 Azure AD 自动将用户帐户预配到 NetSuite 以及取消其预配而需要在 NetSuite OneWorld 和 Azure AD 中执行的步骤。

> [!WARNING]
> 由于 Microsoft 用来将用户预配到 NetSuite 的 NetSuite API 发生更改，此预配集成将停止与 NetSuite 的 Spring 2021 更新版本一起工作。  此更新将于 2021 年 2 月至 4 月期间到达 NetSuite 客户处。 结果是，Azure Active Directory 企业应用库中 NetSuite 应用程序的预配功能很快就会被删除。 应用程序的 SSO 功能将保持不变。 Microsoft 正在与 NetSuite 合作来构建一种新的现代化预配集成，但在这完成之前，目前没有启用 ETA。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

*   Azure Active Directory 租户。
*   NetSuite OneWorld 订阅。 请注意，目前仅 NetSuite OneWorld 支持自动用户预配。
*   具有管理员权限的 Netsuite 用户帐户。
*   与 Azure AD 集成需要 2FA 豁免。 请联系 NetSuite 支持团队请求此豁免。

## <a name="assigning-users-to-netsuite-oneworld"></a>将用户分配到 NetSuite OneWorld

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中应用程序的用户和组。

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 NetSuite 应用的用户。 确定后，可按照此处的说明将这些用户分配到 NetSuite 应用：

[向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>将用户分配到 NetSuite OneWorld 的重要提示

*   建议将单个 Azure AD 用户分配到 NetSuite 以测试预配配置。 其他用户和/或组可以稍后分配。

*   将用户分配到 NetSuite 时，必须选择有效的用户角色。 “默认访问权限”角色不可用于预配。

## <a name="enable-user-provisioning"></a>启用用户预配

本部分将指导完成以下操作：将 Azure AD 连接到 NetSuite 的用户帐户预配 API，配置预配服务以便基于 Azure AD 中的用户和组分配在 NetSuite 中创建、更新和禁用所分配的用户帐户。

> [!TIP] 
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 NetSuite 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-user-account-provisioning"></a>配置用户帐户预配：

本部分的目的是概述如何对 NetSuite 启用 Active Directory 用户帐户的用户预配。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

1. 如果已为 NetSuite 配置单一登录，请使用搜索字段搜索 NetSuite 实例。 否则，请选择“添加”并在应用程序库中搜索“NetSuite”。 从搜索结果中选择 NetSuite，并将其添加到应用程序列表。

1. 选择 NetSuite 实例，并选择“预配”选项卡。

1. 将“预配模式”  设置为“自动”  。 

    ![屏幕截图显示 NetSuite“预配”页，其中“预配模式”设置为“自动”或者可以设置的其他值。](./media/netsuite-provisioning-tutorial/provisioning.png)

1. 在“管理员凭据”部分中，提供以下配置设置：
   
    a. 在“管理员用户名”文本框中，键入在 NetSuite.com 中已分配“系统管理员”配置文件的 NetSuite 帐户名称。
   
    b. 在“管理员密码”文本框中，键入此帐户的密码。
      
1. 在 Azure 门户中，单击“测试连接”，确保 Azure AD 可以连接到 NetSuite 应用。

1. 在“通知电子邮件”字段中输入应收到预配错误通知的用户或组的电子邮件地址，并选中复选框。

1. 单击“保存” 

1. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 NetSuite”。

1. 在“属性映射”部分，查看从 Azure AD 同步到 NetSuite 的用户属性。 请注意，选为“匹配”属性的属性将用于匹配 NetSuite 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

1. 要为 NetSuite 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

1. 单击“保存” 

这会开始将“用户和组”部分中分配的任何用户和/或组初始同步到 NetSuite。 请注意，初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动日志的链接，这些日志描述了预配服务对 NetSuite 应用执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [配置单一登录](netsuite-tutorial.md)
