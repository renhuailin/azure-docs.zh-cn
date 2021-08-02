---
title: Azure 虚拟桌面（经典）环境 - Azure
description: Azure 虚拟桌面（经典）环境的基本元素。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9062565f5c13d7b278b57068df79c1bb4de66424
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745054"
---
# <a name="azure-virtual-desktop-classic-environment"></a>Azure 虚拟桌面（经典）环境

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[本文](../environment-setup.md)。

Azure 虚拟桌面是一种服务，使用户能够轻松安全地访问其虚拟化桌面和 RemoteApp。 本主题将详细介绍 Azure 虚拟桌面环境的一般结构。

## <a name="tenants"></a>租户

Azure 虚拟桌面租户是用于管理 Azure 虚拟桌面环境的主要界面。 每个 Azure 虚拟桌面租户必须与包含要登录到环境的用户的 Azure Active Directory 相关联。 从 Azure 虚拟桌面租户中，可以开始创建主机池来运行用户的工作负荷。

## <a name="host-pools"></a>主机池

主机池是在运行 Azure 虚拟桌面代理时注册到 Azure 虚拟桌面作为会话主机的 Azure 虚拟机的集合。 主机池中的所有会话主机虚拟机都应源自同一映像，以实现一致的用户体验。

主机池可以是以下两种类型之一：

- 个人主机池，其中每个会话主机分配给各个用户。
- 共用主机池，其中会话主机可以接受来自主机池内某个应用组的任何授权用户的连接。

可以在主机池上设置附加属性，以更改其负载均衡行为、每个会话主机可以使用的会话数，以及用户在登录到 Azure 虚拟桌面会话时可以对主机池中会话主机执行的操作。 可以通过应用组控制发布给用户的资源。

## <a name="app-groups"></a>应用组

应用组是在主机池中的会话主机上安装的应用程序的逻辑分组。 应用组可以是以下两种类型之一：

- RemoteApp，其中用户可访问单独选择并发布到应用组的 RemoteApp
- 桌面，其中用户可访问完整桌面

默认情况下，在创建主机池时，将自动创建一个桌面应用组（名为“桌面应用程序组”）。 可以随时删除此应用组。 但是，如果存在桌面应用组，则无法在主机池中创建另一个桌面应用组。 若要发布 RemoteApp，必须创建 RemoteApp 应用组。 可以创建多个 RemoteApp 应用组来适应不同的辅助角色方案。 不同的 RemoteApp 应用组还可以包含重叠的 RemoteApp。

若要将资源发布到用户，必须将用户分配给应用组。 将用户分配到应用组时，请注意以下事项：

- 不可以将用户分配给同一主机池中的桌面应用组和 RemoteApp 应用组。
- 可以将用户分配给同一主机池中的多个应用组，其源将是这两个应用组的累积。

## <a name="tenant-groups"></a>租户组

在 Azure 虚拟桌面中，大多数设置和配置都会在 Azure 虚拟桌面租户中发生。 Azure 虚拟桌面租户包含主机池、应用组和应用组用户分配。 但是，在某些情况下，可能需要同时管理多个 Azure 虚拟桌面租户，特别是当你是云服务提供商 (CSP) 或托管合作伙伴时。 在这些情况下，可以使用自定义 Azure 虚拟桌面租户组放置每个客户的 Azure 虚拟桌面租户，并集中管理访问权限。 但是，如果只管理单个 Azure 虚拟桌面租户，则租户组概念不适用，可以继续操作并管理默认租户组中存在的租户。

## <a name="end-users"></a>最终用户

将用户分配到其应用组后，他们可以使用任何 Azure 虚拟桌面客户端连接到 Azure 虚拟桌面部署。

## <a name="next-steps"></a>后续步骤

若要了解有关委托访问以及如何将角色分配给用户的详细信息，请参阅 [Azure 虚拟桌面中的委托访问](delegated-access-virtual-desktop-2019.md)。

若要了解如何设置 Azure 虚拟桌面租户，请参阅[在 Azure 虚拟桌面中创建租户](tenant-setup-azure-active-directory.md)。

若要了解如何连接到 Azure 虚拟桌面，请参阅以下文章之一：

- [从 Windows 10 或 Windows 7 进行连接](connect-windows-7-10-2019.md)
- [从 Web 浏览器进行连接](connect-web-2019.md)
