---
title: 已启用 Azure Arc 的机器学习（预览版）
description: 配置已启用 Azure Arc 的 Kubernetes 群集，以在 Azure 机器学习中训练机器学习模型
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 06/18/2021
ms.topic: how-to
ms.openlocfilehash: 0842552aa3e5e172a7aa725d07c9a83b63da6667
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745296"
---
# <a name="configure-azure-arc-enabled-machine-learning-preview"></a>配置已启用 Azure Arc 的机器学习（预览版）

了解如何配置已启用 Azure Arc 的机器学习来进行训练。

## <a name="what-is-azure-arc-enabled-machine-learning"></a>什么是已启用 Azure Arc 的机器学习？

借助 Azure Arc，你可以在任何 Kubernetes 环境中（无论是本地、多云还是边缘）运行 Azure 服务。

已启用 Azure Arc 的机器学习允许你配置和使用已启用 Azure Arc 的 Kubernetes 群集，以训练和管理 Azure 机器学习中的机器学习模型。

已启用 Azure Arc 的机器学习支持以下训练方案：

* 使用 2.0 CLI 训练模型
  * 分布式训练
  * 超参数扫描
* 使用 Azure 机器学习 Python SDK 训练模型
  * 超参数优化
* 生成并使用机器学习管道
* 使用出站代理服务器训练本地模型
* 使用 NFS 数据存储训练本地模型

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free)。
* 已启用 Azure Arc 的 Kubernetes 群集。 有关详细信息，请参阅[将现有 Kubernetes 群集连接到 Azure Arc 快速入门指南](../azure-arc/kubernetes/quickstart-connect-cluster.md)。
* 满足[已启用 Azure Arc 的 Kubernetes 群集扩展先决条件](../azure-arc/kubernetes/extensions.md#prerequisites)。
  * Azure CLI 版本 >= 2.24.0
  * Azure CLI k8s-extension 扩展版本 >= 0.4.3
* Azure 机器学习工作区。 如果还没有工作区，请在开始之前[创建一个工作区](how-to-manage-workspace.md?tabs=python)。
  * Azure 机器学习 Python SDK 版本 >= 1.30

## <a name="deploy-azure-machine-learning-extension"></a>部署 Azure 机器学习扩展

已启用 Azure Arc 的 Kubernetes 具有群集扩展功能，可用于安装各种代理，包括 Azure 策略、监视、机器学习等。 Azure 机器学习需要使用 Microsoft.AzureML.Kubernetes 群集扩展，才能在 Kubernetes 群集上部署 Azure 机器学习代理。 安装 Azure 机器学习扩展后，可以将群集附加到 Azure 机器学习工作区，并使用它进行训练。

使用 `k8s-extension` Azure CLI 扩展将 Azure 机器学习扩展部署到已启用 Azure Arc 的 Kubernetes 群集。

1. 登录到 Azure
    
    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

1. 部署 Azure 机器学习扩展

    ```azurecli
    az k8s-extension create --name amlarc-compute --extension-type Microsoft.AzureML.Kubernetes --configuration-settings enableTraining=True  --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group> --scope cluster
    ```

    >[!IMPORTANT]
    > 若要启用已启用 Azure Arc 的群集进行训练，必须将 `enableTraining` 设置为 True。 运行此命令会在与 Arc 群集相同的资源组下创建 Azure 服务总线和 Azure 中继资源。 这些资源用于与群集通信。 修改它们会破坏用作训练计算目标的附加群集。

    部署用于模型训练的 Azure 机器学习扩展时，还可以配置以下设置：

    |配置设置键名  |说明  |
    |--|--|
    | ```enableTraining``` | 默认为 `False`。 如果设置为 `True`，将创建用于训练机器学习模型的扩展实例。  |
    |```logAnalyticsWS```  | 默认为 `False`。 Azure 机器学习扩展与 Azure LogAnalytics 工作区集成。 如果设置为 `True`，将通过 LogAnalytics 工作区提供日志查看和分析功能。 可能要支付 LogAnalytics 工作区费用。   |
    |```installNvidiaDevicePlugin```  | 默认为 `True`。 在 Nvidia GPU 硬件上进行训练需要安装 Nvidia 设备插件。 无论 Kubernetes 群集是否有 GPU 硬件，Azure 机器学习扩展都会在 Azure 机器学习实例创建期间默认安装 Nvidia 设备插件。 如果你不打算使用 GPU 进行训练或已安装 Nvidia 设备插件，请设置为 `False`。  |
    |```installBlobfuseSysctl```  | 如果“enableTraining=True”，则默认为 `True`。 训练需要使用 Blobfuse 1.3.7。 默认情况下，Azure 机器学习会在创建扩展实例时安装 Blobfuse。 如果 Kubernetes 群集上已安装 Blobfuse 1.37，则将此配置设置设为 `False`。   |
    |```installBlobfuseFlexvol```  | 如果“enableTraining=True”，则默认为 `True`。 训练需要使用 Blobfuse Flexvolume。 默认情况下，Azure 机器学习将 Blobfuse Flexvolume 安装在默认路径中。 如果 Kubernetes 群集上已安装 Blobfuse Flexvolume，则将此配置设置设为 `False`。   |
    |```volumePluginDir```  | 要安装的 Blobfuse Flexvolume 的主机路径。 仅当“enableTraining=True”时适用。 默认情况下，Azure 机器学习在默认路径 /etc/kubernetes/volumeplugins 下安装 Blobfuse Flexvolume。 通过指定此配置设置来指定自定义安装位置。```   |

    > [!WARNING]
    > 如果群集中已安装 Nvidia 设备插件、Blobfuse 和 Blobfuse Flexvolume，重新安装它们可能会导致扩展安装错误。 将 `installNvidiaDevicePlugin`、`installBlobfuseSysctl` 和 `installBlobfuseFlexvol` 设置为 `False`，以防止安装错误。

1. 验证 AzureML 扩展部署

    ```azurecli
    az k8s-extension show --name amlarc-compute --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <resource-group>
    ```

    在响应中，查找 `"extensionType": "amlarc-compute"` 和 `"installState": "Installed"`。 请注意，它可能会在前几分钟显示 `"installState": "Pending"`。

    当 `installState` 显示“Installed”时，请在将 kubeconfig 文件指向你的群集的情况下，在计算机上运行以下命令，以检查 azureml 命名空间下的所有 Pod 是否都处于“Running”状态：

   ```bash
    kubectl get pods -n azureml
   ```

## <a name="attach-arc-cluster-studio"></a>附加 Arc 群集（工作室）

附加一个已启用 Azure Arc 的 Kubernetes 群集，使其可用于工作区训练。

1. 导航到 [Azure 机器学习工作室](https://ml.azure.com)。
1. 在“管理”下，选择“计算” 。
1. 选择“附加的计算”选项卡。
1. 选择“+新建”>“Kubernetes (预览版)”

   ![附加 Kubernetes 群集](./media/how-to-attach-arc-kubernetes/attach-kubernetes-cluster.png)

1. 输入计算名称，并从下拉列表中选择已启用 Azure Arc 的 Kubernetes 群集。

   ![配置 Kubernetes 群集](./media/how-to-attach-arc-kubernetes/configure-kubernetes-cluster.png)

1. （可选）对于高级方案，请浏览并上传配置文件。

   ![上传配置文件](./media/how-to-attach-arc-kubernetes/upload-configuration-file.png)

1. 选择“附加”

    在“附加的计算”选项卡中，群集的初始状态为“正在创建”。 成功附加群集后，状态将更改为“成功”。 否则，状态将更改为“失败”。

    ![预配资源](./media/how-to-attach-arc-kubernetes/provision-resources.png)

### <a name="advanced-attach-scenario"></a>高级附加方案

使用 JSON 配置文件在已启用 Azure Arc 的 Kubernetes 群集上配置高级计算目标功能。

下面是一个配置文件示例：

```json
{
   "namespace": "amlarc-testing",
   "defaultInstanceType": "gpu_instance",
   "instanceTypes": {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
         }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         },
         "resources": {
            "requests": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            },
            "limits": {
               "cpu": "4",
               "memory": "16Gi",
               "nvidia.com/gpu": "0"
            }
         }
      }
   }
}
```

可以使用配置文件配置以下自定义计算目标属性：

* `namespace` - 默认为 `default` 命名空间。 这是运行作业和 Pod 的命名空间。 请注意，在设置非默认命名空间时，该命名空间必须已经存在。 创建命名空间需要群集管理员权限。

* `defaultInstanceType` - 默认情况下运行训练作业的实例类型。 如果指定了 `instanceTypes` 属性，则需要 `defaultInstanceType`。 `defaultInstanceType` 的值必须是 `instanceTypes` 属性中定义的值之一。

    > [!IMPORTANT]
    > 目前，仅支持使用计算机目标名称的作业提交。 因此，该配置将始终默认为 defaultInstanceType。

* `instanceTypes` - 用于训练作业的实例类型列表。 每个实例类型由 `nodeSelector` 和 `resources requests/limits` 属性定义：

  * `nodeSelector` - 一个或多个节点标签，用于标识群集中的节点。 为群集节点创建标签需要群集管理员权限。 如果指定了此属性，训练作业将安排在具有指定节点标签的节点上运行。 你可以使用 `nodeSelector` 来瞄准用于训练工作负载放置的节点子集。 在群集具有不同的 SKU 或不同类型的节点（如 CPU 或 GPU 节点）的情况下，此功能非常有用。 例如，你可以为所有 GPU 节点创建节点标签，并为 GPU 节点池定义 `instanceType`。 计划训练作业时，这样做将专门瞄准 GPU 节点池。 

  * `resources requests/limits` - 指定资源请求并限制要运行的训练作业 Pod。 默认为 1 个 CPU 和 4GB 内存。

    >[!IMPORTANT]
    > 默认使用 1 个 CPU 和 4GB 内存部署群集资源。 如果为群集配置的资源较少，作业运行将失败。 为确保作业成功完成，建议始终根据训练作业需求来指定资源请求/限制。 下面是一个默认配置文件示例：
    >
    > ```json
    > {
    >    "namespace": "default",
    >    "defaultInstanceType": "defaultInstanceType",
    >    "instanceTypes": {
    >       "defaultInstanceType": {
    >          "nodeSelector": null,
    >          "resources": {
    >             "requests": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             },
    >             "limits": {
    >                "cpu": "1",
    >                "memory": "4Gi",
    >                "nvidia.com/gpu": "0"
    >             }
    >          }
    >       }
    >    }
    > }
    > ```

## <a name="attach-arc-cluster-python-sdk"></a>附加 Arc 群集 (Python SDK)

以下 Python 代码演示如何附加已启用 Azure Arc 的 Kubernetes 群集并将其用作训练的计算目标：

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
    amlarc_compute = ws.compute_targets[amlarc_compute_name]
    if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
        print("found compute target: " + amlarc_compute_name)
else:
    print("creating new compute target...")

    amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id) 
    amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
    amlarc_compute.wait_for_completion(show_output=True)
    
     # For a more detailed view of current KubernetesCompute status, use get_status()
    print(amlarc_compute.get_status().serialize())
```

