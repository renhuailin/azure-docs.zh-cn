---
title: Azure Active Directory 中自定义角色的应用权限 | Microsoft Docs
description: 在 Azure 门户、PowerShell 或图形 API 中预览自定义 Azure AD 角色的企业应用权限。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 08/06/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: b0a8b3abf4e119096959b640be4b44051a198916
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736147"
---
# <a name="enterprise-application-permissions-for-custom-roles-in-azure-active-directory"></a>Azure Active Directory 中自定义角色的企业应用程序权限

本文包含适用于 Azure Active Directory (Azure AD) 中的自定义角色定义的当前可用的企业应用程序权限。 在本文中，你将找到一些常见方案的权限列表以及企业应用程序权限的完整列表。

## <a name="license-requirements"></a>许可要求

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="enterprise-application-permissions"></a>企业应用程序权限

有关如何使用这些权限的详细信息，请参阅[分配自定义角色来管理企业应用](custom-enterprise-apps.md)

#### <a name="assigning-users-or-groups-to-an-application"></a>将用户或组分配到应用程序

委托可以访问基于 SAML 的单一登录应用程序的用户和组的分配。 所需的权限

- microsoft.directory/servicePrincipals/appRoleAssignedTo/update

#### <a name="creating-gallery-applications"></a>创建库应用程序

委托创建 Azure AD 库应用程序，例如 ServiceNow、F5、Salesforce 等。 所需的权限：

- microsoft.directory/applicationTemplates/instantiate

#### <a name="configuring-basic-saml-urls"></a>配置基本 SAML URL

委托对基于 SAML 的单一登录应用程序的基本 SAML 配置的更新和读取。 所需的权限：

- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/applications.myOrganization/authentication/update

#### <a name="rolling-over-or-creating-signing-certs"></a>滚动或创建签名证书

委托对基于 SAML 的单一登录应用程序的签名证书管理。 所需的权限。

microsoft.directory/applications/credentials/update

#### <a name="update-expiring-sign-in-cert-notification-email-address"></a>更新即将到期的登录证书通知电子邮件地址

委托更新基于 SAML 的单一登录应用程序即将到期的登录证书通知电子邮件地址。 所需的权限：

- microsoft.directory/applications.myOrganization/authentication/update
- microsoft.directory/applications.myOrganization/permissions/update
- microsoft.directory/servicePrincipals/authentication/update
- microsoft.directory/servicePrincipals/basic/update

#### <a name="manage-saml-token-signature-and-sign-in-algorithm"></a>管理 SAML 令牌签名和登录算法

委托更新基于 SAML 的单一登录应用程序的 SAML 令牌签名和登录算法。 所需的权限：

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

#### <a name="manage-user-attributes-and-claims"></a>管理用户特性和声明

委托对基于 SAML 的单一登录应用程序的用户特性和声明的创建、删除和更新。 所需的权限：

- microsoft.directory/applicationPolicies/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/servicePrincipals/policies/update

## <a name="app-provisioning-permissions"></a>应用预配权限

执行任何写入操作（例如通过 UI 管理作业、架构或凭据）也需要读取权限才能查看预配页。

若要将范围设置为所有用户和组或已分配的用户和组，目前需要 synchronizationJob 和 synchronizationCredentials 权限。

#### <a name="turn-on-or-restart-provisioning-jobs"></a>启用或重启预配作业

委派打开、关闭和重启资源预配作业的功能。 所需的权限：

- microsoft.directory/servicePrincipals/synchronizationJobs/manage  

#### <a name="configure-the-provisioning-schema"></a>配置预配架构  

委托对属性映射的更新。 所需的权限：

- microsoft.directory/servicePrincipals/synchronizationSchema/manage  

#### <a name="read-provisioning-settings-associated-with-the-application-object"></a>读取与应用程序对象关联的预配设置

委托读取与对象关联的预配设置的能力。 所需的权限：

- microsoft.directory/applications/synchronization/standard/read

