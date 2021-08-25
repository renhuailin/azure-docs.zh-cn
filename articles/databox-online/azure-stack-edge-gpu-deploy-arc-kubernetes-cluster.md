---
title: 在 Azure Stack Edge Pro GPU 设备的 Kubernetes 上启用 Azure Arc | Microsoft Docs
description: 介绍如何在 Azure Stack Edge Pro GPU 设备的现有 Kubernetes 群集上启用 Azure Arc。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/11/2021
ms.author: alkohli
ms.openlocfilehash: 1f3ecf919337c17514af7d9e0d3d83b2dc75bf1d
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769253"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备的 Kubernetes 群集上启用 Azure Arc

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何在 Azure Stack Edge Pro 设备的现有 Kubernetes 群集上启用 Azure Arc。 

此过程适用于已查看 [Azure Stack Edge Pro 设备上的 Kubernetes 工作负荷](azure-stack-edge-gpu-kubernetes-workload-management.md)一文且熟悉[什么是已启用 Azure Arc 的 Kubernetes（预览版）？](../azure-arc/kubernetes/overview.md)中的概念的用户。


## <a name="prerequisites"></a>先决条件

在 Kubernetes 群集上启用 Azure Arc 之前，请确保已在 Azure Stack Edge Pro 设备和将用于访问该设备的客户端上满足了以下先决条件：

### <a name="for-device"></a>对于设备

