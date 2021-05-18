---
title: Azure AD 密码保护 - Azure Active Directory
description: 使用 Azure AD 密码保护在本地 Active Directory 域服务环境中禁止使用弱密码
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5df1cb158821fb0cd85d90f9ba3b79d80adf45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743915"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>为 Active Directory 域服务强制实施本地 Azure AD 密码保护

Azure AD 密码保护可以检测并阻止已知的弱密码及其变种，还可以阻止特定于你的组织的其他弱术语。 Azure AD 密码保护的本地部署使用 Azure AD 中存储的相同的全局和自定义禁止密码列表，并检查本地密码更改，执行的检查与 Azure AD 与针对基于云的更改进行的检查相同。 将在密码更改和密码重置事件期间，针对本地 Active Directory 域服务 (AD DS) 域控制器执行这些检查。

## <a name="design-principles"></a>设计原理

Azure AD 密码保护的设计遵循以下原则：

* 域控制器 (DC) 不必直接与 Internet 通信。
* DC 上未打开任何新的网络端口。
* 不需要 AD DS 架构更改。 该软件使用现有的 AD DS 容器和 serviceConnectionPoint 架构对象。 
* 不要求最低 AD DS 域或林功能级别 (DFL/FFL)。
* 该软件不会在它保护的 AD DS 域中创建或要求帐户。
* 用户明文密码永远不会离开域控制器（无论是在密码验证操作期间，还是在其他任何时间）。
* 该软件不依赖于其他 Azure AD 功能。 例如，Azure AD 密码哈希同步 (PHS) 与 Azure AD 密码保护无关或后者不需要前者。
* 支持增量部署，但仅在安装了域控制器代理（DC 代理）的情况下才会强制执行。

## <a name="incremental-deployment"></a>增量部署

Azure AD 密码保护支持在 AD DS 域中的所有 DC 之间进行增量部署。 请务必了解真正这一方面的真正含义以及取舍权衡。

Azure AD 密码保护 DC 代理软件只有安装在 DC 上时才能验证密码，并且仅对发送到该 DC 的密码更改进行验证。 无法控制 Windows 客户端计算机选择哪些 DC 来处理用户密码更改。 为了保证行为的一致性和通用 Azure AD 密码保护安全实施，必须在域中的所有 DC 上安装 DC 代理软件。

许多组织希望在全面部署之前在其 DC 的子集上仔细测试 Azure AD 密码保护。 为了支持这种情况，Azure AD 密码保护支持部分部署。 即使域中的其他 DC 未安装 DC 代理软件，给定 DC 上的 DC 代理软件也会主动验证密码。 这种部分部署类型不太安全，因此建议仅用于测试目的。

## <a name="architectural-diagram"></a>体系结构图

在本地 AD DS 环境中部署 Azure AD 密码保护之前，请务必了解基础的设计和功能概念。 下图显示了 Azure AD 密码保护的组件如何协同工作：

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Azure AD 密码保护代理服务在当前 AD DS 林中任何已加入域的计算机上运行。 服务的主要用途是将来自 DC 的密码策略下载请求转发到 Azure AD，然后将响应从 Azure AD 返回到 DC。
* DC 代理的密码筛选器 DLL 接收来自操作系统的用户密码验证请求。 筛选器将这些请求转发到在 DC 上本地运行的 DC 代理服务。
* Azure AD 密码保护的 DC 代理服务从 DC 代理的密码筛选器 DLL 接收密码验证请求。 DC 代理服务通过使用当前（本地可用）密码策略来处理这些请求，并返回结果 pass 或 fail。 

## <a name="how-azure-ad-password-protection-works"></a>Azure AD 密码保护的工作原理

本地 Azure AD 密码保护组件的工作原理如下所示：

1. 每个 Azure AD 密码保护代理服务实例通过在 Active Directory 中创建 serviceConnectionPoint 对象，将自身播发到林中的 DC。

    Azure AD 密码保护的每个 DC 代理服务还在 Active Directory 中创建一个 serviceConnectionPoint 对象。 此对象主要用于报告和诊断。

1. DC 代理服务负责发起从 Azure AD 下载新密码策略的操作。 第一步是通过在林中查询代理 serviceConnectionPoint 对象，找到 Azure AD 密码保护代理服务。

1. 当找到可用的代理服务时，DC 代理向代理服务发送密码策略下载请求。 代理服务会将请求发送到 Azure AD，然后将响应返回到 DC 代理服务。

1. DC 代理服务从 Azure AD 收到新的密码策略后，该服务将该策略存储在其域 sysvol 文件夹共享的根目录中的专用文件夹内。 DC 代理服务还会监视此文件夹，以确定是否从域中的其他 DC 代理服务复制了更新的策略。

1. DC 代理服务始终在服务启动时请求新策略。 DC 代理服务启动后，会每小时检查一次当前本地可用策略的存在时间。 如果策略存在时间超过一小时，DC 代理会通过代理服务从 Azure AD 请求新策略，如前所述。 如果当前策略的存在时间不超过一小时，DC 代理将继续使用该策略。

1. 当 DC 收到接收密码更改事件时，缓存的策略用于确定是接受还是拒绝新密码。

### <a name="key-considerations-and-features"></a>关键注意事项和功能

* 每当下载 Azure AD 密码保护密码策略时，该策略特定于租户。 换句话说，密码策略始终是 Microsoft 全局禁止密码列表和每租户自定义禁止密码列表的组合。
* DC 代理通过 TCP 通过 RPC 与代理服务通信。 代理服务在动态或静态 RPC 端口上侦听这些调用，具体取决于配置。
* DC 代理从不侦听网络可用的端口。
* 代理服务从不调用 DC 代理服务。
* 代理服务是无状态的。 它从不缓存策略，也从不缓出从 Azure 下载的任何其他状态。
* DC 代理服务始终使用最新的本地可用密码策略来评估用户的密码。 如果本地 DC 上没有可用的密码策略，将自动接受该密码。 发生这种情况时，将记录一条事件消息以警告管理员。
* Azure AD 密码保护不是实时的策略应用程序引擎。 杂技 Azure AD 中进行密码策略配置更改的时间与在所有 DC 上分发并实施更改的时间之间可能会有延迟。
* Azure AD 密码保护作为现有 AD DS 密码策略的补充，而不是替代。 这包括任何其他可能已安装的第三方密码筛选器 dll。 AD DS 始终要求在接受密码之前，所有密码验证组件都必须同意。

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Azure AD 密码保护的林/租户绑定

在 AD DS 林中部署 Azure AD 密码保护需要将该林注册到 Azure AD。 还必须将部署的每个代理服务注册到 Azure AD。 这些林和代理注册与特定 Azure AD 租户相关联，该租户由注册期间使用的凭据隐式标识。

林中的 AD DS 林和所有已部署的代理服务必须注册到同一个租户。 不支持将 AD DS 林或该林中的任何代理服务注册到其他 Azure AD 租户。 这种配置错误的部署的症状包括无法下载密码策略。

> [!NOTE]
> 因此，具有多个 Azure AD 租户的客户必须选择一个可分辨租户来注册每个林，以实现 Azure AD 密码保护的目的。

## <a name="download"></a>下载

[Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)提供了两个用于 Azure AD 密码保护的必需代理安装程序。

## <a name="next-steps"></a>后续步骤

要开始使用本地 Azure AD 密码保护，请完成以下操作说明：

> [!div class="nextstepaction"]
> [部署本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
