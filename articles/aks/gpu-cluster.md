---
title: 在 Azure Kubernetes 服务 (AKS) 上使用 GPU
description: 了解如何在 Azure Kubernetes 服务 (AKS) 上将 GPU 用于高性能计算或图形密集型工作负荷
services: container-service
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: fa7415f015ad17cc2e8a5ff4822c8ff53578f054
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751529"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 上将 GPU 用于计算密集型工作负荷

图形处理单元 (GPU) 通常用于计算密集型工作负荷，例如图形和可视化工作负荷。 AKS 支持创建启用 GPU 的节点池，以在 Kubernetes 中运行这些计算密集型工作负荷。 有关可用的启用了 GPU 的 VM 的详细信息，请参阅 [Azure 中 GPU 优化 VM 的大小][gpu-skus]。 对于 AKS 节点池，建议的最小大小为 Standard_NC6。

> [!NOTE]
> 启用 GPU 的 VM 包含专用硬件，这些硬件定价较高，其可用性受区域限制。 有关详细信息，请参阅[定价][azure-pricing]工具和[区域可用性][azure-availability]。

目前，使用支持 GPU 的节点池这一功能仅适用于 Linux 节点池。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅[快速入门：使用 Azure CLI 部署 Azure Kubernetes 服务群集][aks-quickstart]。

还需安装并配置 Azure CLI 2.0.64 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="get-the-credentials-for-your-cluster"></a>获取群集的凭据

使用 [az aks get-credentials][az-aks-get-credentials] 命令获取 AKS 群集的凭据。 以下示例命令获取 myResourceGroup 资源组中 myAKSCluster 的凭据 。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-the-nvidia-device-plugin"></a>添加 NVIDIA 设备插件

可通过两种做法添加 NVIDIA 设备插件：

* 使用 AKS GPU 映像
* 手动安装 NVIDIA 设备插件

> [!WARNING]
> 可以采取上述任一做法，但在使用 AKS GPU 映像的群集中不应手动安装 NVIDIA 设备插件守护程序集。

### <a name="update-your-cluster-to-use-the-aks-gpu-image-preview"></a>将群集更新为使用 AKS GPU 映像（预览版）

AKS 提供完全配置的 AKS 映像，其中已包含[适用于 Kubernetes 的 NVIDIA 设备插件][nvidia-github]。

注册 `GPUDedicatedVHDPreview` 功能：

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

