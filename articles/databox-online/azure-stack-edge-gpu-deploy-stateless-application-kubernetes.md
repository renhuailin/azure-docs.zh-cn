---
title: 使用 kubectl 在 Azure Stack Edge Pro GPU 设备上部署 Kubernetes 无状态应用程序 | Microsoft Docs
description: 介绍如何在 Microsoft Azure Stack Edge Pro 设备上使用 kubectl 创建和管理 Kubernetes 无状态应用程序部署。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: fd55ce702ae3c9485fc2dcc37fc90915a8990ce7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636804"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>通过 kubectl 在 Azure Stack Edge Pro GPU 设备上部署 Kubernetes 无状态应用程序

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何在现有的 Kubernetes 群集上使用 kubectl 命令部署无状态应用程序。 本文还逐步讲解如何在无状态应用程序中创建和设置 Pod。

## <a name="prerequisites"></a>先决条件

在创建 Kubernetes 群集并使用 `kubectl` 命令行工具之前，需要确保：

- 你有单节点 Azure Stack Edge Pro 设备的登录凭据。

- Windows PowerShell 5.0 或更高版本安装在 Windows 客户端系统上，用于访问 Azure Stack Edge Pro 设备。 也可以使用任何其他安装了受支持的操作系统的客户端。 本文介绍使用 Windows 客户端时的过程。 若要下载最新版 Windows PowerShell，请参阅[安装 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell)。

- Azure Stack Edge Pro 设备上启用了计算。 若要启用计算，请在设备的本地 UI 中转到“计算”页。 然后选择要为计算启用的网络接口。 选择“启用”。 启用计算会导致在设备的该网络接口上创建虚拟交换机。 有关详细信息，请参阅[在 Azure Stack Edge Pro 上启用计算网络](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。

- 你的 Azure Stack Edge Pro 设备上有一个 v1.9 或更高版本的 Kubernetes 群集服务器正在运行。 有关详细信息，请参阅[在 Microsoft Azure Stack Edge Pro 设备上创建和管理 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)。

- 已安装 `kubectl`。

## <a name="deploy-a-stateless-application"></a>部署无状态应用程序

在我们开始之前，你应该已经执行以下操作：

1. 创建 Kubernetes 群集。
2. 设置命名空间。
3. 将用户与命名空间相关联。
4. 将用户配置保存到 `C:\Users\<username>\.kube`。
5. 安装 `kubectl`。

现在，你可以开始在 Azure Stack Edge Pro 设备上运行和管理无状态应用程序部署。 在开始使用 `kubectl` 之前，需要验证 `kubectl` 的版本是否正确。

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>验证你是否有正确的 kubectl 版本并设置配置

若要检查 `kubectl` 的版本，请执行以下操作：

1. 验证 `kubectl` 的版本是否大于或等于 1.9：

   ```powershell
   kubectl version
   ```
    
   输出示例如下所示：
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   在此示例中，kubectl 的客户端版本为 v1.15.2，是兼容的，因此可以继续。

2. 获取在 Kubernetes 群集上运行的 Pod 的列表。 Pod 是在 Kubernetes 群集上运行的应用程序容器或进程。

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   命令用法的示例如下所示：
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   输出会指出，因为群集上没有运行任何应用程序，所以找不到任何资源 (Pod)。

   此命令将使用配置文件填充“C:\Users\\&lt;username&gt;\\.kube\" 的目录结构。 Kubectl 命令行工具将使用这些文件来创建和管理 Kubernetes 群集上的无状态应用程序。  

3. 手动检查“C:\Users\\&lt;username&gt;\\.kube\"的目录结构，以验证 kubectl 是否已使用以下子文件夹填充了它：

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> 若要查看所有 kubectl 命令的列表，请键入 `kubectl --help`。

### <a name="create-a-stateless-application-using-a-deployment"></a>使用部署创建无状态应用程序

在确认 kubectl 命令行版本正确且你具有所需的配置文件之后，便可以创建无状态应用程序部署了。

Pod 是 Kubernetes 应用程序的基本执行单位，是你创建或部署的 Kubernetes 对象模型中的最小和最简单的单位。 Pod 还封装了存储资源、唯一的网络 IP 以及对容器应如何运行进行治理的选项。

你创建的无状态应用程序的类型是 nginx Web 服务器部署。

用于创建和管理无状态应用程序部署的所有 kubectl 命令都需要指定与配置关联的命名空间。 在教程[在 Microsoft Azure Stack Edge Pro 设备上创建和管理 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)中连接到 Azure Stack Edge Pro 设备上的群集时，你使用 `New-HcsKubernetesNamespace` 创建了命名空间。

若要在 kubectl 命令中指定命名空间，请使用 `kubectl <command> -n <namespace-string>`。

按照以下步骤创建 nginx 部署：

1. 通过创建 Kubernetes 部署对象应用无状态应用程序：

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   在此示例中，应用程序 YAML 文件的路径是外部源。

   下面是该命令的示例用法及其输出：

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   也可将以下 Markdown 保存到本地计算机，并替换 -f 参数中的路径和文件名。 例如，“C:\Kubernetes\deployment.yaml”。 应用程序部署的配置将如下所示：

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   此命令创建一个默认的 nginx-deployment，后者有两个用于运行应用程序的 Pod。

2. 获取你创建的 Kubernetes nginx-deployment 的说明：

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   此命令的示例用法和输出如下所示：
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   对于 replicas 设置，你会看到：
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   replicas 设置指示你的部署规范需要两个 Pod，并且指示这些 Pod 已创建并更新，可供你使用。

   > [!NOTE]
   > 副本集会替换由于某个原因（例如，设备节点故障或设备升级中断）而被删除或终止的 Pod。 因此，我们建议你使用副本集，即使你的应用程序只需要一个 Pod。

3. 若要列出部署中的 Pod，请使用以下命令：

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   此命令的示例用法和输出如下所示：
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   输出会确认我们有两个 Pod，它们具有可以使用 kubectl 引用的唯一名称。

4. 若要查看部署中单个 Pod 的信息，请使用以下命令：

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

  此命令的示例用法和输出如下所示：

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>通过增加副本计数来重新缩放应用程序部署

每个 Pod 都用于运行给定应用程序的单个实例。 如果要横向扩展应用程序以运行多个实例，可以将 Pod 数增加到每个实例一个。 在 Kubernetes 中，这称为复制。
可以通过应用新的 YAML 文件来增加应用程序部署中的 Pod 数。 YAML 文件将 replicas 设置更改为 4，这会将部署中的 Pod 数增加到 4。 若要将 Pod 数从 2 增加到 4，请使用以下命令：

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

也可将以下 Markdown 保存到本地计算机，并替换 `kubectl apply` 的 -f 参数的路径和文件名。 例如，“C:\Kubernetes\deployment-scale.yaml”。 应用程序部署缩放的配置将如下所示：

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

若要验证部署是否有 4 个 Pod，请使用以下命令：

```powershell
kubectl get pods -l app=nginx
```

重新缩放部署（从 2 个 Pod 到 4 个 Pod）的示例输出如下所示：

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

正如你从输出中看到的那样，你的部署中现在有 4 个可以运行应用程序的 Pod。

### <a name="delete-a-deployment"></a>删除部署

若要删除部署（包括所有 Pod），需要运行 `kubectl delete deployment` 并指定部署名称 nginx-deployment 和命名空间名称。 若要删除部署，请使用以下命令：

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

下面显示了命令用法和输出的示例：

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>后续步骤

[Kubernetes 概述](azure-stack-edge-gpu-kubernetes-overview.md)