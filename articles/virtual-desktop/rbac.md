---
title: 内置角色 Windows 虚拟桌面-Azure
description: 适用于 Azure RBAC 的 Windows 虚拟桌面的内置角色概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 52f0151fd4be7505cf7beea0eeb54f8e34404997
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577617"
---
# <a name="built-in-roles-for-windows-virtual-desktop"></a>Windows 虚拟桌面的内置角色

Windows 虚拟桌面使用 Azure 基于角色的访问控制 (RBAC) 将角色分配给用户和管理员。 这些角色授予管理员权限来执行特定任务。 若要了解有关 Azure RBAC 的内置角色的详细信息，请参阅 [azure 内置角色](../role-based-access-control/built-in-roles.md)。

Azure 的标准内置角色为所有者、参与者和读者。 但是，Windows 虚拟桌面具有其他角色，使你可以为主机池、应用组和工作区分隔管理角色。 这种分离使你可以更精细地控制管理任务。 这些角色的命名遵循 Azure 的标准角色和最小特权方法。

Windows 虚拟桌面没有特定的所有者角色。 但是，你可以对服务对象使用标准所有者角色。

## <a name="desktop-virtualization-contributor"></a>桌面虚拟化参与者

桌面虚拟化参与者角色可用于管理部署的各个方面。 但是，它不会授予你对计算资源的访问权限。 还需要用户访问管理员角色才能向用户或用户组发布应用程序组。


- DesktopVirtualization/\* 
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-reader"></a>桌面虚拟化读者

桌面虚拟化读者角色允许您查看部署中的所有内容，但不允许进行任何更改。

- DesktopVirtualization/ \* /read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-contributor"></a>主机池参与者

主机池参与者角色可用于管理主机池的所有方面，包括对资源的访问权限。 你将需要额外的参与者角色 "虚拟机参与者" 来创建虚拟机。 你将需要 AppGroup 和工作区参与者角色才能使用门户创建主机池，或者可以使用桌面虚拟化参与者角色。

以下列表描述了此角色可以访问的权限：

- DesktopVirtualization/hostpools/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="host-pool-reader"></a>主机池读取器

主机池读取者角色允许您查看主机池中的所有内容，但不允许您进行任何更改。

- DesktopVirtualization/hostpools/ \* /read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-contributor"></a>应用程序组参与者

利用应用程序组参与者角色，你可以管理应用组的所有方面。 如果要将应用组发布到用户或用户组，则需要 "用户访问管理员" 角色。

以下列表描述了此角色可以访问的权限：

- DesktopVirtualization/applicationgroups/\*
- DesktopVirtualization/hostpools/read
- DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="application-group-reader"></a>应用程序组读取器

利用应用程序组读取者角色，你可以查看应用组中的所有内容，并且不会允许你进行任何更改。

以下列表描述了此角色可以访问的权限：

- DesktopVirtualization/applicationgroups/ \* /read
- DesktopVirtualization/applicationgroups/read
- DesktopVirtualization/hostpools/read
- DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-contributor"></a>工作区参与者

工作区参与者角色使你可以管理工作区的所有方面。 若要获取添加到应用组中的应用程序的信息，还需要为应用程序组读取者角色分配。

以下列表描述了此角色可以访问的权限：

- DesktopVirtualization/工作区/\*
- DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="workspace-reader"></a>工作区读者

工作区读者角色使你可以查看工作区中的所有内容，但不允许你进行任何更改。

以下列表描述了此角色可以访问的权限：

- DesktopVirtualization/工作区/读取
- DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="user-session-operator"></a>用户会话操作员

用户会话操作员角色允许您发送消息、断开连接会话，并使用 "注销" 功能对会话主机以外的会话进行签名。 但是，此角色不允许执行会话主机管理，如删除会话主机、更改排出模式等。 此角色可以查看分配，但不能修改管理员。 建议将此角色分配给特定的主机池。 如果在资源组级别授予此权限，则管理员将对资源组下的所有主机池具有读取权限。

以下列表描述了此角色可以访问的权限：

- DesktopVirtualization/hostpools/read
- DesktopVirtualization/hostpools/sessionhosts/read
- DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="session-host-operator"></a>Session Host 运算符

"会话主机参与者" 角色允许您查看和删除会话主机，以及更改排出模式。 它们不能使用 Azure 门户添加会话主机，因为它们对主机池对象没有写入权限。 如果注册令牌有效 (生成且未过期) ，则可以使用此角色将会话主机添加到外部 Azure 门户的主机池（如果管理员通过虚拟机参与者角色提供了计算权限）。

以下列表描述了此角色可以访问的权限：

- DesktopVirtualization/hostpools/read
- DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*
