---
title: ISO 27001 共享服务蓝图示例控制
description: ISO 27001 共享服务蓝图示例的控制映射。 每个控制措施都映射到一个或多个协助评估的 Azure Policy 定义。
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 31cc7bea93de75e8b3f6d21813d79a32c26085df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632056"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>ISO 27001 共享服务蓝图示例的控制映射

以下文章详细说明了 Azure 蓝图 ISO 27001 共享服务蓝图示例如何映射到 ISO 27001 控制措施。 有关控制措施的详细信息，请参阅 [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html)。

以下映射适用于 **ISO 27001:2013** 控制措施。 使用右侧的导航栏可直接跳转到特定的控制映射。 许多的映射控制措施都是使用 [Azure Policy](../../../policy/overview.md) 计划实施的。 若要查看完整计划，请在 Azure 门户中打开“策略”，并选择“定义”页。 然后，找到并选择“\[预览\] 审核 ISO 27001:2013 控制措施并部署特定 VM 扩展以支持审核要求”内置策略计划。

> [!IMPORTANT]
> 下面的每个控件都与一个或多个 [Azure Policy](../../../policy/overview.md) 定义关联。 这些策略有助于[评估控制的合规性](../../../policy/how-to/get-compliance-data.md)；但是，控制与一个或多个策略之间通常不是一对一或完全匹配。 因此，Azure Policy 中的符合性仅引用策略本身；这不确保你完全符合控件的所有要求。 此外，符合性标准包含目前未由任何 Azure Policy 定义处理的控件。 因此，Azure Policy 中的符合性只是整体符合性状态的部分视图。 此符合性蓝图示例的控件和 Azure Policy 定义之间的关联可能会随着时间的推移而发生变化。 若要查看更改历史记录，请参阅 [GitHub 提交历史记录](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md)。

## <a name="a612-segregation-of-duties"></a>A.6.1.2 职责分离

仅分配一个 Azure 订阅所有者并不能实现管理冗余。 相反，分配过多的 Azure 订阅所有者会增大违规的可能性，因为会有更多的所有者帐户可能会泄密。 此蓝图可帮助你通过分配两个用于审核 Azure 订阅所有者数目的 [Azure Policy](../../../policy/overview.md) 定义，来保持适当的 Azure 订阅所有者数目。 管理订阅所有者权限有助于实现适当的职责分离。

- 只多只为订阅指定 3 个所有者
- 应该为你的订阅分配了多个所有者

## <a name="a821-classification-of-information"></a>A.8.2.1 信息分类

Azure 的 [SQL 漏洞评估服务](../../../../azure-sql/database/sql-vulnerability-assessment.md)可以帮助你发现数据库中存储的敏感数据并提供用于对该数据进行分类的建议。 此蓝图分配了一个 [Azure Policy](../../../policy/overview.md) 定义来审核在 SQL 漏洞评估过程中查明的漏洞是否已更正。

- 应该修复 SQL 数据库中的漏洞

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 访问网络和网络服务

[Azure 基于角色的访问控制 (Azure RBAC)](../../../../role-based-access-control/overview.md) 可帮助管理谁有权访问 Azure 资源。 此蓝图可帮助你通过分配七个 [Azure Policy](../../../policy/overview.md) 定义来控制对 Azure 资源的访问。 这些策略将审核可能允许更高资源访问权限的资源类型和配置的使用。
了解违反这些策略的资源有助于采取纠正措施来确保仅限已授权的用户访问 Azure 资源。

- 显示具有不使用密码的帐户的 Linux VM 中的审核结果
- 显示允许通过没有密码的帐户进行远程连接的 Linux VM 中的审核结果
- 应将存储帐户迁移到新 Azure 资源管理器资源
- 应将虚拟机迁移到新的 Azure 资源管理器资源
- 审核不使用托管磁盘的 VM

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 管理特权访问权限

此蓝图通过分配四个 [Azure Policy](../../../policy/overview.md) 定义用于审核拥有所有者和/或写入权限的外部帐户，以及拥有所有者和/或写入权限、但未启用多重身份验证的帐户，来帮助你限制和控制特权访问权限。 Azure 基于角色的访问控制 (Azure RBAC) 可帮助管理谁有权访问 Azure 资源。 此蓝图还分配了三个 Azure Policy 定义，用于审核 Azure Active Directory 身份验证在 SQL 服务器和 Service Fabric 中的使用。 使用 Azure Active Directory 身份验证可以简化权限管理，以及集中化数据库用户和其他 Microsoft 服务的标识管理。 此蓝图还分配一个 Azure Policy 定义，用于审核自定义 Azure RBAC 规则的使用。 了解实施自定义 Azure RBAC 规则的位置有助于验证需求以及实施是否适当，因为自定义 Azure RBAC 规则容易出错。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户
- 应该为 SQL 服务器预配 Azure Active Directory 管理员
- Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证
- 审核自定义 RBAC 规则的使用

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 管理用户的机密身份验证信息

