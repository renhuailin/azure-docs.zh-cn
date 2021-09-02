---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: 29f5e96c18220f0bbdec5f91107c9220fc2fba0b
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123355255"
---
Azure 实例元数据服务 (IMDS) 提供有关当前正在运行的虚拟机实例的信息。 可以使用它来管理和配置虚拟机。
这些信息包括 SKU、存储、网络配置和即将发生的维护事件。 有关可用数据的完整列表，请参阅[终结点类别摘要](#endpoint-categories)。

IMDS 适用于运行虚拟机 (VM) 的实例和虚拟机规模集实例。 所有终结点均支持使用 [Azure 资源管理器](/rest/api/resources/)创建和管理的 VM。 只有“实例”类别的“经过证明的”类别和“网络”部分支持通过经典部署模型创建的 VM。 “经过证明的”终结点仅在有限的程度上提供这样的支持。

IMDS 是一个 REST API，在已知的、不可路由的 IP 地址 (`169.254.169.254`) 上提供。 只能从 VM 中访问它。 VM 与 IMDS 之间的通信绝不会离开主机。
让 HTTP 客户端在查询 IMDS 时绕过 VM 中的 Web 代理并同等对待 `169.254.169.254` 和 [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md)。

## <a name="usage"></a>使用情况

### <a name="access-azure-instance-metadata-service"></a>访问 Azure 实例元数据服务

若要访问 IMDS，请从 [Azure 资源管理器](/rest/api/resources/)或 [Azure 门户](https://portal.azure.com)创建一个 VM，并使用以下示例。
如需更多示例，请参阅 [Azure 实例元数据示例](https://github.com/microsoft/azureimds)。

下面是用于检索实例的所有元数据的示例代码。 若要访问特定的数据源，请参阅[终结点类别](#endpoint-categories)，其中概述了所有可用的功能。

**请求**

> [!IMPORTANT]
> 此示例会绕过代理。 查询 IMDS 时，必须绕过代理。 有关其他信息，请参阅[代理](#proxies)。

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2021-02-01" | ConvertTo-Json -Depth 64
```

`-NoProxy` 需要 PowerShell V6 或更高版本。 请参阅我们的[示例存储库](https://github.com/microsoft/azureimds)，了解具有较旧 PowerShell 版本的示例。

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2021-02-01" | jq
```

---

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>安全性和身份验证

实例元数据服务仅供从不可路由的 IP 地址上正在运行的虚拟机实例中访问。 VM 仅限与它们自身相关的元数据/功能交互。 API 仅限 HTTP，且从不离开主机。

为了确保请求直接适用于 IMDS，并防止意外的或不需要的请求重定向，请求必须符合以下条件：
- 必须包含标头 `Metadata: true`
- 不得包含 `X-Forwarded-For` 标头

任何不符合这两项要求的请求都会被服务拒绝。

> [!IMPORTANT]
> IMDS 不是敏感数据的通道。 此 API 不进行身份验证，并且对 VM 上的所有进程开放。 应将通过此服务公开的信息视为与 VM 内运行的所有应用程序共享的信息。

如果不需要 VM 上的每个进程都访问 IMDS 终结点，可以通过设置本地防火墙规则来限制访问。 例如，如果只有已知系统服务需要访问实例元数据服务，则可以在 IMDS 终结点上设置防火墙规则，仅允许特定进程访问或拒绝访问其余进程。 


## <a name="proxies"></a>代理

IMDS 不用于在代理后使用，系统不支持那样做。 大多数 HTTP 客户端提供了一个选项，供你对你的请求禁用代理，当与 IMDS 通信时必须使用此功能。 有关详细信息，请参阅客户端的文档。

> [!IMPORTANT]
> 即使你不知道环境中的任何代理配置的信息，也必须重写任何默认的客户端代理设置。 代理配置可以被自动发现，未能绕过这样的配置就必须冒服务中断的风险（如果将来要更改计算机的配置的话）。

## <a name="rate-limiting"></a>速率限制

一般情况下，对 IMDS 的请求限制为每秒 5 个请求。 系统会拒绝超过此阈值的请求，并显示 429 响应。 对[托管标识](#managed-identity)类别的请求限制为每秒 20 个请求，并发请求数限制为 5 个。

## <a name="http-verbs"></a>HTTP 谓词

目前支持以下 HTTP 谓词：

| 谓词 | 说明 |
|------|-------------|
| `GET` | 检索请求的资源

## <a name="parameters"></a>参数

终结点可以支持必需的和/或可选的参数。 有关详细信息，请参阅[架构](#schema)以及相关的特定终结点的文档。

### <a name="query-parameters"></a>查询参数

IMDS 终结点支持 HTTP 查询字符串参数。 例如： 

```
http://169.254.169.254/metadata/instance/compute?api-version=2021-01-01&format=json
```

指定参数：

| 名称 | 值 |
|------|-------|
| `api-version` | `2021-01-01`
| `format` | `json`

系统会拒绝具有重复的查询参数名称的请求。

### <a name="route-parameters"></a>路由参数

对于某些返回较大 json blob 的终结点，我们支持将路由参数追加到请求终结点，以便向下筛选到响应的某个子集： 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
参数对应的索引/键将用于逐级向下找到 json 对象（如果你与已分析的表示形式交互）。

例如，`/metatadata/instance` 会返回 json 对象：
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

若要将响应向下筛选到计算属性，则可发送以下请求： 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

类似地，若要筛选到嵌套的属性或特定的数组元素，则可不断地追加键： 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
将会筛选到 `Network.interface` 属性中的第一个元素，并返回：

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> 筛选到叶节点时，`format=json` 将不起作用。 对于这些查询，需要显式指定 `format=text`，因为默认格式为 json。

## <a name="schema"></a>架构

### <a name="data-format"></a>数据格式

默认情况下，IMDS 以 JSON 格式返回数据 (`Content-Type: application/json`)。 但是，支持响应筛选（请参阅[路由参数](#route-parameters)）的终结点还支持 `text` 格式。

若要访问非默认响应格式，请在请求中将所请求的格式指定为查询字符串参数。 例如：

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

在 json 响应中，所有基元都将为 `string` 类型，缺失值或不适用的值始终包括在内，但会被设置为空字符串。

### <a name="versioning"></a>版本控制

IMDS 进行了版本控制，在 HTTP 请求中指定 API 版本是必需的。 此要求的唯一例外是 [versions](#versions) 终结点，该终结点可用于动态检索可用的 API 版本。

在添加更新的版本时，早期版本仍可供访问以保持兼容性（如果脚本依赖于特定的数据格式）。

如果不指定版本，则会收到错误消息，其中会列出受支持的最新版本：
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>支持的 API 版本
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01
- 2021-01-01
- 2021-02-01
- 2021-03-01
- 2021-05-01

### <a name="swagger"></a>Swagger

可在以下位置找到 IMDS 的完整 Swagger 定义： https://github.com/Azure/azure-rest-api-specs/blob/main/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>区域可用性

此服务在所有 Azure 云中正式发布。

## <a name="root-endpoint"></a>根终结点

根终结点为 `http://169.254.169.254/metadata`。

## <a name="endpoint-categories"></a>终结点类别

IMDS API 包含多个表示不同数据源的终结点类别，每一个都包含一个或多个终结点。 有关详细信息，请查看每个类别。

| 类别根 | 说明 | 引入的版本 |
|---------------|-------------|--------------------|
| `/metadata/attested` | 请参阅[证明数据](#attested-data) | 2018-10-01
| `/metadata/identity` | 请参阅[通过 IMDS 托管的托管标识](#managed-identity) | 2018-02-01
| `/metadata/instance` | 请参阅[实例元数据](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | 请参阅[通过 IMDS 检索负载均衡器元数据](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | 请参阅[通过 IMDS 计划的计划事件](#scheduled-events) | 2017-08-01
| `/metadata/versions` | 请参阅[版本](#versions) | 不适用

## <a name="versions"></a>版本

> [!NOTE]
> 此功能已随版本 2020-10-01 一起发布，该版本目前正处于推出阶段，在某些区域可能尚不可用。

### <a name="list-api-versions"></a>列出 API 版本

返回受支持的 API 版本的集合。

```
GET /metadata/versions
```

#### <a name="parameters"></a>参数

无（此终结点未进行版本控制）。

#### <a name="response"></a>响应

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>实例元数据

### <a name="get-vm-metadata"></a>获取 VM 元数据

公开 VM 实例的重要元数据，其中包括计算、网络和存储。 

```
GET /metadata/instance
```

#### <a name="parameters"></a>参数

| 名称 | 必需/可选 | 说明 |
|------|-------------------|-------------|
| `api-version` | 必需 | 用于处理请求的版本。
| `format` | 可选* | 响应的格式（`json` 或 `text`）。 *注意：使用请求参数时可能需要

此终结点支持通过[路由参数](#route-parameters)进行响应筛选。

#### <a name="response"></a>响应

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

架构明细：

**计算**

| 数据 | 说明 | 引入的版本 |
|------|-------------|--------------------|
| `azEnvironment` | VM 运行时所在的 Azure 环境 | 2018-10-01
| `customData` | 此功能[在 IMDS 中](#frequently-asked-questions)已弃用且已禁用。 它已被 `userData` 取代 | 2019-02-01
| `evictionPolicy` | 设置逐出[现成 VM](../articles/virtual-machines/spot-vms.md) 的方式。 | 2020-12-01
| `isHostCompatibilityLayerVm` | 标识 VM 是否在主机兼容性层上运行 | 2020-06-01
| `licenseType` | [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit)许可证的类型。 这仅适用于启用了 AHB 的 VM | 2020-09-01
| `location` | VM 在其中运行的 Azure 区域 | 2017-04-02
| `name` | VM 的名称 | 2017-04-02
| `offer` | 提供 VM 映像的信息，仅适用于从 Azure 映像库部署的映像 | 2017-04-02
| `osProfile.adminUsername` | 指定管理员帐户的名称 | 2020-07-15
| `osProfile.computerName` | 指定计算机的名称 | 2020-07-15
| `osProfile.disablePasswordAuthentication` | 指定是否禁用密码身份验证。 这仅适用于 Linux VM | 2020-10-01
| `osType` | Linux 或 Windows | 2017-04-02
| `placementGroupId` | 虚拟机规模集的[放置组](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
| `plan` | 包含 VM 的名称、产品和发布者（如果是 Azure 市场映像）的[计划](/rest/api/compute/virtualmachines/createorupdate#plan) | 2018-04-02
| `platformUpdateDomain` |  正在运行 VM 的[更新域](../articles/virtual-machines/availability.md) | 2017-04-02
| `platformFaultDomain` | 正在运行 VM 的[容错域](../articles/virtual-machines/availability.md) | 2017-04-02
| `priority` | VM 的优先级。 有关详细信息，请参阅[现成 VM](../articles/virtual-machines/spot-vms.md) | 2020-12-01
| `provider` | VM 的提供商 | 2018-10-01
| `publicKeys` | [公钥的集合](/rest/api/compute/virtualmachines/createorupdate#sshpublickey)，已分配给 VM 和路径 | 2018-04-02
| `publisher` | VM 映像的发布者 | 2017-04-02
| `resourceGroupName` | 虚拟机的[资源组](../articles/azure-resource-manager/management/overview.md) | 2017-08-01
| `resourceId` | 资源的[完全限定](/rest/api/resources/resources/getbyid) ID | 2019-03-11
| `sku` | VM 映像的特定 SKU | 2017-04-02
| `securityProfile.secureBootEnabled` | 标识是否在 VM 上启用了 UEFI 安全启动 | 2020-06-01
| `securityProfile.virtualTpmEnabled` | 标识是否在 VM 上启用了虚拟受信任的平台模块 (TPM) | 2020-06-01
| `storageProfile` | 请参阅下面的“存储配置文件” | 2019-06-01
| `subscriptionId` | 虚拟机的 Azure 订阅 | 2017-08-01
| `tags` | 虚拟机的[标记](../articles/azure-resource-manager/management/tag-resources.md)  | 2017-08-01
| `tagsList` | 格式化为 JSON 数组以方便编程分析的标记  | 2019-06-04
| `userData` | 创建 VM 时指定的一组数据，在预配期间或之后使用（Base64 编码）  | 2021-01-01
| `version` | VM 映像的版本 | 2017-04-02
| `vmId` | VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
| `vmScaleSetName` | 虚拟机规模集的[虚拟机规模集名称](../articles/virtual-machine-scale-sets/overview.md) | 2017-12-01
| `vmSize` | [VM 大小](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | 虚拟机的[可用性区域](../articles/availability-zones/az-overview.md) | 2017-12-01

**存储配置文件**

VM 的存储配置文件分为三个类别：映像引用、操作系统磁盘和数据磁盘，以及本地临时磁盘的其他对象。

映像引用对象包含有关 OS 映像的以下信息：

| 数据 | 说明 |
|------|-------------|
| `id` | 资源 ID
| `offer` | 平台或市场映像的套餐
| `publisher` | 映像发布者
| `sku` | 映像 SKU
| `version` | 平台或市场映像的版本

OS 磁盘对象包含有关 VM 所用 OS 磁盘的以下信息：

| 数据 | 说明 |
|------|-------------|
| `caching` | 缓存要求
| `createOption` | 有关 VM 创建方式的信息
| `diffDiskSettings` | 临时磁盘设置
| `diskSizeGB` | 磁盘大小 (GB)
| `image`   | 源用户映像虚拟硬盘
| `managedDisk` | 托管磁盘参数
| `name`    | 磁盘名称
| `vhd`     | 虚拟硬盘
| `writeAcceleratorEnabled` | 磁盘上是否启用了 writeAccelerator

数据磁盘阵列包含附加到 VM 的数据磁盘列表。 每个数据磁盘对象包含以下信息：

数据 | 说明 | 引入的版本 |
|------|-----------|--------------------|
| `bytesPerSecondThrottle`* | 磁盘读取/写入配额（字节） | 2021-05-01
| `caching` | 缓存要求 | 2019-06-01
| `createOption` | 有关 VM 创建方式的信息 | 2019-06-01
| `diffDiskSettings` | 临时磁盘设置 | 2019-06-01
| `diskCapacityBytes`* | 磁盘大小（字节） | 2021-05-01
| `diskSizeGB` | 磁盘大小 (GB) | 2019-06-01
| `encryptionSettings` | 磁盘的加密设置 | 2019-06-01
| `image` | 源用户映像虚拟硬盘 | 2019-06-01
| `isSharedDisk`* | 确定磁盘是否可在资源间共享 | 2021-05-01
| `isUltraDisk` | 标识数据磁盘是否为超级磁盘 | 2021-05-01
| `lun`     | 磁盘的逻辑单元号 | 2019-06-01
| `managedDisk` | 托管磁盘参数 | 2019-06-01
| `name` | 磁盘名称 | 2019-06-01
| `opsPerSecondThrottle`* | 磁盘读取/写入配额 (IOPS) | 2021-05-01
| `osType` | 磁盘中包含的 OS 类型 | 2019-06-01
| `vhd` | 虚拟硬盘 | 2019-06-01
| `writeAcceleratorEnabled` | 磁盘上是否启用了 writeAccelerator | 2019-06-01

\* 仅为超级磁盘填充这些字段；在非超级磁盘中时，它们将为空字符串。

资源磁盘对象包含附加到 VM 的[本地临时磁盘](../articles/virtual-machines/managed-disks-overview.md#temporary-disk)（如果有）的大小（以 KB 为单位）。
如果 [VM 没有本地临时磁盘](../articles/virtual-machines/azure-vms-no-temp-disk.yml)，则此值为 0。 

| 数据 | 说明 | 引入的版本 |
|------|-------------|--------------------|
| `resourceDisk.size` | VM 的本地临时磁盘大小（以 KB 为单位） | 2021-02-01

**Network**

| 数据 | 说明 | 引入的版本 |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | VM 的本地 IPv4 地址 | 2017-04-02
| `ipv4.publicIpAddress` | VM 的公共 IPv4 地址 | 2017-04-02
| `subnet.address` | VM 的子网地址 | 2017-04-02
| `subnet.prefix` | 子网前缀，例如 24 | 2017-04-02
| `ipv6.ipAddress` | VM 的本地 IPv6 地址 | 2017-04-02
| `macAddress` | VM mac 地址 | 2017-04-02

### <a name="get-user-data"></a>获取用户数据

在创建新 VM 时，可以指定一组要在 VM 预配期间或之后使用的数据，并通过 IMDS 进行检索。 在[此处](../articles/virtual-machines/user-data.md)检查端到端用户的数据体验。 

若要设置用户数据，请使用[此处](https://aka.ms/ImdsUserDataArmTemplate)的快速入门模板。 下面的示例介绍如何通过 IMDS 检索此数据。 此功能随 `2021-01-01` 及更高版本一并发布。

> [!NOTE]
> 安全声明：IMDS 对 VM 上的所有应用程序开放，敏感数据不应放在用户数据中。


#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
$userData = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text"
[System.Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($userData))
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

---


#### <a name="sample-1-tracking-vm-running-on-azure"></a>示例 1：跟踪 Azure 上正在运行的 VM

作为服务提供商，可能需要跟踪运行软件的 VM 数目，或者代理需要跟踪 VM 的唯一性。 为了能够获取 VM 的唯一 ID，请使用实例元数据服务中的 `vmId` 字段。

**请求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**响应**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>示例 2：不同数据副本的放置

对于某些方案，不同数据副本的放置至关重要。 例如，对于 [HDFS 副本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)或者对于通过[业务流程协调程序](https://kubernetes.io/docs/user-guide/node-selection/)进行的容器放置，可能需要知道正在运行 VM 的 `platformFaultDomain` 和 `platformUpdateDomain`。
你还可以使用实例的[可用性区域](../articles/availability-zones/az-overview.md)来做出这些决策。
可以通过 IMDS 直接查询此数据。

**请求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**响应**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>示例 3：获取 VM 标记

VM 标记包含在实例/计算/标记终结点下的实例 API。
标记可能已应用到 Azure VM 中，以逻辑方式将其归入一个分类。 可使用以下请求检索分配给 VM 的标记。

**请求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

---

**响应**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

`tags` 字段是带有用分号分隔的标记的字符串。 如果标记本身使用了分号，则此输出可能会出现问题。 如果编写分析程序以编程方式提取标记，则应该依赖于 `tagsList` 字段。 `tagsList` 字段是不带分隔符的 JSON 数组，因此更易于分析。 可以使用以下请求检索分配给 VM 的 tagsList。

**请求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**响应**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>示例 4：在支持案例期间获取有关 VM 的详细信息

作为服务提供商，你可能会接到支持电话，了解有关 VM 的详细信息。 请求客户共享计算元数据可以提供基本信息，以支持专业人员了解有关 Azure 上的 VM 类型。

**请求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "bytesPerSecondThrottle": "979202048",
            "caching": "None",
            "createOption": "Empty",
            "diskCapacityBytes": "274877906944",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "isSharedDisk": "false",
            "isUltraDisk": "true",
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/MicrosoftCompute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "opsPerSecondThrottle": "65280",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        },
        "resourceDisk": {
            "size": "4096"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "bytesPerSecondThrottle": "979202048",
            "caching": "None",
            "createOption": "Empty",
            "diskCapacityBytes": "274877906944",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "isSharedDisk": "false",
            "isUltraDisk": "true",
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "opsPerSecondThrottle": "65280",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        },
        "resourceDisk": {
            "size": "4096"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>示例 5：获取运行 VM 的 Azure 环境

Azure 具有各种主权云，如 [Azure 政府](https://azure.microsoft.com/overview/clouds/government/)。 有时你需要使用 Azure 环境来做出一些运行时决策。 以下示例显示了如何实现此行为。

**请求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**响应**

```
AzurePublicCloud
```

此处列出了 Azure 环境的云和值。

| 云 | Azure 环境 |
|-------|-------------------|
| [全球所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure 中国世纪互联](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure 德国](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>示例 6：检索网络信息

**请求**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**响应**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>示例 7：检索公共 IP 地址

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>证明数据

### <a name="get-attested-data"></a>获取“经过证明的”数据

IMDS 可帮助确保提供的数据来自 Azure。 Microsoft 会对此信息的一部分进行签名，以便你可以确认 Azure 市场中的映像是你正在 Azure 上运行的映像。

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>参数

| 名称 | 必需/可选 | 说明 |
|------|-------------------|-------------|
| `api-version` | 必需 | 用于处理请求的版本。
| `nonce` | 可选 | 用作加密 nonce 的 10 位字符串。 如果未提供值，则 IMDS 使用当前的 UTC 时间戳。

#### <a name="response"></a>响应

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> 由于 IMDS 的缓存机制，可能会返回以前缓存的 nonce 值。

签名 blob 是 [pkcs7](https://aka.ms/pkcs7) 签名的文档版本。 它包含用于签名的证书以及某些特定于 VM 的详细信息。

对于使用 Azure 资源管理器创建的 VM，该文档包括 `vmId`、`sku`、`nonce`、`subscriptionId`、文档创建和到期的 `timeStamp` 以及关于映像的计划信息。 该计划信息只针对 Azure 市场映像进行填充。

对于使用经典部署模型创建的 VM，仅保证填充 `vmId`。 可以从响应中提取证书，并使用它来确认响应是否有效并来自 Azure。

解码的文档包含以下字段：

| 数据 | 说明 | 引入的版本 |
|------|-------------|--------------------|
| `licenseType` | [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit)许可证的类型。 这仅适用于启用了 AHB 的 VM。 | 2020-09-01
| `nonce` | 可以随请求提供的可选字符串。 如果未提供 `nonce`，则会使用当前的协调世界时时间戳。 | 2018-10-01
| `plan` | [Azure 市场映像计划](/rest/api/compute/virtualmachines/createorupdate#plan)。 包含计划 ID（名称）、产品映像或产品/服务（产品）和发布者 ID（发布者）。 | 2018-10-01
| `timestamp.createdOn` | 创建签名文档时的 UTC 时间戳 | 2018-20-01
| `timestamp.expiresOn` | 签名文档到期时的 UTC 时间戳 | 2018-10-01
| `vmId` | VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2018-10-01
| `subscriptionId` | 虚拟机的 Azure 订阅 | 2019-04-30
| `sku` | VM 映像的特定 SKU | 2019-11-01

> [!NOTE]
> 对于经典（非 Azure 资源管理器）VM，只保证填充 vmId。

示例文档：
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>示例 1：验证 VM 是否在 Azure 中运行

Azure 市场供应商希望确保其软件仅获许在 Azure 中运行。 如果有人将 VHD 复制到本地环境，则供应商需要能够检测到此操作。 通过 IMDS，这些供应商可以获取能确保只从 Azure 响应的签名数据。

> [!NOTE]
> 此示例需要安装 jq 实用工具。

**验证**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

验证签名是否来自 Microsoft Azure 并检查证书链中是否存在错误。

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**结果**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

验证签名是否来自 Microsoft Azure 并检查证书链中是否存在错误。

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> 由于 IMDS 的缓存机制，可能会返回以前缓存的 `nonce` 值。

如果在初始请求中提供了 `nonce` 参数，则可以比较签名文档中的 `nonce`。

> [!NOTE]
> 公有云和每个主权云的证书将有所不同。

| 云 | 证书 |
|-------|-------------|
| [全球所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure 中国世纪互联](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure 德国](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> 对于公有云，证书可能不完全匹配 `metadata.azure.com`。 出于此原因，证书验证应允许任何 `.metadata.azure.com` 子域中的公用名称。

如果由于验证期间出现网络限制，导致中间证书无法下载，可以固定中间证书。 Azure 会滚动更新证书，这是标准 PKI 做法。 发生滚动更新时，必须更新固定的证书。 每当规划某项更改来更新中间证书时，就会更新 Azure 博客并向 Azure 客户发出通知。 

可在 [PKI 存储库](https://www.microsoft.com/pki/mscorp/cps/default.htm)中找到中间证书。 每个区域的中间证书可能不同。

> [!NOTE]
> Azure 中国世纪互联的中间证书将来自 DigiCert 全局根 CA（而不是 Baltimore）。
如果在更改根证书链颁发机构的过程中已固定 Azure 中国的中间证书，则必须更新中间证书。


## <a name="managed-identity"></a>托管标识

可以在 VM 上启用由系统分配的托管标识。 还可以将一个或多个用户分配的托管标识分配给 VM。
然后，可以从 IMDS 请求托管标识的令牌。 使用这些令牌来通过其他 Azure 服务（如 Azure Key Vault）进行身份验证。

有关启用此功能的详细步骤，请参阅[获取访问令牌](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

## <a name="load-balancer-metadata"></a>负载均衡器元数据
将虚拟机或虚拟机集实例置于 Azure 标准负载均衡器后面时，可以使用 IMDS 检索与负载均衡器和实例相关的元数据。 有关详细信息，请参阅[检索负载均衡器信息](../articles/load-balancer/instance-metadata-service-load-balancer.md)。

## <a name="scheduled-events"></a>计划事件
可以使用 IMDS 获取计划事件的状态。 然后，用户可以指定一组在发生这些事件时要运行的操作。 有关详细信息，请参阅 [Linux 计划事件](../articles/virtual-machines/linux/scheduled-events.md)或 [Windows 计划事件](../articles/virtual-machines/windows/scheduled-events.md)。


## <a name="sample-code-in-different-languages"></a>不同语言的示例代码

下表列出了在 VM 中使用不同语言调用 IMDS 的相关示例：

| 语言 | 示例 |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>错误和调试

如果找不到某个数据元素，或者请求的格式不正确，则实例元数据服务返回标准 HTTP 错误。 例如：

| HTTP 状态代码 | 原因 |
|------------------|--------|
| `200 OK` | 请求已成功。
| `400 Bad Request` | 查询叶节点时缺少 `Metadata: true` 标头或缺少参数 `format=json`
| `404 Not Found` | 请求的元素不存在
| `405 Method Not Allowed` | 此终结点不支持 HTTP 方法（谓词）。
| `410 Gone` | 在一段时间后重试最长 70 秒
| `429 Too Many Requests` | 已超出 API [速率限制](#rate-limiting)
| `500 Service Error` | 请稍后重试

## <a name="frequently-asked-questions"></a>常见问题

- 我收到错误 `400 Bad Request, Required metadata header not specified`。 这是什么意思呢？
  - IMDS 需要在请求中传递标头 `Metadata: true`。 将该标头传入 REST 调用将允许访问 IMDS。

- 为什么我无法获取我的 VM 的计算信息？
  - 当前 IMDS 仅支持 Azure 资源管理器创建的实例。

- 我一段时间以前通过 Azure 资源管理器创建了 VM。 为什么我无法看到计算元数据信息？
  - 如果在 2016 年 9 月之后创建了 VM，请添加[标记](../articles/azure-resource-manager/management/tag-resources.md)以开始查看计算元数据。 如果在 2016 年 9 月之前创建了 VM，请在 VM 实例中添加或删除扩展或数据磁盘以刷新元数据。

- 用户数据与自定义数据是否相同？
  - 用户数据提供了与自定义数据类似的功能，使你可以将自己的元数据传递给 VM 实例。 不同之处在于，用户数据是通过 IMDS 进行检索的，并且在 VM 实例的整个生存期内保持不变。 现有的自定义数据功能将继续按照[本文](../articles/virtual-machines/custom-data.md)所述正常运行。 但是，只能通过本地系统文件夹获取自定义数据，而不能通过 IMDS 获取。

- 为什么我看不到为新版本填充的任何数据？
  - 如果在 2016 年 9 月之后创建了 VM，请添加[标记](../articles/azure-resource-manager/management/tag-resources.md)以开始查看计算元数据。 如果在 2016 年 9 月之前创建了 VM，请在 VM 实例中添加或删除扩展或数据磁盘以刷新元数据。

- 我为什么会收到错误 `500 Internal Server Error` 或 `410 Resource Gone`？
  - 请重试请求。 有关详细信息，请参阅[临时故障处理](/azure/architecture/best-practices/transient-faults)。 如果问题持续存在，请在 Azure 门户中为 VM 创建支持问题。

- 这是否适用于虚拟机规模集实例？
  - 是的，IMDS 适用于虚拟机规模集实例。

- 我在虚拟机规模集中更新了我的标记，但与单实例 VM 不同，这些标记未出现在实例中。 我哪里出错了吗？
  - 目前，虚拟机规模集的标记仅在重启、重置映像或更改实例的磁盘时向 VM 显示。

- 为什么在 `instance/compute` 详细信息中看不到 VM 的 SKU 信息？
  - 对于通过 Azure 市场创建的自定义映像，Azure 平台不会保留自定义映像的 SKU 信息以及从自定义映像创建的任何 VM 的详细信息。 这是由设计决定的，因此所述信息不会出现在 VM 的 `instance/compute` 详细信息中。

- 为什么调用服务时请求超时？
  - 必须从分配给 VM 的主要网卡的主 IP 地址进行元数据调用。 此外，如果你更改了路由，则 VM 的本地路由表中必须存在 169.254.169.254/32 地址的路由。

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. 转储本地路由表并查找 IMDS 条目。 例如：
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. 验证是否存在 `169.254.169.254` 的路由，并记下相应的网络接口（例如 `172.16.69.7`）。
    1. 转储接口配置并查找与路由表中引用的接口相对应的接口，注明 MAC（物理）地址。
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. 确认该接口对应于 VM 的主 NIC 和主 IP。 可以通过在 Azure 门户中查看网络配置，或通过 Azure CLI 查找它来找到主 NIC 和 IP。 请记下专用 IP（如果使用 CLI，还要记下 MAC 地址）。 下面是一个 PowerShell CLI 示例：
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. 如果它们不匹配，请更新路由表，以使主 NIC 和 IP 成为目标。

    ### <a name="linux"></a>[Linux](#tab/linux/)

    1. 使用诸如 `netstat -r` 等命令转储本地路由表，并查找 IMDS 条目（例如）：
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. 验证是否存在 `169.254.169.254` 的路由，并记下相应的网络接口（例如 `eth0`）。
    1. 转储路由表中相应接口的接口配置（请注意，配置文件的确切名称可能有所不同）
        ```console
        ~$ cat /etc/netplan/50-cloud-init.yaml
        network:
        ethernets:
            eth0:
                dhcp4: true
                dhcp4-overrides:
                    route-metric: 100
                dhcp6: false
                match:
                    macaddress: 00:0d:3a:e4:c7:2e
                set-name: eth0
        version: 2
        ```
    1. 如果使用的是动态 IP，请记下 MAC 地址。 如果使用的是静态 IP，可以记下列出的 IP 和/或 MAC 地址。
    1. 确认该接口对应于 VM 的主 NIC 和主 IP。 可以通过在 Azure 门户中查看网络配置，或通过 Azure CLI 查找它来找到主 NIC 和 IP。 请记下专用 IP（如果使用 CLI，还要记下 MAC 地址）。 下面是一个 PowerShell CLI 示例：
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: ipexample606 True 00-0D-3A-E4-C7-2E
        ```
    1. 如果它们不匹配，请更新路由表，以使主 NIC/IP 成为目标。

    ---

- Windows Server 中的故障转移群集
  - 使用故障转移群集查询 IMDS 时，有时需要向路由表添加路由。 下面介绍如何操作：

    1. 使用管理员特权打开命令提示符。

    1. 运行以下命令，并记下 IPv4 路由表中网络目标 (`0.0.0.0`) 接口的地址。

    ```bat
    route print
    ```

    > [!NOTE]
    > 以下示例输出来自启用了故障转移群集的 Windows Server VM。 为简单起见，输出仅包含 IPv4 路由表。

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    运行以下命令并使用网络目标 (`0.0.0.0`) 接口的地址，在此示例中为 `10.0.1.10`。

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>支持

如果在多次尝试后无法获取元数据响应，则可以在 Azure 门户中创建支持问题。

## <a name="product-feedback"></a>产品反馈

你可以访问[此处](https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627)，在“虚拟机”>“实例元数据服务”下向我们的用户反馈渠道提供产品反馈和想法

## <a name="next-steps"></a>后续步骤

- [获取 VM 的访问令牌](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Linux 计划事件](../articles/virtual-machines/linux/scheduled-events.md)

- [Windows 计划事件](../articles/virtual-machines/windows/scheduled-events.md)
