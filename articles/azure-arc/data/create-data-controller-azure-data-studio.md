---
title: 在 Azure Data Studio 中创建数据控制器
description: 在 Azure Data Studio 中创建数据控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 22ad2d65710a3fc149f5a83fb511244ac3be2203
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733233"
---
# <a name="create-data-controller-in-azure-data-studio"></a>在 Azure Data Studio 中创建数据控制器

可以通过部署向导和笔记本使用 Azure Data Studio 创建数据控制器。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先决条件

- 需要访问 Kubernetes 群集，并将 kubeconfig 文件配置为指向要部署到的 Kubernetes 群集。
- 需要 [安装客户端工具](install-client-tools.md) ，包括 **Azure Data Studio** 称为 **Azure Arc** 和的 Azure Data Studio 扩展 **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** 。
- 需要在 Azure Data Studio 中登录到 Azure。  为此，请键入 CTRL/Command + SHIFT + P 以打开命令文本窗口，然后键入 **Azure**。  选择 **"Azure：登录"**。   在面板中，单击右上方的 "+" 图标以添加 Azure 帐户。

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>使用部署向导创建 Azure Arc 数据控制器

按照以下步骤使用部署向导创建 Azure Arc 数据控制器。

1. 在 Azure Data Studio 中，单击左侧导航栏上的 "连接" 选项卡。
2. 单击 "连接" 面板顶部的 **"..." 按钮，** 然后选择 "**新建部署 ...** "。
3. 在新建部署向导中，选择 " **Azure Arc 数据控制器**"，并单击底部的 " **选择** " 按钮。
4. 确保必备工具可用并满足所需的版本。 **单击 "下一步"**。
5. 使用默认的 kubeconfig 文件或选择另一个文件。  单击 **“下一步”** 。
6. 选择 Kubernetes 群集上下文。 单击 **“下一步”** 。
7. 根据目标 Kubernetes 群集选择部署配置文件。 **单击 "下一步"**。
8. 如果使用的是 Azure Red Hat OpenShift 或 Red Hat OpenShift 容器平台，请应用安全上下文约束。 按照在 [OpenShift 上为启用了 Azure Arc 的数据服务应用安全上下文约束](how-to-apply-security-context-constraint.md)中的说明进行操作。

   >[!IMPORTANT]
   >在 Azure Red Hat OpenShift 或 Red Hat OpenShift 容器平台上，必须先应用安全上下文约束，然后才能创建数据控制器。

1. 选择所需的订阅和资源组。
1. 选择 Azure 位置。
   
   此处选择的 Azure 位置是 Azure 中的位置，其中存储了有关数据控制器和所管理的数据库实例的 *元* 数据。 数据控制器和数据库实例实际上 crewted 在 Kubernetes 群集中的任何位置。

10. 选择适当的连接模式。 了解有关 [连接模式](https://docs.microsoft.com/azure/azure-arc/data/connectivity)的详细信息。 **单击 "下一步"**。

    如果选择 "直接连接模式"，则需要如 " [创建服务主体](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)" 中所述。

11. 输入数据控制器的名称，并为将在其中创建数据控制器的命名空间输入名称。

    数据控制器和命名空间名称将用于在 Kubernetes 群集中创建自定义资源，因此它们必须符合 [Kubernetes 命名约定](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names)。
    
    如果命名空间已存在，则在命名空间还不包含其他 Kubernetes 对象-pod 等时，将使用该命名空间。 如果命名空间不存在，则将创建命名空间的尝试。  在 Kubernetes 群集中创建命名空间需要 Kubernetes 群集管理员权限。  如果没有 Kubernetes 群集管理员权限，请在完成此向导之前，让 Kubernetes 群集管理员执行 [使用 Kubernetes-本机工具创建数据控制器](./create-data-controller-using-kubernetes-native-tools.md) 一文中的前几个步骤。


12. 选择将在其中部署数据控制器的存储类。 
13.  输入用户名和密码，并确认数据控制器管理员用户帐户的密码。 单击 **“下一步”** 。

14. 查看部署配置。
15. 单击 " **部署** " 以将所需配置或 **脚本部署到笔记本** ，以查看部署说明或进行任何必要的更改，如存储类名称或服务类型。 单击笔记本顶部的 " **全部运行** "。

## <a name="monitoring-the-creation-status"></a>监视创建状态

创建控制器需要几分钟才能完成。 可以通过以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假设你创建了一个名为 "arc" 的数据控制器和 Kubernetes 命名空间。  如果使用了不同的命名空间/数据控制器名称，则可以将 "arc" 替换为您的名称。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可以通过运行如下命令来检查任何特定 pod 的创建状态。  这对于解决任何问题特别有用。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>创建问题疑难解答

如果在创建 troubles 的过程中遇到任何问题，请参阅 [故障排除指南](troubleshoot-guide.md)。