此蓝图分配三个 [Azure Policy](../../../policy/overview.md) 定义用于审核未启用多重身份验证的帐户。 即使某个身份验证信息片段已泄密，多重身份验证也有助于保护帐户的安全。 通过监视未启用多重身份验证的帐户，可以识别出更有可能会泄密的帐户。 此蓝图还将分配两个 Azure Policy 定义用于审核 Linux VM 密码文件权限，并在这些权限设置不当时发出警报。 使用这种设置可以采取纠正措施，以确保验证器不会泄密。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应在对订阅拥有读取权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA
- 显示未将密码文件权限设为 0644 的 Linux VM 中的审核结果

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 评审用户访问权限

[Azure 基于角色的访问控制 (Azure RBAC)](../../../../role-based-access-control/overview.md) 可帮助你管理谁有权访问 Azure 中的资源。 使用 Azure 门户可以评审有权访问 Azure 资源的用户及其权限。 此蓝图分配四个 [Azure Policy](../../../policy/overview.md) 定义用于审核应该优先评审的帐户，包括已淘汰的帐户，以及具有提升权限的外部帐户。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户
- 应从订阅中删除拥有所有者权限的外部帐户
- 应从订阅中删除具有写入权限的外部帐户

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 删除或调整访问权限

[Azure 基于角色的访问控制 (Azure RBAC)](../../../../role-based-access-control/overview.md) 可帮助你管理谁有权访问 Azure 中的资源。 使用 [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) 和 Azure RBAC 可以更新用户角色，以反映组织变化。 如果需要，可以阻止帐户登录（或将其删除），这会立即删除其 Azure 资源访问权限。 此蓝图分配两个 [Azure Policy](../../../policy/overview.md) 定义用于审核应该考虑删除的已淘汰帐户。

- 应从订阅中删除弃用的帐户
- 应从订阅中删除拥有所有者权限的已弃用帐户

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 安全登录过程

此蓝图分配了三个 Azure Policy 定义，以用于审核未启用多重身份验证的帐户。 Azure AD 多重身份验证通过要求使用另一种形式的身份验证提供额外的安全性，从而提供增强式身份验证。 通过监视未启用多重身份验证的帐户，可以识别出更有可能会泄密的帐户。

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 应在对订阅拥有读取权限的帐户上启用 MFA
- 应对订阅中拥有写入权限的帐户启用 MFA

## <a name="a943-password-management-system"></a>A.9.4.3 密码管理系统

此蓝图通过分配 10 个 [Azure Policy](../../../policy/overview.md) 定义用于审核不强制实施最低强度和其他密码要求的 Windows VM，来帮助你强制实施强密码。 识别违反密码强度策略的 VM 有助于采取纠正措施，以确保所有 VM 用户帐户的密码符合策略。

- 显示未启用密码复杂性设置的 Windows VM 中的审核结果
- 显示未将最长密码期限设为 70 天的 Windows VM 中的审核结果
- 显示未将最短密码期限设为 1 天的 Windows VM 中的审核结果
- 显示未将最短密码长度限制为 14 个字符的 Windows VM 中的审核结果
- 显示允许重用之前的 24 个密码的 Windows VM 中的审核结果

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 有关使用加密控制措施的策略

此蓝图通过分配 13 个 [Azure Policy](../../../policy/overview.md) 定义用于强制实施特定的加密控制措施并审核弱加密设置的使用，来帮助你针对加密控制措施的使用强制实施自己的策略。 了解 Azure 资源中的哪些位置采用欠佳的加密配置有助于采取纠正措施，以确保根据信息安全策略配置资源。 具体而言，此蓝图分配的策略要求对 Blob 存储帐户和 Data Lake Storage 帐户加密；要求对 SQL 数据库实施透明数据加密；审核存储帐户、SQL 数据库、虚拟机磁盘和自动化帐户变量是否缺少加密；审核是否与存储帐户、函数应用、Web 应用、API 应用和 Redis 缓存建立了不安全的连接；审核虚拟机弱密码加密；审核未加密的 Service Fabric 通信。

- 应该只能通过 HTTPS 访问函数应用
- 只能通过 HTTPS 访问 Web 应用程序
- 只能通过 HTTPS 访问 API 应用
- 显示未存储使用可逆加密的密码的 Windows VM 中的审核结果
- 应在虚拟机上启用磁盘加密
- 自动化帐户变量应进行加密
- 只能与 Azure Cache for Redis 建立安全连接
- 应该启用安全传输到存储帐户
- Service Fabric 群集应将 ClusterProtectionLevel 属性设置为 EncryptAndSign
- 应在 SQL 数据库上启用透明数据加密

