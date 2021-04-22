---
title: Azure Active Directory 和 Workday 集成参考
description: 技术深入探讨 Workday-HR 驱动的预配
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 02/09/2021
ms.author: chmutali
ms.openlocfilehash: 2b1a43ee6b13d32c0eaed92538cf9c25405e061b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104325"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Azure Active Directory 预配与 Workday 的集成方式

[Azure Active Directory 用户预配服务](../app-provisioning/user-provisioning.md)与 [Workday HCM](https://www.workday.com) 集成，便于管理用户的标识生命周期。 Azure Active Directory 提供三个预生成集成： 

* [Workday 到本地 Active Directory 的用户预配](../saas-apps/workday-inbound-tutorial.md)
* [Workday 到 Azure Active Directory 的用户预配](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday 写回](../saas-apps/workday-writeback-tutorial.md)

本文介绍集成的工作原理，以及如何为不同的 HR 方案自定义预配行为。 

## <a name="establishing-connectivity"></a>建立连接 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>限制 Workday API 对 Azure AD 终结点的访问
Azure AD 预配服务使用基本身份验证连接到 Workday Web 服务 API 终结点。  

为了进一步保护 Azure AD 预配服务与 Workday 之间的连接，可以限制访问，使指定的集成系统用户仅从允许的 Azure AD IP 范围访问 Workday API。 请与 Workday 管理员联系，以便在 Workday 租户中完成以下配置。 

1. 下载 Azure 公有云的[最新 IP 范围](https://www.microsoft.com/download/details.aspx?id=56519)。 
1. 打开文件，搜索标记“AzureActiveDirectory” 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP 范围](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. 复制 addressPrefixes 元素中列出的所有 IP 地址范围，并使用范围生成 IP 地址列表。
1. 登录到 Workday 管理门户。 
1. 访问“维护 IP 范围”任务，为 Azure 数据中心创建新的 IP 范围。 将 IP 范围指定为逗号分隔的列表（使用 CIDR 表示法）。  
1. 访问“管理身份验证策略”任务以创建新的身份验证策略。 在身份验证策略中，使用身份验证来允许列表指定 Azure AD IP 范围以及可从此 IP 范围进行访问的安全组。 保存更改。 
1. 访问“激活所有挂起的身份验证策略更改”任务以确认更改。

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>使用受约束的安全组限制对 Workday 中工作人员数据的访问

通过[配置 Workday 集成系统用户](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday)的默认步骤可获取检索 Workday 租户中所有用户的权限。 在某些集成方案中，你可能需要限制访问权限，以便 Get_Workers API 调用仅返回且 Workday Azure AD 连接器仅处理属于特定监察组织的用户。 

可通过与 Workday 管理员协作并配置受约束的集成系统安全组来满足此要求。 有关此操作完成方法的详细信息，请参阅[此 workday 社区文章](https://community.workday.com/forums/customer-questions/620393)（需要 Workday 社区登录凭据才能查看本文）

使用受约束的 ISSG（集成系统安全组）来限制访问这一策略在以下方案中特别有用： 
* **分阶段推出方案**：拥有大型 Workday 租户，并计划分阶段推出 Workday 到 Azure AD 的自动预配。 在此方案中，建议配置受约束的 ISSG 以便只有范围内的工作人员对 Azure AD 可见，而不使用 Azure AD 范围筛选器排除不在当前阶段范围内的用户。
* **多个预配作业方案**：拥有大型 Workday 租户和多个 AD 域，每个域支持不同的业务单位/部门/公司。 为支持此拓扑，需要运行多个 Workday 到 Azure AD 预配作业，其中每个作业都预配一组特定的工作人员。 在此方案中，建议配置受约束的 ISSG 以使相关工作人员数据对 Azure AD 可见，而不使用 Azure AD 范围筛选器来排除工作人员数据。 

### <a name="workday-test-connection-query"></a>Workday 测试连接查询

若要测试与 Workday 的连接，Azure AD 发送以下 Get_Workers Workday Web 服务请求。 

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

在 Workday 驱动预配的上下文中，完全同步是指从 Workday 提取所有标识并确定要向每个工作人员对象应用哪些预配规则的过程。 首次启动预配时以及从 Azure 门户或使用图形 API 重启预配时，会发生完全同步。 

Azure AD 发送以下 Get_Workers Workday Web 服务请求来检索工作人员数据。 此查询将在 Workday 事务日志中查找所有日期有效的（自完全同步运行时间对应的日期开始的）工作人员条目。 

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
Response_Group 节点用于指定要从 Workday 提取哪些工作人员属性。 有关 Response_Group 节点中每个标志的说明，请参阅 Workday [Get_Workers API 文档](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)。 

Response_Group 节点中指定的某些标志值是基于 Workday Azure AD 预配应用程序中配置的属性来计算的。 有关用于设置标志值的条件，请参阅 *受支持的实体* 部分。 

来自 Workday 的针对上述查询的 Get_Workers 响应内容包括工作人员记录数和页面计数。

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
为检索结果集的下一页，下一个 Get_Workers 查询将页码指定为 Response_Filter 中的参数 。

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Azure AD 预配服务将处理每个页面，并在完全同步期间循环访问所有有效的工作人员。对于从 Workday 导入的每个工作人员条目：
* 应用 [XPATH 表达式](workday-attribute-reference.md)以从 Workday 检索属性。
* 应用属性映射和匹配规则，以及 
* 服务确定要在目标 (Azure AD/AD) 中执行的操作。 

处理完成后，它会将与完全同步的起始时间相关的时间戳作为水印保存。 此水印用作增量同步周期的起点。 

## <a name="how-incremental-sync-works"></a>增量同步的工作原理

完全同步后，Azure AD 预配服务会维护 `LastExecutionTimestamp` 并用它来创建增量查询以检索增量更改。 在增量同步期间，Azure AD 将以下类型的查询发送到 Workday： 

* [查询手动更新](#query-for-manual-updates)
* [查询有效日期更新和终止](#query-for-effective-dated-updates-and-terminations)
* [查询未来日期入职者](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>查询手动更新

以下 Get_Workers 请求将查询在上次执行时间与当前执行时间之间发生的手动更新。 

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

### <a name="query-for-effective-dated-updates-and-terminations"></a>查询有效日期更新和终止

以下 Get_Workers 请求将查询在上次执行时间与当前执行时间之间发生的有效日期更新。 

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

### <a name="query-for-future-dated-hires"></a>查询未来日期的聘用信息

如果上述任何查询返回了未来日期的聘用信息，则使用以下 Get_Workers 请求提取有关未来日期的新聘用的信息。 新聘用信息的 WID 属性用于执行查找，并且有效日期设置为入职日期和时间。 

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

## <a name="retrieving-worker-data-attributes"></a>正在检索工作人员数据属性

Get_Workers API 可以返回与工作人员关联的不同数据集。 Azure AD 预配服务根据预配架构中配置的 [XPATH API 表达式](workday-attribute-reference.md)确定要从 Workday 中检索的数据集。 相应地，Response_Group 标志在 Get_Workers 请求中设置 。 

下表提供了有关用于检索特定数据集的映射配置指导。 

| \# | Workday 实体                       | 默认包括 | 在映射中指定以提取非默认实体的 XPATH 模式             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | 个人数据                        | 是                 | wd:Worker\_Data/wd:Personal\_Data                                             |
| 2  | 雇佣数据                      | 是                 | wd:Worker\_Data/wd:Employment\_Data                                           |
| 3  | 其他作业数据                  | 是                 | wd:Worker\_Data/wd:Employment\_Data/wd:Worker\_Job\_Data\[@wd:Primary\_Job=0\]|
| 4  | 组织数据                    | 是                 | wd:Worker\_Data/wd:Organization\_Data                                         |
| 5  | 管理链数据                | 是                 | wd:Worker\_Data/wd:Management\_Chain\_Data                                    |
| 6  | 监督组织             | 是                 | 'SUPERVISORY'                                                                 |
| 7  | Company                              | 是                 | 'COMPANY'                                                                     |
| 8  | 业务部门                        | 否                  | 'BUSINESS\_UNIT'                                                              |
| 9  | 业务部门层次结构              | 否                  | 'BUSINESS\_UNIT\_HIERARCHY'                                                   |
| 10 | 公司层次结构                    | 否                  | 'COMPANY\_HIERARCHY'                                                          |
| 11 | Cost Center                          | 否                  | 'COST\_CENTER'                                                                |
| 12 | 成本中心层次结构                | 否                  | 'COST\_CENTER\_HIERARCHY'                                                     |
| 13 | 资金                                 | 否                  | 'FUND'                                                                        |
| 14 | 资金层次结构                       | 否                  | 'FUND\_HIERARCHY'                                                             |
| 15 | 礼品                                 | 否                  | 'GIFT'                                                                        |
| 16 | 礼品层次结构                       | 否                  | 'GIFT\_HIERARCHY'                                                             |
| 17 | 授予                                | 否                  | 'GRANT'                                                                       |
| 18 | 授权层次结构                      | 否                  | 'GRANT\_HIERARCHY'                                                            |
| 19 | 企业站点层次结构              | 否                  | 'BUSINESS\_SITE\_HIERARCHY'                                                   |
| 20 | 矩阵组织                  | 否                  | 'MATRIX'                                                                      |
| 21 | 支付组                            | 否                  | 'PAY\_GROUP'                                                                  |
| 22 | Programs                             | 否                  | 'PROGRAMS'                                                                    |
| 23 | 程序层次结构                    | 否                  | 'PROGRAM\_HIERARCHY'                                                          |
| 24 | 区域                               | 否                  | 'REGION\_HIERARCHY'                                                           |
| 25 | 位置层次结构                   | 否                  | 'LOCATION\_HIERARCHY'                                                         |
| 26 | 帐户预配数据            | 否                  | wd:Worker\_Data/wd:Account\_Provisioning\_Data                                |
| 27 | 背景检查数据                | 否                  | wd:Worker\_Data/wd:Background\_Check\_Data                                    |
| 28 | 福利资格数据             | 否                  | wd:Worker\_Data/wd:Benefit\_Eligibility\_Data                                 |
| 29 | 权益注册数据              | 否                  | wd:Worker\_Data/wd:Benefit\_Enrollment\_Data                                  |
| 30 | 职业数据                          | 否                  | wd:Worker\_Data/wd:Career\_Data                                               |
| 31 | 补偿数据                    | 否                  | wd:Worker\_Data/wd:Compensation\_Data                                         |
| 32 | 临时工税务机构数据 | 否                  | wd:Worker\_Data/wd:Contingent\_Worker\_Tax\_Authority\_Form\_Type\_Data       |
| 33 | 开发项数据                | 否                  | wd:Worker\_Data/wd:Development\_Item\_Data                                    |
| 34 | 员工合同数据              | 否                  | wd:Worker\_Data/wd:Employee\_Contracts\_Data                                  |
| 35 | 员工评审数据                 | 否                  | wd:Worker\_Data/wd:Employee\_Review\_Data                                     |
| 36 | 反馈收到的数据               | 否                  | wd:Worker\_Data/wd:Feedback\_Received\_Data                                   |
| 37 | 工作人员目标数据                     | 否                  | wd:Worker\_Data/wd:Worker\_Goal\_Data                                         |
| 38 | 照片数据                           | 否                  | wd:Worker\_Data/wd:Photo\_Data                                                |
| 39 | 限定数据                   | 否                  | wd:Worker\_Data/wd:Qualification\_Data                                        |
| 40 | 相关人员数据                 | 否                  | wd:Worker\_Data/wd:Related\_Persons\_Data                                     |
| 41 | 角色数据                            | 否                  | wd:Worker\_Data/wd:Role\_Data                                                 |
| 42 | 技能数据                           | 否                  | wd:Worker\_Data/wd:Skill\_Data                                                |
| 43 | 连续配置文件数据              | 否                  | wd:Worker\_Data/wd:Succession\_Profile\_Data                                  |
| 44 | 人才评估数据               | 否                  | wd:Worker\_Data/wd:Talent\_Assessment\_Data                                   |
| 45 | 用户帐户数据                    | 否                  | wd:Worker\_Data/wd:User\_Account\_Data                                        |
| 46 | 工作人员文档数据                 | 否                  | wd:Worker\_Data/wd:Worker\_Document\_Data                                     |

>[!NOTE]
>表中列出的每个 Workday 实体都受 Workday 中的“域安全策略”保护。 如果在设置正确的 XPATH 后无法检索与实体关联的任何属性，请与 Workday 管理员联系，以确保为与预配应用相关联的集成系统用户配置相应的域安全策略。 例如，若要检索技术数据，需要具有对 Workday 域“工作人员数据: 技能和经验”的 Get 访问权限  。 

下面是有关如何扩展 Workday 集成以满足特定要求的一些示例。 

**示例 1**

假设你想要从 Workday 检索以下数据集，并将其用于预配规则：

* 成本中心
* 成本中心层次结构
* 支付组

默认情况下，不包括上述数据集。 检索这些数据集：
1. 登录到 Azure 门户并打开 Workday 到 AD/Azure AD 用户预配应用。 
1. 在“预配”边栏选项卡中，编辑映射，并从高级部分打开 Workday 属性列表。 
1. 添加以下属性定义，并将其标记为“必需”。 这些属性不会映射到 AD 或 Azure AD 中的任何属性。 他们只是充当向连接器指示的信号，以便检索“成本中心”、“成本中心层次结构”和“支付组”信息。 

     > [!div class="mx-tdCol2BreakAll"]
     >| 属性名称 | XPATH API 表达式 |
     >|---|---|
     >| CostCenterHierarchyFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='COST_CENTER_HIERARCHY']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='COST_CENTER']/wd:Organization_Data/wd:Organization_Code/text() |
     >| PayGroupFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='PAY_GROUP']/wd:Organization_Data/wd:Organization_Reference_ID/text() |

1. Get_Workers 响应中提供“成本中心”和“支付组”数据集后，可以使用以下 XPATH 值检索成本中心名称、成本中心代码和支付组。 

     > [!div class="mx-tdCol2BreakAll"]
     >| 属性名称 | XPATH API 表达式 |
     >|---|---|
     >| CostCenterName  | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Cost Center']/wd:Organization_Name/text() |
     >| CostCenterCode | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Cost Center']/wd:Organization_Code/text() |
     >| PayGroup | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Pay Group']/wd:Organization_Name/text() |

**示例 2**

假设你想要检索与用户关联的认证。 此信息是限定数据集的一部分。 若要将此数据集作为 Get_Workers 响应的一部分获取，请使用以下 XPATH： 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

**示例 3**

假设你要检索分配给某个工作人员的预配组。 此信息是帐户预配数据集的一部分。 若要将此数据集作为 Get_Workers 响应的一部分获取，请使用以下 XPATH： 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="handling-different-hr-scenarios"></a>处理不同的 HR 方案

### <a name="retrieving-international-job-assignments-and-secondary-job-details"></a>检索国际作业分配和辅助作业详细信息

默认情况下，Workday 连接器检索与工作人员的主作业相关联的属性。 连接器还支持检索与国际作业分配或辅助作业关联的其他作业数据。 

使用以下步骤检索与国际作业分配关联的属性： 

1. 将 Workday 连接 URL 设置为使用 Workday Web Service API 版本 30.0 或更高版本。 在 Workday 预配应用中相应地设置[正确的 XPATH 值](workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)。 
1. 使用 `Worker_Job_Data` 节点上的选择器 `@wd:Primary_Job=0` 以检索正确的属性。 
   * **示例1：** 将 `SecondaryBusinessTitle` 设置为使用 XPATH `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Title/text()`
   * **示例2：** 将 `SecondaryBusinessLocation` 设置为使用 XPATH `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Location_Reference/@wd:Descriptor`

 

## <a name="next-steps"></a>后续步骤

* [了解如何配置 Workday 到 Active Directory 预配](../saas-apps/workday-inbound-tutorial.md)
* [了解如何配置回写到 Workday](../saas-apps/workday-writeback-tutorial.md)
* [详细了解入站预配支持的 Workday 特性](workday-attribute-reference.md)

