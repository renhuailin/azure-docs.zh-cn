---
title: 路由如何配合 Azure Dev Spaces 工作
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述驱动 Azure Dev Spaces 的进程以及路由的工作原理
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 2d2c6f336222b4ae0907d6579289a8cad8d73aa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91977963"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>路由如何配合 Azure Dev Spaces 工作

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces 为你提供了多种方法来快速循环访问和调试 Kubernetes 应用程序，并在 Azure Kubernetes 服务 (AKS) 群集上与团队协作。 项目在开发空间中运行之后，Azure Dev Spaces 会为项目提供其他网络和路由功能。

本文介绍路由如何配合 Dev Spaces 工作。

## <a name="how-routing-works"></a>路由的工作原理

开发空间构建在 AKS 之上，并使用相同的[网络概念](../aks/concepts-network.md)。 Azure Dev Spaces 还有一个集中的 ingressmanager 服务，并且会将其自己的入口控制器部署到 AKS 群集。 ingressmanager 服务会监视具有开发空间的 AKS 群集，并使用入口对象增强群集中的 Azure Dev Spaces 入口控制器，以便路由到应用程序 Pod。 每个 Pod 中的 devspaces-proxy 容器会根据 URL 将 HTTP 流量的 `azds-route-as` HTTP 标头添加到开发空间。 例如，对 URL http://azureuser.s.default.serviceA.fedcba09...azds.io 的请求会获取具有 `azds-route-as: azureuser` 的 HTTP 标头。 如果已经存在一个 `azds-route-as` 标头，devspaces-proxy 容器将不会添加该标头。

在从群集外部向服务发出 HTTP 请求时，该请求会转到入口控制器。 入口控制器会根据该请求的入口对象和规则将它直接路由到相应的 Pod。 Pod 中的 devspaces-proxy 容器接收该请求，根据 URL 添加 `azds-route-as` 标头，然后将该请求路由到应用程序容器。

在从群集中的其他服务向服务发出 HTTP 请求时，该请求会首先经过调用方服务的 devspaces-proxy 容器。 该 devspaces-proxy 容器会查看 HTTP 请求并检查 `azds-route-as` 标头。 根据该标头，devspaces-proxy 容器将会查找与该标头值关联的服务的 IP 地址。 如果找到了 IP 地址，devspaces-proxy 容器会将该请求重新路由到该 IP 地址。 如果未找到 IP 地址，devspaces-proxy 容器会将该请求路由到父级应用程序容器。

例如，应用程序 serviceA 和 serviceB 部署到名为“default”的父开发空间  。 serviceA 依赖于 serviceB 并向它发出 HTTP 调用 。 Azure 用户基于名为“azureuser”的 default 空间创建子开发空间 。 Azure 用户还会将其自己的 serviceA 版本部署到其子空间。 在向 http://azureuser.s.default.serviceA.fedcba09...azds.io 发出请求时：

![Azure Dev Spaces 路由](media/how-dev-spaces-works/routing.svg)

1. 入口控制器会查找与 URL 关联的 Pod（即 serviceA.azureuser）的 IP。
1. 入口控制器在 Azure 用户的开发空间中查找该 Pod 的 IP，并将该请求路由到 serviceA.azureuser Pod。
1. serviceA.azureuser Pod 中的 devspaces-proxy 容器会接收该请求，并添加 `azds-route-as: azureuser` 作为 HTTP 标头。
1. serviceA.azureuser Pod 中的 devspaces-proxy 容器会将该请求路由到 serviceA.azureuser Pod 中的 serviceA 应用程序容器  。
1. serviceA.azureuser Pod 中的 serviceA 应用程序会发出对 serviceB 的调用  。 serviceA 应用程序还包含用于保留现有 `azds-route-as` 标头（在本例中为 `azds-route-as: azureuser`）的代码。
1. serviceA.azureuser Pod 中的 devspaces-proxy 容器会接收该请求，并根据 `azds-route-as` 标头的值查找 serviceB 的 IP 。
1. serviceA.azureuser Pod 中的 devspaces-proxy 容器找不到 serviceB.azureuser 的 IP 。
1. serviceA.azureuser Pod 中的 devspaces-proxy 容器会在父空间（即 serviceB.default）中查找 serviceB 的 IP  。
1. serviceA.azureuser Pod 中的 devspaces-proxy 容器会查找 serviceB.default 的 IP，并将该请求路由到 serviceB.default Pod  。
1. serviceB.default Pod 中的 devspaces-proxy 容器会接收该请求，并将该请求路由到 serviceB.default Pod 中的 serviceB 应用程序容器  。
1. serviceB.default Pod 中的 serviceB 应用程序会将响应返回到 serviceA.azureuser Pod  。
1. serviceA.azureuser Pod 中的 devspaces-proxy 容器会接收该响应，并将该响应路由到 serviceA.azureuser Pod 中的 serviceA 应用程序容器  。
1. serviceA 应用程序会接收该响应，然后返回其自己的响应。
1. serviceA.azureuser Pod 中的 devspaces-proxy 容器会从 serviceA 应用程序容器接收该响应，并将该响应路由到位于群集外部的原始调用方 。

所有其他非 HTTP 的 TCP 流量均通过入口控制器传递并且 devspaces-proxy 容器未经修改。

## <a name="sharing-a-dev-space"></a>共享开发空间

在与团队合作时，可以在整个团队中共享开发空间，并且可以创建派生的开发空间。 任何对开发人员空间的资源组具有参与者访问权限的人员均可使用该开发空间。

还可以创建派生自另一开发空间的新开发空间。 在创建派生的开发空间时，azds.io/parent-space=PARENT-SPACE-NAME 标签会添加到该派生的开发空间的命名空间。 此外，父开发空间中的所有应用程序都会与该派生的开发空间共享。 如果将应用程序的更新版本部署到该派生的开发空间，该应用程序版本将只存在于该派生的开发空间中，父开发空间将不会受到影响。 最多可以有三个级别的派生开发空间或祖父级空间。

派生的开发空间还会智能地在其自己的应用程序和从其父级共享的应用程序之间路由请求。 路由的工作方式是：尝试将请求路由到派生的开发空间中的应用程序，并从父开发空间回退到共享的应用程序。 如果应用程序不在父空间中，则路由将会回退到祖父级空间中的共享应用程序。

例如：
* default 开发空间具有应用程序 serviceA 和 serviceB  。
* azureuser 开发空间派生自 default 。
* serviceA 的更新版本会部署到 azureuser 。

在使用 azureuser 时，对 serviceA 的所有请求都将路由到 azureuser 中的更新版本  。 对 serviceB 的请求将首先尝试被路由到 serviceB 的 azureuser 版本  。 由于该版本不存在，因此该请求将会被路由到 serviceB 的 default 版本 。 如果删除 serviceA 的 azureuser 版本，则对 serviceA 的所有请求都将会回退到使用 serviceA 的 default 版本    。

## <a name="next-steps"></a>后续步骤

若要查看 Azure Dev Spaces 如何使用路由来提供快速迭代和开发的示例，请参阅[使用 Azure Dev Spaces 远程调试代码的工作原理][how-it-works-remote-debugging]。


[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md