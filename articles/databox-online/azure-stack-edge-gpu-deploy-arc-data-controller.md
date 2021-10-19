---
title: 在 Azure Stack Edge Pro GPU 设备上部署 Azure Arc 数据控制器 | Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro GPU 设备上部署 Azure Arc 数据控制器和 Azure 数据服务。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: e1078bb12b6d33eb4d4d35cc3e1e11f91ef39e9b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705541"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上部署 Azure 数据服务

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文将介绍在 Azure Stack Edge Pro GPU 设备上创建 Azure Arc 数据控制器并部署 Azure 数据服务的过程。 

Azure Arc 数据控制器是可在客户管理的环境中启用 Azure 数据服务的本地控制平面。 在 Azure Stack Edge Pro GPU 设备上运行的 Kubernetes 群集中创建 Azure Arc 数据控制器后，可以在该数据控制器上部署 SQL 托管实例之类的 Azure 数据服务。

创建数据控制器并部署 SQL 托管实例的过程涉及到使用 PowerShell 和 `kubectl` - 一个本机工具，可让用户通过命令行访问设备上的 Kubernetes 群集。


## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 你可以访问 Azure Stack Edge Pro GPU 设备，并已按照[激活 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) 中所述激活了该设备。

1. 已在该设备上启用计算角色。 当你按照[在 Azure Stack Edge Pro GPU 设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)中的说明在设备上配置了计算时，还在设备上创建了 Kubernetes 群集。

1. 你有来自本地 Web UI 的“设备”页面的 Kubernetes API 终结点。 有关详细信息，请参阅[获取 Kubernetes API 终结点](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)中的说明。

1. 可以访问要连接到设备的客户端。 
    1. 本文使用一个运行 PowerShell 5.0 或更高版本的 Windows 客户端系统来访问设备。 可以使用装有[受支持操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)的任何其他客户端。 
    1. 在客户端上安装 `kubectl`。 对于客户端版本：
        1. 确定设备上安装的 Kubernetes 服务器版本。 在设备的本地 UI 中，转到“软件更新”页。 注意此页中的“Kubernetes 服务器版本”。
        1. 下载客户端，与主节点相差不超过一个次要版本的客户端。 不过，客户端版本可比主节点最多领先一个次要版本。 例如，v1.3 主节点应该可以使用 v1.1、v1.2 和 v1.3 节点，并且应该可以使用 v1.2、v1.3 和 v1.4 客户端。 有关 Kubernetes 客户端版本的详细信息，请参阅 [Kubernetes 版本和版本偏差支持策略](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)。
    
1. （可选）[安装用于部署和管理已启用 Azure Arc 的数据服务的客户端工具](../azure-arc/data/install-client-tools.md)。 这些工具不是必需的，但建议使用。  
1. 确保设备上有足够的资源用于预配数据控制器和一个 SQL 托管实例。 至少需要 16 GB RAM 和 4 个 CPU 核心才能预配数据控制器和一个 SQL 托管实例。 有关详细指导，请转到[已启用 Azure Arc 的数据服务部署的最低要求](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements)。


## <a name="configure-kubernetes-external-service-ips"></a>配置 Kubernetes 外部服务 IP

