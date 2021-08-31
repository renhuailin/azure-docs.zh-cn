---
title: 将本地 MySQL 迁移到 Azure Database for MySQL：安全性
description: 迁移到基于云的服务并不意味着整个 Internet 都可以随时访问它。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: f0d0613dca258050cec75d544438b781c1f9fe96
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285229"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-security"></a>将本地 MySQL 迁移到 Azure Database for MySQL：安全性

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>先决条件

[业务连续性和灾难恢复](12-business-continuity-and-disaster-recovery.md)

## <a name="overview"></a>概述

迁移到基于云的服务并不意味着整个 Internet 都可以随时访问它。 Azure 提供一流的安全性，可确保持续保护数据工作负载免受不良执行组件和恶意程序的侵害。

## <a name="authentication"></a>身份验证

Azure Database for MySQL 支持 MySQL 用户连接的基本身份验证机制，但也支持[与 Azure Active Directory 的集成](../../concepts-azure-ad-authentication.md)。 这种安全集成通过在 MySQL 登录过程中发出类似于密码的令牌来工作。 [配置 Active Directory 集成](../../howto-configure-sign-in-azure-ad-authentication.md)非常简单，不仅支持用户，还支持 AAD 组。

这种紧密集成使管理员和应用程序能够利用 [Azure 标识保护](../../../active-directory/identity-protection/overview-identity-protection.md)的增强安全功能发现标识问题。

> [!NOTE] 
> MySQL 5.7 及更高版本支持此安全功能。 只要提供了 `clear-text` 选项，就支持大多数[应用程序驱动程序](../../howto-configure-sign-in-azure-ad-authentication.md)。

## <a name="threat-protection"></a>威胁防护

如果用户或应用程序凭据被入侵，日志不太可能反映任何失败的登录尝试。 被入侵的凭据可能允许不良执行组件访问和下载数据。 [Azure 威胁防护](../../concepts-security.md#threat-protection)可以监视登录中的异常情况（例如异常位置、稀有用户或暴力攻击）和其他可疑活动。 如果某些内容 `look` 不正常，则可以向管理员发送通知。

## <a name="audit-logging"></a>审核日志

MySQL 具有强大的内置审核日志功能。 默认情况下，Azure Database for MySQL 中的此[审核日志功能处于禁用状态](../../concepts-audit-logs.md)。 可以通过更改 `audit\_log\_enabled` 服务器参数来启用服务器级日志记录。 启用后，可以开启[诊断日志记录](../../howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)，通过 [Azure Monitor](../../../azure-monitor/overview.md) 和 [Log Analytics](../../../azure-monitor/logs/design-logs-deployment.md) 访问日志。

要查询用户连接相关事件，请运行以下 KQL 查询：

```
AzureDiagnostics  
| where ResourceProvider =="MICROSOFT.DBFORMYSQL" 
| where Category == 'MySqlAuditLogs' and event\_class\_s == "connection\_log"  
| project TimeGenerated, LogicalServerName\_s, event\_class\_s, event\_subclass\_s  
, event\_time\_t, user\_s , ip\_s , sql\_text\_s  
| order by TimeGenerated asc
```

## <a name="encryption"></a>Encryption

MySQL 实例中的数据默认为静态加密。 任何自动备份也经过加密，以防止数据可能泄露给未经授权的各方。 此加密通常使用创建实例时创建的密钥执行。 除了此默认加密密钥之外，管理员还可以选择[自带密钥 (BYOK)。](../../concepts-data-encryption-mysql.md)

使用客户管理的密钥策略时，了解与密钥生命周期管理相关的职责至关重要。 客户密钥存储在 [Azure Key Vault](../../../key-vault/general/basic-concepts.md) 中，然后通过策略访问。 遵循密钥管理的所有建议至关重要，丢失加密密钥等同于丢失数据访问权限。

除了客户管理的密钥之外，还可以使用服务级密钥来[添加双重加密](../../concepts-infrastructure-double-encryption.md)。 实施此功能可提供高度加密的静态数据，但它确实带有加密性能处罚。 应执行测试。

数据可以在传输过程中使用 SSL/TLS 进行加密。 如前所述，可能需要[修改你的应用程序](../../howto-configure-ssl.md)来支持此更改和配置适当的 TLS 验证设置。

## <a name="firewall"></a>防火墙

设置好用户并加密静态数据后，迁移团队应审查网络数据流。 Azure Database for MySQL 提供了多种机制，通过仅向授权的用户、应用程序和设备提供访问权限来保护网络层。

保护 MySQL 实例的第一道防线是实施[防火墙规则](../../concepts-firewall-rules.md)。 通过内部或外部 IP 访问实例时，IP 地址可以限制为仅有效位置。 如果 MySQL 实例旨在仅服务于内部应用程序，则[限制公共访问](../../howto-deny-public-network-access.md)。

将应用程序与 MySQL 工作负载一起移至 Azure 时，很可能会设置采用中心辐射模式的多个虚拟网络，且需要配置[虚拟网络对等互连](../../../virtual-network/virtual-network-peering-overview.md)。

## <a name="private-link"></a>专用链接

要将 Azure Database for MySQL 的访问权限限制为仅向内部 Azure 资源提供，请启用[专用链接](../../concepts-data-access-security-private-link.md)。 专用链接确保 MySQL 实例被分配一个私有 IP 而不是公共 IP 地址。

> [!NOTE]
> 还有许多其他的 [Azure 网络基本注意事项](../../concepts-data-access-and-security-vnet.md)需要考虑，但这不是本指南的重点。

查看一组可跨所有 Azure 资源实施的潜在[安全基线](/azure/mysql/security-baseline)任务。 并非参考链接中描述的所有项目都适用于特定数据工作负载或 Azure 资源。

## <a name="security-checklist"></a>安全清单

  - 尽可能使用 Azure AD 身份验证。

  - 启用高级威胁防护。

  - 启用所有审核功能。

  - 请考虑自带密钥 (BYOK) 策略。

  - 实现防火墙规则。

  - 利用专用终结点实现不通过 Internet 传输的工作负载。  


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [摘要](./14-summary.md)