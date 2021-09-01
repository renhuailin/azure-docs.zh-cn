---
title: 在 Azure Data Studio 中创建数据控制器
description: 在 Azure Data Studio 中创建数据控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 4884730dc163b7720b5c2abb9c4f1fb529e5e2ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748726"
---
# <a name="create-data-controller-in-azure-data-studio"></a>在 Azure Data Studio 中创建数据控制器

可以使用 Azure Data Studio 通过部署向导和笔记本创建数据控制器。


## <a name="prerequisites"></a>先决条件

- 需要访问 Kubernetes 群集，并将 kubeconfig 文件配置为指向要部署到的 Kubernetes 群集。
- 你需要[安装 Azure Data Studio 等客户端工具](install-client-tools.md)、Azure Data Studio 扩展（亦称为 Azure Arc）以及带有 `arcdata` 扩展的 Azure CLI。 
- 你需要在 Azure Data Studio 中登录到 Azure。  为此：请键入 Ctrl/Command+Shift+P 以打开命令文本窗口，然后键入“Azure”。  选择“Azure: 登录”。   在出现的面板中，单击右上角的 + 图标添加 Azure 帐户。

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>使用部署向导创建 Azure Arc 数据控制器

按照以下步骤使用部署向导创建 Azure Arc 数据控制器。

1. 在 Azure Data Studio 中，单击左侧导航栏上的“连接”选项卡。
1. 单击“连接”面板顶部的“…”按钮，然后选择“新建部署…” 
1. 在新建部署向导中，选择“Azure Arc 数据控制器”，然后单击底部的“选择”按钮 。
1. 确保必备工具可用并满足所需的版本。 单击“下一步”。
1. 使用默认的 kubeconfig 文件或选择另一个文件。  单击“下一步”。
1. 选择 Kubernetes 群集上下文。 单击“下一步”。
1. 根据目标 Kubernetes 群集选择部署配置文件。 单击“下一步”。
1. 选择所需的订阅和资源组。
1. 选择 Azure 位置。
   
   此处选择的 Azure 位置是 Azure 中将存储数据控制器元数据及其所管理数据库实例元数据的位置。 数据控制器和数据库实例实际上将在 Kubernetes 群集中创建，无论该群集在何处。
   
   完成操作后，单击“下一步”。

1. 为数据控制器和将在其中创建数据控制器的命名空间输入名称。

    数据控制器和命名空间名称将用于在 Kubernetes 群集中创建自定义资源，因此它们必须符合 [Kubernetes 命名约定](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names)。
    
    如果命名空间已存在，则在命名空间还不包含其他 Kubernetes 对象（pod 等）的情况下，将使用该命名空间。如果命名空间不存在，则会尝试创建命名空间。  在 Kubernetes 群集中创建命名空间需要 Kubernetes 群集管理员权限。  如果你没有 Kubernetes 群集管理员权限，请让 Kubernetes 群集管理员执行[使用 Kubernetes 原生工具创建数据控制器](./create-data-controller-using-kubernetes-native-tools.md)一文中的前几个步骤；在你完成此向导之前，Kubernetes 管理员需要执行这些步骤。


1. 选择将在其中部署数据控制器的存储类。 
1.  输入用户名和密码，并确认数据控制器管理员用户帐户的密码。 单击“下一步”。

1. 查看部署配置。
1. 单击“部署”以部署所需的配置，或单击“脚本到笔记本”以查看部署说明或进行任何必要的更改（例如存储类名称或服务类型） 。 单击笔记本顶部的“全部运行”。

## <a name="monitoring-the-creation-status"></a>监视创建状态

创建控制器需要几分钟才能完成。 可使用以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假设你创建了一个名为“arc”的数据控制器和 Kubernetes 命名空间。  如果使用其他命名空间/数据控制器名称，则可以将“arc”替换为自己的名称。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可运行如下命令来检查任何特定 Pod 的创建状态。  这对于排查问题特别有用。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>排查创建问题

如果在创建过程中遇到任何问题，请参阅[故障排除指南](troubleshoot-guide.md)。
