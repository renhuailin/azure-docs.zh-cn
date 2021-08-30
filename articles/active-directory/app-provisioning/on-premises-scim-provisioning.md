---
title: 从 Azure AD 本地应用预配到已启用 SCIM 的应用
description: 本文介绍如何使用 Azure AD 预配服务将用户预配到已启用 SCIM 的本地应用中。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: 11bd751ebc95b1a9486534b34c771fde64eced21
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437342"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>从 Azure AD 本地应用程序预配到已启用 SCIM 的应用

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 如需请求功能访问权限，请使用[访问请求窗体](https://aka.ms/onpremprovisioningpublicpreviewaccess)”。 我们正在准备正式发布版，将在接下来的几个月内向更多客户和连接器开放该预览版。

Azure Active Directory (Azure AD) 预配服务支持 [SCIM 2.0](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) 客户端，该客户端可用于自动将用户预配到云或本地应用程序中。 本文概述如何使用 Azure AD 预配服务将用户预配到已启用 SCIM 的本地应用程序中。 如果要将用户预配到使用 SQL 作为数据存储的非 SCIM 本地应用程序，请参阅 [Azure AD ECMA 连接器主机通用 SQL 连接器教程](tutorial-ecma-sql-connector.md)。 如果想将用户预配到 DropBox 和 Atlassian 等云应用中，请参阅应用特定的[教程](../../active-directory/saas-apps/tutorial-list.md)。

![显示 SCIM 体系结构的关系图。](./media/on-premises-scim-provisioning/scim-4.png)

## <a name="prerequisites"></a>先决条件
- 具有 Azure AD Premium P1 或 Premium P2（或 EMS E3 或 E5）的 Azure AD 租户。 [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- 可安装代理的管理员角色。 此任务是一次性的，应该使用混合管理员或全局管理员的 Azure 帐户。 
- 可在云中配置应用程序的管理员角色（应用程序管理员、云应用程序管理员、全局管理员或具有权限的自定义角色）。

## <a name="on-premises-app-provisioning-to-scim-enabled-apps"></a>从本地应用预配到已启用 SCIM 的应用
将用户预配到已启用 SCIM 的应用：

 1. 从[库](../../active-directory/manage-apps/add-application-portal.md)中添加本地 SCIM 应用。
 1. 转到应用并选择“预配” > “下载预配代理” 。
 1. 选择“本地连接”并下载预配代理。
 1. 将代理复制到 SCIM 终结点所在的虚拟机或服务器上。
 1. 打开预配代理安装程序，同意服务条款，然后选择“安装”。
 1. 打开预配代理向导，当系统提示你指定要启用的扩展时，选择“本地预配”。
 1. 当系统提示你授权时，请提供 Azure AD 管理员的凭据。 需要混合管理员或全局管理员。
 1. 选择“确认”以确认安装成功。
 1. 返回到应用程序并选择“本地连接”。
 1. 从下拉列表中选择安装的代理，然后选择“分配代理”。
 1. 等待 10 分钟或重启服务器或 VM 上的 Azure AD Connect 预配代理服务。
 1. 在“租户 URL”框中提供 SCIM 终结点的 URL。 示例为 https://localhost:8585/scim。
 
     ![显示正在分配代理的屏幕截图。](./media/on-premises-scim-provisioning/scim-2.png)
 1. 选择“测试连接”，然后保存凭据。
 1. 配置应用程序所需的任何[属性映射](customize-application-attributes.md)或[范围](define-conditional-rules-for-provisioning-user-accounts.md)规则。
 1. 通过向应用程序[分配用户和组](../../active-directory/manage-apps/add-application-portal-assign-users.md)将用户添加到范围。
 1. [按需](provision-on-demand.md)测试预配几名用户。
 1. 将更多用户分配到应用程序以将他们添加到范围中。
 1. 转到“预配”窗格并选择“开始预配” 。
 1. 使用[预配日志](../../active-directory/reports-monitoring/concept-provisioning-logs.md)进行监视。

## <a name="additional-requirements"></a>其他需求
* 确保你的 [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) 实现符合 [Azure AD SCIM 要求](use-scim-to-provision-users-and-groups.md)。
  
  Azure AD 提供开源[参考代码](https://github.com/AzureAD/SCIMReferenceCode/wiki)，供开发人员用来启动他们的 SCIM 实现。 代码按原样提供。
* 支持 /schemaDiscovery 终结点，以减少 Azure 门户中所需的配置。 

## <a name="next-steps"></a>后续步骤

- [应用预配](user-provisioning.md)
- [Azure AD ECMA 连接器主机安装](on-premises-ecma-install.md)
- [Azure AD ECMA 连接器主机配置](on-premises-ecma-configure.md)
- [泛型 SQL 连接器](on-premises-sql-connector-configure.md)
- [教程：ECMA 连接器主机泛型 SQL 连接器](tutorial-ecma-sql-connector.md)
