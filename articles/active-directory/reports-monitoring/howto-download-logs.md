---
title: 如何下载 Azure Active Directory 中的日志 | Microsoft Docs
description: 了解如何下载 Azure Active Directory 中的活动日志。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/14/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e43e94b3d813df96befdc9bc2bf802a3da93bd5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110083811"
---
# <a name="how-to-download-logs-in-azure-active-directory"></a>如何：下载 Azure Active Directory 中的日志

Azure Active Directory (Azure AD) 门户可让用户访问三种活动日志：

- **[登录](concept-sign-ins.md)** - 有关登录以及用户如何使用资源的信息。
- **[审核](concept-audit-logs.md)** - 有关应用于租户的更改（如用户和组管理）或应用于租户资源的更新的信息。
- **[预配](concept-provisioning-logs.md)** - 由预配服务执行的活动（例如在 ServiceNow 中创建组，或从 Workday 导入用户）。

Azure AD 会将这些日志中的数据存储有限的一段时间。 以 IT 管理员身份可以下载活动日志，进行长期备份。

本文介绍如何下载 Azure AD 中的活动日志。  

## <a name="what-you-should-know"></a>要点

- 在 Azure AD 门户中，可以找到活动日志的多个入口点。 例如，[用户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers)或[组](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)页上的“活动”部分。 但是，只有一个位置提供日志的初始未筛选视图：[Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 页上的“监视”部分。    

- Azure AD 仅将活动日志存储特定的一段时间。 有关详细信息，请参阅 [Azure AD 将报表数据存储多长时间？](reference-reports-data-retention.md) 

- 通过下载日志，可以控制日志的存储时间。 

- 最多可下载 250 000 条记录。 如果要下载更多数据，请使用报告 API。

- 下载基于设置的筛选器。 

- Azure AD 支持下载以下格式：

    - **CSV** 

    - **JSON** 

- 下载的文件中的时间戳始终基于 UTC。



## <a name="what-license-do-you-need"></a>需要什么许可证？

所有版本的 Azure AD 都提供下载活动日志数据的选项。

还可以使用 Microsoft Graph 下载活动日志；但是，下载日志语法上要求高级许可证。


## <a name="who-can-do-it"></a>谁可以执行此任务？

全局管理员工作时，应该使用特权更低的帐户来执行此任务。 若要访问审核日志，可以使用以下角色：

- 报表读取者
- 全局读取者
- 安全管理员
- 安全读取者


## <a name="how-to-do-it"></a>如何实现


要下载活动日志：

1. 导航到所关注的活动日志视图：
 
    - [登录日志](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)
    
    - [审核日志](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)    
       
    - [预配日志](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/ProvisioningEvents)    
   

2.  “添加”必需的筛选器。  

    ![添加筛选器](./media/\howto-download-logs/add-filter.png)    

3. “下载”数据。

    ![下载日志](./media/\howto-download-logs/download-log.png)

## <a name="next-steps"></a>后续步骤

- [Azure AD 中的登录日志](concept-sign-ins.md)
- [Azure AD 中的审核日志](concept-audit-logs.md)