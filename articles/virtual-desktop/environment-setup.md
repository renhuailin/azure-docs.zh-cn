---
title: Azure 虚拟桌面环境 - Azure
description: 了解 Azure 虚拟桌面环境的基本元素，例如主机池和应用组。
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b6162657520f91168f46c43c1d6d7f5cbfbe6d38
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113031728"
---
# <a name="azure-virtual-desktop-environment"></a>Azure 虚拟桌面环境

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[本文](./virtual-desktop-fall-2019/environment-setup-2019.md)。

Azure 虚拟桌面是一种服务，使用户能够轻松安全地访问其虚拟化桌面和 RemoteApp。 本主题将详细介绍 Azure 虚拟桌面环境的一般结构。

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

若要将资源发布到用户，必须将用户分配给应用组。 将用户分配给应用组时，请注意以下事项：

- 可以将用户分配给同一主机池中的桌面应用组和 RemoteApp 应用组。 但是，用户只能为每个会话启动一种类型的应用组。 用户无法在单个会话中同时启动这两种类型的应用组。
- 可以将用户分配给同一主机池中的多个应用组，其源将是这两个应用组的累加。

## <a name="workspaces"></a>工作区

工作区是 Azure 虚拟桌面中的应用程序组的逻辑分组。 每个 Azure 虚拟桌面应用程序组必须与一个工作区关联，用户才能查看向其发布的远程应用和桌面。

## <a name="end-users"></a>最终用户

将用户分配到其应用组后，他们可以使用任何 Azure 虚拟桌面客户端连接到 Azure 虚拟桌面部署。

## <a name="next-steps"></a>后续步骤

若要了解有关委托访问以及如何将角色分配给用户的详细信息，请参阅 [Azure 虚拟桌面中的委托访问](delegated-access-virtual-desktop.md)。

若要了解如何设置 Azure 虚拟桌面主机池，请参阅[使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)。

若要了解如何连接到 Azure 虚拟桌面，请参阅以下文章之一：

- [使用 Windows 10 或 Windows 7 进行连接](./user-documentation/connect-windows-7-10.md)
- [使用 Web 浏览器建立连接](./user-documentation/connect-web.md)
- [使用 Android 客户端进行连接](./user-documentation/connect-android.md)
- [使用 macOS 客户端进行连接](./user-documentation/connect-macos.md)
- [使用 iOS 客户端进行连接](./user-documentation/connect-ios.md)