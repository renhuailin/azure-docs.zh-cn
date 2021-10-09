---
title: include 文件
description: include 文件
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 07/29/2021
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 1a5983384f9fd696963fbcc4f18286ed450d0b40
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645441"
---
下面是 Azure Active Directory (Azure AD) 服务的使用限制和其他服务限制。

| 类别 | 限制 |
| --- | --- |
| 租户 | 单个用户最多可以是 500 个 Azure AD 租户的成员或来宾。<br/>单个用户最多可以创建 200 个目录。 |
| 域 | 最多可以添加 5000 个托管域名。 如果将所有域设置为与本地 Active Directory 联合，则可在每个租户中添加不超过 2500 个域名。 |
|资源 |<ul><li>默认情况下，Azure Active Directory 免费版用户最多可以在单个租户中创建 50,000 个 Azure AD 资源。 如果你有至少一个经过验证的域，则组织的默认默认 Azure AD 服务配额会扩展到 300000 个 Azure AD 资源。 即使在你执行了内部管理员接管，并且组织已转换为具有至少一个已验证的域的托管租户之后，自助服务注册为组织创建的 Azure AD 服务配额仍然为 50,000 个 Azure AD 资源。 此服务限制与 Azure AD 定价页上 500000 个资源的定价层限制无关。 若要超过默认配额，必须联系 Microsoft 支持部门。</li><li>非管理员用户最多可以创建 250 个 Azure AD 资源。 活动资源和可还原的已删除资源都会计入此配额。 只能还原在不到 30 天前删除的 Azure AD 资源。 不再可还原的已删除 Azure AD 资源在 30 天内按四分之一的值计入此配额。 如果开发人员在其日常工作期间可能会反复超过此配额，你可以[创建并分配一个自定义角色](../articles/active-directory/roles/quickstart-app-registration-limits.md)，并为此角色授予创建无限个应用注册的权限。</li></ul> |
| 架构扩展 |<ul><li>String 类型扩展最多只能有 256 个字符。 </li><li>Binary 类型扩展限制在 256 字节以内。</li><li>只能将 100 个扩展值（包括所有类型和所有应用程序）写入任何单一 Azure AD 资源中。</li><li>仅“用户”、“组”、“TenantDetail”、“设备”、“应用程序”和“ServicePrincipal”实体可以用字符串类型或二进制文件类型单一值属性进行扩展。</li></ul> |
| 应用程序 | <ul><li>最多有 100 位用户可以是单一应用程序的所有者。</li><li>用户、组或服务主体最多可以有 1,500 个应用角色分配。 限制是针对所有应用角色的服务主体、用户或组，而不是针对单个应用角色的分配数量的限制。</li><li>基于密码的单一登录 (SSO) 应用的用户数上限为 48，这意味着每个应用的用户名/密码对仅限 48 个密钥。 若要添加额外的用户，请参阅[排查 Azure AD 中基于密码的单一登录](../articles/active-directory/manage-apps/troubleshoot-password-based-sso.md#i-cant-add-another-user-to-my-password-based-sso-app)中的疑难解答说明。</li><li>一位用户最多只能有 48 个应用配置用户名和密码凭据。</li></ul> |
|应用程序清单 |最多可在应用程序清单中添加 1200 个条目。 |
| 组 |<ul><li>一个非管理员用户最多可在 Azure AD 组织中创建 250 个组。 任何可以管理组织中的组的 Azure AD 管理员可以创建无限数量的组（最多可达 Azure AD 对象限额）。 如果指定了一个角色来删除用户的限制，请向其分配权限较低的内置角色，如“用户管理员”或“组管理员”。</li><li>一个 Azure AD 组织最多可以有 5000 个动态组。</li><li>在单个 Azure AD 组织（租户）中最多可以创建 400 个可分配角色的组。</li><li>最多有 100 位用户可以是单一组的所有者。</li><li>任意数量的 Azure AD 资源都可以是单个组的成员。</li><li>一个用户可以是任意数量的组的成员。</li><li>默认情况下，使用 Azure AD Connect 时，一个组中可以从本地 Active Directory 同步到 Azure Active Directory 的成员数目限制为 50,000。 如果需要同步超出此成员数限制的组，则必须载入 [Azure AD Connect 同步 V2 终结点 API](../articles/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2.md)。</li><li>并非所有方案都支持 Azure AD 中的嵌套组</li><li>选择组列表时，最多可将组过期策略分配给 500 个 Microsoft 365 组。 如果将策略应用于所有 Microsoft 365 组，则没有任何限制。</li></ul><br/> 目前，以下是嵌套组支持的方案。<ul><li> 可以将一个组添加为另一个组的成员，并且可以实现组嵌套。</li><li> 组成员身份声明（当将应用程序配置为接收令牌中的组成员身份声明时，将包括登录用户所属的嵌套组）</li><li>条件访问（当条件访问策略具有组作用域时）</li><li>限制访问自助式密码重置</li><li>限制哪些用户可以进行 Azure AD 联接和设备注册</li></ul><br/>以下方案不支持嵌套组：<ul><li> 应用角色分配（支持向应用分配组，但嵌套在直接分配的组中的组将没有访问权限），可用于访问和预配</li><li>基于组的许可（将许可证自动分配给组的所有成员）</li><li>Microsoft 365 组。</li></ul> |
| 应用程序代理 | <ul><li>每个应用代理应用程序每秒最多 500 个事务</li><li>Azure AD 组织每秒最多 750 个事务</li></ul><br/>事务定义为单一 http 请求和对唯一资源的响应。 当受到限制时，客户端会收到 429 响应（请求过多）。 |
| 访问面板 |无论分配的许可证如何，每个用户可以在访问面板中看到的应用程序数量都没有限制。  |
| 报告 | 在报告中最多可查看或下载 1,000 行。 系统会截断其他任何数据。 |
| 管理单元 | Azure AD 资源可以是不超出 30 个管理单位的成员。 |
| Azure AD 角色和权限 | <ul><li>最多可在 Azure AD 组织中创建 30 个 [Azure AD 自定义角色](/azure/active-directory//users-groups-roles/roles-custom-overview?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)。</li><li>租户范围内单个主体的 Azure AD 自定义角色分配最多为 100 个。</li><li>非租户范围（例如管理单元或 Azure AD 对象）内单个主体的 Azure AD 内置角色分配最多为 100 个。 租户范围内的 Azure AD 内置角色分配没有限制。</li><li>无法将组添加为[组所有者](../articles/active-directory/fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context#object-ownership)。</li><li>用户读取其他用户租户信息的能力只能通过以下方式限制：使用 Azure AD组织范围内的开关禁用所有非管理员用户对所有租户信息的访问（不推荐使用）。 有关详细信息，请参阅[限制成员用户的默认权限](../articles/active-directory/fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context#restrict-member-users-default-permissions)。</li><li>在管理员角色成员身份添加和撤销生效之前，最多可能需要 15 分钟或注销/登录。</li></ul> |
