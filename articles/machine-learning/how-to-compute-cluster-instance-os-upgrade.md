---
title: 升级计算群集和实例的主机 OS
titleSuffix: Azure Machine Learning
description: 将计算群集和计算实例的主机 OS 从 Ubuntu 16.04 LTS 升级到 18.04 LTS。
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: how-to
ms.openlocfilehash: e6ef72031f000c0b638d47e235b75eccf3f885f8
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112461388"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>升级计算实例和计算群集的主机 OS

Azure 机器学习计算群集和计算实例是托管的计算基础结构。 Microsoft 会将已安装的主机 OS 以及程序包和软件版本作为一项托管服务进行管理。

计算群集和计算实例的主机 OS 一直是 Ubuntu 16.04 LTS。 **2021 年 4 月 30 日**，Ubuntu 将结束对 16.04 的支持。 从 __2021 年 3 月 15 日__ 开始，Microsoft 会自动将主机 OS 更新为 Ubuntu 18.04 LTS。 更新到 18.04 可确保持续从 Ubuntu 社区获得安全更新和支持。 此更新会逐步在各个 Azure 区域推出，到 2021 年 4 月 9 日将在所有区域提供。 有关 Ubuntu 结束支持 16.04 的详细信息，请参阅 [Ubuntu 发布博客](https://wiki.ubuntu.com/Releases)。

> [!TIP]
> * 主机 OS 不是你可能会在训练或部署模型时为[环境](how-to-use-environments.md)指定的 OS 版本。 环境在 Docker 内运行。 Docker 在主机 OS 上运行。
> * 如果你当前正在使用基于 Ubuntu 16.04 的环境进行训练或部署，Microsoft 建议你改为使用基于 Ubuntu 18.04 的映像。 有关详细信息，请参阅[如何使用环境](how-to-use-environments.md)和 [Azure 机器学习容器存储库](https://github.com/Azure/AzureML-Containers/tree/master/base)。
> * 使用基于 Ubuntu 18.04 的 Azure 机器学习计算实例时，默认 Python 版本为 Python 3.8。
## <a name="creating-new-resources"></a>创建新的资源

2021 年 4 月 9 日以后创建的计算群集或计算实例默认使用 Ubuntu 18.04 LTS 作为主机 OS。 无法选择其他主机 OS。

## <a name="upgrade-existing-resources"></a>升级现有资源

如果你在 2021 年 3 月 15 日之前创建了现有计算群集或计算实例，则需要采取措施将主机 OS 升级到 Ubuntu 18.04。 建议你在 2021 年 4 月 9 日之后执行这些操作，以确保我们的更改已推出到所有区域（具体取决于你从中访问 Azure 机器学习的区域）：

* Azure 机器学习计算群集：

    * 如果将群集配置为最少节点数 = 0，则在完成所有作业后，群集会自动升级并缩减为零个节点。
    * 如果最少节点数 > 0，请暂时将最少节点数更改为零，允许群集缩减为零个节点。

    若要详细了解如何更改最少节点数，请查看 [az ml computetarget update amlcompute](/cli/azure/ml(v1)/computetarget/update#az_ml_computetarget_update_amlcompute) Azure CLI 命令或 [AmlCompute.update()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK 参考。

* Azure 机器学习计算实例：创建一个新的计算实例（将使用 Ubuntu 18.04）并删除旧实例。

    * 可以从新的计算实例访问存储在工作区文件共享、数据存储或数据集中的任何笔记本。
    * 如果你已创建了自定义 conda 环境，则可以从现有实例中导出这些环境，再将其导入到新实例中。 有关 conda 导出和导入的信息，请参阅 docs.conda.io 上的 [conda 文档](https://docs.conda.io/)。

    有关详细信息，请参阅这两篇文章：[什么是计算实例](concept-compute-instance.md)和[创建和管理 Azure 机器学习计算实例](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>检查主机 OS 版本

若要了解如何检查主机 OS 版本，请参阅有关如何[检查 Ubuntu 版本](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)的 Ubuntu 社区 wiki 页。

> [!TIP]
> 若要使用 wiki 中提供的 `lsb_release -a` 命令，可以[在计算实例上使用终端会话](how-to-access-terminal.md)。
## <a name="next-steps"></a>后续步骤

如有任何其他问题或疑问，请通过 [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) 联系我们。
