---
title: 测试环境、请求限制和服务限制
titleSuffix: Microsoft identity platform
description: 了解在部署 Azure Active Directory 测试环境以及测试与 Microsoft 标识平台集成的应用时需要考虑的请求限制和服务限制。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2021
ms.author: ryanwi
ms.reviewer: arcrowe
ms.openlocfilehash: 35f7694dbdf3d7c4f3065f19dc89bac7d7b1915e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367368"
---
# <a name="throttling-and-service-limits-to-consider-for-testing"></a>在测试时要考虑的请求限制和服务限制
作为开发人员，你希望在将应用程序发布到生产环境之前对其进行测试。 在测试受 Microsoft 标识平台保护的应用程序时，应设置一个用于测试的 Azure Active Directory (Azure AD) 环境和租户。  

对于与 Microsoft 标识平台集成的应用程序，需要在 Azure AD 租户中为其创建并管理目录对象（例如应用注册、服务主体、组和用户）。  任何影响应用行为的生产租户设置都应复制到测试租户中。 使用所需的条件访问、权限授予、声明映射、令牌生存期和令牌颁发策略填充测试租户。 应用程序还可能使用了计算或存储等 Azure 资源，需要将这些资源添加到测试环境中。 测试环境可能需要占用大量的资源，具体取决于要测试的应用。

为了确保所有客户都能可靠地使用服务，Azure AD 和其他服务会限制可为每个客户和每个租户创建的资源数量。 在设置测试环境以及部署目录对象和 Azure 资源时，你可能会达到其中的一些服务限制和配额。

Azure AD、Microsoft Graph 和其他 Azure 服务还会限制对服务的并发调用数，或限制每个客户的计算负载量，以防止过度使用资源。 这种做法称为请求限制，可确保 Azure 服务能够在避免服务中断的情况下处理使用情况和传入请求。 请求限制可能在应用程序、租户或整个服务的级别发生。 当应用程序具有大量的租户内请求或跨租户请求时，通常会发生请求限制。  在运行时，应用程序可以通过作为其业务逻辑一部分的 Microsoft Graph 读取或更新 Azure AD 目录对象。 例如，读取或设置用户属性、更新用户的日历，或代表用户发送电子邮件。  在运行时，应用程序还可以部署、访问、更新和删除 Azure 资源。 在测试期间，应用程序可能会达到这些运行时请求限制；在部署资源或目录对象时，可能会达到前面所述的服务限制。

## <a name="azure-ad-service-limits-relevant-to-testing"></a>与测试相关的 Azure AD 服务限制
可在[此处](/azure/active-directory/enterprise-users/directory-service-limits-restrictions)找到常规 Azure AD 使用量约束和服务限制。  可在[此处](/azure/azure-resource-manager/management/azure-subscription-service-limits)找到常规 Azure 订阅和服务限制、配额与约束。

下表列出了在设置测试环境或运行测试时需要考虑的 Azure AD 服务限制。 

