---
title: az arcdata dc 调试引用
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc 调试命令的参考文章。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 219471cf793394c024061f39319a98cc89477c76
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778154"
---
# <a name="az-arcdata-dc-debug"></a>az arcdata dc 调试
## <a name="commands"></a>命令
| Command | 说明|
| --- | --- |
[az arcdata dc 调试复制日志](#az-arcdata-dc-debug-copy-logs) | 复制日志。
[az arcdata dc 调试转储](#az-arcdata-dc-debug-dump) | 触发内存转储。
## <a name="az-arcdata-dc-debug-copy-logs"></a>az arcdata dc 调试复制日志
从数据控制器复制调试日志 - 系统中需要 Kubernetes 配置。
```bash
az arcdata dc debug copy-logs --k8s-namespace -k 
                              [--container -c]  
                              
[--target-folder -d]  
                              
[--pod]  
                              
[--resource-kind]  
                              
[--resource-name]  
                              
[--timeout -t]  
                              
[--skip-compress]  
                              
[--exclude-dumps]  
                              
[--exclude-system-logs ]  
                              
[--use-k8s]
```
### <a name="required-parameters"></a>必需参数
#### `--k8s-namespace -k`
数据控制器的 Kubernetes 命名空间。
### <a name="optional-parameters"></a>可选参数
#### `--container -c`
复制具有相似名称的容器的日志（可选），默认情况下会复制所有容器的日志。 不能多次指定。 如果多次指定，则使用最后一个
#### `--target-folder -d`
要将日志复制到的目标文件夹路径。 （可选）默认情况下在本地文件夹中创建结果。  不能多次指定。 如果多次指定，则使用最后一个
#### `--pod`
复制具有相似名称的 Pod 的日志。 （可选）默认情况下会复制所有 Pod 的日志。 不能多次指定。 如果多次指定，则使用最后一个
#### `--resource-kind`
复制特定类型的资源的日志。 不能多次指定。 如果多次指定，则使用最后一个。 如果已指定，则还应指定--resource name 以标识该资源。
#### `--resource-name`
复制指定名称的资源的日志。 不能多次指定。 如果多次指定，则使用最后一个。 如果已指定，则还应指定 --resource-kind 以标识该资源。
#### `--timeout -t`
等待命令完成的秒数。 默认值为 0，表示无限制
#### `--skip-compress`
是否跳过对结果文件夹的压缩。 默认值为 False，即压缩结果文件夹。
#### `--exclude-dumps`
是否从结果文件夹中排除转储。 默认值为 False，即包含转储。
#### `--exclude-system-logs `
是否从集合中排除系统日志。 默认值为 False，即包含系统日志。
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
订阅的名称或 ID。 可以使用 `az account set -s NAME_OR_ID` 配置默认订阅。
#### `--verbose`
提高日志记录详细程度。 使用 --debug 获取完整的调试日志。
## <a name="az-arcdata-dc-debug-dump"></a>az arcdata dc 调试转储
触发内存转储，并从容器中复制 - 系统中需要 Kubernetes 配置。
```bash
az arcdata dc debug dump --k8s-namespace -k 
                         [--container -c]  
                         
[--target-folder -d]  
                         
[--use-k8s]
```
### <a name="required-parameters"></a>必需的参数
#### `--k8s-namespace -k`
数据控制器的 Kubernetes 命名空间。
### <a name="optional-parameters"></a>可选参数
#### `--container -c`
为了转储正在运行的进程而要触发的目标容器。
`controller`
#### `--target-folder -d`
要将转储复制出来的目标文件夹。`./output/dump`
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
订阅的名称或 ID。 可以使用 `az account set -s NAME_OR_ID` 配置默认订阅。
#### `--verbose`
提高日志记录详细程度。 使用 --debug 获取完整的调试日志。
