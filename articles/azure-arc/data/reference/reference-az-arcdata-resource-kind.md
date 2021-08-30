---
title: az arcdata resource-kind reference
titleSuffix: Azure Arc-enabled data services
description: az arcdata resource-kind 命令的参考文章。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 597bc660140ec2f0ac856a3f6c67db600480aa63
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778732"
---
# <a name="az-arcdata-resource-kind"></a>az arcdata resource-kind
## <a name="commands"></a>命令
| Command | 说明|
| --- | --- |
[az arcdata resource-kind list](#az-arcdata-resource-kind-list) | 列出可定义和创建的 Arc 可用自定义资源类型。
[az arcdata resource-kind get](#az-arcdata-resource-kind-get) | 获取 Arc 资源类型的模板文件。
## <a name="az-arcdata-resource-kind-list"></a>az arcdata resource-kind list
列出可定义和创建的 Arc 可用自定义资源类型。 列出后，可继续获取定义或创建该自定义资源时所需的模板文件。
```bash
az arcdata resource-kind list 
```
### <a name="examples"></a>示例
用于列出 Arc 的可用自定义资源类型的示例命令。
```bash
az arcdata resource-kind list
```
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
## <a name="az-arcdata-resource-kind-get"></a>az arcdata resource-kind get
获取 Arc 资源类型的模板文件。
```bash
az arcdata resource-kind get --kind -k 
                             [--dest -d]
```
### <a name="examples"></a>示例
用于获取 Arc 资源类型的 CRD 模板文件的示例命令。
```bash
az arcdata resource-kind get --kind SqlManagedInstance
```
### <a name="required-parameters"></a>必需参数
#### `--kind -k`
模板文件所需的 Arc 资源的类型。
### <a name="optional-parameters"></a>可选参数
#### `--dest -d`
在其中放置模板文件的目录。
`template`
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