1. 转到设备的本地 Web UI，然后转到“计算”。
1. 选择为计算启用的网络。 

    ![本地 UI 中的计算页 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. 确保除了已为其他外部服务或容器配置的 IP 以外，还提供三个额外的 Kubernetes 外部服务 IP。 数据控制器将使用两个服务 IP，创建 SQL 托管实例时将使用第三个 IP。 需要为每个要部署的额外数据服务提供一个 IP。 

    ![本地 UI 中的计算页 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. 应用设置，然后，这些新 IP 将在现有的 Kubernetes 群集上立即生效。 


## <a name="deploy-azure-arc-data-controller"></a>部署 Azure Arc 数据控制器

在部署数据控制器之前，需要创建一个命名空间。

### <a name="create-namespace"></a>创建命名空间 

创建新的专用命名空间用于部署数据控制器。 还要创建一个用户，然后向该用户授予对创建的命名空间的访问权限。 

> [!NOTE]
> 命名空间和用户名需要遵循 [DNS 子域命名约定](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names)。

1. [连接到 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
1. 创建命名空间。 类型：

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. 创建用户。 类型： 

    `New-HcsKubernetesUser -UserName <User name>`

1. 配置文件以纯文本格式显示。 复制此文件并将其保存为 *配置* 文件。 

    > [!IMPORTANT]
    > 不要将配置文件保存为 *.txt* 文件，而是保存不带任何扩展名的文件。

1. 配置文件应位于本地计算机上用户配置文件的 `.kube` 文件夹中。 将该文件复制到用户配置文件的该文件夹中。

    ![客户端上的配置文件位置](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. 向用户授予对创建的命名空间的访问权限。 类型： 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    下面是上述命令的示例输出。 在此示例中，我们将创建 `myadstest` 命名空间和 `myadsuser` 用户，并向该用户授予对该命名空间的访问权限。
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. 将 DNS 条目添加到系统上的 hosts 文件中。 

    1. 以管理员身份运行记事本，并打开位于 `C:\windows\system32\drivers\etc\hosts` 处的 `hosts` 文件。 
    2. 使用在本地 UI 的“设备”页中保存的信息（先决条件）在 hosts 文件中创建条目。 

        例如，复制此终结点 `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` 可以创建包含设备 IP 地址和 DNS 域的以下条目： 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. 若要验证是否可以连接到 Kubernetes pod，请启动 cmd 提示符或 PowerShell 会话。 类型：
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
现在，可将数据控制器和数据服务应用程序部署到命名空间中，然后查看应用程序及其日志。

### <a name="create-data-controller"></a>创建数据控制器

数据控制器是部署到 Kubernetes 群集的 pod 集合，用于提供 API、控制器服务、引导程序以及监视数据库和仪表板。 遵循以下步骤在 Kubernetes 群集上创建一个数据控制器，该群集位于先前在 Azure Stack Edge 设备上创建的命名空间中。   

1. 收集创建数据控制器时所需的以下信息：

    
    |Column1  |Column2  |
    |---------|---------|
    |数据控制器名称     |数据控制器的描述性名称。 例如，`arctestdatacontroller`。         |
    |数据控制器用户名     |数据控制器管理员用户的任何用户名。 数据控制器用户名和密码用于向执行管理功能的数据控制器 API 进行身份验证。          |
    |数据控制器密码     |数据控制器管理员用户的密码。 请选择安全的密码，并只与需要拥有群集管理员特权的人共享。         |
    |Kubernetes 命名空间的名称     |要在其中创建数据控制器的 Kubernetes 命名空间的名称。         |
    |Azure 订阅 ID     |要在其中创建 Azure 数据控制器资源的 Azure 订阅 GUID。         |
    |Azure 资源组名称     |要在其中创建 Azure 数据控制器资源的资源组的名称。         |
    |Azure 位置     |要将数据控制器资源元数据存储到的 Azure 位置。 有关可用区域的列表，请参阅“Azure 全球基础结构/各区域的产品”。|


1. 连接到 PowerShell 接口。 若要创建数据控制器，请键入： 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    下面是上述命令的示例输出。

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    完成部署大约需要 5 分钟。

    > [!NOTE]
    > 在当前版本中，在 Azure Stack Edge Pro GPU 设备上的 Kubernetes 群集中创建的数据控制器只能以断开连接模式工作。 断开连接模式适用于数据控制器，而不适用于设备。

### <a name="monitor-data-creation-status"></a>监视数据创建状态

1. 打开另一个 PowerShell 窗口。
1. 使用以下 `kubectl` 命令监视数据控制器的创建状态。 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    创建控制器后，状态应是 `Ready`。
    下面是上述命令的示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. 若要识别分配给数据控制器上运行的外部服务的 IP，请使用 `kubectl get svc -n <namespace>` 命令。 下面是示例输出：

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>部署 SQL 托管实例

成功创建数据控制器后，可以使用模板在该数据控制器上部署 SQL 托管实例。

### <a name="deployment-template"></a>部署模板

使用以下部署模板在设备上的数据控制器中部署 SQL 托管实例。

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>元数据名称
    
元数据名称是 SQL 托管实例的名称。 前面 `deployment.yaml` 中的关联 pod 的名称为 `sqlex-n`（`n` 是与应用程序关联的 pod 数）。 
    
#### <a name="password-and-username-data"></a>密码和用户名数据

数据控制器用户名和密码用于向执行管理功能的数据控制器 API 进行身份验证。 部署模板中数据控制器用户名和密码的 Kubernetes 机密是 base64 编码的字符串。 

可以使用联机工具对所需的用户名和密码进行 base64 编码，或者根据平台使用内置的 CLI 工具。 使用联机 Base64 编码工具时，请在该工具中提供（创建数据控制器时输入的）用户名和密码字符串，该工具将生成相应的 Base64 编码字符串。
    
#### <a name="service-type"></a>服务类型

服务类型应设置为 `LoadBalancer`。
    
#### <a name="storage-class-name"></a>存储类名称

可以在 Azure Stack Edge 设备上识别在部署中用于数据、备份、数据日志和日志的存储类。 使用 `kubectl get storageclass` 命令获取设备上部署的存储类。

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
在上面的示例输出中，设备上的 `ase-node-local` 存储类应在模板中指定。
 
#### <a name="spec"></a>规范

若要在 Azure Stack Edge 设备上创建 SQL 托管实例，可以在 `deployment.yaml` 的 spec 节中指定内存和 CPU 要求。 每个 SQL 托管实例必须至少请求 2 GB 内存和 1 个 CPU 核心，如以下示例中所示。 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

有关数据控制器和 1 个 SQL 托管实例的详细大小调整指导，请查看 [SQL 托管实例大小调整详细信息](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)。

### <a name="run-deployment-template"></a>运行部署模板

使用以下命令运行 `deployment.yaml`：

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

下面是上述命令的示例输出：

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

示例输出中的 `sqlex-0` pod 指示 SQL 托管实例的状态。

## <a name="remove-data-controller"></a>删除数据控制器

若要删除数据控制器，请删除它所部署到的专用命名空间。


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>后续步骤

- [在 Azure Stack Edge Pro 上部署无状态应用程序](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)。
