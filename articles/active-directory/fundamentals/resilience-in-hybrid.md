---
title: 在 Azure Active Directory 中构建更灵活的混合身份验证
description: 介绍构建弹性混合基础结构的架构师和 IT 管理员。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c45b362bc37df71346fc3b635c8ae4a51f62cdc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919341"
---
# <a name="build-resilience-in-your-hybrid-architecture"></a>在混合体系结构中构建复原能力

混合身份验证允许用户使用其在本地的标识来访问基于云的资源。 混合基础结构包括云和本地组件。

* 云组件包括 Azure AD、Azure 资源和服务、组织的基于云的应用和 SaaS 应用程序。

* 本地组件包含本地应用程序、SQL 数据库等资源，以及 Windows Server Active Directory 之类的标识提供程序。 

> [!IMPORTANT]
> 在混合基础结构中规划复原能力时，最大程度地减少了依赖关系和单点故障。 

Microsoft 提供了三种混合身份验证机制。 这些选项按复原顺序列出。 建议尽可能实现密码哈希同步。

* [密码哈希同步](../hybrid/whatis-phs.md) (PHS) 使用 Azure AD Connect 将密码哈希的哈希值与 Azure AD 进行同步，使用户能够使用其本地密码控制登录到基于云的资源。 PHS 只有用于同步的本地依赖项，不能用于身份验证。

* [传递身份验证](../hybrid/how-to-connect-pta.md) (PTA) 将用户重定向到 Azure AD 进行登录。 然后，通过部署在企业网络中的代理，根据本地 Active Directory 验证用户名和密码。 PTA 在本地占用了其 Azure AD PTA 驻留在本地服务器上的代理。

* [联合](../hybrid/whatis-fed.md) 客户部署 AD FS 的联合身份验证服务，然后 Azure AD 验证联合身份验证服务生成的 SAML 断言。 联合在本地基础结构上的相关性最高，因此更多的故障点。 

   
你可能会在你的组织中使用一种或多种方法。 有关详细信息，请参阅[为 Azure AD 混合标识解决方案选择正确的身份验证方法](../hybrid/choose-ad-authn.md)。 本文包含可帮助您决定方法的决策树。

## <a name="password-hash-synchronization"></a>密码哈希同步

Azure AD 的最简单且最灵活的混合身份验证选项是 [密码哈希同步](../hybrid/whatis-phs.md) ，在处理身份验证请求时，它不会有任何本地标识基础结构依赖关系。 在将具有密码哈希的标识同步到 Azure AD 后，用户可以对不依赖本地标识组件的云资源进行身份验证。 

![PHS 的体系结构关系图](./media/resilience-in-hybrid/admin-resilience-password-hash-sync.png)

如果选择此身份验证选项，则在本地标识组件变为不可用时，不会出现中断。 发生本地中断的原因有很多，包括硬件故障、电源中断、自然灾害和恶意软件攻击。 

### <a name="how-do-i-implement-phs"></a>如何实现实现 PHS？

若要实现 PHS，请参阅以下资源：

* [与 Azure AD Connect 实现密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md)

* [启用密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md)

如果你的要求不能使用 PHS，请使用传递身份验证。

## <a name="pass-through-authentication"></a>直通身份验证

传递身份验证依赖于位于服务器上本地的身份验证代理。 持久性连接（或服务总线）存在于 Azure AD 与本地 PTA 代理之间。 承载身份验证代理的服务器以及本地 Windows Server Active Directory (或其他标识提供者) 都是潜在的故障点。 

![PTA 的体系结构关系图](./media/resilience-in-hybrid/admin-resilience-pass-through-authentication.png)

### <a name="how-do-i-implement-pta"></a>如何实现实现 PTA？

若要实现直通身份验证，请参阅以下资源。

* [直通身份验证的工作原理](../hybrid/how-to-connect-pta-how-it-works.md)

* [直通身份验证安全性深入研究](../hybrid/how-to-connect-pta-security-deep-dive.md)

* [安装 Azure AD 传递身份验证](../hybrid/how-to-connect-pta-quick-start.md)

* 如果使用的是 PTA，请定义 [高度可用的拓扑](../hybrid/how-to-connect-pta-quick-start.md)。

 ## <a name="federation"></a>联合

联合涉及在 Azure AD 与联合身份验证服务之间创建信任关系，其中包括终结点、令牌签名证书和其他元数据的交换。 请求进入 Azure AD 时，它会读取配置，并将用户重定向到配置的终结点。 此时，用户将与联合身份验证服务进行交互，后者会发出 Azure AD 验证的 SAML 断言。 

下图显示了企业 Active Directory 联合身份验证服务 (的拓扑，该拓扑包含跨多个本地数据中心的冗余联合服务器和 web 应用程序代理服务器的 AD FS) 。 此配置依赖于企业网络基础结构组件，例如 DNS、网络负载平衡和地理关联功能、防火墙等。所有本地组件和连接都容易出现故障。 有关详细信息，请访问 [AD FS 容量规划文档](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity) 。

> [!NOTE]
>  联合具有最大数量的本地依赖项，因此，最可能的故障点。 虽然此图显示了 AD FS，但其他本地标识提供者可能会根据类似的设计注意事项来实现高可用性、可伸缩性和故障转移。

![联合的体系结构关系图](./media/resilience-in-hybrid/admin-resilience-federation.png)

 ### <a name="how-do-i-implement-federation"></a>如何实现实现联合？

如果要实现联合身份验证策略或要使其更具弹性，请参阅以下资源。

* [什么是联合身份验证](../hybrid/whatis-fed.md)

* [联合身份验证的工作原理](../hybrid/how-to-connect-fed-whatis.md)

* [Azure AD 联合身份验证兼容性列表](../hybrid/how-to-connect-fed-compatibility.md)

* 遵循 [AD FS 容量规划文档](https://docs.microsoft.com/windows-server/identity/ad-fs/design/planning-for-ad-fs-server-capacity)

* [在 Azure IaaS 中部署 AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)

* [启用 PHS](../hybrid/tutorial-phs-backup.md) 以及联合

## <a name="next-steps"></a>后续步骤
适用于管理员和架构师的复原资源
 
* [通过凭据管理构建复原能力](resilience-in-credentials.md)

* [用设备状态生成复原能力](resilience-with-device-states.md)

* [使用持续存取评估 (CAE) 生成复原能力 ](resilience-with-continuous-access-evaluation.md)

* [在外部用户身份验证中构建复原能力](resilience-b2b-authentication.md)

* [使用应用程序代理通过应用程序访问生成恢复能力](resilience-on-premises-access.md)

面向开发人员的复原资源

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)