| 类别         | 限制   |
|-------------------|----------------|
| 租户 | 单个用户最多可以创建 200 个目录。|
| 资源 | <ul><li>默认情况下，Azure Active Directory 免费版用户最多可以在单个租户中创建 50,000 个 Azure AD 资源。 如果你有至少一个经过验证的域，则组织的默认默认 Azure AD 服务配额会扩展到 300000 个 Azure AD 资源。 即使在你执行了内部管理员接管，并且组织已转换为具有至少一个已验证的域的托管租户之后，自助服务注册为组织创建的 Azure AD 服务配额仍然为 50,000 个 Azure AD 资源。 此服务限制与 Azure AD 定价页上 500000 个资源的定价层限制无关。 若要超过默认配额，必须联系 Microsoft 支持部门。</li><li>非管理员用户最多可以创建 250 个 Azure AD 资源。 活动资源和可还原的已删除资源都会计入此配额。 只能还原在不到 30 天前删除的 Azure AD 资源。 不再可还原的已删除 Azure AD 资源在 30 天内按四分之一的值计入此配额。 如果开发人员在其日常工作期间可能会反复超过此配额，你可以创建并分配一个自定义角色，并为此角色授予创建无限个应用注册的权限。</li></ul>|
| 应用程序| <ul><li>用户、组或服务主体最多可以有 1,500 个应用角色分配。</li><li>一位用户最多只能有 48 个应用配置用户名和密码凭据。</li></ul>|
| 应用程序清单| 最多可在应用程序清单中添加 1200 个条目。 |
| 组 |  <ul><li>一个非管理员用户最多可在 Azure AD 组织中创建 250 个组。 任何可以管理组织中的组的 Azure AD 管理员可以创建无限数量的组（最多可达 Azure AD 对象限额）。 如果指定了一个角色来删除用户的限制，请向其分配权限较低的内置角色，如“用户管理员”或“组管理员”。</li><li>一个 Azure AD 组织最多可以有 5000 个动态组。</li><li>在单个 Azure AD 组织（租户）中最多可以创建 300 个可分配角色的组。</li><li>任意数量的 Azure AD 资源都可以是单个组的成员。</li><li>一个用户可以是任意数量的组的成员。</li></ul>|
| Azure AD 角色和权限 | <ul><li>最多可在 Azure AD 组织中创建 30 个 Azure AD 自定义角色。</li><li>租户范围内单个主体的 Azure AD 自定义角色分配最多为 100 个。</li><li>非租户范围（例如管理单元或 Azure AD 对象）内单个主体的 Azure AD 内置角色分配最多为 100 个。 租户范围内的 Azure AD 内置角色分配没有限制。</li></ul>|


## <a name="throttling-limits-relevant-to-testing"></a>与测试相关的请求限制

以下全局 Microsoft Graph 请求限制适用：

| 请求类型 | 所有租户中的每个应用 |
|-------------------|----------------|
| 请求类型 | 所有租户中的每个应用 |
| 任意 | 每秒 2000 个请求| 

下表列出了在运行测试时需要考虑的 Azure AD 请求限制。 请求限制基于令牌桶算法，该算法会将请求的各项成本累加。 然后将请求成本总和与预先确定的限制进行比较。 只有超过了限制的请求才会受到请求限制。 有关请求成本的更详细信息，请参阅[标识和访问服务限制](/graph/throttling#pattern)。 可在[此处](/graph/throttling#service-specific-limits)找到 Microsoft Graph 的其他特定于服务的限制。

| 限制类型 | 资源单位配额 | 写入配额 |
|-------------------|----------------|----------------|
| 应用程序+租户对 | 每 10 秒 S:3500、M:5000、L:8000 个 | 每 2 分钟 30 秒 3000 个 |
| application | 每 20 秒 150,000 个 | 每 5 分钟 70,000 个 |
| tenant | 不适用 | 每 5 分钟 18,000 个 |

应用程序 + 租户对限制因请求所针对的租户中的用户数而异。 租户大小定义如下：S - 50 个以下的用户，M - 50 到 500 个用户，L - 500 个以上的用户。

## <a name="what-happens-when-a-throttling-limit-is-exceeded"></a>超过请求限制会发生什么情况？ 
请求限制行为取决于请求的类型和数目。 例如，如果请求量很高，则所有请求类型都会受到请求限制。 阈值限制根据请求类型的不同而异。 因此，可能会遇到写入被限制，但读取仍受允许的情况。

超过请求限制时，你将收到 HTTP 状态代码 `429 Too many requests`，并且请求会失败。 响应包含 `Retry-After` 头值，该值指定在发送下一个请求之前应用程序应该等待（或休眠）的秒数。 重试请求。  如果在尚未达到重试时间值的情况下发送请求，该请求不会得到处理，并会返回一个新的重试时间值。 如果请求再次失败并出现 429 错误代码，则表示你仍然受到请求限制。 继续使用建议的 `Retry-After` 延迟并重试请求，直到成功。

## <a name="next-steps"></a>后续步骤
了解如何[设置测试环境](test-setup-environment.md)。

