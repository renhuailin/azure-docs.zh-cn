---
title: 在 Azure Active Directory B2C 中配置 N8 Identity 的教程
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 中配置 TheAccessHub Admin Tool 以解决客户帐户迁移和客户服务请求 (CSR) 管理的教程。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9fee7965e7834f0e05dba91f8652a0e155087b14
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257868"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置 TheAccessHub Admin Tool 的教程

在本示例教程中，我们提供了有关如何将 Azure Active Directory (AD) B2C 与 N8 Identity 的 [TheAccessHub Admin Tool](https://n8id.com/products/theaccesshub-admintool/) 集成的指导。 此解决方案可解决客户帐户迁移和客户服务请求 (CSR) 管理。  

如果你有以下一个或多个需求，则此解决方案适合你：

- 你已有一个站点，并且想要迁移到 Azure AD B2C。 但是，你在迁移客户帐户（包括密码）时遇到困难

- 你需要借助适用于 CSR 的工具来管理 Azure AD B2C 帐户。

- 你需要对 CSR 使用委派管理。

- 你想要从多个存储库同步数据并将数据合并到 Azure AD B2C。

## <a name="pre-requisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- [Azure AD B2C 租户](./tutorial-create-tenant.md)。 租户必须链接到 Azure 订阅。

- TheAccessHub Admin Tool 环境：请联系 [N8 Identity](https://n8id.com/contact/) 预配新环境。

- [可选] 你要从中迁移客户数据的任何数据库或轻型目录访问协议 (LDAP) 的连接信息和凭据信息。

- [可选] 使用[自定义策略](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)配置了 Azure AD B2C 环境（如果要将 TheAccessHub Admin Tool 集成到注册策略流中）。

## <a name="scenario-description"></a>方案描述

TheAccessHub Admin Tool 与 Azure 中的任何其他应用程序一样运行。 它可以在 N8 Identity 的 Azure 订阅中运行，也可以在客户的订阅中运行。 以下体系结构图体现了实施详情。

![显示 n8identity 体系结构图的图像](./media/partner-n8identity/n8identity-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户到达登录页。 用户选择“注册”以创建新帐户，并在页面中输入信息。 Azure AD B2C 收集用户属性。
| 2. | Azure AD B2C 调用 TheAccessHub Admin Tool 并传递用户属性
| 3. | TheAccessHub Admin Tool 在现有数据库中检查当前用户信息。  
| 4. | 用户记录从数据库同步到 TheAccessHub Admin Tool。
| 5. | TheAccessHub Admin Tool与委派的 CSR/支持管理员共享数据。
| 6. | TheAccessHub Admin Tool 与 Azure AD B2C 同步用户记录。
| 7. |根据 TheAccessHub Admin Tool 的成功/失败响应，Azure AD B2C 向用户发送自定义的欢迎电子邮件。

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>在 Azure AD B2C 租户中创建全局管理员

TheAccessHub Admin Tool 需要拥有权限才能代表全局管理员读取用户信息，以及在 Azure AD B2C 租户中执行更改。 对普通管理员进行的更改不会影响 TheAccessHub Admin Tool 与租户交互的能力。

若要创建全局管理员，请执行以下步骤：

1. 在 Azure 门户中，以管理员身份登录到 Azure AD B2C 租户。 导航到“Azure Active Directory” > “用户” 
2. 选择“新建用户”
3. 选择“创建用户”以创建普通目录用户，而不是客户
4. 填写标识信息窗体

   a. 输入用户名，例如“theaccesshub”

   b. 输入名称，例如“TheAccessHub Service Account”

5. 选择“显示密码”并复制初始密码以供将来使用
6. 分配全局管理员角色

   a. 选择用户的当前角色 User 并对其进行更改

   b. 检查全局管理员的记录

   c. 选择“创建” > 

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>将 TheAccessHub Admin Tool 连接到 Azure AD B2C 租户

TheAccessHub Admin Tool 使用 Microsoft Graph API 来读取目录以及对目录进行更改。 它充当租户中的全局管理员。 TheAccessHub Admin Tool 还需要其他权限，你可以在工具中授予这些权限。

若要授权 TheAccessHub Admin Tool 访问你的目录，请执行以下步骤：

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool

2. 导航到“系统管理” > “Azure AD B2C 配置” 

3. 选择“授权连接”

4. 在新窗口中，使用全局管理员帐户登录。 如果是首次使用新的服务帐户登录，系统可能会要求你重置密码。

5. 按照提示进行操作并选择“接受”，向 TheAccessHub Admin Tool 授予所请求的权限。

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>使用企业标识配置新的 CSR 用户

创建将使用现有企业 Azure Active Directory 凭据访问 TheAccessHub Admin Tool 的 CSR/支持用户。

若要为 CSR/支持用户配置单一登录 (SSO)，建议执行以下步骤：

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool。

2. 导航到“管理器工具” > “管理同事” 

3. 选择“添加同事”

4. 单击“选择同事类型”，然后选择“Azure 管理员”

5. 输入同事的个人资料信息

   a. 选择本组织将控制谁有权管理该用户。

   b. 对于“登录 ID/Azure AD 用户名”，请提供用户 Azure Active Directory 帐户的用户主体名称。

   c. 在“TheAccessHub 角色”选项卡上，选择托管角色 Helpdesk。 此角色将允许用户访问“管理同事”视图。 你仍需要将用户添加到组中或使其成为组织所有者，这样该用户才能代表客户执行操作。

6. 选择“提交”。

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>使用新标识配置新的 CSR 用户

创建将使用 TheAccessHub Admin Tool 独有的新本地凭据访问 TheAccessHub Admin Tool 的 CSR/支持用户。 这种配置主要由不使用 Azure AD 的组织使用。

若要在不使用 SSO 的情况下[设置 CSR/支持用户](https://youtu.be/iOpOI2OpnLI)，请执行以下步骤：

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool

2. 导航到“管理器工具” > “管理同事” 

3. 选择“添加同事”

4. 单击“选择同事类型”，然后选择“本地管理员”

5. 输入同事的个人资料信息

   a. 选择本组织将控制谁有权管理该用户。

   b. 在“TheAccessHub 角色”选项卡上，选择托管角色 Helpdesk。  此角色将允许用户访问“管理同事”视图。 你仍需要将用户添加到组中或使其成为组织所有者，这样该用户才能代表客户执行操作。

6. 复制“登录 ID/电子邮件地址”和“一次性密码”属性。  将其提供给新用户。 用户将使用这些凭据登录到 TheAccessHub Admin Tool。 用户首次登录时，系统将提示用户输入新密码。

7. 选择“提交”

## <a name="configure-partitioned-csr-administration"></a>配置分区 CSR 管理

在 TheAccessHub Admin Tool 中管理客户和 CSR/支持用户的权限使用组织层次结构进行管理。 所有同事和客户都驻留在相应的本组织中。 可以将特定的同事或同事组分配为组织所有者。  组织所有者可以管理其负责的组织或子组织中的同事和客户（对其进行更改）。 若要允许多个同事管理一组用户，可以创建具有多个成员的组。 然后，可以将该组分配为组织所有者，这样该组的所有成员便能管理组织中的同事和客户。

### <a name="create-a-new-group"></a>创建一个新组

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool

2. 导航到“组织”>“管理组”

3. 选择“添加组”

4. 输入“组名”、“组说明”和“组所有者”  

5. 查找要使其成为组成员的同事并选中对应的复选框，然后选择“添加”

6. 在页面底部，你可以看到组的所有成员。

7. 如果需要，可以通过选择行末尾的 x 来删除成员

8. 选择“提交”

### <a name="create-a-new-organization"></a>创建一个新组织

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool

2. 导航到“组织”>“管理组织”

3. 选择“添加组织”

4. 提供“组织名称”、“组织所有者”和“父组织”。  

    a. 理想的组织名称是与客户数据对应的值。 加载同事数据和客户数据时，如果在加载过程中提供组织名称，则可以自动将同事添加到组织中。

    b. 所有者表示负责管理此组织以及任何子组织中的客户和同事的人员或组。

    c. 父组织表示其他组织在本质上也负责管理此组织。

5. 选择“提交”。

### <a name="modify-the-hierarchy-via-the-tree-view"></a>通过树视图修改层次结构

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool

2. 导航到“管理器工具” > “树视图” 

3. 在此表示形式中，可以使用图形方式呈现哪些同事和组可以管理哪些组织。

4. 可以通过将组织拖放到要作为父级的组织上方来修改层次结构。

5. 完成对层次结构的更改后，选择“保存”。

## <a name="customize-welcome-notification"></a>自定义欢迎通知

使用 TheAccessHub 将用户从以前的身份验证解决方案迁移到 Azure AD B2C 时，你可能需要自定义用户欢迎通知，该通知由 TheAccessHub 在迁移过程中发送给用户。 该消息通常包含一个链接，供客户用于在 Azure AD B2C 目录中设置新密码。

若要自定义通知，请执行以下操作：

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub

2. 导航到“系统管理” > “通知” 

3. 选择“创建同事模板”

4. 选择“编辑”

5. 根据需要更改“消息”字段和“模板”字段。 “模板”字段能够识别 HTML，并且可以向客户电子邮件地址发送 HTML 格式的通知。

6. 完成后，选择“保存”。

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>将数据从外部数据源迁移到 Azure AD B2C

使用 TheAccessHub Admin Tool，可以从各种数据库、LDAP 和 CSV 文件导入数据，然后将这些数据推送到 Azure AD B2C 租户。 这是通过在 TheAccessHub Admin Tool 中将数据加载到 Azure AD B2C 用户同事类型来完成的。  如果数据源不是 Azure 本身，则会同时将数据放置在 TheAccessHub Admin Tool 和 Azure AD B2C 中。 如果外部数据源不是计算机上的简单 .csv 文件，请在执行数据加载之前设置数据源。 以下步骤介绍了如何创建数据源以及执行数据加载。

### <a name="configure-a-new-data-source"></a>配置新数据源

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool

2. 导航到“系统管理” > “数据源” 

3. 选择“添加数据源”

4. 提供此数据源的“名称”和“类型” 

5. 根据数据源类型填写窗体数据：

   对于数据库

   a. “类型”- 数据库

   b. “数据库类型”- 选择一种受支持的数据库类型。

   c. “连接 URL”- 输入格式正确的 JDBC 连接字符串。 例如：``jdbc:postgresql://myhost.com:5432/databasename``

   d. “用户名”- 输入用于访问数据库的用户名

   e. “密码”- 输入用于访问数据库的密码

   f. “查询”- 输入用于提取客户详细信息的 SQL 查询。 例如：``SELECT * FROM mytable;``

   g. 选择“测试连接”，你将看到数据的示例，以确保连接正常运行。

   对于 LDAP

   a. “类型”- LDAP

   b. “主机”- 输入运行 LDAP 服务器的计算机的主机名或 IP。 例如：``mysite.com``

   c. “端口”- 输入 LDAP 服务器正在侦听的端口号。

   d. “SSL”- 如果 TheAccessHub Admin Tool 应使用 SSL 安全地与 LDAP 通信，则选中此复选框。 强烈建议使用 SSL。

   e. “登录 DN”- 输入用于登录的用户帐户的 DN，并执行 LDAP 搜索

   f. “密码”- 输入用户的密码

   g. “基准 DN”- 输入要在其中执行搜索的层次结构上方的 DN

   h. “筛选器”- 输入用于获取客户记录的 LDAP 筛选器字符串

   i. “属性”- 输入客户记录中要传递给 TheAccessHub Admin Tool 的属性的逗号分隔列表

   j. 选择“测试连接”，你将看到数据的示例，以确保连接正常运行。

   对于 OneDrive

   a. “类型”- OneDrive for Business

   b. 选择“授权连接”

   c. 此时将打开一个新窗口，提示你登录到 OneDrive。请使用对 OneDrive 帐户具有读取访问权限的用户帐户登录。 TheAccessHub Admin Tool 将为该用户读取 CSV 加载文件。

   d. 按照提示进行操作并选择“接受”，向 TheAccessHub Admin Tool 授予所请求的权限。

6. 完成后，选择“保存”。  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>将数据从数据源同步到 Azure AD B2C

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool

2. 导航到“系统管理” > “数据同步” 

3. 选择“新加载”

4. 单击“选择同事类型”，然后选择“Azure AD B2C 用户”

5. 在弹出对话框中选择“源”，然后选择数据源。 如果创建了 OneDrive 数据源，另请选择该文件。

6. 如果在此加载中不想创建新的客户帐户，则将第一个策略“在 TheAccessHub 中找不到该同事时执行的操作”更改为“不执行任何操作” 

7. 如果在此加载中不想更新现有客户帐户，则将第二个策略“在源与 TheAccessHub 数据不匹配时执行的操作”更改为“不执行任何操作” 

8. 选择“下一步”

9. 在“搜索映射配置”中，我们可以确定如何将加载记录与已加载到 TheAccessHub Admin Tool 的客户关联起来。 选择源中的一个或多个标识属性。 将这些属性与 TheAccessHub Admin Tool 中具有相同值的属性相匹配。 如果找到匹配项，则会替代现有记录；否则，将创建一个新客户。 可以对多个检查进行排序。 例如，可以先检查电子邮件地址，然后检查名字和姓氏。

10. 在左侧菜单中选择“数据映射”。

11. 在“数据映射配置”中，指定应从源属性填充的 TheAccessHub Admin Tool 属性。 无需映射所有属性。 对于现有客户，未映射的属性将保持不变。

12. 如果映射到值为现有组织的名称的 org_name 属性，则创建的新客户将放置在该组织中。

13. 选择“下一步”

14. 如果此负载应重复，可以指定“每日”/“每周”或“每月”计划。 否则，请保留默认计划“立即”。

15. 选择“提交”

16. 如果选择了“立即”计划，则会立即将新记录添加到“数据同步”屏幕中。 验证进度达到 100% 后，选择该新记录以查看加载的预期结果。 对于计划的加载，这些记录将仅在计划的时间之后出现。

17. 如果没有错误，则选择“运行”以提交更改。 否则，从“更多”菜单中选择“删除”以删除加载。  接着可以更正源数据或加载映射，然后重试。 相反，如果错误较少，则可以手动更新记录，并对每个记录选择“更新”以进行更正。 最后，你可以暂时忽略所有错误而继续操作，稍后在 TheAccessHub Admin Tool 中解决这些错误以提供支持干预。

18. 在加载阶段，如果数据同步记录变为 100%，则表明由加载导致的所有更改均已完成。 客户应开始出现在 Azure AD B2C 中或接收更改。

## <a name="synchronize-azure-ad-b2c-customer-data"></a>同步 Azure AD B2C 客户数据 

作为一次性操作或持续操作，TheAccessHub Admin Tool 可以从 Azure AD B2C 将所有客户信息同步到 TheAccessHub Admin Tool。 这可确保 CSR/支持管理员看到最新的客户信息。

若要将数据从 Azure AD B2C 同步到 TheAccessHub Admin Tool，请执行以下操作：

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool

2. 导航到“系统管理” > “数据同步” 

3. 选择“新加载”

4. 单击“选择同事类型”，然后选择“Azure AD B2C 用户”

5. 对于“选项”步骤，请保留默认值。

6. 选择“下一步”

7. 对于“数据映射和搜索”步骤，请保留默认值。 如果映射到值为现有组织的名称的 org_name 属性，则创建的新客户将放置在该组织中。

8. 选择“下一步”

9. 如果此负载应重复，可以指定“每日”/“每周”或“每月”计划。 否则，请保留默认计划“立即”。 建议定期从 Azure AD B2C 同步。

10. 选择“提交”

11. 如果选择了“立即”计划，则会立即将新记录添加到“数据同步”屏幕中。 验证进度达到 100% 后，选择该新记录以查看加载的预期结果。 对于计划的加载，这些记录将仅在计划的时间之后出现。

12. 如果没有错误，则选择“运行”以提交更改。 否则，从“更多”菜单中选择“删除”以删除加载。 接着可以更正源数据或加载映射，然后重试。 相反，如果错误较少，则可以手动更新记录，并对每个记录选择“更新”以进行更正。 最后，你可以暂时忽略所有错误而继续操作，稍后在 TheAccessHub Admin Tool 中解决这些错误以提供支持干预。

13. 在加载阶段，如果数据同步记录变为 100%，则表明由加载导致的所有更改均已完成。

## <a name="configure-azure-ad-b2c-policies"></a>配置 Azure AD B2C 策略

有时，TheAccessHub Admin Tool 同步功能可能无法将其状态与 Azure AD B2C 保持同步。 我们可以利用 TheAccessHub Admin Tool 的 API 和 Azure AD B2C 策略，在发生更改时立即通知 TheAccessHub Admin Tool。 此解决方案要求具有 [Azure AD B2C 自定义策略](./user-flow-overview.md)方面的技术知识。 在下一节中，我们将介绍用于向 TheAccessHub Admin Tool 通知自定义注册策略中的新帐户的示例策略步骤和安全证书。

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>创建用于调用 TheAccessHub Admin Tool 的 API 的安全凭据

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool

2. 导航到“系统管理” > “管理工具” > “API 安全”  

3. 选择“生成”

4. 复制“证书密码”

5. 选择“下载”以获取客户端证书。

6. 按照本[教程](./secure-rest-api.md#https-client-certificate-authentication )中所述将客户端证书添加到 Azure AD B2C。

### <a name="retrieve-your-custom-policy-examples"></a>检索自定义策略示例

1. 使用 N8 Identity 提供的凭据登录到 TheAccessHub Admin Tool

2. 导航到“系统管理” > “管理工具” > “Azure B2C 策略”  

3. 提供你的 Azure AD B2C 租户域和 Identity Experience Framework 配置中的两个 Identity Experience Framework ID

4. 选择“保存”

5. 选择“下载”以获取 zip 文件，其中包含用于在客户注册时将客户添加到 TheAccessHub Admin Tool 的基本策略。

6. 按照本[教程](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)中所述，开始在 Azure AD B2C 中设计自定义策略。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参阅以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)