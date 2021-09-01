---
title: az sql mi-arc dag 参考
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc dag 命令的参考文章。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 4ebd6e71c14c9a319e99e68eca15f19a21d52033
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779768"
---
# <a name="az-sql-mi-arc-dag"></a>az sql mi-arc dag
## <a name="commands"></a>命令
| Command | 说明|
| --- | --- |
[创建 az sql mi-arc dag](#az-sql-mi-arc-dag-create) | 创建分布式可用性组自定义资源
[删除 az sql mi-arc dag](#az-sql-mi-arc-dag-delete) | 删除 sqlmi 实例上的分布式可用性组自定义资源。
[显示 az sql mi-arc dag](#az-sql-mi-arc-dag-show) | 显示分布式可用性组自定义资源。
## <a name="az-sql-mi-arc-dag-create"></a>创建 az sql mi-arc dag
创建分布式可用性组自定义资源，以创建分布式可用性组
```bash
az sql mi-arc dag create --name -n 
                         --dag-name -d  
                         
--local-instance-name -l  
                         
--local-primary -p  
                         
--remote-instance-name -r  
                         
--remote-mirroring-url -u  
                         
--remote-mirroring-cert-file -f  
                         
[--k8s-namespace -k]  
                         
[--path]  
                         
[--use-k8s]
```
### <a name="examples"></a>示例
例 1：创建分布式可用性组自定义资源 dagCr1，以在本地 sqlmi 实例 sqlmi1 和远程 sqlmi 实例 sqlmi2 之间创建分布式可用性组 dagName1。 其需要远程 sqlmi 主镜像 remotePrimary:5022 和远程 sqlmi 镜像终结点证书文件 ./sqlmi2.cer。
```bash
az sql mi-arc dag create --name=dagCr1 --dag-name=dagName1  --local-instance-name=sqlmi1 --local-primary=true --remote-instance-name=sqlmi2  --remote-mirroring-url==remotePrimary:5022 --remote-mirroing-cert-file="./sqlmi2.cer"
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
分布式可用性组资源的名称。
#### `--dag-name -d`
此 SQL 托管实例的分布式可用性组的名称。 本地和远程都必须使用同一名称。
#### `--local-instance-name -l`
本地 SQL 托管实例的名称
#### `--local-primary -p`
True 表示本地 SQL 托管实例为异地主实例。 False 表示本地 SQL 托管实例为异地辅助实例
#### `--remote-instance-name -r`
远程 SQL 托管实例或远程 SQL 可用性组的名称
#### `--remote-mirroring-url -u`
远程 SQL 托管实例或远程 SQL 可用性组的镜像终结点 URL
#### `--remote-mirroring-cert-file -f`
远程 SQL 托管实例或远程 SQL 可用性组的镜像终结点公共证书的文件名。 仅支持 PEM 格式
### <a name="optional-parameters"></a>可选参数
#### `--k8s-namespace -k`
SQL 托管实例所在的命名空间。 若未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
#### `--path`
自定义资源规范的路径，即 custom/spec.json
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
## <a name="az-sql-mi-arc-dag-delete"></a>删除 az sql mi-arc dag
删除 sqlmi 实例上的分布式可用性组自定义资源，以删除分布式可用性组。 其需要自定义资源名称
```bash
az sql mi-arc dag delete --name 
                         [--k8s-namespace -k]  
                         
[--use-k8s]
```
### <a name="examples"></a>示例
例 1：删除名为 dagCr1 的分布式可用性组资源。
```bash
az sql mi-arc dag delete --name=dagCr1
```
### <a name="required-parameters"></a>必需参数
#### `--name`
分布式可用性组资源的名称。
### <a name="optional-parameters"></a>可选参数
#### `--k8s-namespace -k`
SQL 托管实例所在的命名空间。 若未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
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
## <a name="az-sql-mi-arc-dag-show"></a>显示 az sql mi-arc dag
显示分布式可用性组自定义资源。 其需要自定义资源名称
```bash
az sql mi-arc dag show --name 
                       [--k8s-namespace -k]  
                       
[--use-k8s]
```
### <a name="examples"></a>示例
例 1：显示名为 dagCr1 的分布式可用性组资源。
```bash
az sql mi-arc dag show --name=dagCr1
```
### <a name="required-parameters"></a>必需参数
#### `--name`
分布式可用性组资源的名称。
### <a name="optional-parameters"></a>可选参数
#### `--k8s-namespace -k`
SQL 托管实例所在的命名空间。 若未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
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
