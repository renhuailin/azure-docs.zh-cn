---
title: CLI (v2) YAML 架构概述
titleSuffix: Azure Machine Learning
description: CLI (v2) YAML 架构的概述和索引。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 4d98ebbebbb25e2ab903e06162114f8fa026fbda
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779742"
---
# <a name="cli-v2-yaml-schemas"></a>CLI (v2) YAML 架构

Azure 机器学习 CLI (v2) 是 Azure CLI 的扩展，经常使用，有时需要具有特定架构的 YAML 文件。 本文列出了 YAML 文件的参考文档和源架构。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="assets"></a>资产

参考 | URI
- | -
[工作区](reference-yaml-workspace.md) | https://azuremlschemas.azureedge.net/latest/workspace.schema.json
[计算](reference-yaml-compute.md) | https://azuremlschemas.azureedge.net/latest/compute.schema.json
[环境](reference-yaml-environment.md) | https://azuremlschemas.azureedge.net/latest/environment.schema.json
[数据集](reference-yaml-dataset.md) | https://azuremlschemas.azureedge.net/latest/dataset.schema.json
[Model](reference-yaml-model.md) | https://azuremlschemas.azureedge.net/latest/model.schema.json

## <a name="datastores"></a>数据存储

参考 | URI
- | -
[Azure Blob](reference-yaml-datastore-blob.md) | https://azuremlschemas.azureedge.net/latest/azureBlob.schema.json
[Azure 文件](reference-yaml-datastore-files.md) | https://azuremlschemas.azureedge.net/latest/azureFile.schema.json
[Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen1.schema.json
[Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md) | https://azuremlschemas.azureedge.net/latest/azureDataLakeGen2.schema.json

## <a name="jobs"></a>作业

参考 | URI
- | -
[命令](reference-yaml-job-command.md) | https://azuremlschemas.azureedge.net/latest/commandJob.schema.json
[Sweep](reference-yaml-job-sweep.md) | https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json

## <a name="endpoints"></a>终结点

参考 | URI
- | -
[托管联机（实时）](reference-yaml-endpoint-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.schema.json
[托管批处理](reference-yaml-endpoint-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchEndpoint.schema.json
[Kubernetes (k8s) 联机（实时）](reference-yaml-endpoint-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnlineEndpoint.schema.json

## <a name="deployments"></a>部署

参考 | URI
- | -
[托管联机（实时）](reference-yaml-deployment-managed-online.md) | https://azuremlschemas.azureedge.net/latest/managedOnlineDeployment.schema.json
[托管批处理](reference-yaml-deployment-managed-batch.md) | https://azuremlschemas.azureedge.net/latest/batchDeployment.schema.json
[Kubernetes (k8s) 联机（实时）](reference-yaml-deployment-k8s-online.md) | https://azuremlschemas.azureedge.net/latest/k8sOnelineDeployment.schema.json

## <a name="next-steps"></a>后续步骤

- [安装并使用 CLI (v2)](how-to-configure-cli.md)