## <a name="a1241-event-logging"></a>A.12.4.1 事件日志记录

此蓝图通过分配七个 [Azure Policy](../../../policy/overview.md) 定义用于审核 Azure 资源的日志设置，来帮助你确保记录系统事件。
诊断日志针对 Azure 资源中执行的操作提供见解。

- 审核 Dependency Agent 部署 - VM 映像 (OS) 未列出
- 审核虚拟机规模集中的 Dependency Agent 部署 - VM 映像 (OS) 未列出
- [预览]:审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- 审核虚拟机规模集中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- 审核诊断设置
- 应启用 SQL 服务器上的审核

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 管理员和操作员日志

此蓝图分配了七个 Azure Policy 定义，以用于审核 Azure 资源的日志设置，从而帮助你确保系统事件会被记录。 诊断日志针对 Azure 资源中执行的操作提供见解。

- 审核 Dependency Agent 部署 - VM 映像 (OS) 未列出
- 审核虚拟机规模集中的 Dependency Agent 部署 - VM 映像 (OS) 未列出
- [预览]:审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- 审核虚拟机规模集中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- 审核诊断设置
- 应启用 SQL 服务器上的审核

## <a name="a1244-clock-synchronization"></a>A.12.4.4 时钟同步

此蓝图分配了七个 Azure Policy 定义，以用于审核 Azure 资源的日志设置，从而帮助你确保系统事件会被记录。 Azure 日志依赖于同步的内部时钟创建各个资源中事件的时间相关记录。

- 审核 Dependency Agent 部署 - VM 映像 (OS) 未列出
- 审核虚拟机规模集中的 Dependency Agent 部署 - VM 映像 (OS) 未列出
- [预览]:审核 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- 审核虚拟机规模集中的 Log Analytics 代理部署 - VM 映像 (OS) 未列出
- 审核诊断设置
- 应启用 SQL 服务器上的审核

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 在可操作的系统上安装软件

自适应应用程序控制是 Azure 安全中心内的一个解决方案，可帮助你控制哪些应用程序可在 Azure 中的 VM 上运行。 此蓝图分配一个 Azure Policy 定义用于监视对允许的应用程序集的更改。 此功能帮助你控制软件和应用程序在 Azure VM 上的安装。

- 应在计算机中启用自适应应用程序控制以定义安全应用程序

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 管理技术漏洞

此蓝图分配了五个 [Azure Policy](../../../policy/overview.md) 定义，以用于在 Azure 安全中心内监视缺少的系统更新、操作系统漏洞、SQL 漏洞和虚拟机漏洞，来帮助你管理信息系统漏洞。 Azure 安全中心提供报告功能，使你能够实时洞察已部署的 Azure 资源的安全状态。

- 监视 Azure 安全中心 Endpoint Protection 的缺失情况
- 应在计算机上安装系统更新
- 应该修复计算机上安全配置中的漏洞
- 应该修复 SQL 数据库中的漏洞
- 应该通过漏洞评估解决方案修复漏洞

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 软件安装的限制

自适应应用程序控制是 Azure 安全中心内的一个解决方案，可帮助你控制哪些应用程序可在 Azure 中的 VM 上运行。 此蓝图分配一个 Azure Policy 定义用于监视对允许的应用程序集的更改。 软件安装限制有助于减少出现软件漏洞的可能性。

- 应在计算机中启用自适应应用程序控制以定义安全应用程序

## <a name="a1311-network-controls"></a>A.13.1.1 网络控制措施

此蓝图通过分配一个 [Azure Policy](../../../policy/overview.md) 定义用于监视具有宽松规则的网络安全组，来帮助你管理和控制网络。 过于宽松的规则可能会允许意外的网络访问，应该对其进行评审。 此蓝图还分配了三个 Azure Policy 定义，以用于监视不受保护的终结点、应用程序和存储帐户。 不受防火墙保护的终结点和应用程序，以及具有无限制访问权限的存储帐户，可能会允许意外访问信息系统中包含的信息。

- 应该限制通过面向 Internet 的终结点进行访问
- 存储帐户应限制网络访问

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 信息传输策略和过程

该蓝图通过分配两个 [Azure Policy](../../../policy/overview.md) 定义来审核与存储帐户和 Azure Cache for Redis 建立的不安全连接，从而帮助确保使用 Azure 服务传输的信息是安全的。

- 只能与 Azure Cache for Redis 建立安全连接
- 应该启用安全传输到存储帐户

## <a name="next-steps"></a>后续步骤

了解 ISO 27001 共享服务蓝图的控制映射后，请访问以下文章来了解体系结构以及如何部署此示例：

> [!div class="nextstepaction"]
> [ISO 27001 共享服务蓝图 - 概述](./index.md)
> [ISO 27001 共享服务蓝图 - 部署步骤](./deploy.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
