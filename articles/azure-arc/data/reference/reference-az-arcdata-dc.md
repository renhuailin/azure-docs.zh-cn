---
title: az arcdata dc 参考
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc 命令的参考文章。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f5b804ca5bf0c38165d848f75de7a3cd428fb2f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778547"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>命令
| Command | 说明|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | 创建数据控制器。
[az arcdata dc delete](#az-arcdata-dc-delete) | 删除数据控制器。
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | 终结点命令。
[az arcdata dc status](reference-az-arcdata-dc-status.md) | 状态命令。
[az arcdata dc config](reference-az-arcdata-dc-config.md) | 配置命令。
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | 调试数据控制器。
[az arcdata dc export](#az-arcdata-dc-export) | 导出指标、日志或使用情况。
[az arcdata dc upload](#az-arcdata-dc-upload) | 上传导出的数据文件。
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
创建数据控制器 - 系统中需要 kube 配置以及以下环境变量 ['AZDATA_USERNAME', 'AZDATA_PASSWORD']。
```bash
az arcdata dc create --k8s-namespace -k 
                     --name -n  
                     
--connectivity-mode  
                     
--resource-group -g  
                     
--location -l  
                     
[--profile-name]  
                     
[--path -p]  
                     
[--storage-class]  
                     
[--infrastructure]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-annotations]  
                     
[--service-labels]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]
```
### <a name="examples"></a>示例
部署数据控制器。
```bash
az arcdata dc create --name name --k8s-namespace namespace  --connectivity-mode indirect --resource-group group  --location location, --subscription subscription
```
### <a name="required-parameters"></a>必需参数
#### `--k8s-namespace -k`
数据控制器要部署到的 Kubernetes 命名空间。 如果此命名空间已存在，则将使用它。 如果此命名空间不存在，则将尝试先创建它。
#### `--name -n`
数据控制器的名称。
#### `--connectivity-mode`
与操作数据控制器的 Azure 建立间接或直接的连接。
#### `--resource-group -g`
应在其中添加数据控制器资源的 Azure 资源组。
#### `--location -l`
将在其中存储数据控制器元数据的 Azure 位置（例如美国东部）。
### <a name="optional-parameters"></a>可选参数
#### `--profile-name`
现有配置文件的名称。 有关可用选项，请运行 `az arcdata dc config list`。 以下各项之一：['azure-arc-gke'、'azure-arc-eks'、'azure-arc-kubeadm'、'azure-arc-aks-default-storage'、'azure-arc-azure-openshift'、'azure-arc-ake'、'azure-arc-openshift'、'azure-arc-aks-hci'、'azure-arc-aks-premium-storage']。
#### `--path -p`
包含要使用的自定义配置文件的目录的路径。 若要创建自定义配置文件，请运行 `az arcdata dc config init`。
#### `--storage-class`
用于所有数据的存储类，以及需要它们的所有数据控制器 pod 的日志永久性卷。
#### `--infrastructure`
数据控制器将在其上运行的基础结构。 允许的值：['aws'、'gcp'、'azure'、'alibaba'、'onpremises'、'other'、'auto']
#### `--labels`
要应用于所有数据控制器资源的标签的逗号分隔列表。
#### `--annotations`
要应用于所有数据控制器资源的注释的逗号分隔列表。
#### `--service-annotations`
要应用于所有外部数据控制器服务的注释的逗号分隔列表。
#### `--service-labels`
要应用于所有外部数据控制器服务的标签的逗号分隔列表。
#### `--storage-labels`
要应用于数据控制器创建的所有 PVC 的标签的逗号分隔列表。
#### `--storage-annotations`
要应用于数据控制器创建的所有 PVC 的注释的逗号分隔列表。
#### `--use-k8s`
使用本地 Kubernetes API 创建数据控制器。
### <a name="global-arguments"></a>全局参数
#### `--debug`
提高日志记录详细程度以显示所有调试日志。
#### `--help -h`
显示此帮助消息并退出。
#### `--output -o`
输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。  默认值：json。
#### `--query -q`
JMESPath 查询字符串。 请参阅 [http://jmespath.org](http://jmespath.org)，获取详细信息和示例。
#### `--subscription`
订阅的名称或 ID。 可使用 `az account set -s NAME_OR_ID` 配置默认订阅。
#### `--verbose`
提高日志记录详细程度。 使用 --debug 获取完整的调试日志。
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
删除数据控制器 - 系统上需要 kube 配置。
```bash
az arcdata dc delete --name -n 
                     --k8s-namespace -k  
                     
[--force -f]  
                     
[--yes -y]
```
### <a name="examples"></a>示例
部署数据控制器。
```bash
az arcdata dc delete --name name --k8s-namespace namespace
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
数据控制器名称。
#### `--k8s-namespace -k`
数据控制器所在的 Kubernetes 命名空间。
### <a name="optional-parameters"></a>可选参数
#### `--force -f`
强制删除数据控制器及其所有数据服务。
#### `--yes -y`
在无确认提示的情况下删除数据控制器。
### <a name="global-arguments"></a>全局参数
#### `--debug`
提高日志记录详细程度以显示所有调试日志。
#### `--help -h`
显示此帮助消息并退出。
#### `--output -o`
输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。  默认值：json。
#### `--query -q`
JMESPath 查询字符串。 请参阅 [http://jmespath.org](http://jmespath.org)，获取详细信息和示例。
#### `--subscription`
订阅的名称或 ID。 可使用 `az account set -s NAME_OR_ID` 配置默认订阅。
#### `--verbose`
提高日志记录详细程度。 使用 --debug 获取完整的调试日志。
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
将指标、日志或使用情况导出到文件。
```bash
az arcdata dc export --type -t 
                     --path -p  
                     
--k8s-namespace -k  
                     
[--force -f]  
                     
[--use-k8s]
```
### <a name="required-parameters"></a>必需参数
#### `--type -t`
要导出的数据的类型。 选项：日志、指标和使用情况。
#### `--path -p`
完整路径或相对路径，包括要导出的文件的文件名。
#### `--k8s-namespace -k`
数据控制器所在的 Kubernetes 命名空间。
### <a name="optional-parameters"></a>可选参数
#### `--force -f`
强制创建输出文件。 覆盖同一路径中的任何现有文件。
#### `--use-k8s`
使用本地 Kubernetes API 来执行此操作。
### <a name="global-arguments"></a>全局参数
#### `--debug`
提高日志记录详细程度以显示所有调试日志。
#### `--help -h`
显示此帮助消息并退出。
#### `--output -o`
输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。  默认值：json。
#### `--query -q`
JMESPath 查询字符串。 请参阅 [http://jmespath.org](http://jmespath.org)，获取详细信息和示例。
#### `--subscription`
订阅的名称或 ID。 可使用 `az account set -s NAME_OR_ID` 配置默认订阅。
#### `--verbose`
提高日志记录详细程度。 使用 --debug 获取完整的调试日志。
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
将从数据控制器导出的数据文件上传到 Azure。
```bash
az arcdata dc upload --path -p 
                     
```
### <a name="required-parameters"></a>必需参数
#### `--path -p`
完整路径或相对路径，包括要上传的文件的文件名。
### <a name="global-arguments"></a>全局参数
#### `--debug`
提高日志记录详细程度以显示所有调试日志。
#### `--help -h`
显示此帮助消息并退出。
#### `--output -o`
输出格式。  允许的值：json、jsonc、none、table、tsv、yaml、yamlc。  默认值：json。
#### `--query -q`
JMESPath 查询字符串。 请参阅 [http://jmespath.org](http://jmespath.org)，获取详细信息和示例。
#### `--subscription`
订阅的名称或 ID。 可使用 `az account set -s NAME_OR_ID` 配置默认订阅。
#### `--verbose`
提高日志记录详细程度。 使用 --debug 获取完整的调试日志。
