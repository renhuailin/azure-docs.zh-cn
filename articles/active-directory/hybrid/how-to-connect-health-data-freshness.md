---
title: Azure AD Connect Health -“运行状况服务数据不是最新的”警报 | Microsoft Docs
description: 本文档介绍了“运行状况服务数据不是最新的”警报的原因以及如何对此警报进行故障排除。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bd83071e725d68a424642f68680748247a26706
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113110072"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>“运行状况服务数据不是最新的”警报

## <a name="overview"></a>概述

Azure AD Connect Health 监视的本地计算机上的代理会定期将数据上传到 Azure AD Connect Health 服务。 如果服务不从代理接收数据，则门户显示的信息将过时。 为强调该问题，该服务将显示“运行状况服务数据不是最新的”警报。 如果服务在过去两小时内未收到完整数据，将生成此警报。  

- 如果运行状况服务在过去两小时内只收到部分从服务器发送来的数据类型，则会触发“警告”状态的警报 。 “警告”状态的警报不会触发向配置的收件人发送电子邮件通知的操作。 
- 如果运行状况服务在过去两小时内未从服务器收到任何数据类型，则会触发“错误”状态的警报。 错误状态警报会触发向配置的收件人发送电子邮件通知的操作。

服务从本地计算机上运行的代理获取数据，具体取决于服务类型。 下表列出了计算机上运行的代理、其功能以及服务生成的数据类型。 在某些情况下，相关过程涉及多个服务，其中任何一个服务都可能是触发警报的原因。 

## <a name="understanding-the-alert"></a>了解警报

“警报详细信息”边栏选项卡显示警报发生的时间和最后检测到的时间。 每两小时运行一次的后台进程会生成警报并对其进行重新评估。 在下面的示例中，初始警报出现在 03/10 上午 9:59。 03/12 上午 10:00 再次评估该警报时该警报仍然存在。 边栏选项卡中还详细注明了运行状况服务上次接收到特定数据类型的时间。 
 
 ![Azure AD Connect Health 警报详细信息](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
下表将服务类型映射到相应的必需数据类型：

| 服务类型 | 代理（Windows 服务名称） | 用途 | 生成的数据类型  |
| --- | --- | --- | --- |  
| Azure AD Connect（同步） | Azure AD Connect Health Sync Insights 服务 | 收集特定于 AAD Connect 的信息（连接器、同步规则等） | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Azure AD Connect Health Sync 监视服务 | 收集特定于 AAD Connect 的性能计数器、ETW 跟踪、文件 | 性能计数器 |
| AD DS | Azure AD Connect Health AD DS Insights 服务 | 执行综合测试，收集拓扑信息、复制元数据 |  - Adds-TopologyInfo-Json <br /> - Common-TestData-Json（创建测试结果）   | 
|  | Azure AD Connect Health AD DS 监视服务 | 收集特定于 ADDS 的性能计数器、ETW 跟踪、文件 | - 性能计数器  <br /> - Common-TestData-Json（上传测试结果）  |
| AD FS | Azure AD Connect Health AD FS Diagnostics 服务 | 执行综合测试 | TestResult（创建测试结果） | 
| | Azure AD Connect Health AD FS Insights 服务  | 收集 ADFS 使用情况指标 | Adfs-UsageMetrics |
| | Azure AD Connect Health AD FS Monitoring 服务 | 收集特定于 ADFS 的性能计数器、ETW 跟踪、文件 | TestResult（上传测试结果） |

## <a name="troubleshooting-steps"></a>疑难解答步骤 

下面给出了诊断问题所需的步骤。 第一步是适用于所有服务类型的一组基本检查。 

> [!IMPORTANT] 
> 此警报遵守 Connect Health [数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)

* 确保安装最新版本的代理。 查看[版本历史记录](reference-connect-health-version-history.md)。 
* 确保在计算机上运行 Azure AD Connect Health 代理服务。 例如，适用于 AD FS 的 Connect Health 应该有三个服务。
  ![验证 Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* 请务必仔细审阅并满足[要求部分](how-to-connect-health-agent-install.md#requirements)。
* 使用[测试连接工具](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)来发现连接问题。
* 若有 HTTP 代理，请按照以下[配置步骤](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy)操作。 


## <a name="next-steps"></a>后续步骤
如果在上述任一步骤中确定了问题，请解决问题并等待警报消除。 警报后台进程每 2 小时运行一次，因此，最多需要 2 小时来消除警报。 

* [Azure AD Connect Health 数据保留策略](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health 常见问题](reference-connect-health-faq.yml)