#### <a name="read-provisioning-settings-associated-with-your-service-principal"></a>读取与服务主体关联的预配设置

委托读取与服务主体关联的预配设置的能力。 所需的权限：

- microsoft.directory/servicePrincipals/synchronization/standard/read

#### <a name="authorize-application-access-for-provisioning"></a>授权应用程序访问以进行预配  

委派授权应用程序访问以进行预配的能力。 输入 Oauth 持有者令牌示例。 所需的权限：

- microsoft.directory/servicePrincipals/synchronizationCredentials/manage

## <a name="application-proxy-permissions"></a>应用程序代理权限

对应用程序的应用程序代理属性执行任何写入操作还需要更新应用程序基本属性和身份验证的权限。

对应用程序的应用程序代理属性进行读取和执行任何写入操作还需要具有查看连接器组的读取权限，因为这是页面上显示的属性列表的一部分。

#### <a name="delegate-application-proxy--connector-management"></a>委托应用程序代理连接器管理

针对连接器管理委托创建、读取、更新和删除操作。 所需的权限：

- microsoft.directory/connectorGroups/allProperties/read
- microsoft.directory/connectorGroups/allProperties/update
- microsoft.directory/connectorGroups/create
- microsoft.directory/connectorGroups/delete
- microsoft.directory/connectors/allProperties/read
- microsoft.directory/connectors/create


#### <a name="delegate-application-proxy-settings-management"></a>委托应用程序代理设置管理

针对应用中的应用程序代理属性委托创建、读取、更新和删除操作。 所需的权限：

- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/applications/applicationProxy/update 
- microsoft.directory/applications/applicationProxyAuthentication/update 
- microsoft.directory/applications/applicationProxySslCertificate/update 
- microsoft.directory/applications/applicationProxyUrlSettings/update 
- microsoft.directory/applications/basic/update
- microsoft.directory/applications/authentication/update
- microsoft.directory/connectorGroups/allProperties/read

#### <a name="read-application-proxy-settings-for-an-app"></a>读取应用的应用程序代理设置

针对应用中的应用程序代理属性委托读取权限。 所需的权限：
 
- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/connectorGroups/allProperties/read 

#### <a name="update-url-configuration-application-proxy-settings-for-an-app"></a>更新应用的 URL 应用程序代理设置 

委托创建、读取、更新和删除 (CRUD) 权限，以便更新应用程序代理外部 URL、内部 URL 和 SSL 证书属性。 所需的权限： 

- microsoft.directory/applications/applicationProxy/read 
- microsoft.directory/connectorGroups/allProperties/read 
- microsoft.directory/applications/basic/update 
- microsoft.directory/applications/authentication/update
- microsoft.directory/applications/applicationProxyAuthentication/update 
- microsoft.directory/applications/applicationProxySslCertificate/update 
- microsoft.directory/applications/applicationProxyUrlSettings/update

## <a name="full-list-of-permissions"></a>权限的完整列表

