---
title: Azure Active Directory 和 Workday 集成参考
description: 技术深入探讨 Workday-HR 驱动的预配
services: active-directory
author: cmmdesai
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 01/18/2021
ms.author: chmutali
ms.openlocfilehash: 251e1d4249373ec52afb3d7edaa2325c992b66f1
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98570153"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Azure Active Directory 预配与 Workday 的集成方式

[Azure Active Directory 用户预配服务](../app-provisioning/user-provisioning.md) 与 [Workday HCM](https://www.workday.com) 集成，以管理用户的标识生命周期。 Azure Active Directory 提供三个预先构建的集成： 

* [Workday 到本地 Active Directory 用户预配](../saas-apps/workday-inbound-tutorial.md)
* [Workday 到 Azure Active Directory 用户预配](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday 写回](../saas-apps/workday-writeback-tutorial.md)

本文介绍集成的工作原理，以及如何为不同的 HR 方案自定义设置行为。 

## <a name="establishing-connectivity"></a>建立连接 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>限制 Workday API 对 Azure AD 终结点的访问
Azure AD 预配服务使用基本身份验证连接到 Workday Web 服务 API 终结点。  

为了进一步保护 Azure AD 预配服务与 Workday 之间的连接，可以限制访问，使指定的 integration system 用户仅从允许的 Azure AD IP 范围访问 Workday Api。 请与你的 Workday 管理员联系，以在你的 Workday 租户中完成以下配置。 

1. 下载 Azure 公有云的 [最新 IP 范围](https://www.microsoft.com/download/details.aspx?id=56519) 。 
1. 打开该文件并搜索标记 **AzureActiveDirectory** 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP 范围](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. 复制元素 *addressPrefixes* 中列出的所有 IP 地址范围，并使用范围生成 IP 地址列表。
1. 登录到 Workday 管理门户。 
1. 访问 " **维护 IP 范围** " 任务，为 Azure 数据中心创建新的 IP 范围。 使用 CIDR 表示法指定 IP 范围 (以逗号分隔的列表形式) 。  
1. 访问 " **管理身份验证策略** " 任务以创建新的身份验证策略。 在 "身份验证策略" 中，使用 " **身份验证" 白名单** 指定 Azure AD ip 范围和允许从此 ip 范围进行访问的安全组。 保存更改。 
1. 访问 " **激活所有挂起的身份验证策略更改** " 任务以确认更改。

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>使用受约束的安全组限制对 Workday 中辅助角色数据的访问

[配置 workday 集成系统用户](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday)的默认步骤将授予访问权限，以检索 Workday 租户中的所有用户。 在某些集成方案中，你可能想要限制访问权限，以便 Get_Workers API 调用返回仅属于特定监察组织的用户，并由 Workday Azure AD 连接器进行处理。 

你可以通过使用 Workday 管理员并配置受约束的集成系统安全组来满足此要求。 有关此操作的详细信息，请参阅 [此 workday 社区文章](https://community.workday.com/forums/customer-questions/620393) (*需要 workday 社区登录凭据才能访问* 本文) 

此策略使用受约束的 ISSG (集成系统安全组来限制访问) 在以下情况下特别有用： 
* **分阶段推出方案**：拥有大型 workday 租户，并计划执行 Workday 分阶段推出以 Azure AD 自动预配。 在此方案中，我们建议配置受约束的 ISSG，而不是将不在当前阶段范围内的用户排除在 Azure AD 范围筛选器中，以便只有范围内的工作线程对 Azure AD 可见。
* **多个预配作业方案**：拥有一个大型 Workday 租户和多个 AD 域，每个域支持不同的业务单位/部门/公司。 若要支持此拓扑，你需要运行多个 Workday 来 Azure AD 设置作业，每个作业都预配一组特定的辅助角色。 在这种情况下，我们建议配置受约束的 ISSG，以使相关辅助角色数据对 Azure AD 可见，而不是使用 Azure AD 范围筛选器来排除辅助数据。 

### <a name="workday-test-connection-query"></a>Workday 测试连接查询

若要测试与 Workday 的连接，Azure AD 发送以下 *Get_Workers* Workday Web Services 请求。 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>完全同步的工作原理

Workday 驱动预配的上下文中的 **完全同步** 是指从 workday 提取所有标识并确定要应用到每个辅助角色对象的预配规则的过程。 当你首次打开预配时，以及在从 Azure 门户或使用图形 Api *重新启动预* 配时，将发生完全同步。 

Azure AD 发送以下 *Get_Workers* Workday Web Services 请求来检索辅助角色数据。 此查询将查找与完全同步运行对应的时间起的所有有效辅助角色条目的 Workday 事务日志。 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
" *Response_Group* " 节点用于指定从 Workday 提取哪些辅助属性。 有关 *Response_Group* 节点中每个标志的说明，请参阅 WORKDAY [Get_Workers API 文档](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)。 

*Response_Group* "节点中指定的某些标志值基于 Workday Azure AD 预配应用程序中配置的属性进行计算。 有关用于设置标志值的条件，请参阅 *支持的实体* 部分。 

针对上述查询的 Workday 的 *Get_Workers* 响应包括辅助角色记录数和页面计数。

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
为了检索结果集的下一页，下一个 *Get_Workers* 查询将页码指定为 *Response_Filter* 中的参数。

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Azure AD 预配服务将处理每个页面，并在完全同步期间遍历所有有效的工作线程。对于从 Workday 导入的每个辅助角色条目：
* [XPATH 表达式](workday-attribute-reference.md)应用于从 Workday 检索属性值。
* 应用属性映射和匹配规则，并 
* 服务确定要在目标 (Azure AD/AD) 中执行的操作。 

处理完成后，它会将与完全同步开始相关的时间戳作为水印进行保存。 此水印用作增量同步周期的起点。 

## <a name="how-incremental-sync-works"></a>增量同步的工作原理

完全同步后，Azure AD 预配服务 `LastExecutionTimestamp` 将维护并使用它来创建增量查询以检索增量更改。 在增量同步期间，Azure AD 将以下类型的查询发送到 Workday： 

* [查询手动更新](#query-for-manual-updates)
* [查询有效的更新和终止](#query-for-effective-dated-updates-and-terminations)
* [查询未来招聘日期](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>查询手动更新

以下 *Get_Workers* 请求查询在上次执行与当前执行时间之间发生的手动更新。 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>查询有效的更新和终止

以下 *Get_Workers* 请求查询在上次执行与当前执行时间之间发生的有效更新。 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>查询未来招聘日期

如果上述任何查询返回了将来过期的员工，则使用以下 *Get_Workers* 请求来提取有关未来的新员工的信息。 新雇用的 *WID* 属性用于执行查找，并且生效日期设置为雇用日期和时间。 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="retrieving-worker-data-attributes"></a>正在检索辅助数据属性

*Get_Workers* API 可以返回与辅助角色关联的不同数据集。 根据设置架构中配置的 [XPATH API 表达式](workday-attribute-reference.md) ，Azure AD 预配服务将确定要从 Workday 中检索的数据集。 相应地， *Response_Group* 标志是在 *Get_Workers* 请求中设置的。 

下表提供了有关用于检索特定数据集的映射配置的指导。 

| \# | Workday 实体                       | 默认包括 | 用于在映射中指定以提取非默认实体的 XPATH 模式             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | 个人数据                        | 是                 | wd： Worker \_ data/wd：个人 \_ 数据                                             |
| 2  | 雇用数据                      | 是                 | wd： Worker \_ data/wd：招聘 \_ 数据                                           |
| 3  | 其他作业数据                  | 是                 | wd： worker \_ data/wd：招聘 \_ 数据/wd：工作人员 \_ 作业 \_ 数据 \[ @wd:Primary \_ 作业 = 0\]|
| 4  | 组织数据                    | 是                 | wd： Worker \_ data/wd：组织 \_ 数据                                         |
| 5  | 管理链数据                | 是                 | wd： Worker \_ data/wd：管理 \_ 链 \_ 数据                                    |
| 6  | 监督组织             | 是                 | 监控                                                                 |
| 7  | Company                              | 是                 | 上市公司                                                                     |
| 8  | 业务部门                        | 否                  | "业务 \_ 单元"                                                              |
| 9  | 业务部门层次结构              | 否                  | "业务 \_ 部门 \_ 层次结构"                                                   |
| 10 | 公司层次结构                    | 否                  | "公司 \_ 层次结构"                                                          |
| 11 | Cost Center                          | 否                  | "成本 \_ 中心"                                                                |
| 12 | 成本中心层次结构                | 否                  | "成本 \_ 中心 \_ 层次结构"                                                     |
| 13 | '                                 | 否                  | '                                                                        |
| 14 | 资金层次结构                       | 否                  | "资金 \_ 层次结构"                                                             |
| 15 | 送                                 | 否                  | 送                                                                        |
| 16 | 礼品层次结构                       | 否                  | "礼品 \_ 层次结构"                                                             |
| 17 | 授予                                | 否                  | 准予                                                                       |
| 18 | 授权层次结构                      | 否                  | "GRANT \_ 层次结构"                                                            |
| 19 | 企业站点层次结构              | 否                  | "业务 \_ 站点 \_ 层次结构"                                                   |
| 20 | 矩阵组织                  | 否                  | 矩阵                                                                      |
| 21 | 支付组                            | 否                  | "支付 \_ 组"                                                                  |
| 22 | 计划                             | 否                  | 程序                                                                    |
| 23 | 程序层次结构                    | 否                  | "项目 \_ 层次结构"                                                          |
| 24 | 区域                               | 否                  | "区域 \_ 层次结构"                                                           |
| 25 | 位置层次结构                   | 否                  | "位置 \_ 层次结构"                                                         |
| 26 | 帐户预配数据            | 否                  | wd： Worker \_ data/wd：帐户 \_ 预配 \_ 数据                                |
| 27 | 背景检查数据                | 否                  | wd： Worker \_ data/wd：背景 \_ 检查 \_ 数据                                    |
| 28 | 福利资格数据             | 否                  | wd： Worker \_ data/wd：福利 \_ 资格 \_ 数据                                 |
| 29 | 权益注册数据              | 否                  | wd： Worker \_ data/wd：福利 \_ 注册 \_ 数据                                  |
| 30 | 职业数据                          | 否                  | wd： Worker \_ data/wd：职业 \_ 数据                                               |
| 31 | 补偿数据                    | 否                  | wd： Worker \_ data/wd：补偿 \_ 数据                                         |
| 32 | 临时工作人员税务主管机构数据 | 否                  | wd： Worker \_ Data/wd：临时 \_ 工作人员 \_ 税务 \_ 主管机构 \_ \_ 类型 \_ 数据       |
| 33 | 开发项数据                | 否                  | wd： Worker \_ data/wd：开发 \_ 项 \_ 数据                                    |
| 34 | 员工合同数据              | 否                  | wd： Worker \_ data/wd：员工 \_ 合同 \_ 数据                                  |
| 35 | 员工评审数据                 | 否                  | wd： Worker \_ data/wd：员工 \_ 评审 \_ 数据                                     |
| 36 | 反馈收到的数据               | 否                  | wd： Worker \_ data/wd：反馈 \_ 收到的 \_ 数据                                   |
| 37 | 辅助角色目标数据                     | 否                  | wd： Worker \_ data/wd：辅助角色 \_ 目标 \_ 数据                                         |
| 38 | 照片数据                           | 否                  | wd： Worker \_ data/wd：照片 \_ 数据                                                |
| 39 | 限定数据                   | 否                  | wd： Worker \_ data/wd：限定 \_ 数据                                        |
| 40 | 相关人员数据                 | 否                  | wd： Worker \_ data/wd：相关 \_ 人员 \_ 数据                                     |
| 41 | 角色数据                            | 否                  | wd： Worker \_ data/wd：角色 \_ 数据                                                 |
| 42 | 技能数据                           | 否                  | wd： Worker \_ data/wd：技能 \_ 数据                                                |
| 43 | 连续配置文件数据              | 否                  | wd： Worker \_ data/wd：连续 \_ 配置文件 \_ 数据                                  |
| 44 | 人才评估数据               | 否                  | wd： Worker \_ data/wd：人才 \_ 评估 \_ 数据                                   |
| 45 | 用户帐户数据                    | 否                  | wd： Worker \_ data/wd：用户 \_ 帐户 \_ 数据                                        |
| 46 | 工作线程文档数据                 | 否                  | wd： Worker \_ data/wd： worker \_ 文档 \_ 数据                                     |

下面是有关如何扩展 Workday 集成以满足特定要求的一些示例。 

**示例 1**

假设你想要从 Workday 检索以下数据集，并将其用于预配规则：

* 成本中心
* 成本中心层次结构
* 支付组

默认情况下，不包括上述数据集。 检索这些数据集：
1. 登录到 Azure 门户并打开 Workday 到 AD/Azure AD 用户预配应用。 
1. 在 "设置" 边栏选项卡中，编辑映射，并从 "高级" 部分打开 Workday 属性列表。 
1. 添加以下属性定义，并将其标记为 "必需"。 这些属性不会映射到 AD 或 Azure AD 中的任何属性。 他们只是将信号发送到连接器，以便检索成本中心、成本中心层次结构和支付组信息。 

     > [!div class="mx-tdCol2BreakAll"]
     >| 属性名称 | XPATH API 表达式 |
     >|---|---|
     >| CostCenterHierarchyFlag  |  wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data [wd： Organization_Data/wd： Organization_Type_Reference/wd： ID [ @wd:type = ' Organization_Type_ID '] = ' COST_CENTER_HIERARCHY ']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data [wd： Organization_Data/wd： Organization_Type_Reference/wd： ID [ @wd:type = ' Organization_Type_ID "] = ' COST_CENTER ']/wd： Organization_Data/wd： Organization_Code/text ( # A1 |
     >| PayGroupFlag  |  wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data [wd： Organization_Data/wd： Organization_Type_Reference/wd： ID [ @wd:type = ' Organization_Type_ID "] = ' PAY_GROUP ']/wd： Organization_Data/wd： Organization_Reference_ID/text ( # A1 |

1. 在 *Get_Workers* 响应中提供 "成本中心" 和 "支付组" 数据集后，可以使用以下 XPATH 值检索成本中心名称、成本中心代码和支付组。 

     > [!div class="mx-tdCol2BreakAll"]
     >| 属性名称 | XPATH API 表达式 |
     >|---|---|
     >| CostCenterName  | wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data/wd： Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = "成本中心"]/wd： Organization_Name/text ( # A1 |
     >| CostCenterCode | wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data/wd： Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = "成本中心"]/wd： Organization_Code/text ( # A1 |
     >| PayGroup | wd： Worker/wd： Worker_Data/wd： Organization_Data/wd： Worker_Organization_Data/wd： Organization_Data [ wd:Organization_Type_Reference/@wd:Descriptor = "支付组"]/wd： Organization_Name/text ( # A1 |

**示例 2**

假设你想要检索与用户关联的认证。 此信息作为 *限定数据* 集的一部分提供。 若要将此数据集作为 *Get_Workers* 响应的一部分，请使用以下 XPATH： 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**示例 3**

假设您要检索分配给某个辅助角色的 *预配组* 。 此信息作为 *帐户预配数据* 集的一部分提供。 若要将此数据集作为 *Get_Workers* 响应的一部分，请使用以下 XPATH： 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="next-steps"></a>后续步骤

* [了解如何配置 Workday 以 Active Directory 预配](../saas-apps/workday-inbound-tutorial.md)
* [了解如何配置回发到 Workday](../saas-apps/workday-writeback-tutorial.md)
* [详细了解入站预配支持的 Workday 特性](workday-attribute-reference.md)

