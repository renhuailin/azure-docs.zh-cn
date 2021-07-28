---
title: 容器见疑难解答 | Microsoft Docs
description: 本文介绍如何排查和解决容器见解存在的问题。
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: b7618e9073308da67a8e17c82375a0f05925a542
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627109"
---
# <a name="troubleshooting-container-insights"></a>容器见解疑难解答

使用容器见解配置 Azure Kubernetes 服务 (AKS) 群集的监视时，可能会遇到阻止数据收集或报告状态的问题。 本文详细介绍了一些常见问题及其排查步骤。

## <a name="authorization-error-during-onboarding-or-update-operation"></a>在执行载入或更新操作期间出现授权错误

启用容器见解或者更新群集以支持收集指标时，可能会收到如下错误 - 对象 ID 为 <用户的对象 ID> 的客户端 <用户的标识> 无权对作用域执行操作“Microsoft.Authorization/roleAssignments/write”

在载入或更新过程中，将对群集资源尝试授予“监视指标发布服务器”  角色分配。 如果用户要启动为容器启用容器见解或用于支持收集指标的更新，则该用户必须可以访问 AKS 群集资源作用域上的 **Microsoft.Authorization/roleAssignments/write** 权限。 只有 **所有者** 和 **用户访问管理员** 内置角色的成员才被授权访问此权限。 如果安全策略需要分配粒度级别的权限，我们建议查看[自定义角色](../../role-based-access-control/custom-roles.md)，并将其分配给需要它的用户。

此外，还可以通过执行以下步骤，在 Azure 门户中手动授予此角色：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在 Azure 门户中，单击左上角的“所有服务”  。 在资源列表中，键入 **Kubernetes**。 开始键入时，会根据输入筛选该列表。 选择“Azure Kubernetes”  。
3. 从 Kubernetes 群集列表中选择一个群集。
2. 在左侧菜单中，单击“访问控制 (IAM)”  。
3. 选择“+ 添加”  以添加角色分配，并选择“监视指标发布服务器”角色  ，然后在“选择”框  下键入 **AKS**，以仅根据订阅中定义的群集服务主体筛选结果。 从列表中选择特定于该群集的角色。
4. 选择“保存”  完成角色分配。

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>已启用容器见解，但未报告任何信息

如果容器见解已成功启用和配置，但你无法查看状态信息或日志查询未返回任何结果，你可以按照以下步骤诊断问题：

1. 通过运行以下命令检查代理状态：

    `kubectl get ds omsagent --namespace=kube-system`

    输出应类似于以下示例，指示已正确部署：

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. 如果有 Windows Server 节点，请通过运行以下命令检查代理状态：

    `kubectl get ds omsagent-win --namespace=kube-system`

    输出应类似于以下示例，指示已正确部署：

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. 使用以下命令检查代理版本 *06072018* 或更高版本的部署状态：

    `kubectl get deployment omsagent-rs -n=kube-system`

    输出应类似于以下示例，指示已正确部署：

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. 使用以下命令，检查 Pod 的状态，验证它是否正在运行：`kubectl get pods --namespace=kube-system`

    输出应类似于以下示例，omsagent 状态为“正在运行”：

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>错误消息

下表汇总了使用容器见解时可能会遇到的已知错误。

| 错误消息  | 操作 |
| ---- | --- |
| 错误消息 `No data for selected filters`  | 为新创建的群集建立监视数据流可能需要花费一些时间。 群集的数据至少需要 10 到 15 分钟才能显示。 |
| 错误消息 `Error retrieving data` | 为 Azure Kubernetes 服务群集设置运行状况和性能监视时，会在群集与 Azure Log Analytics 工作区之间建立连接。 Log Analytics 工作区用于存储你的群集的所有监视数据。 当 Log Analytics 工作区已删除时，可能会发生此错误。 检查工作区是否已删除，如果已删除，则需要使用容器见解重新启用对群集的监视，并指定现有工作区或创建新工作区。 若要重新启用，将需要对该群集[禁用](container-insights-optout.md)监视，然后再次[启用](container-insights-enable-new-cluster.md)容器见解。 |
| 通过 az aks cli 添加容器见解出现`Error retrieving data` | 使用 `az aks cli` 启用监视时，可能无法正确部署容器见解。 请检查是否部署了该解决方案。 若要进行验证，请转到你的 Log Analytics 工作区，从左侧的面板中选择“解决方案”来查看该解决方案是否可用。 要解决此问题，需要按照[如何部署容器见解](container-insights-onboard.md)中的说明重新部署该解决方案 |

为了帮助诊断问题，我们提供了一个[故障排除脚本](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot)。

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>未在非 Azure Kubernetes 群集上计划容器见解代理 ReplicaSet Pod

容器见解代理 ReplicaSet pod 依赖于辅助节点上的以下节点选择器（或代理）节点进行计划：

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

如果工作器节点未附加节点标签，则将不会计划代理 ReplicaSet Pod。 有关如何附加标签的说明，请参阅 [Kubernetes 分配标签选择器](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/)。

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>性能图表不显示非 Azure 群集上节点和容器的 CPU 或内存

容器见解代理 Pod 使用节点代理上的 cAdvisor 终结点来收集性能指标。 验证节点上的容器化代理是否配置为允许在群集中的所有节点上打开 `cAdvisor port: 10255` 以收集性能指标。

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>容器见解中未显示非 Azure Kubernetes 群集

要在容器见解中查看非 Azure Kubernetes 群集，需要在支持此见解的 Log Analytics 工作区和容器见解解决方案资源 ContainerInsights（工作区）上具有读取访问权限。

## <a name="metrics-arent-being-collected"></a>未收集指标

1. 验证群集是否处于[受支持的自定义指标区域](../essentials/metrics-custom-overview.md#supported-regions)。

2. 使用以下 CLI 命令验证是否存在“监视指标发布者”角色分配：

    ``` azurecli
    az role assignment list --assignee "SP/UserassignedMSI for omsagent" --scope "/subscriptions/<subid>/resourcegroups/<RG>/providers/Microsoft.ContainerService/managedClusters/<clustername>" --role "Monitoring Metrics Publisher"
    ```
    对于具有 MSI 的群集，每次启用和禁用监视时，用户分配的 omsagent 客户端 ID 都会更改，因此当前 MSI 客户端 ID 上应存在角色分配。 

3. 对于启用了 Azure Active Directory Pod 标识并使用 MSI 的群集：

   - 使用以下命令验证 omsagent Pod 上是否存在所需的标签“kubernetes.azure.com/managedby: aks”：

        `kubectl get pods --show-labels -n kube-system | grep omsagent`

    - 验证使用 https://github.com/Azure/aad-pod-identity#1-deploy-aad-pod-identity 上其中一种受支持的方法启用 Pod 标识时是否启用了异常。

        运行以下命令验证：

        `kubectl get AzurePodIdentityException -A -o yaml`

        应该会收到类似如下的输出：

        ```
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: mic-exception
        namespace: default
        spec:
        podLabels:
        app: mic
        component: mic
        ---
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: aks-addon-exception
        namespace: kube-system
        spec:
        podLabels:
        kubernetes.azure.com/managedby: aks
        ```



## <a name="next-steps"></a>后续步骤

启用监视来捕获 AKS 群集节点和 Pod 的运行状况指标后，可在 Azure 门户中找到这些运行状况指标。 要了解如何使用容器见解，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。
