---
title: Azure Active Directory B2C 配置 N8 标识的教程
titleSuffix: Azure AD B2C
description: 本教程介绍如何将 TheAccessHub 管理员工具与 Azure Active Directory B2C 进行配置，以解决) 管理 (CSR 的客户帐户迁移和客户服务请求。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 337275cef0f2159cb5fac40ac0435408baf3bbef
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170916"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置 TheAccessHub 管理工具的教程

在此示例教程中，我们提供了有关如何将 Azure Active Directory (AD) B2C 与 N8 标识中的 [TheAccessHub 管理工具](https://n8id.com/products/theaccesshub-admintool/) 集成的指导。 此解决方案解决 (CSR) 管理的客户帐户迁移和客户服务请求。  

如果有以下一个或多个需要，则此解决方案适用于你：

- 你已有一个网站，并且想要迁移到 Azure AD B2C。 但是，你的客户帐户的迁移（包括密码）会受到困扰

- 你需要一种工具，让你的 CSR 管理 Azure AD B2C 帐户。

- 你需要为 Csr 使用委派的管理。

- 想要将多个存储库中的数据同步并合并到 Azure AD B2C 中。

## <a name="pre-requisites"></a>先决条件

若要开始，你将需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](./tutorial-create-tenant.md)。 租户必须链接到你的 Azure 订阅。

- TheAccessHub 管理工具环境：联系 [N8 标识](https://n8id.com/contact/) 预配新环境。

- 可有可无 (LDAPs) 你要从中迁移客户数据的任何数据库或轻型目录访问协议的连接和凭据信息。

- 可有可无如果希望将 TheAccessHub 管理工具集成到注册策略流，请将配置 Azure AD B2C 环境用于使用 [自定义策略](./custom-policy-get-started.md)。

## <a name="scenario-description"></a>方案描述

TheAccessHub 管理工具与 Azure 中的任何其他应用程序一样运行。 它可以在 N8 标识的 Azure 订阅中运行，也可以在客户的订阅中运行。 下面的体系结构关系图显示了实现。

![显示 n8identity 体系结构关系图的图像](./media/partner-n8identity/n8identity-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户到达登录页。 用户选择 "注册" 以创建新帐户，并将信息输入到页面中。 Azure AD B2C 收集用户属性。
| 2. | Azure AD B2C 调用 TheAccessHub 管理工具并传递用户属性
| 3. | TheAccessHub 管理工具将检查现有数据库中是否有当前用户信息。  
| 4. | 用户记录将从数据库同步到 TheAccessHub 管理工具。
| 5. | TheAccessHub 管理员工具与委派的 CSR/支持人员管理员共享数据。
| 6. | TheAccessHub 管理工具将用户记录与 Azure AD B2C 同步。
| 7. |根据 TheAccessHub 管理工具的成功/失败响应，Azure AD B2C 向用户发送自定义的欢迎电子邮件。

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>在 Azure AD B2C 租户中创建全局管理员

TheAccessHub 管理工具需要权限来代表全局管理员来读取用户信息和执行 Azure AD B2C 租户中的更改。 对你的常规管理员的更改赢得了;t 影响 TheAccessHub 管理工具与租户的交互能力。

若要创建全局管理员，请执行以下步骤：

1. 在 Azure 门户中，以管理员身份登录到 Azure AD B2C 租户。 导航到 **Azure Active Directory**  >  **用户**
2. 选择 **新用户**
3. 选择 " **创建用户** " 以创建普通目录用户，而不是客户
4. 填写标识信息窗体

   a. 输入用户名，如 "theaccesshub"

   b. 输入名称，例如 "TheAccessHub Service Account"

5. 选择 " **显示密码** " 并复制初始密码以供将来使用
6. 分配全局管理员角色

   a. 选择用户的当前角色 **用户** 进行更改

   b. 检查全局管理员的记录

   c. **选择**  > **创建**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>将 TheAccessHub 管理员工具与 Azure AD B2C 租户连接

TheAccessHub 管理工具使用 Microsoft 图形 API 来读取和更改你的目录。 它充当租户中的全局管理员。 其他权限是 TheAccessHub 管理工具所必需的，你可以从该工具内部向其授予权限。

若要授权 TheAccessHub 管理员工具访问你的目录，请执行以下步骤：

1. 使用由 N8 标识提供的凭据登录到 TheAccessHub 管理工具

2. 导航到 "**系统管理员**"  >  **Azure AD B2C 配置**

3. 选择 **授权连接**

4. 在新窗口中，用全局管理员帐户登录。 如果你是第一次用新的服务帐户登录，系统可能会要求你重置密码。

5. 按照提示进行操作并选择 " **接受** " 以授予 TheAccessHub 管理工具所请求的权限。

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>使用企业标识配置新的 CSR 用户

使用现有企业 Azure Active Directory 凭据创建访问 TheAccessHub 管理工具的 CSR/支持人员。

若要使用单一登录 (SSO) 配置 CSR/支持人员用户，建议执行以下步骤：

1. 使用 N8 标识提供的凭据登录到 TheAccessHub 管理工具。

2. 导航到 **管理器工具**  >  **管理同事**

3. 选择 **添加同事**

4. 选择 **同事类型 Azure 管理员**

5. 输入同事的个人资料信息

   a. 选择本组织将控制谁有权管理此用户。

   b. 对于 "登录 ID/Azure AD 用户名"，提供用户的 Azure Active Directory 帐户的用户主体名称。

   c. 在 "TheAccessHub 角色" 选项卡上，查看 "托管角色" 支持人员。 这将允许用户访问 "管理同事" 视图。 用户仍需置于某个组中，或者将用户作为对客户的作用。

6. 选择“提交”。 

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>使用新标识配置新的 CSR 用户

创建将使用 TheAccessHub 管理工具独有的新本地凭据访问 TheAccessHub 管理工具的 CSR/技术支持用户。 这主要由不使用其企业 Azure AD 的组织使用。

若要在不使用 SSO 的情况下 [设置 CSR/技术支持](https://youtu.be/iOpOI2OpnLI) 用户，请按照以下步骤操作：

1. 使用 N8 标识提供的凭据登录到 TheAccessHub 管理工具

2. 导航到 **管理器工具**  >  **管理同事**

3. 选择 **添加同事**

4. 选择 " **同事" 类型本地管理员**

5. 输入同事的个人资料信息

   a. 选择本组织将控制谁有权管理此用户。

   b. 在 " **TheAccessHub 角色** " 选项卡上，选择 " **支持** 的托管角色"。 这将允许用户访问 "管理同事" 视图。 用户仍需置于某个组中，或者将用户作为对客户的作用。

6. 复制 **登录 ID/电子邮件** 和 **一次性密码** 特性。 将其提供给新用户。 他们将使用这些凭据登录到 TheAccessHub 管理工具。 用户首次登录时，系统将提示用户输入新密码。

7. 选择“提交”

## <a name="configure-partitioned-csr-administration"></a>配置分区 CSR 管理

使用组织层次结构管理在 TheAccessHub 管理工具中管理客户和 CSR/支持人员用户的权限。 所有同事和客户都有一个其所在的本组织。 可以将特定的同事或同事组分配为组织的所有者。  组织所有者可以管理 (对组织或他们所拥有的 suborganizations 中) 的同事和客户进行更改。 若要允许多个同事管理一组用户，可以创建具有多个成员的组。 然后，可以将组分配为组织所有者，并且组的所有成员都可以管理组织中的同事和客户。

### <a name="create-a-new-group"></a>创建一个新组

1. 使用由 N8 标识提供的 **凭据** 登录到 TheAccessHub 管理工具

2. 导航到 **组织 > 管理组**

3. 选择 > **添加组**

4. 输入 **组名称**、 **组描述** 和 **组所有者**

5. 搜索并选中要成为组成员的同事上的复选框，然后选择 >**添加**

6. 在页面的底部，可以看到组的所有成员。

7. 如果需要，可以通过选择该行末尾的 **x** 来删除

8. 选择“提交”

### <a name="create-a-new-organization"></a>创建一个新组织

1. 使用由 N8 标识提供的凭据登录到 TheAccessHub 管理工具

2. 导航到组织 > **管理组织**

3. 选择 > **添加组织**

4. 提供 **组织名称**、 **组织所有者** 和 **父组织**。

    a. 组织名称理想情况下与客户数据相对应的值。 在加载同事和客户数据时，如果在负载中提供组织的名称，则可以将同事自动置于组织中。

    b. 所有者表示将管理此组织中的客户和同事的人员或组，以及中的任何 suborganization。

    c. 父组织指明了它在本质上是其他组织的责任。

5. 选择“提交”。 

### <a name="modify-the-hierarchy-via-the-tree-view"></a>通过树视图修改层次结构

1. 使用由 N8 标识提供的凭据登录到 TheAccessHub 管理工具

2. 导航到 "**管理器工具**"  >  **树视图**

3. 在此表示形式中，可以可视化哪些同事和组可以管理哪些组织。

4. 可以通过将组织高于你想要其成为父组织的组织来修改层次结构。

5. 完成对层次结构的更改后，请选择 " **保存** "。

## <a name="customize-welcome-notification"></a>自定义欢迎通知

当你使用 TheAccessHub 将用户从以前的身份验证解决方案迁移到 Azure AD B2C 时，你可能需要自定义用户欢迎通知，该通知会在迁移过程中由 TheAccessHub 发送到用户。 此消息通常包含客户在 Azure AD B2C 目录中设置新密码的链接。

自定义通知：

1. 使用由 N8 标识提供的凭据登录 TheAccessHub

2. 导航到 **系统管理员**  >  **通知**

3. 选择 " **创建同事" 模板**

4. 选择“编辑”

5. 根据需要更改消息和模板字段。 模板字段是 HTML 感知的，可以向客户电子邮件发送 HTML 格式的通知。

6. 完成后，选择 " **保存** "。

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>将数据从外部数据源迁移到 Azure AD B2C

使用 TheAccessHub 管理工具，你可以从各种数据库、LDAPs 和 CSV 文件导入数据，然后将这些数据推送到 Azure AD B2C 租户。 这是通过在 TheAccessHub 管理工具中将数据加载到 Azure AD B2C 用户同事类型来完成的。  如果数据源不是 Azure 本身，则会将数据放置在 TheAccessHub 管理工具中，并 Azure AD B2C。 如果外部数据源不是计算机上的简单 .csv 文件，请在执行数据加载之前设置数据源。 以下步骤介绍了如何创建数据源以及如何进行数据加载。

### <a name="configure-a-new-data-source"></a>配置新数据源

1. 使用由 N8 标识提供的凭据登录到 TheAccessHub 管理工具

2. 导航到 "**系统管理**"  >  **数据源**

3. 选择 "**添加数据源**"

4. 为此数据源提供 **名称** 和 **类型**

5. 填写窗体数据，具体取决于数据源类型：

   **对于数据库**

   a. **类型** –数据库

   b. **数据库类型** –从一个受支持的数据库类型中选择一个数据库。

   c. **连接 URL** –输入格式正确的 JDBC 连接字符串。 例如：``jdbc:postgresql://myhost.com:5432/databasename``

   d. **用户名** –输入用于访问数据库的用户名

   e. **密码** –输入用于访问数据库的密码

   f. **Query** –输入用于提取客户详细信息的 SQL 查询。 例如：``SELECT * FROM mytable;``

   g. 选择 " **测试连接**"，您将看到数据的示例，以确保连接正常运行。

   **用于 LDAPs**

   a. **类型** – LDAP

   b. **主机** –输入运行 LDAP 服务器的计算机的主机名或 IP。 例如：``mysite.com``

   c. **端口** –输入 LDAP 服务器正在侦听的端口号。

   d. **SSL** –如果 TheAccessHub 管理工具应该使用 ssl 安全地与 LDAP 通信，请选中此框。 强烈建议使用 SSL。

   e. **登录 dn** –输入用于登录的用户帐户的 dn，并执行 LDAP 搜索

   f. **密码** –输入用户的密码

   g. **基本 dn** –在要搜索的层次结构的顶部输入 dn

   h. **Filter** –输入 LDAP 筛选器字符串，该字符串将获取客户记录

   i. **属性** –输入要传递给 TheAccessHub 管理工具的客户记录中以逗号分隔的属性列表

   j. 选择 " **测试连接**"，您将看到数据的示例，以确保连接正常运行。

   **对于 OneDrive**

   a. **类型** – OneDrive for business

   b. 选择 **授权连接**

   c. 新窗口会提示你登录到 **onedrive**，并使用对你的 OneDrive 帐户具有读取访问权限的用户登录。 TheAccessHub 管理工具将为此用户执行读取 CSV 加载文件的操作。

   d. 按照提示进行操作并选择 " **接受** " 以授予 TheAccessHub 管理工具所请求的权限。

6. 完成后，选择 " **保存** "。  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>将数据源中的数据同步到 Azure AD B2C

1. 使用由 N8 标识提供的凭据登录到 TheAccessHub 管理工具

2. 导航到 "**系统管理**" "  >  **数据同步**"

3. 选择 **新负载**

4. 选择 " **同事" 类型** Azure AD B2C 用户

5. 在弹出对话框中选择 " **源**"，选择数据源。 如果创建了 OneDrive 数据源，请选择该文件。

6. 如果你不想在此负载下创建新的客户帐户，请更改第一个策略：**如果在 TheAccessHub 中找不到同事，则****不执行任何** 操作

7. 如果你不想用此负载更新现有的客户帐户，则将第二个策略更改为 **source 和 TheAccessHub 数据不匹配，然后****不执行任何** 操作

8. 选择“下一步”

9. 在 **搜索映射配置** 中，我们确定如何将负载记录与已加载到 TheAccessHub 管理工具的客户关联起来。 选择源中的一个或多个标识特性。 将属性与 TheAccessHub 管理工具中保留相同值的属性相匹配。 如果找到匹配项，则将重写现有记录;否则，将创建一个新客户。 可以对多个检查进行排序。 例如，你可以先检查电子邮件，然后检查名字和姓氏。

10. 在左侧菜单中，选择 " **数据映射**"。

11. 在 Data-Mapping 配置中，分配应从源属性中填充的 TheAccessHub 管理工具属性。 无需映射所有属性。 对于现有客户，未映射的属性将保持不变。

12. 如果映射到属性 org_name 其值是现有组织的名称，则创建的新客户将放置在该组织中。

13. 选择“下一步”

14. 如果此负载应重复，则可以指定每日/每周或每月计划。 否则 **，请保留默认值**。

15. 选择“提交”

16. 如果选择了 " **立即计划** "，则会将一个新的记录立即添加到 "数据同步" 屏幕中。 验证阶段达到100% 后，选择 **新记录** 以查看负载的预期结果。 对于计划的负载，这些记录将仅出现在计划时间之后。

17. 如果没有错误，请选择 " **运行** " 以提交更改。 否则，请在 "**更多**" 菜单中选择 "**删除**" 以删除负载。 然后，可以更正源数据或加载映射，然后重试。 相反，如果错误数较小，则可以手动更新记录，并在每个记录上选择 " **更新** " 以进行更正。 最后，你可以继续处理任何错误，稍后在 TheAccessHub 管理工具中将其作为 **支持干预** 进行解决。

18. 如果 **数据同步** 记录在负载阶段变为100%，则会启动负载产生的所有更改。 客户应开始显示 Azure AD B2C 的更改。

## <a name="synchronize-azure-ad-b2c-customer-data"></a>同步 Azure AD B2C 客户数据 

作为一次性或持续操作，TheAccessHub 管理工具可以将 Azure AD B2C 中的所有客户信息同步到 TheAccessHub 管理工具。 这可确保 CSR/支持人员管理员查看最新的客户信息。

将数据从 Azure AD B2C 同步到 TheAccessHub 管理工具：

1. 使用由 N8 标识提供的凭据登录到 TheAccessHub 管理工具

2. 导航到 "**系统管理**" "  >  **数据同步**"

3. 选择 **新负载**

4. 选择 " **同事" 类型** Azure AD B2C 用户

5. 对于 " **选项** " 步骤，请保留默认值。

6. 选择“下一步”

7. 对于 " **数据映射 & 搜索** " 步骤，请保留默认值。 除非您使用作为现有组织名称的值映射到属性 **org_name** ，否则，将在该组织中放置新创建的客户。

8. 选择“下一步”

9. 如果此负载应重复，则可以指定每日/每周或每月计划。 否则，保留默认值： **Now**。 建议定期从 Azure AD B2C 同步。

10. 选择“提交”

11. 如果选择了 " **立即** 计划"，则会将一个新的记录立即添加到 "数据同步" 屏幕中。 验证阶段达到100% 后，选择新记录以查看负载的预期结果。 对于计划的负载，这些记录将仅出现在计划时间之后。

12. 如果没有错误，请选择 " **运行** " 以提交更改。 否则，请在 "更多" 菜单中选择 " **删除** " 以删除负载。 然后，可以更正源数据或加载映射，然后重试。 相反，如果错误数较小，则可以手动更新记录，并在每个记录上选择 " **更新** " 以进行更正。 最后，你可以继续处理任何错误，稍后在 TheAccessHub 管理工具中将其作为支持干预进行解决。

13. 如果 **数据同步** 记录在负载阶段变为100%，则会启动负载产生的所有更改。

## <a name="configure-azure-ad-b2c-policies"></a>配置 Azure AD B2C 策略

偶尔，同步 TheAccessHub 管理工具的能力限制了其状态保持 Azure AD B2C 的最新状态。 我们可以利用 TheAccessHub 管理工具的 API 和 Azure AD B2C 策略，在发生更改时通知 TheAccessHub 管理工具。 此解决方案需要 [Azure AD B2C 自定义策略](./custom-policy-get-started.md)的技术知识。 在下一部分中，我们将为你介绍一个示例策略步骤和一个安全证书，以便在 Sign-Up 自定义策略中通知 TheAccessHub 管理工具的新帐户。

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>创建安全凭据来调用 TheAccessHub 管理工具的 API

1. 使用由 N8 标识提供的凭据登录到 TheAccessHub 管理工具

2. 导航到 "**系统管理**"  >  **管理工具**  >  **API 安全性**

3. 选择 **生成**

4. 复制 **证书密码**

5. 选择 " **下载** " 以获取客户端证书。

6. 按照本 [教程](./secure-rest-api.md#https-client-certificate-authentication ) ，将客户端证书添加到 Azure AD B2C 中。

### <a name="retrieve-your-custom-policy-examples"></a>检索自定义策略示例

1. 使用由 N8 标识提供的凭据登录到 TheAccessHub 管理工具

2. 导航到 **系统管理**  >  **工具**  >  **Azure B2C 策略**

3. 从标识体验框架配置中为你的 Azure AD B2C 租户域和两个标识体验框架 Id 提供

4. 选择“保存”

5. 选择 " **下载** " 以获取一个 zip 文件，其中包含在客户注册时将客户添加到 TheAccessHub 管理工具的基本策略。

6. 按照本 [教程](./custom-policy-get-started.md) 开始在 Azure AD B2C 中设计自定义策略。

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](./custom-policy-get-started.md?tabs=applications)