### <a name="advanced-attach-scenario"></a>高级附加方案

以下代码演示如何配置高级计算目标属性，如namespace、nodeSelector 或 resources requests/limits：

```python
from azureml.core.compute import KubernetesCompute
from azureml.core.compute import ComputeTarget
import os

ws = Workspace.from_config()

# choose a name for your Azure Arc-enabled Kubernetes compute
amlarc_compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "amlarc-compute")

# resource ID for your Azure Arc-enabled Kubernetes cluster
resource_id = "/subscriptions/123/resourceGroups/rg/providers/Microsoft.Kubernetes/connectedClusters/amlarc-cluster"

if amlarc_compute_name in ws.compute_targets:
   amlarc_compute = ws.compute_targets[amlarc_compute_name]
   if amlarc_compute and type(amlarc_compute) is KubernetesCompute:
      print("found compute target: " + amlarc_compute_name)
else:
   print("creating new compute target...")
   ns = "amlarc-testing"
    
   instance_types = {
      "gpu_instance": {
         "nodeSelector": {
            "accelerator": "nvidia-tesla-k80"
         },
         "resources": {
            "requests": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            },
            "limits": {
               "cpu": "2",
               "memory": "16Gi",
               "nvidia.com/gpu": "1"
            }
        }
      },
      "big_cpu_sku": {
         "nodeSelector": {
            "VMSizes": "VM-64vCPU-256GB"
         }
      }
   }

   amlarc_attach_configuration = KubernetesCompute.attach_configuration(resource_id = resource_id, namespace = ns, default_instance_type="gpu_instance", instance_types = instance_types)
 
   amlarc_compute = ComputeTarget.attach(ws, amlarc_compute_name, amlarc_attach_configuration)

 
   amlarc_compute.wait_for_completion(show_output=True)
    
   # For a more detailed view of current KubernetesCompute status, use get_status()
   print(amlarc_compute.get_status().serialize())
```

## <a name="next-steps"></a>后续步骤

- [使用 2.0 CLI 训练模型](how-to-train-cli.md)
- [配置和提交训练运行](how-to-set-up-training-targets.md)
- [优化超参数](how-to-tune-hyperparameters.md)
- [使用 Scikit-learn 训练模型](how-to-train-scikit-learn.md)
- [训练 TensorFlow 模型](how-to-train-tensorflow.md)
- [训练 PyTorch 模型](how-to-train-pytorch.md)
- [使用 Azure 机器学习管道进行训练](how-to-create-machine-learning-pipelines.md)
- [使用出站代理服务器训练本地模型](../azure-arc/kubernetes/quickstart-connect-cluster.md#5-connect-using-an-outbound-proxy-server)