1. 你有单节点 Azure Stack Edge Pro 设备的登录凭据。
    1. 设备已激活。 请参阅[激活设备](azure-stack-edge-gpu-deploy-activate.md)。
    1. 已通过 Azure 门户配置了设备的计算角色，并且设备具有 Kubernetes 群集。 请参阅[配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。

1. 你对订阅具有所有者访问权限。 你在服务主体的角色分配步骤中需要此访问权限。
 

### <a name="for-client-accessing-the-device"></a>对于访问设备的客户端

1. 你有一个将用于访问 Azure Stack Edge Pro 设备的 Windows 客户端系统。
  
    - 客户端在运行 Windows PowerShell 5.0 或更高版本。 若要下载最新版 Windows PowerShell，请参阅[安装 Windows PowerShell](/powershell/scripting/install/installing-powershell-core-on-windows)。
    
    - 也可使用任何其他客户端，只要安装了[受支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)即可。 本文介绍使用 Windows 客户端时的过程。 
    
1. 你已完成[在 Azure Stack Edge Pro 设备上访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)中所述的过程。 你已：
    
    - 在客户端上安装 `kubectl`。    
    - 确保 `kubectl` 客户端版本与 Azure Stack Edge Pro 设备上运行的 Kubernetes 主版本相差不超过一个版本。 
      - 使用 `kubectl version` 检查在客户端上运行的 kubectl 的版本。 记下完整版本。
      - 在 Azure Stack Edge Pro 设备的本地 UI 中，转到“软件更新”，并记下 Kubernetes 服务器版本号。 
    
        ![验证 Kubernetes 服务器版本号](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - 验证这两个版本是否兼容。 


## <a name="register-kubernetes-resource-providers"></a>注册 Kubernetes 资源提供程序

在 Kubernetes 群集上启用 Azure Arc 之前，需要为你的订阅启用并注册 `Microsoft.Kubernetes` 和 `Microsoft.KubernetesConfiguration`。 

1. 若要启用资源提供程序，请在 Azure 门户中转到你计划将其用于部署的订阅。 转到“资源提供程序”。 
1. 在右窗格中，搜索要添加的提供程序， 在此示例中为 `Microsoft.Kubernetes` 和 `Microsoft.KubernetesConfiguration`。

    ![注册 Kubernetes 资源提供程序](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. 选择资源提供程序，然后在命令栏顶部选择“注册”。 注册需要几分钟。 

    ![注册 Kubernetes 资源提供程序 2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. 刷新 UI，直到看到资源提供程序已注册。 对两个资源提供程序重复此过程。
    
    ![注册 Kubernetes 资源提供程序 3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

还可以通过 `az cli` 注册资源提供程序。 有关详细信息，请参阅[为已启用 Azure Arc 的 Kubernetes 注册两个提供程序](../azure-arc/kubernetes/quickstart-connect-cluster.md#1-register-providers-for-azure-arc-enabled-kubernetes)。

## <a name="create-service-principal-assign-role"></a>创建服务主体并分配角色

1. 确保你有 `Subscription ID` 和用于 Azure Stack Edge 服务资源部署的资源组的名称。 若要获取订阅 ID，请转到 Azure 门户中的 Azure Stack Edge 资源。 导航到“概述”>“基本信息”。

    ![获取订阅 ID](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    若要获取资源组名称，请转到“属性”。

    ![获取资源组名称](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. 若要创建服务主体，请通过 `az cli` 使用以下命令。

    `az ad sp create-for-rbac --skip-assignment --name "<Informative name for service principal>"`  

    若要了解如何登录到 `az cli`，请[在 Azure 门户中启动 Cloud Shell](../cloud-shell/quickstart-powershell.md#start-cloud-shell)

    示例如下。 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. 记下 `appID`、`name`、`password` 和 `tenantID`，因为你需要在下一命令中将其用作输入。

1. 创建新服务主体后，将“`Kubernetes Cluster - Azure Arc Onboarding`”角色分配给新创建的主体。 这是权限有限的内置 Azure 角色（在命令中使用角色 ID）。 请使用以下命令：

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    示例如下。
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    若要详细了解如何创建服务主体和执行角色分配，请参阅[创建已启用 Azure Arc 的加入服务主体](../azure-arc/kubernetes/create-onboarding-service-principal.md)中的步骤。


## <a name="enable-arc-on-kubernetes-cluster"></a>在 Kubernetes 群集上启用 Arc

按照以下步骤配置用于 Azure Arc 管理的 Kubernetes 群集：

1. [连接到设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

1. 类型：

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`

    如果使用的云不是 Azure 公有云，请添加 `CloudEnvironment` 参数。 可以将此参数设置为 `AZUREPUBLICCLOUD`、`AZURECHINACLOUD`、      `AZUREGERMANCLOUD` 和 `AZUREUSGOVERNMENTCLOUD`。

    > [!NOTE]
    > - 若要在设备上部署 Azure Arc，请确保使用[受支持的 Azure Arc 区域](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)。 
    > - 使用 `az account list-locations` 命令找出要传入到 `Set-HcsKubernetesAzureArcAgent` cmdlet 的确切位置名称。 位置名称的格式通常不含任何空格。
    > - `ClientId` 和 `ClientSecret` 为所需参数。 `ClientSecret` 是安全字符串。
    
    以下是示例：
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed&quot; -ResourceGroupName &quot;myaserg1&quot; -ResourceName &quot;myasetestresarc&quot; -Location &quot;westeurope&quot; -TenantId &quot;72f988bf-86f1-41af-91ab-2d7cd011db47&quot; -ClientId &quot;aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b&quot; -ClientSecret &quot;<password>"
    [10.128.44.240]: PS>
    ```
    
    在 Azure 门户中，带有在前一个命令中提供的名称的资源应已创建。

    ![转到 Azure Arc 资源](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. 若要验证 Azure Arc 是否已成功启用，请从 PowerShell 界面运行以下命令：

    `kubectl get deployments -n azure-arc`

    此命令会查找在与 Azure Arc 对应的 `azure-arc` 命名空间中部署的任何应用程序。

    下面是一个示例输出，显示了已在 Kubernetes 群集上的 `azure-arc` 命名空间中部署的 Azure Arc 代理。 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    还可以获取运行于 Kubernetes 群集上的 `azure-arc` 命名空间中的 Pod 的列表。 Pod 是在 Kubernetes 群集上运行的应用程序容器或进程。 

    请使用以下命令：
    
    `kubectl get pods -n azure-arc`
    
    下面是示例输出。
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


如前一输出所示，已启用 Azure Arc 的 Kubernetes 由几个在群集中运行的已部署到 `azure-arc` 命名空间的代理 (operator) 组成。

- `config-agent`：监视群集上应用的源代码管理配置资源的已连接群集并更新符合性状态
- `controller-manager`：是运算符的运算符，用于协调 Azure Arc 组件之间的交互
- `metrics-agent`：收集其他 Arc 代理的指标，以确保这些代理表现出最佳性能
- `cluster-metadata-operator`：收集群集元数据 - 群集版本、节点计数和 Azure Arc 代理版本
- `resource-sync-agent`：将上面提到的群集元数据同步到 Azure
- `clusteridentityoperator`：已启用 Azure Arc 的 Kubernetes 目前支持系统分配的标识。 clusteridentityoperator 维护供其他代理用来与 Azure 通信的托管服务标识 (MSI) 证书。
- `flux-logs-agent`：从在源代码管理配置过程中部署的 flux operator 收集日志。
- `connect-agent`：与 Azure Arc 资源通信。

### <a name="remove-arc-from-the-kubernetes-cluster"></a>从 Kubernetes 群集中删除 Arc

若要删除 Azure Arc 管理，请执行以下步骤：

1. 1. [连接到设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
2. 类型：

    `Remove-HcsKubernetesAzureArcAgent` 


> [!NOTE]
> 默认情况下，从 Git 存储库中删除资源 `yamls` 时，不会从 Kubernetes 群集中删除相应的资源。 需要在 Arc OperatorParams 中设置 `--sync-garbage-collection`，这样，当你从 git 存储库中删除资源时，就能够删除资源。 有关详细信息，请参阅[删除配置](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters)

## <a name="next-steps"></a>后续步骤

若要了解如何运行 Azure Arc 部署，请参阅[在 Azure Stack Edge Pro 设备上通过 GitOps 使用 Redis 部署无状态 PHP `Guestbook` 应用程序](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)
