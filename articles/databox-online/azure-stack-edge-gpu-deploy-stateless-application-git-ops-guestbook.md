---
title: 在 Azure Stack Edge Pro GPU 设备上启用了 Arc 的 Kubernetes 上部署 PHP `Guestbook` 应用 | Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro 设备上启用了 Arc 的 Kubernetes 群集上，使用 GitOps 部署具有 Redis 的 PHP `Guestbook` 无状态应用程序。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 4c5a361fafeaed6452481adc9e6eae856828bfda
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635577"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>在 Azure Stack Edge Pro GPU 的启用了 Arc 的 Kubernetes 群集上部署具有 Redis 的 PHP `Guestbook` 无状态应用程序

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍并展示如何使用 Kubernetes 和 Azure Arc 生成和部署简单的多层 Web 应用。本示例包括以下组成部分：

- 一个单实例 Redis 主机，用于存储 `guestbook` 条目
- 多个复制的 Redis 实例，用于提供读取服务
- 多个 Web 前端实例

部署是在 Azure Stack Edge Pro 设备上启用了 Arc 的 Kubernetes 群集上使用 GitOps 完成的。 

此过程适用于已阅读 [Azure Stack Edge Pro 设备上的 Kubernetes 工作负载](azure-stack-edge-gpu-kubernetes-workload-management.md)一文且熟悉[什么是已启用 Azure Arc 的 Kubernetes（预览版）](../azure-arc/kubernetes/overview.md)中的概念的用户。

> [!NOTE]
> 本文包含对术语“从属”的引用，这是 Microsoft 不再使用的术语。 在从软件中删除该术语后，我们会将其从本文中删除。

## <a name="prerequisites"></a>先决条件

在部署无状态应用程序之前，请确保设备和将用于访问该设备的客户端上已满足以下先决条件：

### <a name="for-device"></a>对于设备

1. 你有单节点 Azure Stack Edge Pro 设备的登录凭据。
    1. 设备已激活。 请参阅[激活设备](azure-stack-edge-gpu-deploy-activate.md)。
    1. 已通过 Azure 门户配置了设备的计算角色，并且设备具有 Kubernetes 群集。 请参阅[配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。

1. 你已在设备上的现有 Kubernetes 群集上启用了 Azure Arc，并在 Azure 门户中具有相应的 Azure Arc 资源。 有关详细步骤，请参阅[在 Azure Stack Edge Pro 设备上启用 Azure Arc](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。

### <a name="for-client-accessing-the-device"></a>对于访问设备的客户端

1. 你有一个将用于访问 Azure Stack Edge Pro 设备的 Windows 客户端系统。
  
    - 客户端在运行 Windows PowerShell 5.0 或更高版本。 若要下载最新版 Windows PowerShell，请参阅[安装 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell)。
    
    - 也可使用任何其他客户端，只要安装了[受支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)即可。 本文介绍使用 Windows 客户端时的过程。 
    
1. 你已完成[在 Azure Stack Edge Pro 设备上访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)中所述的过程。 你已：
    
    - 在客户端上安装 `kubectl`。 <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - 确保 `kubectl` 客户端版本与 Azure Stack Edge Pro 设备上运行的 Kubernetes 主版本相差不超过一个版本。 
      - 使用 `kubectl version` 检查在客户端上运行的 kubectl 的版本。 记下完整版本。
      - 在 Azure Stack Edge Pro 设备的本地 UI 中，转到“概述”，并记下 Kubernetes 软件编号。 
      - 从支持的 Kubernetes 版本中提供的映射中验证这两个版本是否兼容。 <!--insert link-->

1. 你有一个[可用于运行 Azure Arc 部署的 GitOps 配置](https://github.com/kagoyal/dbehaikudemo)。 在本示例中，你将使用以下 `yaml` 文件在 Azure Stack Edge Pro 设备上进行部署。

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>部署配置

按照以下步骤配置 Azure Arc 资源，以通过 Azure 门户部署 GitOps 配置： 

1. 在 Azure 门户中，转到在设备上的 Kubernetes 群集上启用 Azure Arc 时创建的 Azure Arc 资源。 

    ![转到 Azure Arc 资源](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. 转到“配置”并选择“+ 添加配置”。

    ![屏幕截图显示启用了 Azure Arc 的 Kubernetes 群集，并且选择了“添加配置”。](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. 在“添加配置”中，为字段输入适当的值，然后选择“应用”。

    |参数  |说明 |
    |---------|---------|
    |配置名称     | 配置资源的名称。        |
    |运算符实例名称     |用于标识特定配置的运算符的实例名称。 名称是一个长度不超过 253 个字符的字符串，只能包含小写形式、字母数字字符、连字符和句点。         |
    |运算符命名空间     | 设置为 demotestguestbook 以与部署 `yaml` 中指定的命名空间一致。 <br> 字段定义安装运算符的命名空间。 名称是一个长度不超过 253 个字符的字符串，只能包含小写形式、字母数字字符、连字符和句点。         |
    |存储库 URL     |<br>Git 存储库的路径，采用 `http://github.com/username/repo` 或 `git://github.com/username/repo` 格式，是 GitOps 配置所在的位置。         |
    |运算符范围     | 选择“命名空间”。 <br>此参数定义运算符的安装范围。 选择“命名空间”，在部署 yaml 文件中指定的命名空间中安装运算符。       |
    |运算符类型     | 保留默认值。 <br>此参数指定运算符的类型，默认设置为 flux。        |
    |运算符参数     | 将其留空。 <br>此参数包含要传递给 flux 运算符的参数。        |
    |Helm     | 将此参数设置为“已禁用”。 <br>如果你将会执行基于图表的部署，请启用此选项。        |


    ![添加配置](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. 配置部署开始，“运算符状态”显示为“挂起”。 

    ![屏幕截图显示启用了 Azure Arc 的 Kubernetes 群集在刷新时处于挂起状态。](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. 部署需要几分钟时间。 部署完成后，“运算符状态”显示为“已安装”。

    ![屏幕截图显示处于“已安装”状态的启用了 Azure Arc 的 Kubernetes 群集。](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)

## <a name="verify-deployment"></a>验证部署

通过 GitOps 配置进行部署时，会创建一个 `demotestguestbook` 命名空间，该命名空间在位于 Git 存储库中的部署 `yaml` 文件中定义。

1. 应用 GitOps 配置后，[连接到设备的 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
1. 运行以下命令以列出与部署相对应的 `demotestguestbook` 命名空间中运行的 Pod。

    `kubectl get pods -n <your-namespace>`
  
    下面是示例输出。
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. 在此示例中，前端服务被部署为 type:LoadBalancer。 你将需要查找此服务的 IP 地址以查看 `guestbook`。 运行以下命令。

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. `type:LoadBalancer` 前端服务有一个外部 IP 地址。 这个 IP 来自你在设备上配置计算网络设置时为外部服务指定的 IP 地址范围。 使用此 IP 地址查看 `guestbook`，URL 为 `https://<external-IP-address>`。

    ![查看留言簿](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>删除部署

要删除部署，可以从 Azure 门户中删除配置。 删除配置将删除已创建的对象，包括部署和服务。

1. 在 Azure 门户中，转到“Azure Arc 资源”>“配置”。 
1. 找到要删除的配置。 选择“...”调用上下文菜单，然后选择“删除”。
    ![删除配置](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

删除配置可能需要几分钟时间。
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>后续步骤

了解如何[使用 Kubernetes 仪表板监视 Azure Stack Edge Pro 设备上的部署](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)