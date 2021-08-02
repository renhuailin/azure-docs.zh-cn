---
title: 从 Azure AD 本地应用预配到已启用 SCIM 的应用
description: 本文介绍如何从本地应用预配到已启用 SCIM 的应用。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/28/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: bf0b13bf1e2de31d15599bff2aa8750396f021ac
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570031"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>从 Azure AD 本地应用程序预配到已启用 SCIM 的应用

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 可在[此处](https://aka.ms/onpremprovisioningpublicpreviewaccess)请求访问功能。 未来几个月在我们准备发布正式版的过程中，我们会向更多客户和连接者开放该预览版。

Azure AD 预配服务支持 [SCIM 2.0](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) 客户端，使用该客户端可以自动将用户预配到云或本地应用程序中。 本文档概述如何使用 Azure AD 预配服务将用户预配到已启用 SCIM 的本地应用程序中。 如果你希望将用户预配到非 SCIM 本地应用程序（例如非 AD LDAP 目录或 SQL DB）中，请参阅此文（我们将会创建的新文档的链接）。 如果你希望将用户预配到 DropBox、Atlassian 等云应用中，请查看应用特定的[教程](../../active-directory/saas-apps/tutorial-list.md)。 

![体系结构](./media/on-premises-scim-provisioning/scim-4.png)


## <a name="pre-requisites"></a>先决条件
- 具有 Azure AD Premium P1 或 Premium P2（或 EMS E3 或 E5）的 Azure AD 租户。 
    [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- 可安装代理的管理员角色。  这是一次性的工作，应该使用混合管理员或全局管理员的 Azure 帐户。 
- 可在云中配置应用程序的管理员角色（应用程序管理员、云应用程序管理员、全局管理员、具有权限的自定义角色）

## <a name="steps-for-on-premises-app-provisioning-to-scim-enabled-apps"></a>将本地应用预配到已启用 SCIM 的应用的步骤
使用以下步骤预配到已启用 SCIM 的应用。 

 1. 从[库](../../active-directory/manage-apps/add-application-portal.md)中添加“基于代理的 SCIM 预配”应用。
 2. 导航到你的应用 >“预配”>“下载预配代理”。
 3. 单击本地连接并下载预配代理。
 4. 将代理复制到 SCIM 终结点所在的虚拟机或服务器上。
 5. 打开预配代理安装程序，同意服务条款，然后单击“安装”。
 6. 当系统提示你指定要启用的扩展时，请打开预配代理向导并选择本地预配。
 7. 当系统提示你授权时，请提供 Azure AD 管理员（需是混合管理员或全局管理员）的凭据。
 8. 单击“确认”以确认安装成功。
 9. 导航回到你的应用程序 > 本地连接。
 10. 从下拉列表中选择安装的代理，然后单击“分配代理”。
 11. 等待 10 分钟或重启服务器/VM 上的 Azure AD Connect 预配代理服务。
 12. 在租户 URL 字段中提供 SCIM 终结点的 URL (例如 Https://localhost:8585/scim)。
     ![分配代理](./media/on-premises-scim-provisioning/scim-2.png)
 13. 单击“测试连接”并保存凭据。
 14. 配置应用程序所需的任何[属性映射](customize-application-attributes.md)或[范围](define-conditional-rules-for-provisioning-user-accounts.md)规则。  
 15. 通过向应用程序[分配用户和组](../../active-directory/manage-apps/add-application-portal-assign-users.md)将用户添加到范围。
 16. [按需](provision-on-demand.md)测试预配几个用户。
 17. 将其他用户分配到应用程序，以将其添加到范围。
 18. 导航到预配边栏选项卡并点击“开始预配”。
 19. 使用[预配日志](../../active-directory/reports-monitoring/concept-provisioning-logs.md)进行监视。
 

## <a name="things-to-be-aware-of"></a>注意事项
* 确保你的 [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) 实现符合 [Azure AD SCIM 要求](use-scim-to-provision-users-and-groups.md)。
  * Azure AD 提供开源[参考代码](https://github.com/AzureAD/SCIMReferenceCode/wiki)，供开发人员用来启动其 SCIM 实现（代码按原样提供）
* 支持 /schemaDiscovery 终结点，以减少 Azure 门户中所需的配置。 

## <a name="next-steps"></a>后续步骤

- [应用预配](user-provisioning.md)
- [Azure AD ECMA 连接器主机安装](on-premises-ecma-install.md)
- [Azure AD ECMA 连接器主机配置](on-premises-ecma-configure.md)
- [泛型 SQL 连接器](on-premises-sql-connector-configure.md)
- [教程：ECMA 连接器主机泛型 SQL 连接器](tutorial-ecma-sql-connector.md)
