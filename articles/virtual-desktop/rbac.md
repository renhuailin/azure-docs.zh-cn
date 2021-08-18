---
title: Azure 虚拟桌面内置角色 - Azure
description: 适用于 Azure RBAC 的 Azure 虚拟桌面内置角色的概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 25628d4982e2675a6ea818284f4c629b16f22064
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747940"
---
# <a name="built-in-roles-for-azure-virtual-desktop"></a>Azure 虚拟桌面内置角色

Azure 虚拟桌面使用 Azure 基于角色的访问控制 (RBAC) 为用户和管理员分配角色。 这些角色授予管理员权限来执行某些任务。 若要了解有关适用于 Azure RBAC 的内置角色的详细信息，请参阅 [Azure 内置角色](../role-based-access-control/built-in-roles.md)。

Azure 的标准内置角色为所有者、参与者和读取者。 但是，Azure 虚拟桌面具有其他角色，用于将主机池、应用程序组和工作区的管理角色分离开来。 这种分离使你可以更精细地控制管理任务。 这些角色的命名遵循 Azure 的标准角色和最低特权方法。

Azure 虚拟桌面没有特定的所有者角色。 但是，你可以对服务对象使用标准所有者角色。

## <a name="desktop-virtualization-contributor"></a>桌面虚拟化参与者

桌面虚拟化参与者角色允许你管理部署的所有方面。 但是，它不会授予你对计算资源的访问权限。 你还需要用户访问管理员角色，以向用户或用户组发布应用程序组。


- Microsoft.DesktopVirtualization/\* 
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-reader"></a>桌面虚拟化读取者

桌面虚拟化读取者角色允许你查看部署中的所有内容，但不允许进行任何更改。

- Microsoft.DesktopVirtualization/\*/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-host-pool-contributor"></a>桌面虚拟化主机池参与者

主机池参与者角色允许你管理主机池的所有方面，包括对资源的访问。 你将需要额外的参与者角色“虚拟机参与者”来创建虚拟机。 你将需要应用程序组和工作区参与者角色，以使用门户创建主机池，或者可以使用桌面虚拟化参与者角色。

以下列表描述了此角色可以访问的权限：

- Microsoft.DesktopVirtualization/hostpools/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-host-pool-reader"></a>桌面虚拟化主机池读取者

主机池读取者角色允许你查看主机池中的所有内容，但不允许进行任何更改。

- Microsoft.DesktopVirtualization/hostpools/\*/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-application-group-contributor"></a>桌面虚拟化应用程序组参与者

应用程序组参与者角色允许你管理应用程序组的所有方面。 如果要向用户或用户组发布应用程序组，则需要用户访问管理员角色。

以下列表描述了此角色可以访问的权限：

- Microsoft.DesktopVirtualization/applicationgroups/\*
- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-application-group-reader"></a>桌面虚拟化应用程序组读取者

应用程序组读取者角色允许你查看应用程序组中的所有内容，但不允许进行任何更改。

以下列表描述了此角色可以访问的权限：

- Microsoft.DesktopVirtualization/applicationgroups/\*/read
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-workspace-contributor"></a>桌面虚拟化工作区参与者

工作区参与者角色允许你管理工作区的所有方面。 若要获取有关添加到应用程序组中的应用程序的信息，还需具备“应用程序组读取者”角色。

以下列表描述了此角色可以访问的权限：

- Microsoft.DesktopVirtualization/workspaces/\*
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/\*
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-workspace-reader"></a>桌面虚拟化工作区读取者

工作区读取者角色允许你查看工作区中的所有内容，但不允许进行任何更改。

以下列表描述了此角色可以访问的权限：

- Microsoft.DesktopVirtualization/workspaces/read
- Microsoft.DesktopVirtualization/applicationgroups/read
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-user-session-operator"></a>桌面虚拟化用户会话操作员

用户会话操作员角色允许你发送消息、断开会话，并使用“注销”功能将会话从会话主机中注销。 但是，此角色不允许你执行会话主机管理，如删除会话主机、更改排出模式等。 此角色可以查看分配，但不能修改管理员。 建议将此角色分配给特定的主机池。 如果在资源组级别授予此权限，则管理员将对资源组下的所有主机池具有读取权限。

以下列表描述了此角色可以访问的权限：

- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*

## <a name="desktop-virtualization-session-host-operator"></a>桌面虚拟化会话主机操作员

会话主机操作员角色允许你查看和删除会话主机，以及更改排出模式。 它们不能使用 Azure 门户添加会话主机，因为它们没有对主机池对象的写入权限。 如果注册令牌有效（生成且未过期），并且管理员通过虚拟机参与者角色获得了计算权限，则可以使用此角色将会话主机添加到 Azure 门户外部的主机池。

以下列表描述了此角色可以访问的权限：

- Microsoft.DesktopVirtualization/hostpools/read
- Microsoft.DesktopVirtualization/hostpools/sessionhosts/\*
- Microsoft.Resources/subscriptions/resourceGroups/read
- Microsoft.Resources/deployments/read
- Microsoft.Authorization/\*/read
- Microsoft.Insights/alertRules/\*
- Microsoft.Support/\*
