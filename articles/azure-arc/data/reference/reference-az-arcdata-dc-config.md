---
title: az arcdata dc config reference
titleSuffix: Azure Arc-enabled data services
description: az arcdata dc config 命令的参考文章。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: a7936f1bbe56b105ec65dfa76cb7346fa41953ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777923"
---
# <a name="az-arcdata-dc-config"></a>az arcdata dc config
## <a name="commands"></a>命令
| Command | 说明|
| --- | --- |
[az arcdata dc config init](#az-arcdata-dc-config-init) | 初始化可用于 `az arcdata dc create` 的数据控制器配置文件。
[az arcdata dc config list](#az-arcdata-dc-config-list) | 列出可用的配置文件选择。
[az arcdata dc config add](#az-arcdata-dc-config-add) | 在配置文件中为 json 路径添加值。
[az arcdata dc config remove](#az-arcdata-dc-config-remove) | 在配置文件中为 json 路径删除值。
[az arcdata dc config replace](#az-arcdata-dc-config-replace) | 在配置文件中为 json 路径替换值。
[az arcdata dc config patch](#az-arcdata-dc-config-patch) | 基于 json 修补程序文件来修补配置文件。
## <a name="az-arcdata-dc-config-init"></a>az arcdata dc config init
初始化可用于 `az arcdata dc create` 的数据控制器配置文件。 可以在参数中指定配置文件的特定源。
```bash
az arcdata dc config init [--path -p] 
                          [--source -s]  
                          
[--force -f]
```
### <a name="examples"></a>示例
引导式数据控制器配置初始化体验 - 你会收到所需值的提示。
```bash
az arcdata dc config init
```
带参数的 arcdata dc config，在 ./custom 中创建 aks-dev-test 配置文件。
```bash
az arcdata dc config init --source azure-arc-kubeadm --path custom
```
### <a name="optional-parameters"></a>可选参数
#### `--path -p`
用于放置配置文件的文件路径默认为 <cwd>/custom。
#### `--source -s`
配置文件源：['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-dev-test', 'azure-arc-aks-hci', 'azure-arc-kubeadm-dev-test', 'azure-arc-aks-premium-storage']
#### `--force -f`
强制覆盖目标文件。
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
## <a name="az-arcdata-dc-config-list"></a>az arcdata dc config list
列出用于在 `arcdata dc config init` 中使用的可用配置文件选择
```bash
az arcdata dc config list [--config-profile -c] 
                          
```
### <a name="examples"></a>示例
显示所有可用的配置文件名称。
```bash
az arcdata dc config list
```
显示特定配置文件的 json。
```bash
az arcdata dc config list --config-profile aks-dev-test
```
### <a name="optional-parameters"></a>可选参数
#### `--config-profile -c`
默认配置文件：['azure-arc-gke', 'azure-arc-eks', 'azure-arc-kubeadm', 'azure-arc-aks-default-storage', 'azure-arc-azure-openshift', 'azure-arc-ake', 'azure-arc-openshift', 'azure-arc-aks-dev-test', 'azure-arc-aks-hci', 'azure-arc-kubeadm-dev-test', 'azure-arc-aks-premium-storage']
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
## <a name="az-arcdata-dc-config-add"></a>az arcdata dc config add
将值添加到配置文件中的 json 路径。 下面的所有示例都以 Bash 形式提供。  如果使用其他命令行，可能需要适当地进行引用转义。  或者，可以使用修补程序文件功能。
```bash
az arcdata dc config add --path -p 
                         --json-values -j
```
### <a name="examples"></a>示例
添加数据控制器存储。
```bash
az arcdata dc config add --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>必需参数
#### `--path -p`
要设置的配置的数据控制器配置文件路径，即 custom/control.json
#### `--json-values -j`
值的 json 路径的键值对列表：key1.subkey1=value1,key2.subkey2=value2。 你可以提供内联 json 值，如 key='{"kind":"cluster","name":"test-cluster"}'，或 提供文件路径，如 key=./values.json。 Add 命令不支持条件语句。  如果提供的内联值本身是带有“=”和“,”的键值对，请对这些字符进行转义。  例如，key1="key2\=val2\,key3\=val3"。 有关路径的示例，请参阅 http://jsonpatch.com/ 。  如果要访问某个数组，须通过指示索引来实现，如 key. 0 = value
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
## <a name="az-arcdata-dc-config-remove"></a>az arcdata dc config remove
删除位于配置文件中 json 路径处的值。  下面的所有示例都以 Bash 形式提供。  如果使用其他命令行，可能需要适当地进行引用转义。  或者，可以使用修补程序文件功能。
```bash
az arcdata dc config remove --path -p 
                            --json-path -j
```
### <a name="examples"></a>示例
例 1 - 删除数据控制器存储。
```bash
az arcdata dc config remove --path custom/control.json --json-path ".spec.storage"
```
### <a name="required-parameters"></a>必需参数
#### `--path -p`
要设置的配置的数据控制器配置文件路径，即 custom/control.json
#### `--json-path -j`
基于 jsonpatch 库的 json 路径列表，该列表指示要删除的值，例如：key1.subkey1,key2.subkey2。 Remove 命令不支持条件语句。 有关路径的示例，请参阅 http://jsonpatch.com/ 。  如果要访问某个数组，须通过指示索引来实现，如 key. 0 = value
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
## <a name="az-arcdata-dc-config-replace"></a>az arcdata dc config replace
替换位于配置文件中 json 路径处的值。  下面的所有示例都以 Bash 形式提供。  如果使用其他命令行，可能需要适当地进行引用转义。  或者，可以使用修补程序文件功能。
```bash
az arcdata dc config replace --path -p 
                             --json-values -j
```
### <a name="examples"></a>示例
例 1 - 替换单个终结点（数据控制器终结点）的端口。
```bash
az arcdata dc config replace --path custom/control.json --json-values "$.spec.endpoints[?(@.name=="Controller")].port=30080"
```
例 2 - 替换数据控制器存储。
```bash
az arcdata dc config replace --path custom/control.json --json-values "spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}"
```
### <a name="required-parameters"></a>必需参数
#### `--path -p`
要设置的配置的数据控制器配置文件路径，即 custom/control.json
#### `--json-values -j`
值的 json 路径的键值对列表：key1.subkey1=value1,key2.subkey2=value2。 你可以提供内联 json 值，如 key='{"kind":"cluster","name":"test-cluster"}'，或 提供文件路径，如 key=./values.json。 Replace 命令通过 jsonpath 库支持条件语句。  若要使用条件语句，路径开头应使用 $。 这样可执行诸如 -j $.key1.key2[?(@.key3=="someValue"].key4=value 的条件语句。 如果提供的内联值本身是带有“=”和“,”的键值对，请对这些字符进行转义。  例如，key1="key2\=val2\,key3\=val3"。 可以参阅下面的示例。 有关更多帮助，请参阅： https://jsonpath.com/
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
## <a name="az-arcdata-dc-config-patch"></a>az arcdata dc config patch
根据给定的修补程序文件对配置文件进行修补。 请访问 http://jsonpatch.com/ 以更好地了解如何构建路径。 通过 jsonpath 库 https://jsonpath.com/ ，替换操作可在其路径中使用条件语句。 所有修补程序 json 文件必须以名为 "patch" 的键开头，该键包含一系列修补程序及其相应操作（添加、替换、删除）、路径和值。 “删除”操作不需要值，只需要一个路径。 请参阅下面的示例。
```bash
az arcdata dc config patch --path 
                           --patch-file -p
```
### <a name="examples"></a>示例
例 1 - 使用修补程序文件替换单个终结点（数据控制器终结点）的端口。
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=="Controller")].port","value":30080}]}
```
例 2 - 使用修补程序文件替换数据控制器存储。
```bash
az arcdata dc config patch --path custom/control.json --patch ./patch.json

    Patch File Example (patch.json):
        {"patch":[{"op":"replace","path":".spec.storage","value":{"accessMode":"ReadWriteMany","className":"managed-premium","size":"10Gi"}}]}
```
### <a name="required-parameters"></a>必需参数
#### `--path`
要设置的配置的数据控制器配置文件路径，即 custom/control.json
#### `--patch-file -p`
基于 jsonpatch 库的修补程序 json 文件的路径： http://jsonpatch.com/ 。 修补程序 json 文件必须以名为“patch”的键开头，其值是要执行的修补程序操作的数组。 对于修补操作的路径，可以使用点表示法，如用于大多数操作的 key1.key2。 如果要执行替换操作，并且要在需要条件语句的数组中替换值，请使用 jsonpath 表示法，以 $ 作为路径的开头。 这样可以执行诸如 $.key1.key2[?(@.key3=="someValue"].key4 的条件语句。 请参阅下面的示例。 如需有关条件语句的其他帮助，请参阅： https://jsonpath.com/ 。
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
