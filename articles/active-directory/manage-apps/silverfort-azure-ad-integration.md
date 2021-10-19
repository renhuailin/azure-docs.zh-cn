---
title: 使用 Azure AD 和 Silverfort 实现安全的混合访问
description: 本教程介绍如何将 Silverfort 与 Azure AD 集成以实现安全的混合访问
titleSuffix: Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/13/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1515bfe65a9a680a9331a5a4d2b13e4d3633fda0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658246"
---
# <a name="tutorial-configure-silverfort-with-azure-active-directory-for-secure-hybrid-access"></a>教程：为 Silverfort 配置 Azure Active Directory 以实现安全的混合访问

在本教程中，你将学习如何将 Silverfort 与 Azure Active Directory (Azure AD) 集成。 [Silverfort](https://www.silverfort.com/) 使用新颖的无代理技术将所有本地资产和云中的资产连接到 Azure AD。 此解决方案使组织能够在 Azure AD 中的所有环境中应用身份保护、可见性和用户体验。 此解决方案支持对本地和云环境的身份验证活动进行基于风险的普遍监控和评估，并主动预防威胁。  

Silverfort 可以将任何类型的资产无缝连接到 Azure AD，就像它是新式 Web 应用程序一样。 例如：

- 旧版和自主开发应用程序

- 远程桌面和安全外壳 (SSH)

- 命令行工具和其他管理员访问权限

- 文件共享和数据库

- 基础结构和工业系统

这些桥接资产在 Azure AD 中显示为常规应用程序，可以通过条件访问、单点登录 (SSO)、多重身份验证、审核等手段进行保护。

此解决方案结合了所有公司资产和第三方标识和访问管理 (IAM) 平台。 例如，Active Directory、Active Directory 联合身份验证服务 (ADFS)、Azure AD 上的远程身份验证拨入用户服务 (RADIUS)，包括混合环境和多云环境。

## <a name="scenario-description"></a>方案描述

在本指南中，你将在 Azure AD 租户中配置并测试 Silverfort Azure AD 网桥。

配置后，可以创建 Silverfort 的身份验证策略，以便将来自各种标识源的身份验证请求桥接到 Azure AD，从而实现 SSO。 在应用程序被桥接后，便可以在 Azure AD 中进行管理。

下图显示了解决方案中包含的组件以及由 Silverfort 协调的身份验证顺序。

![图像显示了体系结构图](./media/silverfort-azure-ad-integration/silverfort-architecture-diagram.png)

| 步骤 | 说明|
|:---------|:------------|
| 1. | 用户通过 Kerberos、SAML、NTLM、OIDC 和 LDAP 等协议向原始标识提供者 (IdP) 发送身份验证请求。|
| 2. | 响应按原样路由到 Silverfort 以进行验证以检查身份验证状态。|
| 3. | Silverfort 提供了可见性、发现以及桥接到 Azure AD。|
| 4. | 如果应用程序配置为“桥接”，则身份验证决策将传递到 Azure AD。 Azure AD 评估条件访问策略并验证身份验证。|
| 5. | 然后，Silverfort 将发布身份验证状态响应并按原样发送给 IdP。 |
| 6.| IdP 授予或拒绝对资源的访问权限。|
| 7. | 将通知用户是授予还是拒绝了访问权限。 |

## <a name="prerequisites"></a>先决条件

若要为添加到 Azure AD 租户的应用程序设置 SSO，你需要：

- 具有活动订阅的 Azure 帐户。 可以创建[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- Azure 帐户中的以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。

- 支持 SSO 且已预配置并添加到 Azure AD 库的应用程序。 Azure AD 库中的 Silverfort 应用程序已预先配置。 需要从库中将其添加为企业应用程序。

## <a name="onboard-with-silverfort"></a>内置 Silverfort

若要在租户或基础结构中部署 Silverfort，请[联系 Silverfort](https://www.silverfort.com/)。 在相关工作站上安装 Silverfort 桌面应用。

## <a name="configure-silverfort-and-create-a-policy"></a>配置 Silverfort 并创建策略

1. 在浏览器中，登录到 Silverfort 管理控制台。

2. 在主菜单中，导航到“设置”，然后在“常规”部分中滚动到“Azure AD 网桥连接器”。  确认你的租户 ID，然后选择“授权”。

   ![该插图显示 Azure AD 网桥连接器](./media/silverfort-azure-ad-integration/azure-ad-bridge-connector.png)

   ![该插图显示注册确认](./media/silverfort-azure-ad-integration/grant-permission.png)

3. 注册确认在新标签页中显示。关闭此标签页。

   ![该插图显示注册已完成](./media/silverfort-azure-ad-integration/registration-completed.png)

4. 在“设置”页中，选择“保存更改” 

   ![该插图显示 Azure AD 适配器](./media/silverfort-azure-ad-integration/silverfort-azure-ad-adapter.png)

    登录到你的 Azure AD 控制台。 你将看到 Silverfort Azure AD 适配器应用程序注册为企业应用程序。

   ![该插图显示企业应用程序](./media/silverfort-azure-ad-integration/enterprise-application.png)

5. 在 Silverfot 管理控制台中，导航到“策略”页，然后选择“创建策略”。 

6. 将显示“新建策略”对话框。 输入策略名称，这指示将在 Azure 中创建的应用程序名称。 例如，如果要在该策略下添加多个服务器或应用程序，请对其进行命名以反映该策略所涵盖的资源。 在此示例中，我们将为 SL-APP1 服务器创建一个策略。

   ![该插图显示定义策略](./media/silverfort-azure-ad-integration/define-policy.png)

7. 选择适当的身份验证类型和协议。 

8. 在“用户和组”字段中，选择“编辑”图标以配置将受策略影响的用户。 这些用户的身份验证将桥接到 Azure AD。

   ![该插图显示用户和组](./media/silverfort-azure-ad-integration/user-groups.png)

9. 搜索和选择用户、组或组织单位 (OU)。

   ![该插图显示搜索用户](./media/silverfort-azure-ad-integration/search-users.png)

   选定的用户将在“已选定”框中列出。

   ![该插图显示选定用户](./media/silverfort-azure-ad-integration/select-user.png)

10. 选择将应用策略的源。 在此示例中，选择了“所有设备”。

    ![该插图显示源](./media/silverfort-azure-ad-integration/source.png)

11. 将“目标”设置为“SL-App1”。 可以选择“编辑”按钮来更改或添加更多资源或资源组（可选）。

    ![该插图显示目标](./media/silverfort-azure-ad-integration/destination.png)

12. 选择要对 AZURE AD 网桥执行的操作。

    ![该插图显示保持 Azure AD 网桥](./media/silverfort-azure-ad-integration/save-azure-ad-bridge.png)

13. 选择“保存”，以保存新策略。 系统将提示你启用或激活新策略。

    ![该插图显示更改状态](./media/silverfort-azure-ad-integration/change-status.png)

    该策略将出现在“策略”页面的“Azure AD 网桥”部分中：

    ![该插图显示添加策略](./media/silverfort-azure-ad-integration/add-policy.png)

14. 返回到 Azure AD 控制台，并导航到“企业应用程序”。 现在应该会显示新的 Silverfort 应用程序。 此应用程序现在可以包含在 [CA 策略](../authentication/tutorial-enable-azure-mfa.md?bc=/azure/active-directory/conditional-access/breadcrumb/toc.json&toc=/azure/active-directory/conditional-access/toc.json%23create-a-conditional-access-policy)中。

## <a name="next-steps"></a>后续步骤

- [Silverfort Azure AD 适配器](https://azuremarketplace.microsoft.com/marketplace/apps/aad.silverfortazureadadapter?tab=overview)

- [Silverfort 资源](https://www.silverfort.com/resources/)
