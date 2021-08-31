---
title: 创建自定义配置模板
description: 创建自定义配置模板
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dinethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e7d5d470fd967c9f350fddaae2032085caf17c07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737188"
---
# <a name="create-custom-configuration-templates"></a>创建自定义配置模板

本文介绍如何为已启用 Azure Arc 的数据控制器创建自定义配置模板。 

在间接连接模式下，在数据控制器部署过程中所需的参数之一是 `az arcdata dc create --profile-name` 参数。 目前，可以通过运行查询找到内置配置文件的可用列表：

```azurecli
az arcdata dc config list
```
这些配置文件是具有已启用 Azure Arc 的数据控制器的各种设置的模板 JSON 文件，如 docker 注册表和存储库设置、数据和日志的存储类、数据和日志的存储大小、安全性、服务类型等，并可根据环境进行自定义。 

但是，在某些情况下，你可能需要自定义这些配置模板来满足你的需求，并使用 `--path` 参数将自定义配置模板传递到 `az arcdata dc create` 命令，而不是使用 `--profile-name` 参数传递预配置的配置模板。

## <a name="create-customjson-file"></a>创建 custom.json 文件

运行 `az arcdata dc config init` 以根据 Kubernetes 群集的分发情况，使用预定义的设置启动 control.json 文件。
例如，可以按如下所示创建模板 control.json 文件，该文件适用于基于当前工作目录中名为 `custom` 的子目录中的 `azure-arc-kubeadm` 模板的 Kubernetes 群集：

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path custom
```
可在任何编辑器（如 Visual Studio Code）中编辑创建的 control.json 文件，以自定义适用于你环境的设置。

## <a name="use-custom-controljson-file-to-deploy-azure-arc-enabled-data-controller-using-azure-cli-az"></a>使用自定义 control.json 文件通过 Azure CLI (az) 来部署已启用 Azure Arc 的数据控制器

创建模板文件后，可在已启用 Azure Arc 的数据控制器创建命令期间应用该文件，如下所示：

```azurecli
az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect  --k8s-namespace <namespace> --use-k8s

#Example:
#az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription ID> --resource-group my-resource-group --location eastus --connectivity-mode indirect --k8s-namespace <namespace> --use-k8s
```

## <a name="use-custom-controljson-file-for-deploying-azure-arc-data-controller-using-azure-portal"></a>使用自定义 control.json 文件通过 Azure 门户部署 Azure Arc 数据控制器

从“Azure Arc 数据控制器创建”屏幕中，选择“自定义模板”下的“配置自定义模板”。 这将调用一个边栏选项卡来提供自定义设置。 在此边栏选项卡中，可以键入各种设置值，或直接上传预先配置的 control.json 文件。 

确保值正确后，单击“应用”以继续进行 Azure Arc 数据控制器部署。

## <a name="next-steps"></a>后续步骤

[部署数据控制器 - 直接连接模式（先决条件）](create-data-controller-direct-prerequisites.md)

[创建 Azure Arc 数据控制器 (CLI)](create-data-controller-direct-cli.md)
