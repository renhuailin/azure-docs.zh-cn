---
title: az sql mi-arc reference
titleSuffix: Azure Arc-enabled data services
description: az sql mi-arc 命令的参考文章。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 81c8b2dbca45ac25db35fe15c2510b967bab1ae8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778309"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>命令
| Command | 说明|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | 查看和管理 SQL 终结点。
[az sql mi-arc create](#az-sql-mi-arc-create) | 创建 SQL 托管实例。
[az sql mi-arc edit](#az-sql-mi-arc-edit) | 编辑 SQL 托管实例的配置。
[az sql mi-arc delete](#az-sql-mi-arc-delete) | 删除 SQL 托管实例。
[az sql mi-arc show](#az-sql-mi-arc-show) | 显示 SQL 托管实例的详细信息。
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | 从 sql mi 检索可用性组镜像终结点的证书，并存储在文件中。
[az sql mi-arc list](#az-sql-mi-arc-list) | 列出 SQL 托管实例。
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | 配置命令。
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | 创建或删除分布式可用性组。
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
若要设置 SQL 托管实例的密码，请设置环境变量 AZDATA_PASSWORD
```bash
az sql mi-arc create --name -n 
                     --k8s-namespace -k  
                     
[--path]  
                     
[--replicas]  
                     
[--cores-limit -c]  
                     
[--cores-request]  
                     
[--memory-limit -m]  
                     
[--memory-request]  
                     
[--storage-class-data -d]  
                     
[--storage-class-logs -g]  
                     
[--storage-class-datalogs]  
                     
[--storage-class-backups]  
                     
[--volume-size-data]  
                     
[--volume-size-logs]  
                     
[--volume-size-datalogs]  
                     
[--volume-size-backups]  
                     
[--no-wait]  
                     
[--no-external-endpoint]  
                     
[--cert-public-key-file]  
                     
[--cert-private-key-file]  
                     
[--service-cert-secret]  
                     
[--admin-login-secret]  
                     
[--license-type -l]  
                     
[--tier -t]  
                     
[--dev]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-labels]  
                     
[--service-annotations]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]  
                     
[--collation]  
                     
[--language]  
                     
[--agent-enabled]  
                     
[--trace-flags]
```
### <a name="examples"></a>示例
创建 SQL 托管实例。
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace
```
在 HA 方案中创建包含 3 个副本的 SQL 托管实例。
```bash
az sql mi-arc create -n sqlmi2 --replicas 3 --k8s-namespace namespace
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
SQL 托管实例的名称。
#### `--k8s-namespace -k`
要部署 SQL 托管实例的命名空间。 如果未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
### <a name="optional-parameters"></a>可选参数
#### `--path`
指向 SQL 托管实例 json 文件的 azext_arcdata 文件的路径。
#### `--replicas`
此选项指定将在 Kubernetes 群集中部署以实现高可用性的 SQL 托管实例副本数。 允许的值为“3”或“1”，默认值为“1”。
#### `--cores-limit -c`
托管实例的核心限制（整数）。
#### `--cores-request`
对托管实例核心（整数）的请求。
#### `--memory-limit -m`
以整数表示的托管实例的容量限制，后跟 Gi (GB)。 示例：4Gi
#### `--memory-request`
以整数表示的托管实例的容量请求，后跟 Gi (GB)。 示例：4Gi
#### `--storage-class-data -d`
要用于数据文件的存储类（.mdf、.ndf）。 如果未指定任何值，则不会指定存储类，这将导致 Kubernetes 使用默认存储类。
#### `--storage-class-logs -g`
要用于日志的存储类 (/var/log)。 如果未指定任何值，则不会指定存储类，这将导致 Kubernetes 使用默认存储类。
#### `--storage-class-datalogs`
要用于数据库日志的存储类 (.ldf)。 如果未指定任何值，则不会指定存储类，这将导致 Kubernetes 使用默认存储类。
#### `--storage-class-backups`
要用于备份的存储类 (/var/opt/mssql/backups)。 如果未指定任何值，则不会指定存储类，这将导致 Kubernetes 使用默认存储类。
#### `--volume-size-data`
要用于数据的存储卷的大小（正数），后跟 Ki（千字节）、Mi（兆字节）或 Gi（吉字节）。
#### `--volume-size-logs`
要用于日志的存储卷的大小（正数），后跟 Ki（千字节）、Mi（兆字节）或 Gi（吉字节）。
#### `--volume-size-datalogs`
要用于数据日志的存储卷的大小（正数），后跟 Ki（千字节）、Mi（兆字节）或 Gi（吉字节）。
#### `--volume-size-backups`
要用于备份的存储卷的大小（正数），后跟 Ki（千字节）、Mi（兆字节）或 Gi（吉字节）。
#### `--no-wait`
如果给定，该命令不会等待实例处于就绪状态再返回。
#### `--no-external-endpoint`
如果已指定，则不会创建外部服务。 否则，将使用与数据控制器相同的服务类型来创建外部服务。
#### `--cert-public-key-file`
包含用于 SQL Server 的 PEM 格式证书公钥的文件路径。
#### `--cert-private-key-file`
包含用于 SQL Server 的 PEM 格式证书私钥的文件路径。
#### `--service-cert-secret`
要生成的用于托管或将托管 SQL 服务证书的 Kubernetes 机密的名称。
#### `--admin-login-secret`
要生成的用于托管或将托管用户管理员登录帐户凭据的 Kubernetes 机密的名称。
#### `--license-type -l`
要应用于此托管实例的许可证类型。 允许的值为：BasePrice、LicenseIncluded。 默认值为 LicenseIncluded。 无法更改许可证类型。
#### `--tier -t`
新实例的定价层。 允许的值：BusinessCritical（简称为 bc）或 GeneralPurpose（简称为 gp）。 默认值为 GeneralPurpose。 无法更改价格段。
#### `--dev`
如果指定了此项，则会将其视为开发实例，而不会对其进行计费。
#### `--labels`
SQL 托管实例标签的逗号分隔列表。
#### `--annotations`
SQL 托管实例注释的逗号分隔列表。
#### `--service-labels`
要应用于所有外部服务的标签的逗号分隔列表。
#### `--service-annotations`
要应用于所有外部服务的注释的逗号分隔列表。
#### `--storage-labels`
要应用于所有 PVC 的标签的逗号分隔列表。
#### `--storage-annotations`
要应用于所有 PVC 的注释的逗号分隔列表。
#### `--use-k8s`
使用本地 Kubernetes API 创建 SQL 托管实例。
#### `--collation`
实例的 SQL Server 排序规则。
#### `--language`
在实例中，将 SQL Server 区域设置更改为任何支持的语言标识符 (LCID)。
#### `--agent-enabled`
为实例启用 SQL Server 代理。 默认为禁用。 允许的值为“true”或“false”。
#### `--trace-flags`
以逗号分隔的跟踪标志列表。 默认情况下没有标志。
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
## <a name="az-sql-mi-arc-edit"></a>az sql mi-arc edit
编辑 SQL 托管实例的配置。
```bash
az sql mi-arc edit --name -n 
                   [--k8s-namespace -k]  
                   
[--path]  
                   
[--cores-limit -c]  
                   
[--cores-request]  
                   
[--memory-limit -m]  
                   
[--memory-request]  
                   
[--no-wait]  
                   
[--dev]  
                   
[--labels]  
                   
[--annotations]  
                   
[--service-labels]  
                   
[--service-annotations]  
                   
[--agent-enabled]  
                   
[--trace-flags]  
                   
[--use-k8s]
```
### <a name="examples"></a>示例
编辑 SQL 托管实例的配置。
```bash
az sql mi-arc edit --path ./spec.json -n sqlmi1
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
正在编辑的 SQL 托管实例的名称。 不能更改部署实例时为其指定的名称。
### <a name="optional-parameters"></a>可选参数
#### `--k8s-namespace -k`
SQL 托管实例所在的命名空间。 如果未指定命名空间，则将使用 kubeconfig 中定义的命名空间。
#### `--path`
指向 SQL 托管实例 json 文件的 azext_arcdata 文件的路径。
#### `--cores-limit -c`
托管实例的核心限制（整数）。
#### `--cores-request`
对托管实例核心（整数）的请求。
#### `--memory-limit -m`
以整数表示的托管实例的容量限制，后跟 Gi (GB)。 示例：4Gi
#### `--memory-request`
以整数表示的托管实例的容量请求，后跟 Gi (GB)。 示例：4Gi
#### `--no-wait`
如果给定，该命令不会等待实例处于就绪状态再返回。
#### `--dev`
如果指定了此项，则会将其视为开发实例，而不会对其进行计费。
#### `--labels`
SQL 托管实例标签的逗号分隔列表。
#### `--annotations`
SQL 托管实例注释的逗号分隔列表。
#### `--service-labels`
要应用于所有外部服务的标签的逗号分隔列表。
#### `--service-annotations`
要应用于所有外部服务的注释的逗号分隔列表。
#### `--agent-enabled`
为实例启用 SQL Server 代理。 默认为禁用。
#### `--trace-flags`
以逗号分隔的跟踪标志列表。 默认情况下没有标志。
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
删除 SQL 托管实例。
```bash
az sql mi-arc delete --name -n 
                     [--k8s-namespace -k]  
                     
[--use-k8s]
```
### <a name="examples"></a>示例
删除 SQL 托管实例。
```bash
az sql mi-arc delete -n sqlmi1
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
要删除的 SQL 托管实例的名称。
### <a name="optional-parameters"></a>可选参数
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
订阅的名称或 ID。 可以使用 `az account set -s NAME_OR_ID` 配置默认订阅。
#### `--verbose`
提高日志记录详细程度。 使用 --debug 获取完整的调试日志。
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
显示 SQL 托管实例的详细信息。
```bash
az sql mi-arc show --name -n 
                   [--path -p]  
                   
[--k8s-namespace -k]  
                   
[--use-k8s]
```
### <a name="examples"></a>示例
显示 SQL 托管实例的详细信息。
```bash
az sql mi-arc show -n sqlmi1
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
要显示的 SQL 托管实例的名称。
### <a name="optional-parameters"></a>可选参数
#### `--path -p`
应将 SQL 托管实例的完整规范写入到的路径。 如果省略，则会将规范写入标准输出。
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
订阅的名称或 ID。 可以使用 `az account set -s NAME_OR_ID` 配置默认订阅。
#### `--verbose`
提高日志记录详细程度。 使用 --debug 获取完整的调试日志。
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
从 sql mi 检索可用性组镜像终结点的证书，并存储在文件中。
```bash
az sql mi-arc get-mirroring-cert --name -n 
                                 --cert-file  
                                 
[--k8s-namespace -k]  
                                 
[--use-k8s]
```
### <a name="examples"></a>示例
从 sqlmi1 检索可用性组镜像终结点的证书，并存储在文件 fileName1 中
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
```
### <a name="required-parameters"></a>必需参数
#### `--name -n`
SQL 托管实例的名称。
#### `--cert-file`
用于以 PEM 格式存储检索到的证书的本地文件名。
### <a name="optional-parameters"></a>可选参数
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
订阅的名称或 ID。 可以使用 `az account set -s NAME_OR_ID` 配置默认订阅。
#### `--verbose`
提高日志记录详细程度。 使用 --debug 获取完整的调试日志。
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
列出 SQL 托管实例。
```bash
az sql mi-arc list [--k8s-namespace -k] 
                   [--use-k8s]
```
### <a name="examples"></a>示例
列出 SQL 托管实例。
```bash
az sql mi-arc list
```
### <a name="optional-parameters"></a>可选参数
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
订阅的名称或 ID。 可以使用 `az account set -s NAME_OR_ID` 配置默认订阅。
#### `--verbose`
提高日志记录详细程度。 使用 --debug 获取完整的调试日志。