状态可能需要几分钟才显示为“已注册”。 可以使用 [az feature list](/cli/azure/feature#az_feature_list) 命令来检查注册状态：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

当状态显示为“已注册”时，使用 [az provider register](/cli/azure/provider#az_provider_register) 命令来刷新 `Microsoft.ContainerService` 资源提供程序的注册：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

若要安装 aks-preview CLI 扩展，请使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
```

若要更新 aks-preview CLI 扩展，请使用以下 Azure CLI 命令：

```azurecli
az extension update --name aks-preview
```

## <a name="add-a-node-pool-for-gpu-nodes"></a>为 GPU 节点添加节点池

若要将节点池添加到群集，请使用 [az aks nodepool add][az-aks-nodepool-add]。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunp \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --node-taints sku=gpu:NoSchedule \
    --aks-custom-headers UseGPUDedicatedVHD=true \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3
```

以上命令将名为 gpunp 的节点池添加到 myResourceGroup 资源组中的 myAKSCluster  。 该命令还将节点池中节点的 VM 大小设置为 Standard_NC6，启用群集自动缩放程序，将群集自动缩放程序配置为在节点池中保留最少一个节点和最多三个节点，在新节点池中指定专用 AKS GPU 映像节点，并为节点池指定 sku=gpu:NoSchedule 排斥 。

> [!NOTE]
> 只能在创建节点池期间为节点池设置排斥和 VM 大小，但随时可以更新自动缩放程序设置。

> [!NOTE]
> 如果你的 GPU SKU 需要第二代 VM，请使用 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true。 例如：
> 
> ```azurecli
> az aks nodepool add \
>    --resource-group myResourceGroup \
>    --cluster-name myAKSCluster \
>    --name gpunp \
>    --node-count 1 \
>    --node-vm-size Standard_NC6 \
>    --node-taints sku=gpu:NoSchedule \
>    --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true \
>    --enable-cluster-autoscaler \
>    --min-count 1 \
>    --max-count 3
> ```

### <a name="manually-install-the-nvidia-device-plugin"></a>手动安装 NVIDIA 设备插件

或者，可为 NVIDIA 设备插件部署 DaemonSet。 此 DaemonSet 在会每个节点上运行 pod，以便为 GPU 提供所需驱动程序。

使用 [az aks nodepool add][az-aks-nodepool-add] 将节点池添加到群集。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunp \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --node-taints sku=gpu:NoSchedule \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3
```

以上命令将名为 gpunp 的节点池添加到 myResourceGroup 资源组中的 myAKSCluster  。 该命令还将节点池中节点的 VM 大小设置为 Standard_NC6，启用群集自动缩放程序，将群集自动缩放程序配置为在节点池中保留最少一个节点和最多三个节点，并为节点池指定 sku=gpu:NoSchedule 排斥 。

> [!NOTE]
> 只能在创建节点池期间为节点池设置排斥和 VM 大小，但随时可以更新自动缩放程序设置。

使用 [kubectl create namespace][kubectl-create] 命令创建命名空间，例如 gpu-resources：

```console
kubectl create namespace gpu-resources
```

创建名为“nvidia-device-plugin-ds.yam”的文件并粘贴以下 YAML 清单  。 此清单作为 [Kubernetes 项目的 NVIDIA 设备插件][nvidia-github]的一部分提供。

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      - key: "sku"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

使用 [kubectl apply][kubectl-apply] 创建 DaemonSet 并确认是否已成功创建 NVIDIA 设备插件，如以下示例输出中所示：

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>确认 GPU 是可计划的

创建 AKS 群集后，确认 GPU 在 Kubernetes 中是可计划的。 首先，使用 [kubectl get nodes][kubectl-get] 命令列出群集中的节点：

```console
$ kubectl get nodes

NAME                   STATUS   ROLES   AGE   VERSION
aks-gpunp-28993262-0   Ready    agent   13m   v1.20.7
```

现在，使用 [kubectl describe node][kubectl-describe] 命令确认 GPU 是可计划的。 在“容量”部分下，GPU 应列为  `nvidia.com/gpu:  1`。

以下精简示例显示了 GPU 在名为“aks-nodepool1-18821093-0”的节点上可用  ：

```console
$ kubectl describe node aks-gpunp-28993262-0

Name:               aks-gpunp-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
[...]
 nvidia.com/gpu:                 1
[...]
```

## <a name="run-a-gpu-enabled-workload"></a>运行启用了 GPU 的工作负荷

若要查看 GPU 的运行情况，请通过相应的资源请求计划启用了 GPU 的工作负荷。 在此示例中，我们针对 [MNIST 数据集](https://www.tensorflow.org/)运行一个 [Tensorflow](http://yann.lecun.com/exdb/mnist/) 作业。

创建名为“samples-tf-mnist-demo.yaml”的文件并粘贴以下 YAML 清单  。 以下作业清单包括资源限制 `nvidia.com/gpu: 1`：

> [!NOTE]
> 如果在调用驱动程序时收到版本不匹配错误，例如，CUDA 驱动程序版本不足以支持 CUDA 运行时版本，请查看 NVIDIA 驱动程序矩阵兼容性图表 - [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
      tolerations:
      - key: "sku"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
```

使用 [kubectl apply][kubectl-apply] 命令运行该作业。 此命令分析清单文件并创建定义的 Kubernetes 对象：

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>查看启用了 GPU 的工作负荷的状态和输出

将 [kubectl get jobs][kubectl-get] 命令与 `--watch` 参数配合使用，监视作业的进度。 先拉取映像并处理数据集可能需要几分钟时间。 当“COMPLETIONS”列显示“1/1”时，作业便已成功完成   。 使用 `kubetctl --watch`Ctrl-C *退出* 命令：

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

若要查看启用了 GPU 的工作负荷的输出，首先请使用 [kubectl get pods][kubectl-get] 命令获取 Pod 名称：

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

现在，使用 [kubectl logs][kubectl-logs] 命令查看 Pod 日志。 以下示例 pod 日志确认已发现适当的 GPU 设备，即 `Tesla K80`。 为自己的 pod 提供名称：

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="use-container-insights-to-monitor-gpu-usage"></a>使用容器见解监视 GPU 使用情况

[包含 AKS 的容器见解][aks-container-insights]可使用以下指标来监视 GPU 使用情况。

| 指标名称 | 指标维度（标记） | 说明 |
|-------------|-------------------------|-------------|
| containerGpuDutyCycle | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor`   | 在刚过去的采样周期（60 秒）中，GPU 处于繁忙/积极处理容器的状态的时间百分比。 占空比是 1 到 100 之间的数字。 |
| containerGpuLimits | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName` | 每个容器可以将限值指定为一个或多个 GPU。 不能请求或限制为 GPU 的一部分。 |
| containerGpuRequests | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName` | 每个容器可以请求一个或多个 GPU。 不能请求或限制为 GPU 的一部分。 |
| containerGpumemoryTotalBytes | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor` | 可用于特定容器的 GPU 内存量（以字节为单位）。 |
| containerGpumemoryUsedBytes | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor` | 特定容器使用的 GPU 内存量（以字节为单位）。 |
| nodeGpuAllocatable | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `gpuVendor` | 节点中可供 Kubernetes 使用的 GPU 数。 |
| nodeGpuCapacity | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `gpuVendor` | 节点中的 GPU 总数。 |

## <a name="clean-up-resources"></a>清理资源

若要删除本文中创建的相关 Kubernetes 对象，请使用 [kubectl delete job][kubectl delete] 命令，如下所示：

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>后续步骤

若要运行 Apache Spark 作业，请参阅[在 AKS 上运行 Apache Spark 作业][aks-spark]。

有关在 Kubernetes 上运行机器学习 (ML) 工作负荷的更多信息，请参阅 [Kubeflow 实验室][kubeflow-labs]。

如果要详细了解如何配合使用 Azure Kubernetes 服务与 Azure 机器学习，请参阅以下文章：

* [将模型部署到 Azure Kubernetes 服务][azureml-aks]。
* [使用 GPU 为推理部署深度学习模型][azureml-gpu]。
* [借助 Triton 推理服务器高性能地提供服务][azureml-triton]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
[azureml-aks]: ../machine-learning/how-to-deploy-azure-kubernetes-service.md
[azureml-gpu]: ../machine-learning/how-to-deploy-inferencing-gpus.md
[azureml-triton]: ../machine-learning/how-to-deploy-with-triton.md
[aks-container-insights]: monitor-aks.md#container-insights