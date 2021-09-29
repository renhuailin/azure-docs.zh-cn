---
title: 教程：将 Azure Active Directory 与 Amazon Web Services 集成以连接多个帐户 | Microsoft Docs
description: 了解如何在 Azure AD 与 Amazon Web Services 之间配置单一登录（旧教程）。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 6a56116ef50260c451564e571679a28a741584d8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831081"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>教程：Azure Active Directory 与 Amazon Web Services 集成

本教程介绍如何将 Azure Active Directory (Azure AD) 与 Amazon Web Services (AWS) 集成（旧教程）。

这种集成具有以下优势：

- 可在 Azure AD 中控制谁有权限访问 AWS。
- 可以让用户使用其 Azure AD 帐户通过单一登录 (SSO) 自动登录到 AWS。
- 可在一个中心位置（即 Azure 门户）管理帐户。

![Azure AD 与 AWS 集成示意图。](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> 我们不建议使用一个 AWS 应用连接所有 AWS 帐户。 相反，建议使用 [Azure AD SSO 与 AWS 集成](./amazon-web-service-tutorial.md)将 AWS 帐户的多个实例配置为 Azure AD 中 AWS 应用的多个实例。

我们不建议使用一个 AWS 应用连接所有 AWS 帐户，原因如下：

- 此模型不会随 AWS 帐户及其角色的数量的增加而扩展，因此应仅在具有少量 AWS 帐户和角色的情况下才使用此方法。 此方法不会将 AWS 角色导入功能与 Azure AD 用户预配一起使用，因此必须手动添加、更新或删除这些角色。

- 需要使用 Microsoft Graph 浏览器方法来修补分配给应用的所有角色。 不建议使用清单文件方法。

- 客户反映在为单个 AWS 应用添加约 1200 个应用角色后，在应用上执行任何进一步的操作都会开始引发与大小相关的错误。 应用程序对象的大小有硬性限制。

- 在任一帐户中添加角色时，必须手动更新角色。 遗憾的是，这是一种“替换”方法而非“附加”方法 。 另外，如果帐户数量不断增长，这就变成了帐户和角色的 n &times; n 的关系 。

- 所有 AWS 帐户都使用相同的联合元数据 XML 文件。 证书滚动更新时，同时更新所有 AWS 帐户上的证书是一项大规模操作。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 AWS 的集成，需要以下项：

- 一个 Azure AD 订阅。 如果没有 Azure AD 订阅，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。
- 已启用 AWS SSO 的订阅。

> [!NOTE]
> 如非必要，不建议使用生产环境测试本教程中的步骤。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

AWS 支持 SP 和 IdP 发起的 SSO。

## <a name="add-aws-from-the-gallery"></a>从库中添加 AWS

若要配置 AWS 与 Azure AD 的集成，需要从库中将 AWS 添加到托管软件即服务 (SaaS) 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 Azure 门户。
1. 在左窗格上，选择想要使用的 Azure AD 服务。
1. 转到“企业应用程序”，并选择“所有应用程序”。  
1. 若要添加应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入 Amazon Web Services 。
1. 在结果列表中选择“Amazon Web Services”，然后添加该应用。 几秒钟后，该应用将添加到租户。

1. 转到“属性”窗格，然后复制“对象 ID”框中显示的值 。

   ![“属性”窗格上“对象 ID”框的屏幕截图。](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO

本部分将基于名为“Britta Simon”的测试用户配置并测试 AWS 的 Azure AD 单一登录。

若要使单一登录正常运行，Azure AD 需要知道与 Azure AD 用户相对应的 AWS 用户。 换句话说，必须在 Azure AD 用户与 AWS 中的相关用户之间建立链接关系。

在 AWS 中，将 Azure AD 中“用户名”的值指定为 AWS“用户名”的值来建立此链接关系 。

若要配置并测试 AWS 的 Azure AD 单一登录，请执行以下步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
1. [配置 AWS](#configure-aws-sso)，以便在应用程序端配置 SSO 设置。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分，我们将执行以下操作，在 Azure 门户中启用 Azure AD SSO，并在 AWS 应用程序中配置 SSO：

1. 在 Azure 门户的 Amazon Web Services (AWS) 应用程序集成页的左窗格上，选择“单一登录” 。

   ![“单一登录”命令的屏幕截图。](common/select-sso.png)

1. 在“选择单一登录方法”窗格中，选择“SAML/WS-Fed”模式以启用单一登录   。

   ![“选择单一登录方法”窗格的屏幕截图。](common/select-saml-option.png)

1. 在“设置 SAML 单一登录”窗格上，选择“编辑”按钮（铅笔图标） 。

   ![“设置 SAML 单一登录”窗格上“编辑”按钮的屏幕截图。](common/edit-urls.png)

1. 此时将打开“基本 SAML 配置”窗格。 跳过此部分，因为该应用已与 Azure 预先集成。 选择“保存”。

   AWS 应用程序需要特定格式的 SAML 断言。 可以在“应用程序集成”页的“用户属性和声明”部分管理这些属性的值 。

1. 在“设置 SAML 单一登录”窗格上，选择“编辑”按钮 。

   ![“用户属性”窗格上“编辑”按钮的屏幕截图。](common/edit-attribute.png)

1. 在“用户属性”窗格的“用户声明”部分，使用下表中的值配置 SAML 令牌属性 ：

   | 名称            | 源属性                                                            | 命名空间                                |
   | --------------- | --------------------------------------------------------------------------- | ---------------------------------------- |
   | RoleSessionName | user.userprincipalname                                                      | `https://aws.amazon.com/SAML/Attributes` |
   | 角色            | user.assignedroles                                                          | `https://aws.amazon.com/SAML/Attributes` |
   | SessionDuration | “提供介于 900 秒（15 分钟）到 43200 秒（12 小时）之间的值” | `https://aws.amazon.com/SAML/Attributes` |

   a. 选择“添加新声明”，然后在“管理用户声明”窗格，执行以下操作 ：

   ![“用户声明”窗格上“添加新声明”和“保存”按钮的屏幕截图。](common/new-save-attribute.png)

   ![“管理用户声明”窗格的屏幕截图。](common/new-attribute-details.png)

   b. 在“名称”框中，输入属性名称。

   c. 在“命名空间”框中，输入命名空间值。

   d. 对于“源”，请选择“属性”。  

   e. 在“源属性”下拉列表中，选择属性。

   f. 选择“确定”，然后选择“保存” 。

   > [!NOTE]
   > 有关 Azure AD 中的角色的更多信息，请参阅[在应用程序中添加应用角色并在令牌中接收它们](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以下载联合元数据 XML 文件并将其保存在计算机上  。

   ![“SAML 签名证书”窗格上“联合元数据 XML 下载”链接的屏幕截图。](common/metadataxml.png)

### <a name="configure-aws-sso"></a>配置 AWS SSO

1. 在新的浏览器窗口中，以管理员身份登录到 AWS 公司站点。

1. 选择“AWS 主页”图标。

   ![“AWS 主页”图标的屏幕截图。][11]

1. 在“AWS 服务”窗格上的“安全性、标识和合规性”下，选择“IAM(标识与访问管理)”  。

   ![“AWS 服务”窗格上“标识与访问管理”链接的屏幕截图。][12]

1. 在左窗格上，选择“标识提供者”，然后选择“创建提供者” 。

   ![“创建提供者”按钮的屏幕截图。][13]

1. 在“配置提供者”窗格上执行以下步骤：

   ![“配置提供者”窗格的屏幕截图。][14]

   a. 在“提供者类型”下拉列表中，选择“SAML” 。

   b. 在“提供者名称”框中，输入提供者名称（例如： _WAAD_）。

   c. 在“元数据文档”框旁，选择“选择文件”，将下载的联合元数据 XML 文件上传到 Azure 门户 。

   d. 选择“下一步”。

1. 在“验证提供者信息”窗格上，选择“创建” 。

   ![“验证提供者信息”窗格的屏幕截图。][15]

1. 在左窗格上，依次选择“角色”、“创建角色” 。

   ![“角色”窗格上“创建角色”按钮的屏幕截图。][16]

   > [!NOTE]
   > 要导入的角色的角色 Amazon 资源名称 (ARN) 和 SAML 提供程序 ARN 的组合长度不得超过 240 个字符。

1. 在“创建角色”页上，执行以下步骤：

   ![“创建角色”页上“SAML 2.0 联合”受信任实体按钮的屏幕截图。][19]

   a. 在“选择可信实体的类型”下选择“SAML 2.0 联合身份验证”。 

   b. 在“选择 SAML 2.0 提供程序”下，选择前面创建的 SAML 提供程序（例如 WAAD）。

   c. 选择“允许以编程方式和通过 AWS 管理控制台进行访问”。

   d. 在完成时选择“下一步:权限”。

1. 在搜索框中，输入“管理员访问”，选择“AdministratorAccess”复选框，然后选择“下一页: 标签”  

   ![“策略名称”列表的屏幕截图，其中已选中“AdministratorAccess 策略”。](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. 在“添加标签(可选)”窗格中，执行以下操作：

   ![“添加标签(可选)”窗格的屏幕截图。](./media/aws-multi-accounts-tutorial/config2.png)

   a. 在“密钥”框中，输入密钥名称（例如 Azureadtest）。

   b. 在“值(可选)”框中，按照 `<accountname-aws-admin>` 格式输入密钥值。 帐户名应全部小写。

   c. 在完成时选择“下一步:  查看”。

1. 在“查看”窗格中，执行以下操作：

   ![“查看”窗格的屏幕截图，其中已突出显示“角色名称”和“角色描述”框。][34]

   a. 在“角色名称”框中，按 `<accountname-aws-admin>` 格式输入值。

   b. 在“角色描述”框中，输入角色名称使用的值。

   c. 选择“创建角色”。

   d. 创建所需数量的角色，并将其映射到标识提供者。

   > [!NOTE]
   > 同样，请创建其他角色，例如 accountname-finance-admin、accountname-read-only-user、accountname-devops-user 或 accountname-tpm-user，每个角色附加不同的策略   。 根据每个 AWS 帐户的要求，可稍后更改这些角色策略。 建议为 AWS 帐户中的每个角色保留相同的策略。

1. 请记下 AWS 帐户的帐户 ID，此信息位于 Amazon Elastic Compute Cloud (Amazon EC2) 属性窗格或 IAM 仪表板，如下面的屏幕截图所示：

   ![“标识和访问管理”窗格上显示帐户 ID 的位置的屏幕截图。](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. 登录 Azure 门户，然后转到“组”。

1. 创建与之前所创建的 IAM 角色同名的新组，然后记下这些新组的“对象 ID”框中的值。

   ![新组的帐户详细信息的屏幕截图。](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. 从当前 AWS 帐户注销，然后使用要在其中配置 Azure AD SSO 的另一个帐户登录。

1. 在帐户中创建所有角色后，这些角色将显示在这些帐户的“角色”列表中。

   ![角色列表的屏幕截图，其中包含每个角色的名称、说明和受信任的实体。](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

接下来需要为所有帐户中的所有角色捕获所有角色 ARN 和受信任的实体。 还需要使用 Azure AD 应用程序手动对这些对象进行映射。 为此，请执行以下操作：

1. 选择每个角色，复制其角色 ARN 和受信任的实体值。 对于要在 Azure AD 中创建的所有角色，需要使用这些值。

   ![角色 ARN 和受信任实体的“摘要”窗格的屏幕截图。](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. 对于所有帐户中的所有角色，重复上一步操作，然后按 `<Role ARN>,<Trusted entities>` 格式将其存储在文本文件中。

1. 打开 [Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)，然后执行以下操作：

   a. 使用租户的全局管理员或共同管理员凭据登录到 Microsoft Graph 浏览器站点。

   b. 需要足够的权限才能创建角色。 选择“修改权限”。

   ![“Microsoft Graph 浏览器身份验证”窗格上“修改权限”链接的屏幕截图。](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

   c. 在权限列表中，如果已具有权限（如以下屏幕截图所示），选择每个权限，然后选择“修改权限”。

   ![Microsoft Graph 浏览器权限列表的屏幕截图，其中已突出显示适当权限。](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

   d. 再次登录到 Graph 浏览器，接受该站点的使用条件。

   e. 在窗格顶部，选择 GET 方法，再选择 beta 版本，然后在查询框中，输入以下任一内容 ：

   - 若要从租户提取所有服务主体，请使用 `https://graph.microsoft.com/beta/servicePrincipals`。
   - 如果要使用多个目录，请使用 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`该查询包含主域。

   ![Microsoft Graph 浏览器查询“请求正文”窗格的屏幕截图。](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

   f. 从服务主体的列表中，获取需要修改的那一个。

   还可使用 Ctrl+F 从列出的所有服务主体中搜索应用程序。 若要获取特定服务主体，请在查询中包括服务主体对象 ID，即你之前从“Azure AD 属性”窗格复制的信息，如此处所示：

   `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

   ![显示包括对象 ID 的服务主体查询的屏幕截图。](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

   g. 从服务主体对象中提取 appRoles 属性。

   ![用于从服务主体对象中提取 appRoles 属性的代码的屏幕截图](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

   h. 现在需要为应用程序生成新角色。

   i. 以下 JSON 代码是 appRoles 对象的示例。 创建类似的对象，以添加应用程序所需的角色。

   ```
   {
   "appRoles": [
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "msiam_access",
           "displayName": "msiam_access",
           "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
           "isEnabled": true,
           "origin": "Application",
           "value": null
       },
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Admin,WAAD",
           "displayName": "Admin,WAAD",
           "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
           "isEnabled": true,
           "origin": "ServicePrincipal",
           "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
       },
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Auditors,WAAD",
           "displayName": "Auditors,WAAD",
           "id": "bcad6926-67ec-445a-80f8-578032504c09",
           "isEnabled": true,
           "origin": "ServicePrincipal",
           "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
       }    ]
   }
   ```

   > [!Note]
   > 对于修补操作，只能在已添加 msiam_access 之后添加新角色。 还可根据组织的需要添加任意数量的角色。 Azure AD 会在 SAML 响应中将这些角色的值作为声明值发送。

   j. 在 Microsoft Graph 浏览器中，将方法从 GET 更改为 PATCH 。 通过更新 appRoles 属性（类似于前述示例中所示的属性）来修补服务主体对象以获取所需的角色。 选择“运行查询”以执行此修补操作。 随后会显示一条成功消息，确认已创建 AWS 应用程序的角色。

   ![Microsoft Graph 浏览器窗格的屏幕截图，其中方法已更改为 PATCH。](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. 使用更多角色修补服务主体后，可将用户和组分配到相应的角色。 可在 Azure 门户中执行此操作，方法是转到 AWS 应用程序，然后在顶部选择“用户和组”选项卡。

1. 我们建议为每个 AWS 角色创建新组，以便可以在该组中分配该特定角色。 这种一对一映射意味着每个角色分配一个组。 然后，可以添加属于该组的成员。

1. 创建组后，请选择该组并将其分配到应用程序。

   ![“用户和组”窗格的屏幕截图。](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

   > [!Note]
   > 分配组时不支持嵌套组。

1. 若要向组分配角色，请选择角色，然后选择“分配”。

   ![“添加分配”窗格的屏幕截图。](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

   > [!Note]
   > 分配角色后，可通过刷新 Azure 门户会话查看这些角色。

### <a name="test-sso"></a>测试 SSO

本部分将使用 Microsoft“我的应用”测试 Azure AD 单一登录配置。

在“我的应用”中选择 AWS 磁贴后，将打开 AWS 应用程序页面，可在其中选择角色。

![AWS 页面测试 SSO 的屏幕截图。](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

此外，还可以验证 SAML 响应，以查看角色是否作为声明传递。

![SAML 响应的屏幕截图。](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

有关“我的应用”的详细信息，请参阅[在“我的应用”门户中登录和启动应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。

## <a name="next-steps"></a>后续步骤

配置 AWS 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 有关详细信息，请参阅[了解如何通过 Microsoft Cloud App Security 强制实施会话控制](/cloud-app-security/proxy-deployment-aad)。

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/