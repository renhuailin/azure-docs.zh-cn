---
title: Azure Sentinel SAP 解决方案 - 安全内容参考 | Microsoft Docs
description: 了解 Azure Sentinel SAP 解决方案提供的内置安全内容。
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 07/28/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: bec0a6c872077d3982ddaf6e0ffc7e96ec7f54ee
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183494"
---
# <a name="azure-sentinel-sap-solution-security-content-reference-public-preview"></a>Azure Sentinel SAP 解决方案：安全内容参考（公共预览版）

本文详细介绍适用于 [Azure Sentinel SAP 解决方案](sap-deploy-solution.md#deploy-sap-security-content)的 Azure Sentinel 内容。

可用安全内容包括内置工作簿和内置分析规则。 你也可添加与 SAP 相关的[播放列表](watchlists.md)，以便在搜索、检测规则、威胁搜寻和响应 playbook 中使用。

> [!IMPORTANT]
> Azure Sentinel SAP 解决方案目前以预览版提供。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>


## <a name="built-in-workbooks"></a>内置工作簿

使用以下内置工作簿可对通过 SAP 数据连接器引入的数据进行可视化和监视。 部署 SAP 解决方案后，可以在“我的工作簿”选项卡中找到 SAP 工作簿。


|工作簿名称  |描述  |日志 |
|---------|---------|--------- |
|<a name="sap---system-applications-and-products-workbook"></a>SAP - 审核日志浏览器     |显示如下数据： <br><br>常规的系统运行状况，包括一段时间内的用户登录情况、系统引入的事件、消息类和 ID，以及已运行的 ABAP 程序 <br><br>系统中发生的事件的严重性 <br><br>系统中发生的身份验证和授权事件 |使用以下日志中的数据： <br><br>[ABAPAuditLog_CL](sap-solution-log-reference.md#abap-security-audit-log) |
|SAP - 可疑特权操作     |  显示如下数据： <br><br>敏感的和关键的分配 <br><br>对敏感特权用户进行的操作和更改 <br><br>对角色所做的更改  |使用以下日志中的数据： <br><br>[ABAPAuditLog_CL](sap-solution-log-reference.md#abap-security-audit-log) <br><br>[ABAPChangeDocsLog_CL](sap-solution-log-reference.md#abap-change-documents-log) |
|SAP - 初始访问和绕过 SAP 安全机制的尝试     |  显示如下数据： <br><br>对敏感程序、代码和函数模块的执行情况 <br><br>配置更改，包括停用日志 <br><br>在调试模式下所做的更改    |使用以下日志中的数据： <br><br>[ABAPAuditLog_CL](sap-solution-log-reference.md#abap-security-audit-log)<br><br>[ABAPTableDataLog_CL](sap-solution-log-reference.md#abap-db-table-data-log)<br><br>[Syslog](sap-solution-log-reference.md#abap-syslog) |
|SAP - 持久性和数据外泄     |    显示如下数据： <br><br>Internet Communication Framework (ICF) 服务，包括激活和停用，以及有关新服务和服务处理程序的数据 <br><br> 不安全的操作，包括函数模块和程序 <br><br>直接访问敏感表      | 使用以下日志中的数据： <br><br>[ABAPAuditLog_CL](sap-solution-log-reference.md#abap-security-audit-log) <br><br>[ABAPTableDataLog_CL](sap-solution-log-reference.md#abap-db-table-data-log)<br><br>[ABAPSpoolLog_CL](sap-solution-log-reference.md#abap-spool-log)<br><br>[ABAPSpoolOutputLog_CL](sap-solution-log-reference.md#apab-spool-output-log)<br><br>[Syslog](sap-solution-log-reference.md#abap-syslog) |
|     |         | |

有关详细信息，请参阅[教程：可视化和监视数据](monitor-your-data.md)和[部署 SAP 连续威胁监视（公共预览版）](sap-deploy-solution.md)。

## <a name="built-in-analytics-rules"></a>启用内置分析规则

下表列出了 Azure Sentinel SAP 解决方案中包含的内置[分析规则](sap-deploy-solution.md#deploy-sap-security-content)，这些规则通过 Azure Sentinel 解决方案商城部署。

### <a name="built-in-sap-analytics-rules-for-initial-access"></a>适用于初始访问的内置 SAP 分析规则

|规则名称  |说明  |源操作  |策略  |
|---------|---------|---------|---------|
|**SAP - 高 - 从意外的网络登录**     |   标识从意外网络的登录。 <br><br>在 [SAP - 网络](#networks)播放列表中维护网络。    |    从未分配给其中一个网络的 IP 地址登录后端系统。 <br><br>**数据源**：SAPcon - 审核日志    |   初始访问      |
|**SAP - 高 - SPNego 攻击**     | 标识 SPNego 重播攻击。       |  **数据源**：SAPcon - 审核日志 | 影响、横向移动  |
|**SAP - 中 - 暴力攻击**     |     根据后端系统已失败的登录尝试，标识对 SAP 系统的暴力攻击。    |   在计划的时间间隔内，尝试从同一 IP 地址登录多个系统/客户端。 <br><br>**数据源**：SAPcon - 审核日志      | 凭据访问        |
|SAP - 中 - 从同一 IP 多次登录     |  标识来自同一 IP 地址的多位用户在计划的时间间隔内的登录。   <br><br>子用例：[持久性](#built-in-sap-analytics-rules-for-persistency)    |    以多位用户身份在同一 IP 地址登录。 <br><br>**数据源**：SAPcon - 审核日志 | 初始访问        |
|**SAP - 中 - 用户多次登录**     | 标识相同用户在计划的时间间隔内从多个终端的登录。  <br><br>仅通过 Audit SAL 方法使用，适用于 SAP 7.5 及更高版本。      |   以相同用户身份从不同的 IP 地址登录。   <br><br>**数据源**：SAPcon - 审核日志   |  预攻击、凭据访问、初始访问、集合 <br><br>子用例：[持久性](#built-in-sap-analytics-rules-for-persistency)      |
|**SAP - 信息 - 生命周期 - 在系统中实现 SAP 说明**     |   标识系统中的 SAP 说明实现。 | 使用 SNOTE/TCI 实现 SAP 说明。 <br><br>**数据源**：SAPcon - 更改请求      | -  |
| | | | |

### <a name="built-in-sap-analytics-rules-for-data-exfiltration"></a>适用于数据外泄的内置 SAP 分析规则

|规则名称  |说明  |源操作  |策略  |
|---------|---------|---------|---------|
|**SAP - 中 - 非授权服务器的 FTP**     |标识非授权服务器的 FTP 连接。 | 新建 FTP 连接，例如使用 FTP_CONNECT 函数模块。 <br><br>**数据源**：SAPcon - 审核日志   |  发现、初始访问、命令和控制      |
|**SAP - 中 - 不安全的 FTP 服务器配置**     |标识不安全的 FTP 服务器配置，例如当 FTP 允许列表为空或包含占位符时。 | 请勿使用 `SAPFTP_SERVERS_V` 维护视图维护或维护包含 `SAPFTP_SERVERS` 表中占位符的值。 (SM30) <br><br>**数据源**：SAPcon - 审核日志   |  初始访问、命令和控制      |
|**SAP - 中 - 多个文件下载**     |标识用户在特定时间范围内的多个文件下载。 | 使用 SAPGui for Excel、列表等下载多个文件。 <br><br>**数据源**：SAPcon - 审核日志   |  集合、外泄、凭据访问       |
|**SAP - 中 - 多项后台处理执行**     |标识用户在特定时间范围内的多项后台处理。 | 由用户创建并运行的多个任意类型的后台处理作业。 (SP01) <br><br>**数据源**：SAPcon - 后台处理日志、SAPcon - 审核日志   |  集合、外泄、凭据访问       |
|**SAP - 中 - 多项后台处理输出执行**     |标识用户在特定时间范围内的多项后台处理。 | 由用户创建并运行的多个任意类型的后台处理作业。 (SP01) <br><br>**数据源**：SAPcon - 后台处理输出日志、SAPcon - 审核日志   |  集合、外泄、凭据访问       |
|**SAP - 中 - 通过 RFC 登录直接访问的敏感表**     |标识通过 RFC 登录访问的通用表。 <br><br> 维护 [SAP - 敏感表播放列表](#tables)中的表。<br><br> **注意**：仅适用于生产系统。   | 使用 SE11/SE16/SE16N 打开表内容。<br><br>**数据源**：SAPcon - 审核日志   |  集合、外泄、凭据访问     |
|**SAP - 中 - 后台处理接管**     |标识用户，该用户打印由其他人创建的后台处理请求。 | 由一位用户创建后台处理请求，然后由其他用户输出请求。 <br><br>**数据源**：SAPcon - 后台处理日志、SAPcon - 后台处理输出日志、SAPcon - 审核日志   |  集合、外泄、命令和控制      |
|**SAP - 低 - 动态 RFC 目标**     |   使用动态目标标识 RFC 执行。 <br><br>子用例：[绕过 SAP 安全机制的尝试](#built-in-sap-analytics-rules-for-attempts-to-bypass-sap-security-mechanisms)| 执行使用动态目标 (cl_dynamic_destination) 的 ABAP 报表。 例如，DEMO_RFC_DYNAMIC_DEST。   <br><br>**数据源**：SAPcon - 审核日志      |    集合、外泄     |
|**SAP - 低 - 通过对话框登录直接访问敏感表**     |   标识通过对话框登录访问的通用表。      |  使用 `SE11`/`SE16`/`SE16N` 打开表内容。 <br><br>**数据源**：SAPcon - 审核日志      |    发现     |
| | | | |

### <a name="built-in-sap-analytics-rules-for-persistency"></a>适用于持久性的内置 SAP 分析规则

|规则名称  |说明  |源操作  |策略  |
|---------|---------|---------|---------|
|**SAP - 高 - 激活或停用 ICF 服务**     | 标识 ICF 服务的激活或停用。       |  使用 SICF 激活服务。<br><br>**数据源**：SAPcon - 表数据日志 | 命令和控制、横向移动、持久性  |
|**SAP - 高 - 函数模块已测试**     |  标识函数模块测试。       |   使用 `SE37` / `SE80` 测试函数。  <br><br>**数据源**：SAPcon - 审核日志    |   集合、防御规避、横向移动      |
|  **SAP - 高 - HANA DB - 用户管理员操作**   | 标识用户管理操作。        |  创建、更新或删除数据库用户。 <br><br>**数据源**：Linux 代理 - Syslog*       |特权提升         |
|**SAP - 高 - 新 ICF 服务处理程序**     | 标识 ICF 处理程序创建。       |  使用 SICF 为服务分配新处理程序。<br><br>**数据源**：SAPcon - 审核日志 | 命令和控制、横向移动、持久性  |
|**SAP - 高 - 新 ICF 服务**     | 标识 ICF 服务创建。       |  使用 SICF 创建服务。<br><br>**数据源**：SAPcon - 表数据日志 | 命令和控制、横向移动、持久性  |
|**SAP - 中 - 执行过时或不安全的函数模块**     |标识已过时或不安全的 ABAP 函数模块。 <br><br>维护 [SAP - 已过时函数模块](#modules)播放列表中的已过时函数。 请确保为后端的 `EUFUNC` 表激活表记录更改。 (SE13)<br><br> **注意**：仅适用于生产系统。  | 使用 SE37 直接运行过时或不安全的函数模块。 <br><br>**数据源**：SAPcon - 表数据日志   | 发现、命令和控制 |
|**SAP - 中 - 执行已过时/不安全的程序**     |标识已过时或不安全的 ABAP 程序执行。 <br><br> 维护 [SAP - 已过时程序](#programs)播放列表中的已过时应用程序。<br><br> **注意**：仅适用于生产系统。  | 使用 SE38/SA38/SE80 或通过后台作业直接运行程序。  <br><br>**数据源**：SAPcon - 审核日志   | 发现、命令和控制 |
|**SAP - 低 - 用户进行多次密码更改**     |   标识用户执行的多次密码更改。      |   更改用户密码 <br><br>**数据源**：SAPcon - 审核日志      |    凭据访问     |
| | | | |


### <a name="built-in-sap-analytics-rules-for-attempts-to-bypass-sap-security-mechanisms"></a>适用于绕过 SAP 安全机制的尝试的内置 SAP 分析规则

|规则名称  |说明  |源操作  |策略  |
|---------|---------|---------|---------|
|**SAP - 高 - 客户端配置更改**     |     标识客户端配置更改，例如客户端角色或更改录制模式。    |  使用 `SCC4` 事务代码执行客户端配置更改。 <br><br>**数据源**：SAPcon - 审核日志  |    防御规避、外泄、持久性     |
|**SAP - 高 - 数据在调试活动期间已更改**     | 标识调试活动期间对运行时数据的更改。  <br><br>子用例：[持久性](#built-in-sap-analytics-rules-for-persistency)       | 1. 激活调试 ("/h")。 <br>2. 选择要更改的字段并更新字段值。<br><br>**数据源**：SAPcon - 审核日志        |  执行、横向移动       |
|**SAP - 高 - 停用安全审核日志**     | 标识停用安全审核日志。        |  使用 `SM19/RSAU_CONFIG` 禁用安全审核日志。 <br><br>**数据源**：SAPcon - 审核日志 |   外泄、防御规避、持久性      |
|**SAP - 高 - 执行敏感 ABAP 程序**     |标识敏感 ABAP 程序的直接执行。 <br><br>在 [SAP - 敏感 ABAP 程序](#programs)播放列表中维护 ABAP 程序。        | 使用 `SE38`/`SA38`/`SE80` 直接运行程序。 <br> <br>**数据源**：SAPcon - 审核日志      |     外泄、横向移动、执行    |
|**SAP - 高 - 执行敏感事务代码**     | 标识敏感事务代码的执行。 <br><br>在 [SAP - 敏感事务代码](#transactions)播放列表中维护事务代码。       |  运行敏感事务代码。 <br><br>**数据源**：SAPcon - 审核日志      |    发现、执行     |
|**SAP - 高 - 执行敏感函数模块**     | 标识敏感 ABAP 函数模块的执行。 <br><br>子用例：[持久性](#built-in-sap-analytics-rules-for-persistency)<br><br>**注意**：仅适用于生产系统。 <br><br>维护 [SAP - 敏感函数模块](#modules)播放列表中的敏感函数，并确保在 EUFUNC 表的后端激活表日志记录更改。 (SE13)     | 使用 SE37 直接运行敏感函数模块。 <br><br>**数据源**：SAPcon - 表数据日志 | 发现、命令和控制 
|**SAP - 高 - HANA DB - 审核线索策略更改**     |  标识 HANA DB 审核线索策略更改。       |     在安全定义中创建或更新现有审核策略。 <br> <br>**数据源**：Linux 代理 - Syslog    |  横向移动、防御规避、持久性       |
|**SAP - 高 - HANA DB - 停用审核线索**     |    标识 HANA DB 审核日志停用。     |    停用 HANA DB 安全定义中的审核日志。 <br><br>**数据源**：Linux 代理 - Syslog     |  持久性、横向移动、防御规避       |
|**SAP - 高 - 敏感函数模块的 RFC 执行**     | 要用于相关检测的敏感函数模型。    <br><br>在 [SAP - 敏感函数模块](#module)播放列表中维护函数模块。   |      使用 RFC 运行函数模块。  <br><br>**数据源**：SAPcon - 审核日志 |  执行、横向移动、发现       |
|**SAP - 系统配置更改**     | 标识系统配置更改。        |   使用 `SE06` 事务代码来调整系统更改选项或软件组件修改。<br><br>**数据源**：SAPcon - 审核日志 |外泄、防御规避、持久性   |
|**SAP - 中 - 调试活动**     |  标识所有调试相关活动。 <br><br>子用例：[持久性](#built-in-sap-analytics-rules-for-persistency)      |在系统中激活调试 ("/h")、调试活动进程、向源代码添加断点等。 <br><br>**数据源**：SAPcon - 审核日志 |   发现      |
|**SAP - 中 - 安全审核日志配置更改**     |  标识安全审核日志中的配置更改       |   使用 `SM19`/`RSAU_CONFIG`（例如筛选器、状态、录制模式等）更改任何安全审核日志配置。 <br><br>**数据源**：SAPcon - 审核日志      |    持久性、外泄、防御规避     |
|**SAP - 中 - 事务已解锁**     |标识事务解锁。         |     使用 `SM01`/`SM01_DEV`/`SM01_CUS` 解锁事务代码。 <br><br>**数据源**：SAPcon - 审核日志   |  持久性、执行       |
|**SAP - 低 - 动态 ABAP 程序**     | 标识动态 ABAP 编程的执行。 例如，动态创建、更改或删除 ABAP 代码时。 <br><br> 维护 [SAP - ABAP 生成事务](#transactions)播放列表中排除的事务代码。 | 创建使用 ABAP 程序生成命令（例如 INSERT REPORT）的 ABAP 报表，然后运行报表。  <br><br>**数据源**：SAPcon - 审核日志      |    发现、命令和控制、影响     |
| | | | |

### <a name="built-in-sap-analytics-rules-for-suspicious-privileges-operations"></a>适用于可疑特权操作的内置 SAP 分析规则

|规则名称  |说明  |源操作  |策略  |
|---------|---------|---------|---------|
|**SAP - 高 - 敏感特权用户更改**     |  标识敏感特权用户更改。    <br> <br>在 [SAP - 特权用户](#users)播放列表中维护特权用户。 |   使用 `SU01` 更改用户详细信息/授权。 <br><br>**数据源**：SAPcon - 审核日志     |  特权提升、凭据访问       |
|**SAP - 高 - HANA DB - 分配管理员授权**     |   标识管理员权限或角色分配。      |  分配具有任何管理员角色或权限的用户。  <br><br>**数据源**：Linux 代理 - Syslog     | 特权提升        |
|**SAP - 高 - 已登录的敏感特权用户**     |    标识敏感特权用户的对话框登录。 <br><br>在 [SAP - 特权用户](#users)播放列表中维护特权用户。    |  使用 `SAP*` 或其他特权用户登录后端系统。  <br><br>**数据源**：SAPcon - 审核日志     |   初始访问、凭据访问      |
|  **SAP - 高 - 敏感特权用户对其他用户进行更改**   |   标识敏感特权用户对其他用户的更改。       | 使用 SU01 更改用户详细信息/授权。  <br><br>**数据源**：SAPcon - 审核日志     |   特权提升、凭据访问       |
|**SAP - 高 - 敏感用户密码更改和登录**     | 标识特权用户的密码更改。      |  为特权用户更改密码并登录系统。 <br>在 [SAP - 特权用户](#users)播放列表中维护特权用户。<br><br>**数据源**：SAPcon - 审核日志 | 影响、命令和控制、特权提升 |
|**SAP - 高 - 用户创建并使用新用户**     | 标识创建和使用其他用户的用户。  <br><br>子用例：[持久性](#built-in-sap-analytics-rules-for-persistency)      |  使用 SU01 创建一个用户，然后使用新建用户和相同的 IP 地址登录。<br><br>**数据源**：SAPcon - 审核日志 | 发现、预攻击、初始访问  |
|**SAP - 高 - 用户解锁并使用其他用户**     |标识由其他用户解锁和使用的用户。   <br><br>子用例：[持久性](#built-in-sap-analytics-rules-for-persistency)    |  使用 SU01 解锁用户，然后使用已解锁用户和相同的 IP 地址登录。<br><br>**数据源**：SAPcon - 审核日志、SAPcon - 更改文档日志 | 发现、预攻击、初始访问、横向移动  |
|**SAP - 中 - 敏感配置文件分配**     |  标识对用户新分配的敏感配置文件。 <br><br>维护 [SAP - 敏感配置文件](#profiles)播放列表中的敏感配置文件。      |    使用 `SU01` 向用户分配配置文件。 <br><br>**数据源**：SAPcon - 更改文档日志    |  特权提升       |
|**SAP - 中 - 敏感角色分配**     |    标识向用户新分配的敏感角色。     <br><br>维护 [SAP - 敏感角色](#roles)播放列表中的敏感角色。|  使用 `SU01` / `PFCG` 向用户分配角色。 <br><br>**数据源**：SAPcon - 更改文档日志、审核日志     |   特权提升      |
|**SAP - 中 - 关键授权分配 - 新授权值**     | 标识向新用户分配的关键授权对象值。  <br><br>维护 [SAP - 关键授权对象](#objects)播放列表中的关键授权对象。      |  使用 `PFCG` 在角色中分配新授权对象或更新现有授权对象。 <br><br>**数据源**：SAPcon - 更改文档日志      |     特权提升    |
|**SAP - 中 - 关键授权分配 - 新用户分配**     |  标识向新用户分配的关键授权对象值。 <br><br>维护 [SAP - 关键授权对象](#objects)播放列表中的关键授权对象。    |    使用 `SU01`/`PFCG` 将新用户分配给具有关键授权值的角色。 <br><br>**数据源**：SAPcon - 更改文档日志    |  特权提升       |
|**SAP - 中 - 敏感角色更改**     |标识敏感角色更改。 <br><br> 维护 [SAP - 敏感角色](#roles)播放列表中的敏感角色。       |  使用 PFCG 更改角色。 <br><br>**数据源**：SAPcon - 更改文档日志、SAPcon - 审核日志   |  影响、特权提升、持久性    |
| | | | |


## <a name="available-watchlists"></a>可用播放列表

下表列出了可用于 Azure Sentinel SAP 解决方案的[播放列表](sap-deploy-solution.md#deploy-sap-security-content)，以及每个播放列表中的字段。

这些播放列表为 Azure Sentinel SAP 持续威胁监测解决方案提供配置，可通过 Azure Sentinel GitHub 存储库访问，网址如下 https://github.com/Azure/Azure-Sentinel/tree/master/Solutions/SAP/Analytics/Watchlists 。


|播放列表名称  |说明和字段  |
|---------|---------|
|<a name="objects"></a>**SAP - 关键授权对象**     |  应当控制其分配的关键授权对象。     <br><br>- **AuthorizationObject**：SAP 授权对象，例如 `S_DEVELOP`、`S_TCODE` 或 `Table TOBJ` <br>-   **AuthorizationField：SAP** 授权字段，例如 `OBJTYP` 或 `TCD`    <br>- **AuthorizationValue**：SAP 授权字段值，例如 `DEBUG`       <br>- **ActivityField**：SAP 活动字段。 在大多数情况下，此值将为 `ACTVT`。 对于无 **活动**，或仅有 **活动** 字段的授权对象，请填充 `NOT_IN_USE`。        <br>- **活动**：SAP 活动，取决于 `01`：创建；`02`：更改；`03`：显示等授权对象。      <br>- **说明**：有意义的关键授权对象说明。 |
|**SAP - 排除的网络** | 用于已排除网络的内部维护，例如忽略 Web 调度程序、终端服务器等。 <br><br>-**网络**：网络 IP 地址或范围，例如 `111.68.128.0/17`。 <br>-**说明**：有意义的网络说明。|
|**SAP 排除的用户** |已登录系统但必须忽略的系统用户。 例如，对同一用户进行多次登录发出警报。 <br><br>- **用户**：SAP 用户 <br>-**说明**：有意义的用户说明。 |
|<a name="networks"></a>**SAP - 网络**     |  用于标识未授权登录的内部和维护网络。      <br><br>- **网络**：网络 IP 地址或范围，例如 `111.68.128.0/17`。     <br>- **说明**：有意义的网络说明。|
|<a name="users"></a>**SAP - 特权用户**     |   受到额外限制的特权用户。  <br><br>- **用户**：ABAP 用户，例如 `DDIC` 或 `SAP` <br>- **说明**：有意义的用户说明。 |
|<a name= "programs"></a>**SAP - 敏感 ABAP 程序**     |      应当控制其执行的敏感 ABAP 程序（报表）。   <br><br>- **ABAPProgram**：ABAP 程序或报表，例如 `RSPFLDOC`     <br>- **说明**：有意义的程序说明。|
|<a name="module"></a>**SAP - 敏感函数模块**     |   用于标识未授权登录的内部和维护网络。      <br><br>- **FunctionModule**：ABAP 函数模块，例如 `RSAU_CLEAR_AUDIT_LOG`       <br>- **说明**：有意义的模块说明。     |
|<a name="profiles"></a>**SAP - 敏感配置文件**     |  应当控制其分配的敏感配置文件。     <br><br>- **配置文件**：SAP 授权配置文件，例如 `SAP_ALL` 或 `SAP_NEW`      <br>- **说明**：有意义的配置文件说明。|
|<a name="tables"></a>**SAP - 敏感表**     |  其访问权限应当受到控制的敏感表。  <br><br>- **表**：ABAP 字典表，例如 `USR02` 或 `PA008` <br>- **说明**：有意义的表说明。 |
|<a name="roles"></a>**SAP - 敏感角色**     |  应当控制其分配的敏感角色。    <br><br>- **角色**：SAP 授权角色，例如 `SAP_BC_BASIS_ADMIN`  <br>- **说明**：有意义的角色说明。 |
|<a name="transactions"></a>**SAP - 敏感事务**     |     应当控制其执行的敏感事务。  <br><br>- **TransactionCode**：SAP 事务代码，例如 `RZ11` <br>- **说明**：有意义的代码说明。 |
|<a name="systems"></a>**SAP - 系统**     |    根据角色和使用情况描述 SAP 系统环境。<br><br>- **SystemID**：SAP 系统 ID (SYSID) <br>- **SystemRole**：SAP 系统角色，属于以下值之一：`Sandbox`、`Development`、`Quality Assurance`、`Training`、`Production` <br>- **SystemUsage**：SAP 系统使用情况，属于以下值之一：`ERP`、`BW`、`Solman`、`Gateway`、`Enterprise Portal`        |
|<a name="users"></a>**SAP - 排除的用户**     |  出于“用户多次登录”警报等原因，已登录且需要忽略的系统用户。 <br><br>- **用户**：SAP 用户  <br>- **说明**：有意义的用户说明  |
|<a name="networks"></a>**SAP - 排除的网络**     |  维护已排除的内部网络，以忽略 Web 调度程序、终端服务器等。  <br><br>- **网络**：网络 IP 地址或范围，例如 `111.68.128.0/17`  <br>- **说明**：有意义的网络说明 |
|<a name="modules"></a>**SAP - 已过时函数模块**     | 应当控制其执行的已过时函数模块。    <br><br>- **FunctionModule**：ABAP 函数模块，例如 TH_SAPREL  <br>- **说明**：有意义的函数模块说明 |
|<a name="programs"></a>**SAP - 已过时程序**     | 应当控制其执行的已过时 ABAP 程序（报表）。  <br><br>- **ABAPProgram**：ABAP 程序，例如 TH_ RSPFLDOC  <br>- **说明**：有意义的 ABAP 程序说明 |
|<a name="transactions"></a>**SAP - ABAP 生成事务**     |  应当控制其执行的 ABAP 生成事务。 <br><br>- **TransactionCode**：事务代码，例如 SE11  <br>- **说明**：有意义的事务代码说明  |
|<a name="servers"></a>**SAP - FTP 服务器**     |  用于标识未授权连接的 FTP 服务器。    <br><br>- **客户端**：例如 100。  <br>- **FTP_Server_Name**：FTP 服务器名称，例如 http://contoso.com/ <br>-**FTP_Server_Port**：FTP 服务器端口，例如 22。 <br>- **说明** 有意义的 FTP 服务器说明 |
| | |


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [为 SAP 部署 Azure Sentinel 解决方案](sap-deploy-solution.md)
- [Azure Sentinel SAP 解决方案日志参考](sap-solution-log-reference.md)
- [专家配置选项、本地部署和 SAPControl 日志源](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP 解决方案详细的 SAP 要求](sap-solution-detailed-requirements.md)
- [Azure Sentinel SAP 解决方案部署故障排除](sap-deploy-troubleshoot.md)