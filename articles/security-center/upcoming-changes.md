---
title: 即将推出的对 Azure 安全中心的重要更改
description: 即将推出的对 Azure 安全中心的更改，你可能需要了解这些更改并针对这些更改做好计划
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: memildin
ms.openlocfilehash: 0656000a1d6449306e8afe538f846c55c79a31a2
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915280"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>即将推出的对 Azure 安全中心的重要更改

> [!IMPORTANT]
> 此页上的信息与预发行的产品或功能相关，这些产品或功能在正式发布之前可能会进行重大修改（如果有的话）。 对于此处提供的信息，Microsoft 不做任何承诺或者明示或暗示的保证。

在此页上，你将了解针对安全中心计划的更改。 此页介绍对产品所做的计划性修改，这些修改可能会影响安全功能分数或工作流之类的内容。

如果要查找最新的发行说明，可查看 [Azure 安全中心的新增功能](release-notes.md)。


## <a name="planned-changes"></a>计划的更改

- [在 Azure Policy 评估中将“不适用”资源报告为“合规”](#not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments)
- [添加了 35 条预览建议，以扩大 Azure 安全基准的覆盖范围](#35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark)

### <a name="not-applicable-resources-to-be-reported-as-compliant-in-azure-policy-assessments"></a>在 Azure Policy 评估中将“不适用”资源报告为“合规”

**预计更改日期：** 2021 年 1 月

当前，为建议评估且发现不适用的资源在 Azure Policy 中显示为“不合规”。 任何用户操作都不能将资源状态更改为“合规”。 根据此计划内更改，为了显得更加清楚，将资源报告为“合规”。

唯一的影响是 Azure Policy 中合规资源的数量将增加。 Azure 安全中心的安全评分不受影响。

### <a name="35-preview-recommendations-being-added-to-increase-coverage-of-azure-security-benchmark"></a>添加了 35 条预览建议，以扩大 Azure 安全基准的覆盖范围

**预计更改日期：** 2021 年 1 月

Azure 安全基准是由 Microsoft 创作的特定于 Azure 的一组准则，适用于基于常见合规框架的安全与合规最佳做法。 [详细了解 Azure 安全基准](../security/benchmarks/introduction.md)。

将在安全中心添加下列 35 条预览建议，以扩大此基准的覆盖范围。

预览版建议不会显示资源运行不正常，并且在计算安全功能分数时不会包含这些建议。 请尽量修正这些建议，以便在预览期结束之后，借助这些建议提高安全功能分数。 如需详细了解如何响应这些建议，请参阅[修正 Azure 安全中心的建议](security-center-remediate-recommendations.md)。

| 安全控制                     | 新建议                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 启用静态加密            | - Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据<br>- Azure 机器学习工作区应使用客户管理的密钥 (CMK) 进行加密<br>- 应为 MySQL 服务器启用“创建自己的密钥”数据保护<br>- 应为 PostgreSQL 服务器启用“创建自己的密钥”数据保护<br>- 认知服务帐户应启用使用客户管理的密钥 (CMK) 进行数据加密<br>- 容器注册表应使用客户管理的密钥 (CMK) 进行加密<br>- SQL 托管实例应使用客户管理的密钥进行静态数据加密<br>- SQL Server 应使用客户管理的密钥进行静态数据加密<br>- 存储帐户应使用客户管理的密钥 (CMK) 进行加密                                                                                                                                                              |
| 实现安全最佳实践    | - 订阅应有一个联系人电子邮件地址，用于接收安全问题通知<br> - 你的订阅应启用 Log Analytics 代理自动预配<br> - 应启用高严重性警报的电子邮件通知<br> - 应启用向订阅所有者发送高严重性警报的电子邮件通知<br> - 密钥保管库应启用清除保护<br> - 密钥保管库应启用软删除 |
| 管理访问和权限        | - 确保函数应用已启用“客户端证书(传入客户端证书)” |
| 保护应用程序免受 DDoS 攻击 | - 应为应用程序网关启用 Web 应用程序防火墙 (WAF)<br> - 应为 Web 应用程序防火墙 (WAF) 启用 Azure Front Door 服务 |
| 限制未经授权的网络访问 | - 应在 Key Vault 上启用防火墙<br> - 应为 Key Vault 配置专用终结点<br> - 应用程序配置应使用专用链接<br> - Azure Cache for Redis 应驻留在虚拟网络中<br> - Azure 事件网格域应使用专用链接<br> - Azure 事件网格主题应使用专用链接<br> - Azure 机器学习工作区应使用专用链接<br> - Azure SignalR 服务应使用专用链接<br> - Azure Spring Cloud 应使用网络注入<br> - 容器注册表不得允许无限制的网络访问<br> - 容器注册表应使用专用链接<br> - 应为 MariaDB 服务器禁用公用网络访问<br> - 应为 MySQL 服务器禁用公用网络访问<br> - 应为 PostgreSQL 服务器禁用公用网络访问<br> - 存储帐户应使用专用链接连接<br> - 存储帐户应使用虚拟网络规则来限制网络访问<br> - VM 映像生成器模板应使用专用链接|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

相关链接：

- [详细了解 Azure 安全基准](../security/benchmarks/introduction.md)
- [详细了解 Azure Database for MariaDB](../mariadb/overview.md)
- [详细了解 Azure Database for MySQL](../mysql/overview.md)
- [详细了解 Azure Database for PostgreSQL](../postgresql/overview.md)





## <a name="next-steps"></a>后续步骤

有关产品的所有最新更改，请参阅 [Azure 安全中心的新增功能](release-notes.md)。