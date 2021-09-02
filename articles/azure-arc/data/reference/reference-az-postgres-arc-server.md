---
title: az postgres arc-server 参考资料
titleSuffix: Azure Arc-enabled data services
description: az postgres arc-server 命令的参考文章。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: c58819707eb5987bddeb0d8983c29e7519b69818
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778436"
---
# <a name="az-postgres-arc-server"></a>az postgres arc-server
## <a name="commands"></a>命令
| Command | 说明|
| --- | --- |
[az postgres arc-server create](#az-postgres-arc-server-create) | 创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。
[az postgres arc-server edit](#az-postgres-arc-server-edit) | 编辑已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的配置。
[az postgres arc-server delete](#az-postgres-arc-server-delete) | 删除已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。
[az postgres arc-server show](#az-postgres-arc-server-show) | 显示和已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的详细信息。
[az postgres arc-server list](#az-postgres-arc-server-list) | 列出已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。
[az postgres arc-server endpoint](reference-az-postgres-arc-server-endpoint.md) | 管理已启用 Azure Arc 的超大规模 PostgreSQL 服务器组终结点。
## <a name="az-postgres-arc-server-create"></a>az postgres arc-server create
若要设置服务器组的密码，请设置环境变量 AZDATA_PASSWORD
```bash
az postgres arc-server create --name -n 
                              [--path]  
                              
[--k8s-namespace -k]  
                              
[--cores-limit]  
                              
[--cores-request]  
                              
[--memory-limit]  
                              
[--memory-request]  
                              
[--storage-class-data]  
                              
[--storage-class-logs]  
                              
[--storage-class-backups]  
                              
[--volume-claim-mounts]  
                              
[--extensions]  
                              
[--volume-size-data]  
                              
[--volume-size-logs]  
                              
[--volume-size-backups]  
                              
[--workers -w]  
                              
[--engine-version]  
                              
[--no-external-endpoint]  
                              
[--port]  
                              
[--no-wait]  
                              
[--engine-settings]  
                              
[--coordinator-settings]  
                              
[--worker-settings]  
                              
[--use-k8s]
```
### <a name="examples"></a>示例
创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。
```bash
az postgres arc-server create -n pg1 --k8s-namespace namespace --use-k8s
```
创建具有引擎设置的已启用 Azure Arc 的 PostgreSQL 超大规模服务器组。 下面的两个示例都有效。
```bash
az postgres arc-server create -n pg1 --engine-settings "key1=val1" --k8s-namespace namespace 
az postgres arc-server create -n pg1 --engine-settings "key2=val2" --k8s-namespace namespace --use-k8s
```
创建具有批量声明装载的 PostgreSQL 服务器组。
```bash
az postgres arc-server create -n pg1 --volume-claim-mounts backup-pvc:backup 
```
为不同节点角色创建具有特定内存限制的 PostgreSQL 服务器组。
```bash
az postgres arc-server create -n pg1 --memory-limit "coordinator=2Gi,w=1Gi" --workers 1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的名称。
### <a name="optional-parameters"></a>可选参数
#### `--path`
已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的源 json 文件的路径。 此为可选项。
#### `--k8s-namespace -k`
部署已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的 Kubernetes 命名空间。 如果未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
#### `--cores-limit`
每个节点可用的已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的最大 CPU 核心数。 支持小数核心数。 （可选）可以按 <role>= 格式指定包含值的以逗号分隔的角色列表<value>. 有效角色为：“协调器”（“c”）、“工作器”（“w”）。 如果未指定角色，则设置将应用于 PostgreSQL 超大规模服务器组的所有节点。
#### `--cores-request`
每个节点计划服务时可使用的最小 CPU 核心数。 支持小数核心数。 （可选）可以按 <role>= 格式指定包含值的以逗号分隔的角色列表<value>. 有效角色为：“协调器”（“c”）、“工作器”（“w”）。 如果未指定角色，则设置将应用于 PostgreSQL 超大规模服务器组的所有节点。
#### `--memory-limit`
已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的内存限制，表示为数字后跟 Ki（千字节）、Mi（兆字节）或 Gi（千兆字节）。 （可选）可以按 <role>= 格式指定包含值的以逗号分隔的角色列表<value>. 有效角色为：“协调器”（“c”）、“工作器”（“w”）。 如果未指定角色，则设置将应用于 PostgreSQL 超大规模服务器组的所有节点。
#### `--memory-request`
已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的内存请求，表示为数字后跟 Ki（千字节）、Mi（兆字节）或 Gi（千兆字节）。 （可选）可以按 <role>= 格式指定包含值的以逗号分隔的角色列表<value>. 有效角色为：“协调器”（“c”）、“工作器”（“w”）。 如果未指定角色，则设置将应用于 PostgreSQL 超大规模服务器组的所有节点。
#### `--storage-class-data`
要用于数据永久性卷的存储类。
#### `--storage-class-logs`
要用于日志永久性卷的存储类。
#### `--storage-class-backups`
要用于备份永久性卷的存储类。
#### `--volume-claim-mounts`
以逗号分隔的卷声明装载列表。 卷声明装载是（相同命名空间中）的一对现有永久性卷声明和卷类型（和可选元数据，具体取决于卷类型），用冒号分隔。将在 PostgreSQL 服务器组的每个 pod 中装入永久性卷。 装载路径可能取决于卷类型。
#### `--extensions`
应在启动时加载的 Postgres 扩展的逗号分隔列表。 请参阅 postgres 文档了解支持的值。
#### `--volume-size-data`
要用于数据的存储卷的大小（正数），后跟 Ki（千字节）、Mi（兆字节）或 Gi（吉字节）。
#### `--volume-size-logs`
要用于日志的存储卷的大小（正数），后跟 Ki（千字节）、Mi（兆字节）或 Gi（吉字节）。
#### `--volume-size-backups`
要用于备份的存储卷的大小（正数），后跟 Ki（千字节）、Mi（兆字节）或 Gi（吉字节）。
#### `--workers -w`
要在服务器组中预配的工作器节点数。 在预览版中，不支持减少工作器节点数。 参阅文档以了解更多详细信息。
#### `--engine-version`
必须为 11 或 12。 默认值为 12。
`12`
#### `--no-external-endpoint`
如果已指定，则不会创建外部服务。 否则，将使用与数据控制器相同的服务类型来创建外部服务。
#### `--port`
可选。
#### `--no-wait`
如果给定，该命令不会等待实例处于就绪状态再返回。
#### `--engine-settings`
以逗号分隔的 Postgres 引擎设置列表，格式为“key1=val1, key2=val2”。
#### `--coordinator-settings`
要应用于“协调器”节点角色的以逗号分隔的 Postgres 引擎设置列表，格式为“key1=val1, key2=val2”。 指定特定于节点角色的设置时，默认设置将被忽略，并替换为此处提供的设置。
#### `--worker-settings`
要应用于“工作器”节点角色的以逗号分隔的 Postgres 引擎设置列表，格式为“key1=val1, key2=val2”。 指定特定于节点角色的设置时，默认设置将被忽略，并替换为此处提供的设置。
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
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
编辑已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的配置。
```bash
az postgres arc-server edit --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--workers -w]  
                            
[--cores-limit]  
                            
[--cores-request]  
                            
[--memory-limit]  
                            
[--memory-request]  
                            
[--extensions]  
                            
[--port]  
                            
[--no-wait]  
                            
[--engine-settings]  
                            
[--replace-settings]  
                            
[--coordinator-settings]  
                            
[--worker-settings]  
                            
[--admin-password]  
                            
[--use-k8s]
```
### <a name="examples"></a>示例
编辑已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的配置。
```bash
az postgres arc-server edit --path ./spec.json -n pg1 --k8s-namespace namespace --use-k8s
```
编辑具有协调器节点引擎设置的已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。
```bash
az postgres arc-server edit -n pg1 --coordinator-settings "key2=val2" --k8s-namespace namespace
```
编辑已启用 Azure Arc 的超大规模 PostgreSQL 服务器组，并使用新设置 key1=val1 替换现有引擎设置。
```bash
az postgres arc-server edit -n pg1 --engine-settings "key1=val1" --replace-settings --k8s-namespace namespace
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
要编辑的已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的名称。 不能更改部署实例时为其指定的名称。
### <a name="optional-parameters"></a>可选参数
#### `--k8s-namespace -k`
部署已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的 Kubernetes 命名空间。 如果未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
#### `--path`
已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的源 json 文件的路径。 此为可选项。
#### `--workers -w`
要在服务器组中预配的工作器节点数。 在预览版中，不支持减少工作器节点数。 参阅文档以了解更多详细信息。
#### `--cores-limit`
每个节点可用的已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的最大 CPU 核心数，支持小数核心数。 若要删除 cores_limit，请将其值指定为空字符串。 （可选）可以按 <role>= 格式指定包含值的以逗号分隔的角色列表<value>. 有效角色为：“协调器”（“c”）、“工作器”（“w”）。 如果未指定角色，则设置将应用于 PostgreSQL 超大规模服务器组的所有节点。
#### `--cores-request`
每个节点计划服务时可使用的最小 CPU 核心数，支持小数核心数。 若要删除 cores_request，请将其值指定为空字符串。 （可选）可以按 <role>= 格式指定包含值的以逗号分隔的角色列表<value>. 有效角色为：“协调器”（“c”）、“工作器”（“w”）。 如果未指定角色，则设置将应用于 PostgreSQL 超大规模服务器组的所有节点。
#### `--memory-limit`
已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的内存限制，表示为数字后跟 Ki（千字节）、Mi（兆字节）或 Gi（千兆字节）。 若要删除 memory_limit，请将其值指定为空字符串。 （可选）可以按 <role>= 格式指定包含值的以逗号分隔的角色列表<value>. 有效角色为：“协调器”（“c”）、“工作器”（“w”）。 如果未指定角色，则设置将应用于 PostgreSQL 超大规模服务器组的所有节点。
#### `--memory-request`
已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的内存请求，表示为数字后跟 Ki（千字节）、Mi（兆字节）或 Gi（千兆字节）。 若要删除 memory_request，请将其值指定为空字符串。 （可选）可以按 <role>= 格式指定包含值的以逗号分隔的角色列表<value>. 有效角色为：“协调器”（“c”）、“工作器”（“w”）。 如果未指定角色，则设置将应用于 PostgreSQL 超大规模服务器组的所有节点。
#### `--extensions`
应在启动时加载的 Postgres 扩展的逗号分隔列表。 请参阅 postgres 文档了解支持的值。
#### `--port`
可选。
#### `--no-wait`
如果给定，该命令不会等待实例处于就绪状态再返回。
#### `--engine-settings`
以逗号分隔的 Postgres 引擎设置列表，格式为“key1=val1, key2=val2”。 提供的设置将与现有设置合并。 若要删除某个设置，请提供一个空值，如“removedKey=”。 如果更改了需要重启的引擎设置，则将重启服务以立即应用该设置。
#### `--replace-settings`
指定为 --engine-settings 后，会将所有现有自定义引擎设置替换为一组新的设置和值。
#### `--coordinator-settings`
要应用于“协调器”节点角色的以逗号分隔的 Postgres 引擎设置列表，格式为“key1=val1, key2=val2”。 指定特定于节点角色的设置时，默认设置将被忽略，并替换为此处提供的设置。
#### `--worker-settings`
要应用于“工作器”节点角色的以逗号分隔的 Postgres 引擎设置列表，格式为“key1=val1, key2=val2”。 指定特定于节点角色的设置时，默认设置将被忽略，并替换为此处提供的设置。
#### `--admin-password`
如果指定，已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的管理员密码将设置为 AZDATA_PASSWORD 环境变量的值（如果存在），否则为提示的值。
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
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
删除已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。
```bash
az postgres arc-server delete --name -n 
                              [--k8s-namespace -k]  
                              
[--force -f]  
                              
[--use-k8s]
```
### <a name="examples"></a>示例
删除已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的名称。
### <a name="optional-parameters"></a>可选参数
#### `--k8s-namespace -k`
部署已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的 Kubernetes 命名空间。 如果未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
#### `--force -f`
强制删除已启用 Azure Arc 的超大规模 PostgreSQL 服务器组而不进行确认。
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
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
显示和已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的详细信息。
```bash
az postgres arc-server show --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--use-k8s]
```
### <a name="examples"></a>示例
显示和已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的详细信息。
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的名称。
### <a name="optional-parameters"></a>可选参数
#### `--k8s-namespace -k`
部署已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的 Kubernetes 命名空间。 如果未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
#### `--path`
应将已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的完整规范写入到的路径。 如果省略，则会将规范写入标准输出。
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
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
列出已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。
```bash
az postgres arc-server list [--k8s-namespace -k] 
                            [--use-k8s]
```
### <a name="examples"></a>示例
列出已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
```
### <a name="optional-parameters"></a>可选参数
#### `--k8s-namespace -k`
部署已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的 Kubernetes 命名空间。 如果未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
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
