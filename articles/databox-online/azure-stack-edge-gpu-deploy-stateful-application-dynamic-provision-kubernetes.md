---
title: 使用 kubectl 在 Azure Stack Edge Pro GPU 设备上通过动态预配的共享部署 Kubernetes 有状态应用 | Microsoft Docs
description: 介绍如何在 Microsoft Azure Stack Edge Pro GPU 设备上使用 kubectl，通过动态预配的共享创建和管理 Kubernetes 有状态应用程序部署。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 160ba6014bbb2d5cb3ed4e8e4b28a61fe5e8d4cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520687"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-storageclass-on-your-azure-stack-edge-pro-gpu-device"></a>使用 kubectl，在 Azure Stack Edge Pro GPU 设备上通过 StorageClass 运行 Kubernetes 有状态应用程序

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何在 Kubernetes 中使用 StorageClass 动态预配存储和部署，以部署单实例有状态应用程序。 部署使用现有 Kubernetes 群集上的 `kubectl` 命令并部署 MySQL 应用程序。 

此过程适用于已阅读 [Azure Stack Edge Pro 设备上的 Kubernetes 存储](azure-stack-edge-gpu-kubernetes-storage.md)一文且熟悉 [Kubernetes 存储](https://kubernetes.io/docs/concepts/storage/)中的概念的用户。


## <a name="prerequisites"></a>先决条件

在部署有状态应用程序之前，请在设备和将用于访问该设备的客户端上满足以下先决条件：

### <a name="for-device"></a>对于设备

- 你有单节点 Azure Stack Edge Pro 设备的登录凭据。
    - 设备已激活。 请参阅[激活设备](azure-stack-edge-gpu-deploy-activate.md)。
    - 已通过 Azure 门户配置了设备的计算角色，并且设备具有 Kubernetes 群集。 请参阅[配置计算](azure-stack-edge-gpu-deploy-configure-compute.md)。

### <a name="for-client-accessing-the-device"></a>对于访问设备的客户端

- 你有一个将用于访问 Azure Stack Edge Pro 设备的 Windows 客户端系统。
    - 客户端在运行 Windows PowerShell 5.0 或更高版本。 若要下载最新版 Windows PowerShell，请参阅[安装 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell)。
    
    - 也可使用任何其他客户端，只要安装了[受支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)即可。 本文介绍使用 Windows 客户端时的过程。 
    
    - 你已完成[在 Azure Stack Edge Pro 设备上访问 Kubernetes 群集](azure-stack-edge-gpu-create-kubernetes-cluster.md)中所述的过程。 你已：
      - 通过 `New-HcsKubernetesNamespace` 命令创建 `userns1` 命名空间。 
      - 通过 `New-HcsKubernetesUser` 命令创建用户 `user1`。 
      - 通过 `Grant-HcsKubernetesNamespaceAccess` 命令授予对 `userns1` 的 `user1` 访问权限。       
      - 在客户端上安装了 `kubectl`，并且已将具有用户配置的 `kubeconfig` 文件保存到 C:\\Users\\&lt;username&gt;\\.kube。 
    
    - 确保 `kubectl` 客户端版本与 Azure Stack Edge Pro 设备上运行的 Kubernetes 主版本相差不超过一个版本。 
        - 使用 `kubectl version` 检查在客户端上运行的 kubectl 的版本。 记下完整版本。
        - 在 Azure Stack Edge Pro 设备的本地 UI 中，转到“概述”，并记下 Kubernetes 软件编号。 
        - 从支持的 Kubernetes 版本中提供的映射中验证这两个版本是否兼容<!-- insert link-->. 


这样，你就准备好在 Azure Stack Edge Pro 设备上部署有状态应用程序了。 


## <a name="deploy-mysql"></a>部署 MySQL

现在，你将通过使用 PersistentVolumeClaim (PVC) 创建 Kubernetes 部署并将其连接到内置 StorageClass 来运行有状态应用程序。 

用于创建和管理有状态应用程序部署的所有 `kubectl` 命令都需要指定与配置关联的命名空间。 若要在 kubectl 命令中指定命名空间，请使用 `kubectl <command> -n <your-namespace>`。

1. 获取运行于 Kubernetes 群集上的命名空间中的 Pod 的列表。 Pod 是在 Kubernetes 群集上运行的应用程序容器或进程。

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   以下是命令使用情况的示例：
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   输出会指出，因为群集上没有运行任何应用程序，所以找不到任何资源 (Pod)。

1. 你将使用以下 YAML 文件。 `mysql-deployment.yml` 文件说明运行 MySQL 并引用 PVC 的部署。 该文件定义 `/var/lib/mysql` 的卷装载，然后创建用于查找卷大小为 20 GB 的 PVC。 已预配动态 PV，并且已将 PVC 绑定到此 PV。

    将以下 `mysql-deployment.yml` 文件复制并保存到用于访问 Azure Stack Edge Pro 设备的 Windows 客户端上的文件夹中。
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim-sc
    ```
    
2. 复制并作为 `mysql-pvc.yml` 文件保存到你保存了 `mysql-deployment.yml` 的同一文件夹中。 若要使用 Azure Stack Edge Pro 设备在附加的数据磁盘上的内置 StorageClass，请将 PVC 对象中的 `storageClassName` 字段设置为 `ase-node-local`，并且 accessModes 应为 `ReadWriteOnce`。 

    > [!NOTE] 
    > 请确保 YAML 文件具有正确的缩进。 可以使用 [YAML lint](http://www.yamllint.com/) 进行检查以便验证并保存。
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim-sc
    spec:
      storageClassName: ase-node-local
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. 部署 `mysql-pvc.yaml` 文件。

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    下面是部署的示例输出。

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pvc.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim-sc created
    C:\Users\user>
    ```
   记下已创建的 PVC 的名称 - 在本示例中为 `mysql-pv-claim-sc`。 你将在后面的步骤中用到它。

4. 部署 `mysql-deployment.yml` 文件的内容。

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    下面是部署的示例输出。
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
    service/mysql created
    deployment.apps/mysql created
    C:\Users\user>
    ```
    
5. 显示有关部署的信息。

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Thu, 20 Aug 2020 11:14:25 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim-sc
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-695c4d9dcd (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  24s   deployment-controller  Scaled up replica set mysql-695c4d9dcd to 1
    C:\Users\user>
    ```
    

6. 列出由部署创建的 Pod。

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    下面是示例输出。

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                     READY   STATUS    RESTARTS   AGE
    mysql-695c4d9dcd-rvzff   1/1     Running   0          40s
    C:\Users\user>
    ```
    
7. 检查 PersistentVolumeClaim。

    `kubectl describe pvc <your-pvc-name>`

    下面是示例输出。

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim-sc -n userns1
    Name:          mysql-pv-claim-sc
    Namespace:     userns1
    StorageClass:  ase-node-local
    Status:        Bound
    Volume:        pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim-sc","namespace":"userns1"},"spec":{"...
                   pv.kubernetes.io/bind-completed: yes
                   pv.kubernetes.io/bound-by-controller: yes
                   volume.beta.kubernetes.io/storage-provisioner: rancher.io/local-path
                   volume.kubernetes.io/selected-node: k8s-3q7lhq2cl-3q7lhq2
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      20Gi
    Access Modes:  RWO
    VolumeMode:    Filesystem
    Mounted By:    mysql-695c4d9dcd-rvzff
    Events:
      Type    Reason                 Age                From                                                                                                Message
      ----    ------                 ----               ----                                                                                                -------
      Normal  WaitForFirstConsumer   71s (x2 over 77s)  persistentvolume-controller                                                                         waiting for first consumer to be created before binding
      Normal  ExternalProvisioning   62s                persistentvolume-controller                                                                         waiting for a volume to be created, either by external provisioner "rancher.io/local-path" or manually created by system administrator
      Normal  Provisioning           62s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  External provisioner is provisioning volume for claim "userns1/mysql-pv-claim-sc"
      Normal  ProvisioningSucceeded  60s                rancher.io/local-path_local-path-provisioner-6b84988bf9-tx8mz_1896d824-f862-4cbf-912a-c8cc0ca05574  Successfully provisioned volume pvc-dc48253c-82dc-42a4-a7c6-aaddc97c9b8a
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>验证 MySQL 是否正在运行

若要验证应用程序是否正在运行，请键入：

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql -p`

提示时提供密码。 密码在 `mysql-deployment` 文件中。

下面是示例输出。

```powershell
C:\Users\user>kubectl exec mysql-695c4d9dcd-rvzff -i -t -n userns1 -- mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>
```

## <a name="delete-a-deployment"></a>删除部署

若要删除部署，请按名称删除已部署的对象。 这些对象包括部署、服务和 PVC。
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

下面是删除部署和服务时的示例输出。

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
下面是删除 PVC 时的示例输出。

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim-sc -n userns1
persistentvolumeclaim "mysql-pv-claim-sc" deleted
C:\Users\user>
```                                                                                         


## <a name="next-steps"></a>后续步骤

若要了解如何通过 kubectl 配置网络，请参阅[在 Azure Stack Edge Pro 设备上部署无状态应用程序](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)