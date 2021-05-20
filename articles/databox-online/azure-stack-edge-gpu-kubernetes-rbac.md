---
title: 了解 Azure Stack Edge Pro 设备上的 Kubernetes 基于角色的访问控制 | Microsoft Docs
description: 介绍 Kubernetes 基于角色的访问控制在 Azure Stack Edge Pro 设备上的实现方式。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 1b38a9807e05385a378fa6103710fb6b393c7b1f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443142"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 设备上的 Kubernetes 基于角色的访问控制

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

在 Azure Stack Edge Pro 设备上，配置计算角色时，会创建 Kubernetes 群集。 可以使用 Kubernetes 基于角色的访问控制 (Kubernetes RBAC) 来限制对设备上的群集资源的访问。

本文概述了 Kubernetes 提供的 Kubernetes RBAC 系统，以及如何在 Azure Stack Edge Pro 设备上实现 Kubernetes RBAC。 

## <a name="kubernetes-rbac"></a>Kubernetes RBAC

使用 Kubernetes RBAC，可以向用户或用户组分配执行各种操作的权限，例如创建或修改资源，或者查看正在运行的应用程序工作负载的日志。 可将这些权限的范围限制为单个命名空间，也可以授予面向整个群集的权限。 

设置 Kubernetes 群集时，会创建与此群集对应的单个用户，称为群集管理员用户。  `kubeconfig` 文件与群集管理员用户相关联。 `kubeconfig` 文件是一个文本文件，其中包含连接到群集以对用户进行身份验证所需的所有配置信息。

## <a name="namespaces-types"></a>命名空间类型

Kubernetes 资源（如 Pod 和部署）以逻辑方式分组到命名空间中。 这些分组提供了一种以逻辑方式划分 Kubernetes 群集并限制创建、查看或管理资源访问权限的方法。 用户只能与分配的命名空间内的资源进行交互。

命名空间适用于多个用户分布在多个团队或项目的环境。 有关详细信息，请参阅 [Kubernetes 命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)。

Azure Stack Edge Pro 设备具有以下命名空间：

- **系统命名空间** - 此命名空间是核心资源的所在位置，例如 DNS 和代理等网络功能或 Kubernetes 仪表板。 通常不会将应用程序部署到此命名空间中。 使用此命名空间调试任何 Kubernetes 群集问题。 

    设备上有多个系统命名空间，并且会保留与这些系统命名空间对应的名称。 下面是保留的系统命名空间列表： 
    - kube-system
    - metallb-system
    - dbe-namespace
    - default
    - kubernetes-dashboard
    - kube-node-lease
    - kube-public


    切勿将任何保留名称用于你创建的用户命名空间。 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **用户命名空间** - 可通过 kubectl 或通过设备的 PowerShell 接口创建这些命名空间，用于在本地部署应用程序。
 
- **IoT Edge 命名空间** - 可连接到此 `iotedge` 命名空间以管理通过 IoT Edge 部署的应用程序。

- **Azure Arc 命名空间** - 可连接到此 `azure-arc` 命名空间以管理通过 Azure Arc 部署的应用程序。利用 Azure Arc，还可以将应用程序部署到其他用户命名空间中。 

## <a name="namespaces-and-users"></a>命名空间和用户

实际上，将群集划分为多个命名空间非常重要。 

- **多个用户**：如果你有多个用户，则多个命名空间将允许这些用户独立地将其应用程序和服务分别部署在其特定的命名空间中。 
- **单个用户**：即使只有一个用户，多个命名空间也将允许该用户在同一个 Kubernetes 群集中运行多个版本的应用程序。

### <a name="roles-and-rolebindings"></a>角色和角色绑定

Kubernetes 具有角色和角色绑定的概念，使你能够在命名空间级别和群集级别向用户或资源提供权限。 

- **角色**：你可以将用户的权限定义为“角色”，然后使用“角色”在命名空间中授予权限 。 
- **角色绑定**：定义角色后，可以使用“角色绑定”为给定的命名空间分配角色。 

此方法可以从逻辑上分离单个 Kubernetes 群集，使用户只能访问向其分配的命名空间中的应用程序资源。 

## <a name="kubernetes-rbac-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 上的 Kubernetes RBAC

在 Kubernetes RBAC 的当前实现中，通过 Azure Stack Edge Pro，可从受限制的 PowerShell 运行空间执行以下操作：

- 创建命名空间。  
- 创建其他用户。
- 向你授予对你创建的命名空间的管理员访问权限。 请记住，你不能访问群集管理员角色或查看群集范围内的资源。
- 获取 `kubeconfig` 文件，其中包含用于访问 Kubernetes 群集的信息。


Azure Stack Edge Pro 设备具有多个系统命名空间，你可以创建具有 `kubeconfig` 文件的用户命名空间以访问这些命名空间。 用户可以完全控制这些命名空间，可以创建或修改用户，也可以授予用户访问权限。 只有群集管理员具有对系统命名空间和群集范围内的资源的完全访问权限。 `aseuser` 具有对系统命名空间的只读访问权限。

下图描述了 Azure Stack Edge Pro 设备上的 Kubernetes RBAC 实现方式。

![Azure Stack Edge Pro 设备上的 Kubernetes RBAC](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

在该图中，Alice、Bob 和 Chuck 具有对分配的用户命名空间的访问权限，在本例中分别为 `ns1`、`ns2` 和 `ns3`。 在这些命名空间内，他们具有管理员访问权限。 另一方面，群集管理员具有对系统命名空间和群集范围内的资源的管理员访问权限。

用户可以创建命名空间和用户、将用户分配到命名空间或下载 `kubeconfig` 文件。 有关详细的分步说明，请转到[通过 kuebctl 在 Azure Stack Edge Pro 上访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。


在 Azure Stack Edge Pro 设备上使用命名空间和用户时，请注意以下事项：

- 对于任何系统命名空间，你不能执行任何操作，如创建用户、向用户授予或撤消命名空间访问权限。 系统命名空间示例包括 `kube-system`、`metallb-system`、`kubernetes-dashboard`、`default`、`kube-node-lease` 和 `kube-public`。 系统命名空间还包括为部署类型保留的命名空间，如 `iotedge`（IoT Edge 命名空间）和 `azure-arc`（Azure Arc 命名空间）。
- 可以创建用户命名空间，并在这些命名空间内创建其他用户，然后向这些用户授予或撤消命名空间访问权限。
- 你不能使用与任何系统命名空间的名称相同的名称来创建命名空间。 将保留系统命名空间的名称。  
- 你不能使用已被其他用户命名空间使用的名称来创建任何用户命名空间。 例如，如果你已创建一个 `test-ns`，则不能再创建另一个 `test-ns` 命名空间。
- 你不能使用已保留的名称来创建用户。 例如，`aseuser` 是保留用户，不能使用。


## <a name="next-steps"></a>后续步骤

若要了解如何创建用户、创建命名空间以及向用户授予对命名空间的访问权限，请参阅[通过 kubectl 访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。

