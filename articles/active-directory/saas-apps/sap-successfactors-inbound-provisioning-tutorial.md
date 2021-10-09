---
title: 教程：在 AD 和 Azure AD 中配置 SuccessFactors 入站预配
description: 了解如何配置来自 SuccessFactors 的入站预配
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: d4a1350999258b47db4a5188fa9eab92aa56e567
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604939"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>教程：配置 SAP SuccessFactors 到 Active Directory 的用户预配 
本教程旨在说明需要执行哪些步骤才能将用户从 SuccessFactors Employee Central 预配到 Active Directory (AD) 和 Azure AD，以及选择性地将电子邮件地址写回到 SuccessFactors。 

>[!NOTE]
>如果你想要从 SuccessFactors 预配的用户需要本地 AD 帐户和（可选）Azure AD 帐户，则请使用本教程。 如果 SuccessFactors 中的用户只需要 Azure AD 帐户（仅限云的用户），请参阅有关[配置 SAP SuccessFactors 到 Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 的用户预配的教程。 


## <a name="overview"></a>概述

[Azure Active Directory 用户预配服务](../app-provisioning/user-provisioning.md)与 [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 集成，便于管理用户的标识生命周期。 

Azure AD 用户预配服务支持的 SuccessFactors 用户预配工作流可将以下人力资源和标识生命周期管理方案自动化：

* **招聘新员工** - 将新员工添加到 SuccessFactors 后，Active Directory、Azure Active Directory、（可选）Microsoft 365 和 [Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)中会自动创建一个用户帐户，并将电子邮件地址写回到 SuccessFactors。

* **员工属性和个人资料更新** - 在 SuccessFactors 中更新员工记录（例如其姓名、职称或上司）后，Active Directory、Azure Active Directory 中会自动更新相应员工的用户帐户，Microsoft 365 和 [Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)中会选择性地自动更新。

* **员工离职** - 当某个员工在 SuccessFactors 离职时，Active Directory、Azure Active Directory、（可选）Microsoft 365 和 [Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)中会自动禁用其用户帐户。

* **员工返聘** - 当 SuccessFactors 返聘某位员工时，该员工的旧帐户可自动重新激活或重新预配到 Active Directory、Azure Active Directory、Microsoft 365 以及 [Azure AD 支持的其他 SaaS 应用程序](../app-provisioning/user-provisioning.md)（其中，具体操作由你的偏好而定，且后两类应用可选配）。

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

这种 SuccessFactors 到 Active Directory 的用户预配解决方案非常适合以下对象：

* 需要使用预建的、基于云的解决方案进行 SuccessFactors 用户预配的组织

* 需要将用户预配从 SuccessFactors 定向到 Active Directory 的组织

* 要求使用从 [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 获取的数据预配用户的组织

* 要求只根据 [SuccessFactors Employee Central (EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 中检测到的更改信息，加入、移动用户或者使用户保持同步到一个或多个 Active Directory 林、域和 OU 的组织

* 使用 Microsoft 365 收发电子邮件的组织

## <a name="solution-architecture"></a>解决方案体系结构

本节介绍适用于常见混合环境的端到端用户预配解决方案体系结构。 有两个相关的流：

* **权威 HR 数据流 - 从 SuccessFactors 到本地 Active Directory：** 在此流中，工作人员事件（如新雇员、换岗、离职等）首先发生在云 SuccessFactors Employee Central 中，然后事件数据通过 Azure AD 和预配代理流入本地 Active Directory。 根据事件的不同，可能会导致在 AD 中创建/更新/启用/禁用操作。
* **电子邮件写回流 - 从本地 Active Directory 到 SuccessFactors：** 在 Active Directory 中完成帐户创建后，将通过 Azure AD Connect 同步来实现与 Azure AD 的同步，而且电子邮件和用户名特性可写回到 SuccessFactors。

  ![概述](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>端到端用户数据流

1. HR 团队在 SuccessFactors Employee Central 中执行工作人员事务（入职者/换岗者/离职者或新雇员/换岗/离职）
2. Azure AD 预配服务运行来自 SuccessFactors EC 的标识的计划同步，并确定需要进行处理以供与本地 Active Directory 域服务同步的更改。
3. Azure AD 预配服务使用包含 Azure AD 帐户创建/更新/启用/禁用操作的请求有效负载调用本地 Azure AD Connect 预配代理。
4. Azure AD Connect 预配代理使用服务帐户添加/更新 AD 帐户数据。
5. Azure AD Connect 同步引擎运行增量同步以获取 AD 中的更新。
6. Active Directory 域服务更新会与 Azure Active Directory 同步。
7. 如果配置了 [SuccessFactors 写回应用](sap-successfactors-writeback-tutorial.md)，则会根据使用的匹配特性将电子邮件特性写回到 SuccessFactors 中。

## <a name="planning-your-deployment"></a>计划部署

若要将 Cloud HR 驱动的用户预配从 SuccessFactors 配置到 AD，需要涵盖不同方面的重要规划，例如：
* 设置 Azure AD Connect 预配代理 
* 要部署的“SuccessFactors 到 AD 用户预配”用户的数目
* 匹配 ID、特性映射、转换和范围筛选器

有关这些主题的全面指导，请参阅[云 HR 部署计划](../app-provisioning/plan-cloud-hr-provision.md)。 若要了解支持的实体、处理详细信息，以及为不同 HR 场景自定义集成的方式，请参阅 [SAP SuccessFactors 集成参考](../app-provisioning/sap-successfactors-integration-reference.md)。 

## <a name="configuring-successfactors-for-the-integration"></a>为集成配置 SuccessFactors

所有 SuccessFactors 预配连接器的一个常见要求是，它们需要具有适当权限的 SuccessFactors 帐户的凭据才能调用 SuccessFactors OData API。 本部分介绍了在 SuccessFactors 中创建服务帐户并授予适当权限的步骤。 

* [在 SuccessFactors 中创建/识别 API 用户帐户](#createidentify-api-user-account-in-successfactors)
* [创建 API 权限角色](#create-an-api-permissions-role)
* [为 API 用户创建权限组](#create-a-permission-group-for-the-api-user)
* [向权限组授予权限角色](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>在 SuccessFactors 中创建/识别 API 用户帐户
与 SuccessFactors 管理团队或实施合作伙伴进行合作，在 SuccessFactors 中创建和标识将用于调用 OData API 的用户帐户。 在 Azure AD 中配置预配应用时，将需要此帐户的用户名和密码凭据。 

### <a name="create-an-api-permissions-role"></a>创建 API 权限角色

1. 使用有权访问管理中心的用户帐户登录 SAP SuccessFactors。
1. 搜索“管理权限角色”，然后从搜索结果中选择“管理权限角色”。
  ![管理权限角色](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
1. 从权限角色列表中，单击“新建”。
    > [!div class="mx-imgBorder"]
    > ![创建新的权限角色](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
1. 为新的权限角色添加角色名称和说明 。 名称和说明应指出该角色针对的是 API 使用权限。
    > [!div class="mx-imgBorder"]
    > ![权限角色详细信息](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
1. 在“权限设置”下，单击“权限…”，然后向下滚动权限列表，并单击“管理集成工具” 。 选中“允许管理员通过基本身份验证访问 OData API”框。
    > [!div class="mx-imgBorder"]
    > ![管理集成工具](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
1. 还是在该框中向下滚动，然后选择“Employee Central API”。 如下所示添加权限，以使用 ODATA API 进行读取和编辑。 如果计划为“写回到 SuccessFactors”场景使用同一帐户，请选择“编辑”选项。 
    > [!div class="mx-imgBorder"]
    > ![读写权限](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. 在相同的权限框中，转到“用户权限”->“员工数据”，并查看服务帐户可从 SuccessFactors 租户读取的属性。 例如，若要从 SuccessFactors 检索“用户名”属性，请确保针对此属性授予“查看”权限。 同样，查看每个属性的“查看”权限。 

    > [!div class="mx-imgBorder"]
    > ![员工数据权限](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >有关此预配应用检索到的特性的完整列表，请参阅 [SuccessFactors 特性参考](../app-provisioning/sap-successfactors-attribute-reference.md)

1. 单击“完成”。 单击 **“保存更改”** 。

### <a name="create-a-permission-group-for-the-api-user"></a>为 API 用户创建权限组

1. 在 SuccessFactors 管理中心，搜索“管理权限组”，然后从搜索结果中选择“管理权限组”。
    > [!div class="mx-imgBorder"]
    > ![管理权限组](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
1. 从“管理权限组”窗口中，单击“新建”。
    > [!div class="mx-imgBorder"]
    > ![添加新组](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
1. 为新组添加一个组名。 组名应指出该组用于 API 用户。
    > [!div class="mx-imgBorder"]
    > ![权限组名称](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
1. 向组添加成员。 例如，可从“人员池”下拉菜单中选择“用户名”，然后输入将用于集成的 API 帐户的用户名。 
    > [!div class="mx-imgBorder"]
    > ![添加组成员](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
1. 单击“完成”来完成权限组的创建。

### <a name="grant-permission-role-to-the-permission-group"></a>向权限组授予权限角色

1. 在 SuccessFactors 管理中心，搜索“管理权限角色”，然后从搜索结果中选择“管理权限角色”。
1. 从“权限角色列表”中，选择为 API 使用权限创建的角色。
1. 在“将此角色授予…”中，单击“添加…”按钮 。
1. 从下拉菜单中选择“权限组…”，然后单击“选择…”，打开“组”窗口进行搜索并选择上面创建的组 。 
    > [!div class="mx-imgBorder"]
    > ![添加权限组](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
1. 查看“向权限组授予权限角色”。 
    > [!div class="mx-imgBorder"]
    > ![权限角色和组详细信息](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
1. 单击 **“保存更改”** 。

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>配置从 SuccessFactors 到 Active Directory 的用户预配

此部分按步骤介绍如何将用户帐户从 SuccessFactors 预配到集成范围内的每个 Active Directory 域。

* [添加预配连接器应用并下载预配代理](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [安装和配置本地预配代理](#part-2-install-and-configure-on-premises-provisioning-agents)
* [配置与 SuccessFactors 和 Active Directory 的连接](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [配置属性映射](#part-4-configure-attribute-mappings)
* [启用并启动用户预配](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>第 1 部分：添加预配连接器应用并下载预配代理

**若要配置 SuccessFactors 到 Active Directory 的预配：**

1. 转到 <https://portal.azure.com>

2. 在左侧导航栏中选择“Azure Active Directory”。

3. 依次选择“企业应用程序”、“所有应用程序”。 

4. 依次选择“添加应用程序”、“所有”类别。 

5. 搜索“SuccessFactors 到 Active Directory 的用户预配”，然后从库中添加该应用。

6. 添加应用并显示应用详细信息屏幕后，请选择“预配”

7. 将“预配模式”更改为“自动”  

8. 单击显示的信息横幅以下载预配代理。 
   >[!div class="mx-imgBorder"]
   >![下载代理](./media/workday-inbound-tutorial/pa-download-agent.png "下载代理屏幕")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>第 2 部分：安装和配置本地预配代理

要预配到本地 Active Directory，必须在可通过网络访问所需 Active Directory 域的已加入域的服务器上安装预配代理。

将下载的代理安装程序传输到服务器主机，并按照[安装代理部分](../cloud-sync/how-to-install.md)中列出的步骤完成代理配置。

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>第 3 部分：在预配应用中，配置与 SuccessFactors 和 Active Directory 的连接
在此步骤中，我们将在 Azure 门户中建立与 SuccessFactors 和 Active Directory 的连接。 

1. 在 Azure 门户中，返回到在[第 1 部分](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)中创建的从 SuccessFactors 到 Active Directory 用户预配应用

1. 按如下所述完成“管理员凭据”部分：

   * **管理员用户名** - 输入 SuccessFactors API 用户帐户的用户名，并追加公司 ID。 它采用以下格式：用户名\@公司 ID

   * **管理员密码** - 输入 SuccessFactors API 用户帐户的密码。 

   * **租户 URL** - 输入 SuccessFactors OData API 服务终结点的名称。 仅输入不带 http 和 https 的服务器的主机名。 该值应如下所示：\<api-server-name\>.successfactors.com。

   * **Active Directory 林 -** 向代理注册时使用的 Active Directory 域的“名称”。 使用下拉列表选择用于预配的目标域。 此值通常为如下所示的字符串：contoso.com

   * Active Directory 容器 - 输入默认情况下代理应在其中创建用户帐户的容器 DN。
        示例：*OU=Users,DC=contoso,DC=com*
        > [!NOTE]
        > 如果未在属性映射中配置 parentDistinguishedName 属性，则此设置仅对用户帐户创建起作用。 此设置不用于用户搜索或更新操作。 整个域子树属于搜索操作的范围。

   * **通知电子邮件 -** 输入电子邮件地址，然后选中“如果失败，则发送电子邮件”复选框。
     > [!NOTE]
     > 如果预配作业进入[隔离](../app-provisioning/application-provisioning-quarantine-status.md)状态，Azure AD 预配服务将发送电子邮件通知。

   * 单击“测试连接”按钮。 如果连接测试成功，请单击顶部的“保存”按钮。 如果测试失败，请仔细检查代理设置上配置的 SuccessFactors 凭据和 AD 凭据是否有效。
     >[!div class="mx-imgBorder"]
     >![Azure 门户](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * 凭据成功保存后，“映射”部分将显示名为“将 SuccessFactors 用户同步到本地 Active Directory”的默认映射 

### <a name="part-4-configure-attribute-mappings"></a>第 4 部分：配置属性映射

在本部分，将配置用户数据如何从 SuccessFactors 流入 Active Directory。

1. 在“预配”选项卡的“映射”下，单击“将 SuccessFactors 用户同步到本地 Active Directory” 。

1. 在“源对象范围”字段中，可通过定义一组基于特性的筛选器，选择 SuccessFactors 中要预配到 AD 的用户集范围。 默认范围是“SuccessFactors 中的所有用户”。 示例筛选器：

   * 示例：将范围限定为 personIdExternal 为 1000000 到 2000000 的用户（不包括 2000000）

      * 特性：personIdExternal

      * 运算符：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 示例：仅限员工和非临时工

      * 属性：EmployeeID

      * 运算符：IS NOT NULL

   > [!TIP]
   > 首次配置预配应用时，需要测试和验证属性映射和表达式，以确保它提供所需的结果。 Microsoft 建议使用“源对象范围”下的范围筛选器来测试 SuccessFactors 中少量测试用户的映射。 验证确保映射正常工作后，可删除筛选器，也可逐渐扩大范围以包含更多用户。

   > [!CAUTION] 
   > 预配引擎的默认行为是禁用/删除超出范围的用户。 这可能不适合于 SuccessFactors 到 AD 集成。 若要替代此默认行为，请参阅[跳过删除超出范围的用户帐户](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. 在“目标对象操作”字段中，可全局筛选要对 Active Directory 执行的操作。 “创建”和“更新”是最常见的操作。 

1. 在“属性映射”部分中，可以定义将单个 SuccessFactors 属性映射到 Active Directory 属性的方式。

     >[!NOTE]
     >有关此应用程序支持的 SuccessFactors 属性的完整列表，请参阅 [SuccessFactors 属性参考](../app-provisioning/sap-successfactors-attribute-reference.md)

1. 单击现有的属性映射可将其更新，单击屏幕底部的“添加新映射”可添加新的映射。 单个属性映射支持以下属性：

      * **映射类型**

         * **直接映射** - 将 SuccessFactors 属性的值按原样写入 AD 属性

         * **常量映射** - 将静态常量字符串值写入 AD 属性

         * **表达式** - 可基于一个或多个 SuccessFactors 属性将自定义值写入 AD 属性。 [有关详细信息，请参阅这篇有关表达式的文章](../app-provisioning/functions-for-customizing-application-data.md)。

      * **源属性** - SuccessFactors 中的用户属性

      * **默认值** – 可选。 如果源属性的值为空，映射将改为写入此值。
            最常见的配置是将此项留空。

      * **目标属性** - Active Directory 中的用户属性。

      * **使用此属性匹配对象** - 是否应使用此映射来唯一地标识 SuccessFactors 与 Active Directory 之间的用户。 该值通常是在 SuccessFactors 的“工作人员 ID”字段中设置的，它通常映射到 Active Directory 中的某个“员工 ID”属性。

      * 匹配优先级 – 可设置多个匹配属性。 当存在匹配时，会按照此字段定义的顺序进行评估。 一旦找到匹配项，就不再继续评估其他匹配属性。

      * **应用此映射**

         * 始终 – 对用户创建和更新操作均应用此映射

         * 仅创建期间 - 仅对用户创建操作应用此映射

1. 要保存映射，请单击“属性-映射”部分顶部的“保存”。

完成属性映射配置后，可使用[按需预配预配](../app-provisioning/provision-on-demand.md)测试单个用户的预配，然后[启用并启动用户预配服务](#enable-and-launch-user-provisioning)。

## <a name="enable-and-launch-user-provisioning"></a>启用并启动用户预配

SuccessFactors 预配应用配置完成后，并且你已使用[按需预配](../app-provisioning/provision-on-demand.md)验证了单个用户的预配，则可在 Azure 门户中启用预配服务。

> [!TIP]
> 默认情况下，启用预配服务时，它会为范围中的所有用户启动预配操作。 如果映射出错或存在 SuccessFactors 数据问题，则预配作业可能会失败并转入隔离状态。 要避免这种情况，最佳做法是先配置“源对象范围”筛选器并使用[按需预配](../app-provisioning/provision-on-demand.md)对少数测试用户测试你的属性映射，然后再为所有用户启动完全同步。 验证确保映射正常工作且获得所需结果后，可删除筛选器或逐渐扩大范围以包含更多用户。

1. 转到“预配”边栏选项卡，单击“开始预配” 。

1. 此操作将启动初始同步；该过程会耗时数小时，具体时间取决于 SuccessFactors 租户中的用户数。 可检查进度条来跟踪同步周期的进度。 

1. 无论何时，检查 Azure 门户中的“审核日志”选项卡都可以查看预配服务执行的操作。 审核日志列出预配服务执行的所有同步事件（例如正在从 SuccessFactors 中读出哪些用户），随后将其添加或更新到 Active Directory。 

1. 完成初始同步后，系统会在“预配”选项卡中写入一份审核摘要报告，如下所示。

   > [!div class="mx-imgBorder"]
   > ![预配进度条](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>后续步骤

* [详细了解入站预配支持的 SuccessFactors 属性](../app-provisioning/sap-successfactors-attribute-reference.md)
* [了解如何配置到 SuccessFactors 的电子邮件写回](sap-successfactors-writeback-tutorial.md)
* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何在 SuccessFactors 和 Azure Active Directory 之间配置单一登录](successfactors-tutorial.md)
* [了解如何将其他 SaaS 应用程序与 Azure Active Directory 进行集成](tutorial-list.md)
* [了解如何导出和导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)