> [!div class="mx-tableFixed"]
> | 权限 | 说明 |
> | ---------- | ----------- |
> | microsoft.directory/applicationPolicies/allProperties/read | 读取应用程序策略的所有属性 |
> | microsoft.directory/applicationPolicies/allProperties/update | 更新应用程序策略的所有属性 |
> | microsoft.directory/applicationPolicies/basic/update | 更新应用程序策略的标准属性 |
> | microsoft.directory/applicationPolicies/create | 创建应用程序策略 |
> | microsoft.directory/applicationPolicies/createAsOwner | 创建应用程序策略。 添加“创建者”作为第一个所有者 |
> | microsoft.directory/applicationPolicies/delete | 删除应用程序策略 |
> | microsoft.directory/applicationPolicies/owners/read | 读取应用程序策略的所有者 |
> | microsoft.directory/applicationPolicies/owners/update | 更新应用程序策略的所有者属性 |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | 读取应用于对象列表的应用程序策略 |
> | microsoft.directory/applicationPolicies/standard/read | 读取应用程序策略的标准属性 |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | 创建和删除 servicePrincipals，然后读取和更新 Azure Active Directory 中的所有属性 |
> | microsoft.directory/servicePrincipals/allProperties/read | 读取 servicePrincipals 的所有属性 |
> | microsoft.directory/servicePrincipals/allProperties/update | 更新 servicePrincipals 的所有属性 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | 读取服务主体角色分配 |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | 更新服务主体角色分配 |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | 读取分配给服务主体的角色分配 |
> | microsoft.directory/servicePrincipals/audience/update | 更新服务主体的受众属性 |
> | microsoft.directory/servicePrincipals/authentication/update | 更新服务主体的身份验证属性 |
> | microsoft.directory/servicePrincipals/basic/update | 更新服务主体的基本属性 |
> | microsoft.directory/servicePrincipals/create | 创建服务主体 |
> | microsoft.directory/servicePrincipals/createAsOwner | 创建服务主体。 添加“创建者”作为第一个所有者 |
> | microsoft.directory/servicePrincipals/credentials/update | 更新服务主体的凭据属性 |
> | microsoft.directory/servicePrincipals/delete | 删除服务主体 |
> | microsoft.directory/servicePrincipals/disable | 禁用服务主体 |
> | microsoft.directory/servicePrincipals/enable | 启用服务主体 |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | 读取服务主体的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | 管理服务主体的密码单一登录凭据 |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | 读取服务主体的委托权限授予 |
> | microsoft.directory/servicePrincipals/owners/read | 读取服务主体的所有者 |
> | microsoft.directory/servicePrincipals/owners/update | 更新服务主体的所有者 |
> | microsoft.directory/servicePrincipals/permissions/update | 更新服务主体的权限 |
> | microsoft.directory/servicePrincipals/policies/read | 读取服务主体的策略 |
> | microsoft.directory/servicePrincipals/policies/update | 更新服务主体的策略 |
> | microsoft.directory/servicePrincipals/standard/read | 读取服务主体的标准属性 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/servicePrincipals/tag/update | 更新服务主体的标记属性 |
> | microsoft.directory/applicationTemplates/instantiate | 从应用程序模板实例化库应用程序 |
> | microsoft.directory/auditLogs/allProperties/read | 读取审核日志 |
> | microsoft.directory/signInReports/allProperties/read | 读取登录报表 |
> | microsoft.directory/applications/applicationProxy/read | 读取所有类型应用程序的所有应用程序代理属性 |
> | microsoft.directory/applications/applicationProxy/update | 更新所有类型应用程序的所有应用程序代理属性 |
> | microsoft.directory/applications/applicationProxyAuthentication/update | 更新所有类型应用程序的应用程序代理身份验证属性 |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | 更新所有类型应用程序的应用程序代理内部和外部 URL |
> | microsoft.directory/applications/applicationProxySslCertificate/update | 更新所有类型应用程序的应用程序代理自定义域 |
> | microsoft.directory/applications/synchronization/standard/read | 读取与应用程序对象关联的预配设置 |
> | microsoft.directory/connectorGroups/create | 创建应用程序代理连接器组 |
> | microsoft.directory/connectorGroups/delete | 删除应用程序代理连接器组 |
> | microsoft.directory/connectorGroups/allProperties/read | 读取应用程序代理连接器组的所有属性 |
> | microsoft.directory/connectorGroups/allProperties/update | 更新应用程序代理连接器组的所有属性 |
> | microsoft.directory/connectors/create | 创建应用程序代理连接器 |
> | microsoft.directory/connectors/allProperties/read | 读取应用程序代理连接器的所有属性 |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | 管理服务主体资源的作业同步的所有方面 |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | 读取与服务主体关联的预配设置 |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | 管理服务主体资源的架构同步的所有方面 |
> | microsoft.directory/provisioningLogs/allProperties/read | 读取预配日志的所有属性 |

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户、Azure AD PowerShell 或图形 API 创建自定义角色](custom-create.md)
- [列出角色分配](view-assignments.md)
