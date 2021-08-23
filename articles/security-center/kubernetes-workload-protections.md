---
title: 针对 Kubernetes 工作负载的工作负载保护
description: 了解如何使用 Azure 安全中心的一组 Kubernetes 工作负载保护安全建议
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/14/2021
ms.author: memildin
ms.openlocfilehash: fefbc605702539cb882aba4c7802d284b4291a9c
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112062291"
---
# <a name="protect-your-kubernetes-workloads"></a>保护 Kubernetes 工作负载

本页介绍了如何使用 Azure 安全中心提供的一组专用于 Kubernetes 工作负载保护的安全建议。

可以在[使用 Kubernetes 准入控制实现工作负载保护的最佳做法](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control)中更详细地了解这些功能。

如果你启用了 Azure Defender，则安全中心会提供更多的容器安全功能。 具体而言：

- 通过[适用于容器注册表的 Azure Defender](defender-for-container-registries-introduction.md) 扫描容器注册表中是否存在漏洞
- 通过[适用于 Kubernetes 的 Azure Defender](defender-for-kubernetes-introduction.md) 获取针对 K8s 群集的实时威胁检测警报

> [!TIP]
> 有关可能会针对 Kubernetes 群集和节点显示的所有安全建议的列表，请参阅建议参考表的[计算部分](recommendations-reference.md#recs-compute)。



## <a name="availability"></a>可用性

| 方面                          | 详细信息                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| 发布状态：                  | 正式发布版 (GA)                                                                                                                    |
| 定价：                        | 免费                                                                                                                                         |
| 所需角色和权限： | **所有者** 或 **安全管理员**，用以编辑分配<br>**读者**，用以查看建议                                              |
| 环境要求：       | 需要 Kubernetes v1.14（或更高版本）<br>群集上没有 PodSecurityPolicy 资源（旧的 PSP 模型）<br>不支持 Windows 节点 |
| 云：                         | ![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) 国家/主权（US Gov、中国 Gov、其他 Gov） |
|                                 |                                                                                                                                              |


## <a name="set-up-your-workload-protection"></a>设置工作负载保护

Azure 安全中心包含一系列建议，安装 **适用于 Kubernetes 的 Azure Policy 加载项** 后可以获得这些建议。

### <a name="step-1-deploy-the-add-on"></a>步骤 1：部署加载项

若要配置建议，请安装 **适用于 Kubernetes 的 Azure Policy 加载项**。 

- 可以根据[启用 Log Analytics 代理和扩展的自动预配](security-center-enable-data-collection.md#auto-provision-mma)中的说明，自动部署此加载项。 将加载项的自动预配设置为“启用”时，默认情况下会在所有现有和未来的群集（满足加载项安装要求）中启用该扩展。

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="使用安全中心的自动预配工具安装适用于 Kubernetes 的 Policy 加载项":::

- 若要手动部署此加载项，请执行以下操作：

    1. 在“建议”页上，搜索“应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项”建议。 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="建议 **应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项**":::

        > [!TIP]
        > 建议包括在五个不同的安全控件中，在下一步中选择哪个控件都无关紧要。

    1. 从任何安全控件中，选择建议来查看可在其上安装加载项的资源。
    1. 选择相关群集并进行 **修正**。

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="**应在群集上安装并启用适用于 Kubernetes 的 Azure Policy 加载项** 的建议详细信息页面":::

### <a name="step-2-view-and-configure-the-bundle-of-recommendations"></a>步骤 2：查看并配置建议的捆绑包

1. 在加载项安装完成大约 30 分钟后，安全中心会显示以下建议的群集运行状况状态，每个建议都显示在相关安全控件中，如下所示：

    > [!TIP]
    > 某些建议包含参数，必须通过 Azure Policy 自定义参数才能有效地使用这些建议。 例如，若要利用“应当只从受信任的注册表部署容器映像”建议，必须定义受信任的注册表。
    > 
    > 如果没有为建议输入需要进行配置的必需参数，则工作负载将显示为“不正常”。

    | 建议名称                                                         | 安全控制                         | 需要进行配置 |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | 应强制执行容器 CPU 和内存限制                          | 保护应用程序免受 DDoS 攻击 | 否                     |
    | 应避免特权容器                                     | 管理访问和权限            | 否                     |
    | 应强制对容器使用不可变（只读）根文件系统     | 管理访问和权限            | 否                     |
    | 应避免使用特权提升的容器                       | 管理访问和权限            | 否                     |
    | 应避免以根用户身份运行容器                           | 管理访问和权限            | 否                     |
    | 应避免使用共享敏感主机命名空间的容器              | 管理访问和权限            | 否                     |
    | 应强制对容器使用最低权限 Linux 功能       | 管理访问和权限            | **是**                |
    | Pod HostPath 卷装载的使用应仅限于已知列表    | 管理访问和权限            | **是**                |
    | 容器应只侦听允许的端口                              | 限制未经授权的网络访问     | **是**                |
    | 服务应只侦听允许的端口                                | 限制未经授权的网络访问     | **是**                |
    | 应限制对主机网络和端口的使用                     | 限制未经授权的网络访问     | **是**                |
    | 应限制替代或禁用容器 AppArmor 配置文件 | 修正安全配置        | **是**                |
    | 应只从受信任的注册表部署容器映像            | 修正漏洞                | **是**                |
    |||


1. 对于带有必须自定义参数的建议，请设定参数：

    1. 从安全中心的菜单中，选择“安全策略”。
    1. 选择相关订阅。
    1. 从“安全中心的默认策略”部分，选择“查看有效策略”。
    1. 选择“ASC 默认值”。
    1. 打开“参数”选项卡，并根据需要修改这些值。
    1. 选择“查看 + 保存”。
    1. 选择“保存”。


1. 若要强制实施任何建议，请执行以下操作： 

    1. 打开建议详细信息页，然后选择“拒绝”：

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Azure Policy 参数的拒绝选项":::

        这会打开一个窗格，你可以在其中设置作用域。 

    1. 设置作用域后，选择“更改为拒绝”。

1. 若要查看适用于你的群集的建议，请执行以下操作：

    1. 打开安全中心的 [资产清单](asset-inventory.md)页，并对 **Kubernetes 服务** 使用资源类型筛选器。

    1. 选择要调查的群集，并查看可用于该群集的可用建议。 

1. 查看工作负载保护集提供的建议时，你会看到受影响的 pod（“Kubernetes 组件”）的数目随群集一起列出。 有关特定 pod 的列表，请选择该群集，然后选择“采取操作”。

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="查看 K8s 建议针对的受影响 pod"::: 

1. 若要测试强制实施，请使用下面的两个 Kubernetes 部署：

    - 一个是正常的部署，遵循工作负载保护建议捆绑包。
    - 另一个是非正常的部署，不遵循任何建议。

    按原样部署示例 yaml 文件，或参考它们来修正你自己的工作负载（步骤 VIII）。  


## <a name="healthy-deployment-example-yaml-file"></a>正常的部署示例 .yaml 文件

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>非正常的部署示例 .yaml 文件

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>后续步骤

在本文中，你已了解了如何配置 Kubernetes 工作负载保护。 

有关其他相关材料，请参阅以下页面： 

- [针对计算的安全中心建议](recommendations-reference.md#recs-compute)
- [AKS 群集级别的警报](alerts-reference.md#alerts-k8scluster)
- [容器主机级别的警报](alerts-reference.md#alerts-containerhost)
