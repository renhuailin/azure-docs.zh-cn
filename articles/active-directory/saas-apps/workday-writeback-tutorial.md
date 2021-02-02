---
title: 教程：在 Azure Active Directory 中配置 Workday 写回 | Microsoft Docs
description: 了解如何配置从 Azure AD 到 Workday 的属性写回
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3a623a487dd31caf8c85b18771d90e3a6306df68
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953998"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>教程：配置从 Azure AD 到 Workday 的属性写回
本教程的目的是演示将属性从 Azure AD 写回到 Workday 需要执行的步骤。 Workday 写回预配应用支持为以下 Workday 属性分配值：
* 工作电子邮件 
* Workday 用户名
* 工作座机电话号码（包括国家/地区代码、区号、号码和分机号码）
* 工作座机电话号码主标志
* 工作手机号码（包括国家/地区代码、区号和号码）
* 工作手机主标志

## <a name="overview"></a>概述

使用 [Workday 到本地 AD](workday-inbound-tutorial.md) 预配应用或 [Workday 到 Azure AD](workday-inbound-cloud-only-tutorial.md) 预配应用设置入站预配集成之后，可以选择性地配置 Workday 写回应用，以将联系信息（如工作电子邮件和电话号码）写入 Workday。 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>此用户预配解决方案最适合哪些对象？

此 Workday 写回用户预配解决方案非常适合以下对象：

* 需要将 IT 管理的官方属性（如电子邮件地址、用户名和电话号码）写回 Workday 的使用 Microsoft 365 的组织

## <a name="configure-integration-system-user-in-workday"></a>在 Workday 中配置集成系统用户

