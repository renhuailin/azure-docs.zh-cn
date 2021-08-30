---
title: az sql mi-arc endpoint 参考
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc endpoint 命令的参考文章。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: ca3685c6769e45d43b513ea11355795af332a849
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778435"
---
# <a name="az-sql-mi-arc-endpoint"></a>az sql mi-arc endpoint
## <a name="commands"></a>命令
| Command | 说明|
| --- | --- |
[az sql mi-arc endpoint list](#az-sql-mi-arc-endpoint-list) | 列出 SQL 终结点。
## <a name="az-sql-mi-arc-endpoint-list"></a>az sql mi-arc endpoint list
列出 SQL 终结点。
```bash
az sql mi-arc endpoint list [--name -n] 
                            [--k8s-namespace -k]  
                            
[--use-k8s]
```
### <a name="examples"></a>示例
列出 SQL 托管实例的终结点。
```bash
az sql mi-arc endpoint list -n sqlmi1
```
### <a name="optional-parameters"></a>可选参数
#### `--name -n`
要显示的 SQL 实例的名称。 如果省略，则显示所有实例的所有终结点。
#### `--k8s-namespace -k`
SQL 托管实例所在的命名空间。 如果未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
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
