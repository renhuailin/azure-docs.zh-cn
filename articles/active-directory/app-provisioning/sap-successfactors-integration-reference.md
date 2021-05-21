---
title: Azure Active Directory 和 SAP SuccessFactors 集成参考
description: 从技术上深入了解 SAP SuccessFactors-HR 驱动的预配
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: ed97600ca1802629f81f93f4f51c92ad4b1c9bd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256215"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Azure Active Directory 预配如何与 SAP SuccessFactors 集成 

[Azure Active Directory 用户预配服务](../app-provisioning/user-provisioning.md)与 [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 相集成，以便于管理用户的标识生命周期。 Azure Active Directory 提供三个预生成集成： 

* [SuccessFactors 到本地 Active Directory 的用户预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors 到 Azure Active Directory 的用户预配](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [SuccessFactors 写回](../saas-apps/sap-successfactors-writeback-tutorial.md)

本文介绍集成的工作原理，以及如何为不同的 HR 方案自定义预配行为。 

## <a name="establishing-connectivity"></a>建立连接 
Azure AD 预配服务使用基本身份验证连接到 Employee Central OData API 终结点。 设置 SuccessFactors 预配应用时，请使用“管理员凭据”部分中的“租户 URL”参数来配置 [API 数据中心 URL](https://apps.support.sap.com/sap/support/knowledge/en/2215682) 。 

若要进一步保护 Azure AD 预配服务与 SuccessFactors 之间的连接，可以使用下面所述的步骤，在 SuccessFactors IP 允许列表中添加 Azure AD IP 范围：

1. 下载 Azure 公有云的[最新 IP 范围](https://www.microsoft.com/download/details.aspx?id=56519) 
1. 打开文件，搜索标记“AzureActiveDirectory” 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP 范围](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. 复制 addressPrefixes 元素中列出的所有 IP 地址范围，并使用范围生成 IP 地址限制列表。
1. 将 CIDR 值转换为 IP 范围。  
1. 登录到 SuccessFactors 管理门户以将 IP 范围添加到允许列表。 请参阅 SAP [支持说明 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200)。 现在可以在此工具中[输入 IP 范围](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html)。 

## <a name="supported-entities"></a>受支持的实体
对于 SuccessFactors 中的每个用户，Azure AD 预配服务将检索以下实体。 使用 OData API $expand 查询参数扩展每个实体。 请参阅下面的“检索规则”列。 有些实体默认已扩展，而有些实体仅在映射中存在特定的特性时才会扩展。 

| \# | SuccessFactors 实体                  | OData 节点     | 检索规则 |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | 根节点                  | 始终           |
| 2  | PerPersonal                            | personalInfoNav              | 始终           |
| 3  | PerPhone                               | phoneNav                     | 始终           |
| 4  | PerEmail                               | emailNav                     | 始终           |
| 5  | EmpEmployment                          | employmentNav                | 始终           |
| 6  | User                                   | employmentNav/userNav        | 始终           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | 始终           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | 始终           |
| 9  | 用户的经理                         | employmentNav/userNav/manager/empInfo | 始终  |
| 10 | FOCompany                              | employmentNav/jobInfoNav/companyNav | 仅当已映射 `company` 或 `companyId` 特性时 |
| 11 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | 仅当已映射 `department` 或 `departmentId` 特性时 |
| 12 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | 仅当已映射 `businessUnit` 或 `businessUnitId` 特性时 |
| 13 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | 仅当已映射 `costCenter` 或 `costCenterId` 特性时 |
| 14 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | 仅当已映射 `division` 或 `divisionId` 特性时 |
| 15 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | 仅当已映射 `jobCode` 或 `jobCodeId` 特性时 |
| 16 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | 仅当已映射 `payGrade` 特性时 |
| 17 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | 仅当已映射 `location` 特性时 |
| 18 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | 如果映射包含以下特性之一：`officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 19 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | 仅当已映射 `eventReason` 特性时 |
| 20 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | 仅当已映射 `assignmentType` 时 |
| 21 | EmploymentType 选择列表                | employmentNav/jobInfoNav/employmentTypeNav | 仅当已映射 `employmentType` 时 |
| 22 | EmployeeClass 选择列表                 | employmentNav/jobInfoNav/employeeClassNav | 仅当已映射 `employeeClass` 时 |
| 23 | EmplStatus 选择列表                    | employmentNav/jobInfoNav/emplStatusNav | 仅当已映射 `emplStatus` 时 |
| 24 | AssignmentType 选择列表                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | 仅当已映射 `assignmentType` 时 |

## <a name="how-full-sync-works"></a>完全同步的工作原理
在完全同步期间，Azure AD 预配服务将基于特性映射发送以下“GET”OData API 查询，以提取所有活动用户的有效数据。 

> [!div class="mx-tdCol2BreakAll"]
>| 参数 | 说明 |
>| ----------|-------------|
>| OData API 主机 | 将 https 追加到租户 URL。 示例：`https://api4.successfactors.com` |
>| OData API 终结点 | `/odata/v2/PerPerson` |
>| OData $format 查询参数 | `json` |
>| OData $filter 查询参数 | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData $expand 查询参数 | 此参数值取决于映射的特性。 示例：`employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData customPageSize 查询参数 | `100` |

> [!NOTE]
> 在首次初始完全同步期间，Azure AD 预配服务不会拉取非活动/已终止的工作人员数据。

对于每个 SuccessFactors 用户，预配服务将使用映射中定义的匹配特性来查找目标（Azure AD/本地 Active Directory）中的帐户。 例如：如果 personIdExternal 映射到 employeeId 并设置为匹配特性，则预配服务将结合使用 personIdExternal 值和 employeeId 筛选器来搜索用户   。 如果找到用户匹配项，则预配服务会更新目标特性。 如果未找到匹配项，则它会在目标中创建一个新条目。 

若要验证 OData API 终结点为特定 `personIdExternal` 返回的数据，请使用你的 API 数据中心服务器 URL 更新以下 API 查询中的 `SuccessFactorsAPIEndpoint`，并使用 [Postman](https://www.postman.com/downloads/) 之类的工具调用该查询。 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>增量同步的工作原理

完全同步后，Azure AD 预配服务会维护 `LastExecutionTimestamp` 并用它来创建差异查询以检索增量更改。 将计算每个 SuccessFactors 实体中的时间戳特性（例如 `lastModifiedDateTime`、`startDate`、`endDate` 和 `latestTerminationDate`），以确定更改是否发生在 `LastExecutionTimestamp` 与 `CurrentExecutionTime` 之间。 如果是，则将条目更改视为有效，并对其进行同步处理。 

## <a name="reading-attribute-data"></a>读取特性数据

当 Azure AD 预配服务查询 SuccessFactors 时，将检索 JSON 结果集。 JSON 结果集包括存储在 Employee Central 中的一些特性。 默认情况下，预配架构配置为仅检索这些特性的子集。 

若要检索其他特性，请执行以下步骤：
    
1. 浏览到“企业应用程序” -> “SuccessFactors 应用” -> “预配” -> “编辑预配” -> “特性映射页”    。
1. 向下滚动，然后单击“显示高级选项”。
1. 单击“编辑 SuccessFactors 属性列表”。 

   > [!NOTE] 
   > 如果“编辑 SuccessFactors 属性列表”选项未显示在 Azure 门户，则使用 URL https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true 来访问该页面。 

1. 此视图中的“API 表达式”列显示连接器使用的 JSONPath 表达式。

   >[!div class="mx-imgBorder"] 
   >![API 表达式](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. 可以编辑现有的 JSONPath 值，或者使用有效的 JSONPath 表达式向架构添加新的特性。 

下一部分提供了用于编辑 JSONPath 值的常用方案列表。 

## <a name="handling-different-hr-scenarios"></a>处理不同的 HR 方案

JSONPath 是适用于 JSON 的一种查询语言，类似于 XML 的 XPath。 与 XPath 类似，JSONPath 允许从 JSON 有效负载中提取和筛选数据。

使用 JSONPath 转换可以自定义 Azure AD 预配应用的行为，以检索自定义特性并处理重新雇佣、工作人员转换和全局分配等方案。 

本部分介绍如何为以下 HR 方案自定义预配应用： 
* [检索其他特性](#retrieving-additional-attributes)
* [检索自定义特性](#retrieving-custom-attributes)
* [处理工作人员转换方案](#handling-worker-conversion-scenario)
* [处理重新雇佣方案](#handling-rehire-scenario)
* [处理全局分配方案](#handling-global-assignment-scenario)
* [处理并发作业方案](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>检索其他特性

默认的 Azure AD SuccessFactors 预配应用架构随附了 [90 多个预定义的特性](sap-successfactors-attribute-reference.md)。 若要将更多 SuccessFactors 特性添加到预配架构，请使用以下步骤： 

1. 使用以下 OData 查询从 Employee Central 检索有效测试用户的数据。 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. 确定与特性关联的 Employee Central 实体
   * 如果该特性是 EmpEmployment 实体的一部分，则在 employmentNav 节点下查看该特性 。 
   * 如果该特性是 User 实体的一部分，则在 employmentNav/userNav 节点下查看该特性 。
   * 如果该特性是 EmpJob 实体的一部分，则在 employmentNav/jobInfoNav 节点下查看该特性 。 
1. 构造与特性关联的 JSON 路径，并将此新特性添加到 SuccessFactors 特性列表中。 
   * 示例 1：假设你要添加属于 employmentNav 实体一部分的特性 okToRehire，请使用 JSONPath `$.employmentNav.results[0].okToRehire` 
   * 示例 2：假设你要添加属于 userNav 实体一部分的特性 timeZone，请使用 JSONPath `$.employmentNav.results[0].userNav.timeZone` 
   * 示例 3：假设你要添加属于 jobInfoNav 实体一部分的特性 flsaStatus，请使用 JSONPath `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus` 
1. 保存该架构。 
1. 重新开始预配。

### <a name="retrieving-custom-attributes"></a>检索自定义特性

默认情况下，Azure AD SuccessFactors 预配应用中预定义了以下自定义特性： 
* User (userNav) 实体中的 custom01-custom15
* 名为 empNavCustomString1-empNavCustomString15 的 EmpEmployment (employmentNav) 实体中的 customString1-customString15 
* 名为 empJobNavCustomString1-empNavJobCustomString15 的 EmpJobInfo (jobInfoNav) 实体中的 customString1-customString15 

假设在你的 Employee Central 实例中，EmpJobInfo 中的 customString35 特性存储了位置说明 。 你想要将此值传送到 Active Directory physicalDeliveryOfficeName 特性。 若要为此方案配置特性映射，请使用以下步骤： 

1. 编辑 SuccessFactors 特性列表以添加名为 empJobNavCustomString35 的新特性。
1. 将此特性的 JSONPath API 表达式设置为：`$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. 保存并在 Azure 门户中重载映射更改。  
1. 在特性映射边栏选项卡中，将 empJobNavCustomString35 映射到 physicalDeliveryOfficeName 。
1. 保存映射。

扩展此方案： 
* 如果你要映射 User 实体中的 custom35 特性，请使用 JSONPath `$.employmentNav.results[0].userNav.custom35` 
* 如果你要映射 EmpEmployment 实体中的 customString35 特性，请使用 JSONPath `$.employmentNav.results[0].customString35` 

### <a name="handling-worker-conversion-scenario"></a>处理工作人员转换方案

工作人员转换是将现有全职员工转换为合同工，或者将合同工转换为全职员工的过程。 在此方案中，Employee Central 将为同一个 Person 实体添加新的 EmpEmployment 实体以及新的User 实体  。 在上一个 EmpEmployment 实体下嵌套的 User 实体设置为 null 。 若要处理此方案以在转换时显示新的雇佣数据，可以使用下列步骤批量更新预配应用架构：  

1. 打开 SuccessFactors 预配应用的特性映射边栏选项卡。 
1. 向下滚动，然后单击“显示高级选项”。
1. 单击“在此处查看架构”链接打开架构编辑器。 

   >![该屏幕截图显示了用于打开架构编辑器的“在此处查看架构”链接。](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. 单击“下载”链接，以便在编辑之前保存架构副本。 

   >![该屏幕截图显示了架构编辑器，其中选择了“下载”以保存架构副本。](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. 在架构编辑器中，按 Ctrl-H 键打开“查找-替换”控件。
1. 在“查找”文本框中，复制并粘贴值 `$.employmentNav.results[0]`
1. 在“替换”文本框中，复制并粘贴值 `$.employmentNav.results[?(@.userNav != null)]`。 请注意 `!=` 运算符两边的空格，必须添加这些空格才能成功处理 JSONPath 表达式。 
   >![查找-替换-转换](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. 单击“全部替换”选项以更新架构。 
1. 保存该架构。 
1. 上述过程将按如下所示更新所有 JSONPath 表达式： 
   * 旧 JSONPath：`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新 JSONPath：`$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. 重新开始预配。 

### <a name="handling-rehire-scenario"></a>处理重新雇佣方案

通常有两个选项可用于处理重新雇佣： 
* 选项 1：在 Employee Central 中创建新的人员配置文件
* 选项 2：在 Employee Central 中重复使用现有的人员配置文件

如果 HR 流程使用选项 1，则不需要对预配架构进行任何更改。 如果 HR 流程使用选项 2，则 Employee Central 将为同一个 Person 实体添加新的 EmpEmployment 实体以及新的User 实体  。 与转换方案不同，上一个 EmpEmployment 实体中的 User 实体不会设置为 null 。 

若要处理此重新雇佣方案（选项 2）以显示重新雇佣配置文件的最新雇用数据，可以使用下列步骤批量更新预配应用架构：  

1. 打开 SuccessFactors 预配应用的特性映射边栏选项卡。 
1. 向下滚动，然后单击“显示高级选项”。
1. 单击“在此处查看架构”链接打开架构编辑器。   
1. 单击“下载”链接，以便在编辑之前保存架构副本。   
1. 在架构编辑器中，按 Ctrl-H 键打开“查找-替换”控件。
1. 在“查找”文本框中，复制并粘贴值 `$.employmentNav.results[0]`
1. 在“替换”文本框中，复制并粘贴值 `$.employmentNav.results[-1:]`。 此 JSONPath 表达式返回最新的 EmpEmployment 记录。   
1. 单击“全部替换”选项以更新架构。 
1. 保存该架构。 
1. 上述过程将按如下所示更新所有 JSONPath 表达式： 
   * 旧 JSONPath：`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新 JSONPath：`$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. 重新开始预配。 

此架构更改还支持工作人员转换方案。 

### <a name="handling-global-assignment-scenario"></a>处理全局分配方案

为 Employee Central 中的用户处理全局分配时，SuccessFactors 将添加新的 EmpEmployment 实体，并将 assignmentClass 设置为“GA” 。 它还会创建新的 User 实体。 因此，该用户现在具有：
* 一个 EmpEmployment + User 实体，该实体对应于 assignmentClass 设置为“ST”的本地分配  ； 
* 另一个 EmpEmployment + User 实体，该实体对应于 assignmentClass 设置为“GA”的全局分配  

若要提取属于标准分配和全局分配用户配置文件的特性，请使用下列步骤： 

1. 打开 SuccessFactors 预配应用的特性映射边栏选项卡。 
1. 向下滚动，然后单击“显示高级选项”。
1. 单击“在此处查看架构”链接打开架构编辑器。   
1. 单击“下载”链接，以便在编辑之前保存架构副本。   
1. 在架构编辑器中，按 Ctrl-H 键打开“查找-替换”控件。
1. 在“查找”文本框中，复制并粘贴值 `$.employmentNav.results[0]`
1. 在“替换”文本框中，复制并粘贴值 `$.employmentNav.results[?(@.assignmentClass == 'ST')]`。 
1. 单击“全部替换”选项以更新架构。 
1. 保存该架构。 
1. 上述过程将按如下所示更新所有 JSONPath 表达式： 
   * 旧 JSONPath：`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 新 JSONPath：`$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. 重载应用的特性映射边栏选项卡。 
1. 向下滚动，然后单击“显示高级选项”。
1. 单击“编辑 SuccessFactors 属性列表”。
1. 添加新特性以提取全局分配数据。 例如：如果你要提取与某个全局分配配置文件关联的部门名称，可以在将 JSONPath 表达式设置为 `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` 的情况下添加特性 globalAssignmentDepartment。 
1. 现在，可以将这两个部门值传送到 Active Directory 特性，或者选择地使用表达式映射来传送某个值。 示例：以下表达式将 AD department 特性的值设置为 globalAssignmentDepartment（如果存在），否则它会将值设置为与标准分配关联的部门  。 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. 保存映射。 
1. 重新开始预配。 

### <a name="handling-concurrent-jobs-scenario"></a>处理并发作业方案

当 Employee Central 中的用户具有并发/多个作业时，将存在 assignmentClass 设置为“ST”的两个 EmpEmployment 和 User 实体  。 若要提取属于这两个作业的特性，请使用下列步骤： 

1. 打开 SuccessFactors 预配应用的特性映射边栏选项卡。 
1. 向下滚动，然后单击“显示高级选项”。
1. 单击“编辑 SuccessFactors 属性列表”。
1. 假设你想要拉取与作业 1 和作业 2 关联的部门。 预定义的特性 department 已提取第一个作业的 department 值。 可以定义一个名为 secondJobDepartment 的新特性，并将 JSONPath 表达式设置为 `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
1. 现在，可以将这两个部门值传送到 Active Directory 特性，或者选择地使用表达式映射来传送某个值。 
1. 保存映射。 
1. 重新开始预配。 

## <a name="writeback-scenarios"></a>写回方案

本部分介绍不同的写回方案。 其中将会根据 SuccessFactors 中设置电子邮件和电话号码的方式推荐配置方法。

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>电话和电子邮件写回支持的方案 

| \# | 方案要求 | 电子邮件主要 <br> 标志值 | 工作电话 <br> 主要标志值 | 手机 <br> 主要标志值 | 工作电话 <br> mapping | 手机 <br> mapping |
|--|--|--|--|--|--|--|
| 1 | * 仅将业务电子邮件设置为主要联系方式。 <br> * 不设置电话号码。 | true | true | false | \[不设置\] | \[不设置\] | 
| 2 | * 在 SuccessFactors 中，业务电子邮件和业务电话是主要联系方式 <br> * 始终将 Azure AD 电话号码传送到业务电话，并将移动电话传送到手机。 | true | true | false | telephoneNumber | mobile | 
| 3 | * 在 SuccessFactors 中，业务电子邮件和手机是主要联系方式 <br> * 始终将 Azure AD 电话号码传送到业务电话，并将移动电话传送到手机 | true | false | true |  telephoneNumber | mobile | 
| 4 | * 在 SuccessFactors 中，业务电子邮件是主要联系方式 <br> * 在 Azure AD 中，检查工作电话号码是否存在，如果存在，则检查移动电话号码是否也存在；仅当移动电话号码不存在时，才将工作电话号码标记为主要联系方式。 | 是 | 使用表达式映射：`IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | 使用表达式映射：`IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobile | 
| 5 | * 在 SuccessFactors 中，业务电子邮件和业务电话是主要联系方式。 <br> * 在 Azure AD 中，如果移动电话可用，请将其设置为业务电话，否则请使用 telephoneNumber。 | true | true | false | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[不设置\] | 

* 如果写回特性映射中没有电话号码的映射，则写回中仅包括电子邮件。
* 在 Employee Central 中加入新员工时，业务电子邮件和电话号码可能不可用。 如果在加入期间必须将业务电子邮件和业务电话设置为主要联系方式，可以在创建新员工期间为业务电话和电子邮件设置一个虚构值，写回应用最终会更新此值。
 
### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>电话和电子邮件写回不支持的方案

* 在 Employee Central 中加入新员工期间，个人电子邮件和个人电话将设置为主要联系方式。 写回应用无法切换此设置，因此无法将业务电子邮件和业务电话设置为主要联系方式。
* 在 Employee Central 中，业务电话将设置为主要联系方式。 写回应用无法更改此设置，因此无法将手机设置为主要联系方式。
* 写回应用无法读取当前的主要标志设置，因此无法为写入操作使用相同的值。 将始终使用特性映射中配置的标志值。 

## <a name="next-steps"></a>后续步骤

* [了解如何配置 SuccessFactors 到 Active Directory 的预配](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [了解如何配置到 SuccessFactors 的写回](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [详细了解入站预配支持的 SuccessFactors 属性](sap-successfactors-attribute-reference.md)










