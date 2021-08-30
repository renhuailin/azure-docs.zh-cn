---
title: az arcdata dc status 参考
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc status 命令的参考文章。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 9e795c71a5316960bfc7f0b1138afce3ecc4b957
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778060"
---
# <a name="az-arcdata-dc-status"></a>az arcdata dc status
## <a name="commands"></a>命令
| Command | 说明|
| --- | --- |
[az arcdata dc status show](#az-arcdata-dc-status-show) | 显示数据控制器的状态。
## <a name="az-arcdata-dc-status-show"></a>az arcdata dc status show
显示数据控制器的状态。
```bash
az arcdata dc status show [--k8s-namespace -k] 
                          [--use-k8s]
```
### <a name="examples"></a>示例
显示特定 kubernetes 命名空间中数据控制器的状态。
```bash
az arcdata dc status show --k8s-namespace <ns>
```
### <a name="optional-parameters"></a>可选参数
#### `--k8s-namespace -k`
数据控制器所在的 Kubernetes 命名空间。
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