若要创建 Workday 集成系统用户并使其具有检索工作人员数据的权限，请参阅[配置集成系统用户](workday-inbound-tutorial.md#configure-integration-system-user-in-workday)部分。 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>配置 Azure AD 属性写回到 Workday

遵照以下说明，配置将用户电子邮件地址和用户名从 Azure Active Directory 写回到 Workday。

* [添加写回连接器应用并与 Workday 建立连接](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [配置写回属性映射](#part-2-configure-writeback-attribute-mappings)
* [启用并启动用户预配](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：添加写回连接器应用并与 Workday 建立连接

**配置 Workday 写回连接器：**

1. 转到  <https://portal.azure.com> 。

2. 在 Azure 门户中，搜索并选择“Azure Active Directory”。

3. 依次选择“企业应用程序”、“所有应用程序”。 

4. 依次选择“添加应用程序”、“所有”类别。 

5. 搜索“Workday 写回”，然后从库中添加该应用。

6. 添加应用并显示应用详细信息屏幕后，请选择“预配”。

7. 将“预配模式”更改为“自动”  。

8. 按如下所述完成“管理员凭据”部分：

   * **管理员用户名** – 输入 Workday 集成系统帐户的用户名，并附加租户域名。 内容应该如下所示：username\@contoso4

   * **管理员密码 -** 输入 Workday 集成系统帐户的密码

   * **租户 URL –** 输入租户的 Workday Web 服务终结点的 URL。 此值应类似于：`https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources`；其中，contoso4 替换为正确的租户名称，wd3-impl 替换为正确的环境字符串（如有必要） 。

   * **通知电子邮件 -** 输入电子邮件地址，然后选中“如果失败，则发送电子邮件”复选框。

   * 单击“测试连接”按钮。 如果连接测试成功，请单击顶部的“保存”按钮。 如果测试失败，请仔细检查 Workday 中的 Workday URL 和凭据是否有效。

### <a name="part-2-configure-writeback-attribute-mappings"></a>第 2 部分：配置写回属性映射

在此部分中，你将配置写回属性从 Azure AD 流到 Workday 的方式。 

1. 在“映射”下的“预配”选项卡上，单击映射名称。

2. 在“源对象范围”字段中，可以有选择性地在 Azure Active Directory 中筛选要应属于写回一部分的用户集。 默认范围是“Azure AD 中的所有用户”。

3. 在“属性映射”部分中，更新匹配的 ID 以指明 Azure Active Directory 中存储着 Workday 工作人员 ID 或员工 ID 的属性。 常用的匹配方法是将 Workday 工作人员 ID 或员工 ID 同步到 Azure AD 中的 extensionAttribute1-15，然后使用 Azure AD 中的此属性来重新匹配 Workday 中的用户。

4. 通常，将 Azure AD userPrincipalName 属性映射到 Workday UserID 属性，并将 Azure AD mail 属性映射到 Workday EmailAddress 属性   。 

     >[!div class="mx-imgBorder"]
     >![Azure 门户](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. 使用下面共享的指导将电话号码属性值从 Azure AD 映射到 Workday。 

     | Workday 电话属性 | 预期值 | 映射指导 |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | true/false | 其输出为“true”或“false”字符串值的常数或表达式映射。 |
     | WorkphoneLandlineCountryCodeName | [三字母 ISO 3166-1 国家/地区代码](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | 其输出为三字母国家/地区代码的常数或表达式映射。 |
     | WorkphoneLandlineCountryCodeNumber | [国际国家/地区呼叫代码](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | 其输出为有效国家/地区代码（没有 + 号）的常数或表达式映射。 |
     | WorkphoneLandlineNumber | 包含区号的完整电话号码 | 映射到 telephoneNumber 属性。 使用正则表达式删除空格、括号和国家/地区代码。 请参阅以下示例。 |
     | WorkphoneLandlineExtension | 分机号码 | 如果 telephoneNumber 包含分机号码，请使用正则表达式提取值。 |
     | WorkphoneMobileIsPrimary | true/false | 其输出为“true”或“false”字符串值的常数映射或表达式映射 |
     | WorkphoneMobileCountryCodeName | [三字母 ISO 3166-1 国家/地区代码](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | 其输出为三字母国家/地区代码的常数或表达式映射。 |
     | WorkphoneMobileCountryCodeNumber | [国际国家/地区呼叫代码](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | 其输出为有效国家/地区代码（没有 + 号）的常数或表达式映射。 |
     | WorkphoneMobileNumber | 包含区号的完整电话号码 | 映射到 mobile 属性。 使用正则表达式删除空格、括号和国家/地区代码。 请参阅以下示例。 |

     > [!NOTE]
     > 调用 Change_Work_Contact Workday Web 服务时，Azure AD 发送以下常数值： <br>
     > * Communication_Usage_Type_ID 设置为常数字符串“WORK” <br>
     > * Phone_Device_Type_ID 对于移动电话号码设置为常数字符串“Mobile”，对于座机电话号码设置为“Landline”。 <br>
     > 
     > 如果 Workday 租户使用不同 Type_ID，则会遇到写回失败。 若要防止此类失败，可以使用 Workday“维护首选项 ID”任务，并更新 Type_ID 以匹配 Azure AD 使用的值。 <br>
     >  

     参考正则表达式 - 示例 1

     如果 Azure AD 中的电话号码使用自助服务密码重置 (SSPR) 所需的格式进行设置，请使用下面的正则表达式。 <br>
     示例：如果电话号码值为 +1 1112223333 ->，则正则表达式会输出 1112223333

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     参考正则表达式 - 示例 2

     如果 Azure AD 中的电话号码使用格式 (XXX) XXX-XXXX 进行设置，请使用下面的正则表达式。 <br>
     示例：如果电话号码值为 (111) 222-3333 ->，则正则表达式会输出 1112223333

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. 若要保存映射，请单击“属性映射”部分顶部的“保存”。

## <a name="enable-and-launch-user-provisioning"></a>启用并启动用户预配

Workday 预配应用配置完成后，可在 Azure 门户中启用预配服务。

> [!TIP]
> 默认情况下，启用预配服务时，它会为范围中的所有用户启动预配操作。 如果映射出错或存在 Workday 数据问题，则预配作业可能会失败并转入隔离状态。 要避免这种情况，最佳做法是先配置“源对象范围”筛选器并使用少量测试用户来测试属性映射，然后再为所有用户启动完全同步。 验证确保映射正常工作且获得所需结果后，可删除筛选器或逐渐扩大范围以包含更多用户。

1. 在“预配”选项卡中，将“预配状态”设置为“打开”。  

1. 在“范围”下拉列表中，选择“同步所有用户和组” 。 使用此选项时，写回应用会将所有用户的映射属性从 Azure AD 写回 Workday（遵循“映射” -> “源对象范围”中定义的范围规则）。 

   > [!div class="mx-imgBorder"]
   > ![选择写回范围](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > Workday 写回预配应用不支持选项“仅同步已分配的用户和组”。
 

2. 单击“ **保存**”。

3. 此操作将启动初始同步；该过程会耗时数小时，具体时间取决源目录中的用户数。 可检查进度条来跟踪同步周期的进度。 

4. 无论何时，检查 Azure 门户中的“预配日志”选项卡都可以查看预配服务执行的操作。 审核日志会列出预配服务执行的所有单个同步事件，例如从源导入和导出到目标应用程序的用户。  

5. 完成初始同步后，系统会在“预配”选项卡中写入摘要报告，如下所示。

     > [!div class="mx-imgBorder"]
     > ![预配进度条](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>已知问题和限制

* 写回应用对 Communication_Usage_Type_ID 和 Phone_Device_Type_ID  参数使用预定义值。 如果 Workday 租户对这些属性使用不同的值，则写回操作不会成功。 建议解决方法是是在 Workday 中更新 Type_ID。 
* 写回应用配置为更新辅助电话号码时，不会在 Workday 中替换现有辅助电话号码。 它会向工作人员记录额外添加一个辅助电话号码。 没有针对此行为的解决方法。 


## <a name="next-steps"></a>后续步骤

* [详细了解 Azure AD 和 Workday 集成场景和 Web 服务调用](../app-provisioning/workday-integration-reference.md)
* [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)
* [了解如何在 Workday 和 Azure Active Directory 之间配置单一登录](workday-tutorial.md)
* [了解如何将其他 SaaS 应用程序与 Azure Active Directory 进行集成](tutorial-list.md)
* [了解如何导出和导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)

