---
title: 适用于 Linux 的 Azure 实例元数据服务
description: 了解 Azure 实例元数据服务，以及它如何提供有关 Linux 中当前正在运行的虚拟机实例的信息。
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: baf0284198f8d30867ea722a4e0057b6d07c91bd
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883140"
---
# <a name="azure-instance-metadata-service-imds"></a>Azure 实例元数据服务 (IMDS)

Azure 实例元数据服务 (IMDS) 提供有关当前正在运行的虚拟机实例的信息。 你可以使用它来管理和配置虚拟机。
这些信息包括 SKU、存储、网络配置和即将发生的维护事件。 有关可用数据的完整列表，请参阅 " [终结点类别摘要](#endpoint-categories)"。

IMDS 可用于运行 (Vm) 和虚拟机规模集实例的虚拟机实例。 所有终结点都支持使用 [Azure 资源管理器](/rest/api/resources/)创建和管理的 vm。 只有实例类别的证明类别和网络部分支持使用经典部署模型创建的 Vm。 证明终结点仅在有限范围内执行此操作。

IMDS 是一种 REST API，在 () 的已知、不可路由的 IP 地址上可用 `169.254.169.254` 。 只能从 VM 中访问。 VM 与 IMDS 之间的通信绝不会离开主机。
查询 IMDS 时，让 HTTP 客户端绕过 VM 中的 web 代理，并 `169.254.169.254` 将其视为相同 [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) 。

## <a name="usage"></a>用法

### <a name="access-azure-instance-metadata-service"></a>访问 Azure 实例元数据服务

若要访问 IMDS，请从 [Azure 资源管理器](/rest/api/resources/) 或 [Azure 门户](https://portal.azure.com)创建 VM，并使用以下示例。
有关更多示例，请参阅 [Azure 实例元数据示例](https://github.com/microsoft/azureimds)。

下面是检索实例的所有元数据的示例代码。 若要访问特定的数据源，请参阅 [终结点类别](#endpoint-categories) 以获取所有可用功能的概述。

**请求**

> [!IMPORTANT]
> 此示例将跳过代理。 查询 IMDS 时， **必须** 绕过代理。 有关其他信息，请参阅 [代理](#proxies) 。

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
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
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
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
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
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
        }]
    }
}
```

## <a name="security-and-authentication"></a>安全性和身份验证

只能从不可路由的 IP 地址上正在运行的虚拟机实例中访问实例元数据服务。 Vm 限制为与自身相关的元数据/功能进行交互。 API 仅限 HTTP，不会离开主机。

为了确保请求直接适用于 IMDS，并防止意外或不需要的请求重定向，请求：
- **必须** 包含标头 `Metadata: true`
- **不** 能包含 `X-Forwarded-For` 标头

任何不满足 **这两** 个要求的请求都将被服务拒绝。

> [!IMPORTANT]
> IMDS **不** 是敏感数据的通道。 此 API 未经身份验证，并对 VM 上的所有进程开放。 应将通过此服务公开的信息视为与 VM 内运行的所有应用程序共享的信息。

## <a name="proxies"></a>代理

**不** 应在代理后面使用 IMDS，不支持这样做。 大多数 HTTP 客户端提供了一个选项来禁用请求上的代理，并且在与 IMDS 通信时必须使用此功能。 有关详细信息，请参阅客户端的文档。

> [!IMPORTANT]
> 即使你不知道环境中的任何代理配置， **仍必须覆盖任何默认的客户端代理设置**。 代理配置可以自动发现，如果不能跳过此类配置，则会在将来更改计算机的配置时，为你提供 outrage 的风险。

## <a name="rate-limiting"></a>速率限制

通常，对 IMDS 的请求限制为每秒5个请求。 超过此阈值的请求将被拒绝，并出现429响应。 对 [托管标识](#managed-identity) 类别的请求限制为每秒20个请求和5个并发请求。

## <a name="http-verbs"></a>HTTP 谓词

目前支持以下 HTTP 谓词：

| 谓词 | 说明 |
|------|-------------|
| `GET` | 检索请求的资源

## <a name="parameters"></a>参数

终结点可以支持必需和/或可选参数。 有关详细信息，请参阅相关特定终结点的 [架构](#schema) 和文档。

### <a name="query-parameters"></a>查询参数

IMDS 端点支持 HTTP 查询字符串参数。 例如： 

```bash
http://169.254.169.254/metadata/instance/compute?api-version=2019-06-04&format=json
```

指定参数：

| 名称 | Value |
|------|-------|
| `api-version` | `2019-06-04`
| `format` | `json`

具有重复查询参数名称的请求将被拒绝。

### <a name="route-parameters"></a>路由参数

对于返回更大 json blob 的某些终结点，我们支持将路由参数追加到请求终结点，以筛选出响应的一个子集： 

```bash
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
参数对应于将用于向下遍历 json 对象的索引/键是否与分析表示形式交互。

例如， `/metatadata/instance` 返回 json 对象：
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

如果我们要将响应仅筛选为计算属性，我们将发送请求： 
```bash
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

同样，如果要筛选到嵌套的属性或特定的数组元素，请保留追加密钥： 
```bash
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
将从属性筛选为第一个元素 `Network.interface` ，并返回：

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
> 筛选到叶节点时， `format=json` 不起作用。 对于这些查询，需要显式指定 `format=text`，因为默认格式为 json。

## <a name="schema"></a>架构

### <a name="data-format"></a>数据格式

默认情况下，IMDS 以 JSON 格式 () 返回数据 `Content-Type: application/json` 。 但是，支持响应筛选的端点 (参阅 [路由参数](#route-parameters)) 也支持此格式 `text` 。

若要访问非默认响应格式，请在请求中将所请求的格式指定为查询字符串参数。 例如：

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

在 json 响应中，所有基元都为类型 `string` ，缺少值或不适用值始终包括在内，但将设置为空字符串。

### <a name="versioning"></a>版本控制

IMDS 是版本控制的，并且 HTTP 请求中的 API 版本是必需的。 此要求的唯一例外是 [版本](#versions) 终结点，可用于动态检索可用的 API 版本。

在添加更新的版本时，早期版本仍可供访问以保持兼容性（如果脚本依赖于特定的数据格式）。

如果未指定版本，则会收到包含最新支持版本的列表的错误：
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

> [!NOTE]
> 当前正在推出版本 2020-10-01，它在某些区域可能尚不可用。

### <a name="swagger"></a>Swagger

以下位置提供了 IMDS 的完整 Swagger 定义： https://github.com/Azure/azure-rest-api-specs/blob/master/specification/imds/data-plane/readme.md

## <a name="regional-availability"></a>区域可用性

此服务在所有 Azure 云中正式发布。

## <a name="root-endpoint"></a>根终结点

根终结点为 `http://169.254.169.254/metadata` 。

## <a name="endpoint-categories"></a>终结点类别

IMDS API 包含表示不同数据源的多个终结点类别，其中每个都包含一个或多个终结点。 有关详细信息，请参阅每个类别。

| 类别根 | 说明 | 引入的版本 |
|---------------|-------------|--------------------|
| `/metadata/attested` | 请参阅[证明数据](#attested-data) | 2018-10-01
| `/metadata/identity` | 请参阅 [通过 IMDS 的托管标识](#managed-identity) | 2018-02-01
| `/metadata/instance` | 请参阅 [实例元数据](#instance-metadata) | 2017-04-02
| `/metadata/scheduledevents` | 请参阅 [通过 IMDS Scheduled Events](#scheduled-events) | 2017-08-01
| `/metadata/versions` | 查看 [版本](#versions) | 空值

## <a name="versions"></a>版本

> [!NOTE]
> 此功能与版本2020-10-01 一起发布，后者目前已推出，可能尚未在每个区域中提供。

### <a name="list-api-versions"></a>列出 API 版本

返回受支持的 API 版本集。

```bash
GET /metadata/versions
```

#### <a name="parameters"></a>参数

无 (此终结点) 未版本化。

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

公开 VM 实例的重要元数据，包括计算、网络和存储。 

```bash
GET /metadata/instance
```

#### <a name="parameters"></a>参数

| 名称 | 必需/可选 | 说明 |
|------|-------------------|-------------|
| `api-version` | 必须 | 用于处理请求的版本。
| `format` | 可选* | `json`响应 (或 `text`) 的格式。 * 注意：使用请求参数时可能需要

此终结点支持通过 [路由参数](#route-parameters)进行响应筛选。

#### <a name="response"></a>响应

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
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
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
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
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
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
        }]
    }
}
```

架构细目：

**计算**

| 数据 | 说明 | 引入的版本 |
|------|-------------|--------------------|
| `azEnvironment` | VM 运行时所在的 Azure 环境 | 2018-10-01
| `customData` | 此功能目前已禁用。 当该功能可用时，我们将更新此文档 | 2019-02-01
| `isHostCompatibilityLayerVm` | 标识 VM 是否在主机兼容性层上运行 | 2020-06-01
| `licenseType` | [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit)许可证的类型。 这仅适用于启用 AHB 的 Vm | 2020-09-01
| `location` | VM 在其中运行的 Azure 区域 | 2017-04-02
| `name` | VM 的名称 | 2017-04-02
| `offer` | 提供 VM 映像的信息，仅适用于从 Azure 映像库部署的映像 | 2017-04-02
| `osProfile.adminUsername` | 指定管理员帐户的名称 | 2020-07-15
| `osProfile.computerName` | 指定计算机的名称 | 2020-07-15
| `osProfile.disablePasswordAuthentication` | 指定是否禁用密码身份验证。 这仅适用于 Linux Vm | 2020-10-01
| `osType` | Linux 或 Windows | 2017-04-02
| `placementGroupId` | 虚拟机规模集的[放置组](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
| `plan` | 包含 VM 的名称、产品和发布者（如果是 Azure 市场映像）的[计划](/rest/api/compute/virtualmachines/createorupdate#plan) | 2018-04-02
| `platformUpdateDomain` |  正在运行 VM 的[更新域](../manage-availability.md) | 2017-04-02
| `platformFaultDomain` | 正在运行 VM 的[容错域](../manage-availability.md) | 2017-04-02
| `provider` | VM 的提供商 | 2018-10-01
| `publicKeys` | [公钥的集合](/rest/api/compute/virtualmachines/createorupdate#sshpublickey)，已分配给 VM 和路径 | 2018-04-02
| `publisher` | VM 映像的发布者 | 2017-04-02
| `resourceGroupName` | 虚拟机的[资源组](../../azure-resource-manager/management/overview.md) | 2017-08-01
| `resourceId` | 资源的[完全限定](/rest/api/resources/resources/getbyid) ID | 2019-03-11
| `sku` | VM 映像的特定 SKU | 2017-04-02
| `securityProfile.secureBootEnabled` | 标识是否在 VM 上启用了 UEFI 安全启动 | 2020-06-01
| `securityProfile.virtualTpmEnabled` | 标识是否在 VM 上启用了虚拟受信任的平台模块 (TPM) | 2020-06-01
| `storageProfile` | 请参阅下面的存储配置文件 | 2019-06-01
| `subscriptionId` | 虚拟机的 Azure 订阅 | 2017-08-01
| `tags` | 虚拟机的[标记](../../azure-resource-manager/management/tag-resources.md)  | 2017-08-01
| `tagsList` | 格式化为 JSON 数组以方便编程分析的标记  | 2019-06-04
| `version` | VM 映像的版本 | 2017-04-02
| `vmId` | VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
| `vmScaleSetName` | 虚拟机规模集的[虚拟机规模集名称](../../virtual-machine-scale-sets/overview.md) | 2017-12-01
| `vmSize` | [VM 大小](../sizes.md) | 2017-04-02
| `zone` | 虚拟机的[可用性区域](../../availability-zones/az-overview.md) | 2017-12-01

**存储配置文件**

VM 的存储配置文件分为三个类别：映像引用、OS 磁盘和数据磁盘。

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
| `lun`     | 磁盘的逻辑单元号
| `managedDisk` | 托管磁盘参数
| `name`    | 磁盘名称
| `vhd`     | 虚拟硬盘
| `writeAcceleratorEnabled` | 磁盘上是否启用了 writeAccelerator

数据磁盘阵列包含附加到 VM 的数据磁盘列表。 每个数据磁盘对象包含以下信息：

数据 | 说明 |
-----|-------------|
| `caching` | 缓存要求
| `createOption` | 有关 VM 创建方式的信息
| `diffDiskSettings` | 临时磁盘设置
| `diskSizeGB` | 磁盘大小 (GB)
| `encryptionSettings` | 磁盘的加密设置
| `image` | 源用户映像虚拟硬盘
| `managedDisk` | 托管磁盘参数
| `name` | 磁盘名称
| `osType` | 磁盘中包含的 OS 类型
| `vhd` | 虚拟硬盘
| `writeAcceleratorEnabled` | 磁盘上是否启用了 writeAccelerator

**Network**

| 数据 | 说明 | 引入的版本 |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | VM 的本地 IPv4 地址 | 2017-04-02
| `ipv4.publicIpAddress` | VM 的公共 IPv4 地址 | 2017-04-02
| `subnet.address` | VM 的子网地址 | 2017-04-02
| `subnet.prefix` | 子网前缀，例如 24 | 2017-04-02
| `ipv6.ipAddress` | VM 的本地 IPv6 地址 | 2017-04-02
| `macAddress` | VM mac 地址 | 2017-04-02

**VM 标记**

VM 标记包含在实例/计算/标记终结点下的实例 API。
标记可能已应用到 Azure VM 中，以逻辑方式将其归入一个分类。 可使用以下请求检索分配给 VM 的标记。

`tags` 字段是带有用分号分隔的标记的字符串。 如果标记本身使用了分号，则此输出可能会出现问题。 如果编写分析程序以编程方式提取标记，则应该依赖于 `tagsList` 字段。 `tagsList` 字段是不带分隔符的 JSON 数组，因此更易于分析。


#### <a name="sample-1-tracking-vm-running-on-azure"></a>示例 1：跟踪 Azure 上正在运行的 VM

作为服务提供商，可能需要跟踪运行软件的 VM 数目，或者代理需要跟踪 VM 的唯一性。 为了能够获取 VM 的唯一 ID，请使用实例元数据服务中的 `vmId` 字段。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**响应**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>示例2：放置不同的数据副本

对于某些方案，不同数据副本的放置至关重要。 例如，通过[orchestrator](https://kubernetes.io/docs/user-guide/node-selection/)的[HDFS 副本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)或容器放置可能要求你知道 `platformFaultDomain` ，并且 VM 正在 `platformUpdateDomain` 上运行。
你还可以使用实例的[可用性区域](../../availability-zones/az-overview.md)来做出这些决策。
可以通过 IMDS 直接查询此数据。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**响应**

```text
0
```

#### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>示例3：在支持案例期间获取有关 VM 的详细信息

作为服务提供商，你可能会接到支持电话，了解有关 VM 的详细信息。 请求客户共享计算元数据可以提供基本信息，以支持专业人员了解有关 Azure 上的 VM 类型。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
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
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
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
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
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

#### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>示例4：获取运行 VM 的 Azure 环境

Azure 具有各种主权云，如 [Azure 政府](https://azure.microsoft.com/overview/clouds/government/)。 有时你需要使用 Azure 环境来做出一些运行时决策。 以下示例显示了如何实现此行为。

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**响应**

```text
AzurePublicCloud
```

此处列出了 Azure 环境的云和值。

| 云 | Azure 环境 |
|-------|-------------------|
| [所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure 中国世纪互联](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure 德国](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-5-retrieve-network-information"></a>示例5：检索网络信息

**请求**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

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

#### <a name="sample-6-retrieve-public-ip-address"></a>示例6：检索公共 IP 地址

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="attested-data"></a>证明数据

### <a name="get-attested-data"></a>获取证明数据

IMDS 帮助确保提供的数据来自 Azure。 Microsoft 签署此信息的一部分，因此可以确认 Azure Marketplace 中的映像是在 Azure 上运行的映像。

```bash
GET /metadata/attested/document
```

#### <a name="parameters"></a>参数

| 名称 | 必需/可选 | 说明 |
|------|-------------------|-------------|
| `api-version` | 必须 | 用于处理请求的版本。
| `nonce` | 可选 | 用作加密 nonce 的10位字符串。 如果未提供任何值，则 IMDS 将使用当前的 UTC 时间戳。

#### <a name="response"></a>响应

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> 由于 IMDS 的缓存机制，可能会返回以前缓存的 nonce 值。

签名 blob 是文档的 [pkcs7](https://aka.ms/pkcs7)签名版本。 它包含用于签名的证书以及某些特定于 VM 的详细信息。

对于使用 Azure 资源管理器创建的 vm，文档包括 `vmId` 、、 `sku` 、 `nonce` `subscriptionId` 、 `timeStamp` 用于创建和过期文档，以及有关映像的计划信息。 该计划信息只针对 Azure 市场映像进行填充。

对于使用经典部署模型创建的 Vm，仅 `vmId` 保证填充。 你可以从响应中提取证书，并使用它来确认响应是否有效以及是否来自 Azure。

已解码的文档包含以下字段：

| 数据 | 说明 | 引入的版本 |
|------|-------------|--------------------|
| `licenseType` | [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit)许可证的类型。 这仅适用于启用 AHB 的 Vm。 | 2020-09-01
| `nonce` | 可以随请求提供的可选字符串。 如果未 `nonce` 提供，则使用当前的协调世界时时间戳。 | 2018-10-01
| `plan` | [Azure 市场映像计划](/rest/api/compute/virtualmachines/createorupdate#plan)。 包含计划 ID（名称）、产品映像或产品/服务（产品）和发布者 ID（发布者）。 | 2018-10-01
| `timestamp.createdOn` | 创建签名文档时的 UTC 时间戳 | 2018-20-01
| `timestamp.expiresOn` | 签名文档到期时的 UTC 时间戳 | 2018-10-01
| `vmId` | VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2018-10-01
| `subscriptionId` | 虚拟机的 Azure 订阅 | 2019-04-30
| `sku` | VM 映像的特定 SKU | 2019-11-01

> [!NOTE]
> 对于经典 (非 Azure 资源管理器) Vm，只保证可以填充 vmId。

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


#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>示例1：验证 VM 是否正在 Azure 中运行

Azure Marketplace 中的供应商需要确保其软件只能在 Azure 中运行。 如果有人将 VHD 复制到本地环境，则供应商需要能够检测到这一点。 通过 IMDS，这些供应商可以获得仅保证来自 Azure 的响应的签名数据。

> [!NOTE]
> 此示例需要安装 jq 实用程序。

**验证**

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

验证签名是否来自 Microsoft Azure，并检查证书链中是否存在错误。

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

> [!NOTE]
> 由于 IMDS 的缓存机制， `nonce` 可能会返回以前缓存的值。

`nonce`如果在 `nonce` 初始请求中提供了参数，则可以比较签名文档中的。

> [!NOTE]
> 公有云和每个主权云的证书将不同。

| 云 | 证书 |
|-------|-------------|
| [所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure 中国世纪互联](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure 德国](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> 对于公有云，证书可能不完全匹配 `metadata.azure.com` 。 出于此原因，证书验证应允许来自任何子域的公用名称 `.metadata.azure.com` 。

如果在验证期间由于网络限制而无法下载中间证书，则可以固定中间证书。 Azure 会滚动证书，这是标准的 PKI 实践。 发生滚动更新时，必须更新固定证书。 每当计划对更新中间证书的更改时，都会更新 Azure 博客，并通知 Azure 客户。 

可以在 [PKI 存储库](https://www.microsoft.com/pki/mscorp/cps/default.htm)中找到中间证书。 每个区域的中间证书可能不同。

> [!NOTE]
> Azure 中国世纪互联的中间证书将来自 DigiCert Global Root CA，而不是巴尔的摩。
如果将 Azure 中国区的中间证书固定为根链证书颁发机构的一部分，则必须更新中间证书。


## <a name="managed-identity"></a>托管标识

可以在 VM 上启用由系统分配的托管标识。 你还可以将一个或多个用户分配的托管标识分配给 VM。
然后，可以从 IMDS 请求托管标识的令牌。 使用这些令牌对其他 Azure 服务（如 Azure Key Vault）进行身份验证。

有关启用此功能的详细步骤，请参阅[获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

## <a name="scheduled-events"></a>计划事件
您可以使用 IMDS 获取计划事件的状态。 然后，用户可以指定要对这些事件运行的一组操作。 有关详细信息，请参阅 [计划事件](scheduled-events.md)。

## <a name="sample-code-in-different-languages"></a>不同语言的示例代码

下表列出了在 VM 内使用不同语言调用 IMDS 的示例：

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
| `405 Method Not Allowed` | 此终结点不支持 HTTP 方法)  (谓词。
| `410 Gone` | 在一段时间后重试最长 70 秒
| `429 Too Many Requests` | 已超过 API[速率限制](#rate-limiting)
| `500 Service Error` | 请稍后重试

## <a name="frequently-asked-questions"></a>常见问题

**我收到了错误 `400 Bad Request, Required metadata header not specified` 。这是什么意思？**

IMDS 要求 `Metadata: true` 在请求中传递标头。 通过在 REST 调用中传递此标头，可以访问 IMDS。

**为什么我无法获取我的 VM 的计算信息？**

目前，IMDS 仅支持通过 Azure 资源管理器创建的实例。

**我通过 Azure 资源管理器一段时间创建了 VM。为什么我看不到计算元数据信息？**

如果在2016年9月之后创建了 VM，请添加 [标记](../../azure-resource-manager/management/tag-resources.md) 以开始查看计算元数据。 如果在2016年9月之前创建了 VM，请在 VM 实例中添加或删除扩展或数据磁盘以刷新元数据。

**为什么看不到为新版本填充的所有数据？**

如果在2016年9月之后创建了 VM，请添加 [标记](../../azure-resource-manager/management/tag-resources.md) 以开始查看计算元数据。 如果在2016年9月之前创建了 VM，请在 VM 实例中添加或删除扩展或数据磁盘以刷新元数据。

**为什么会收到错误 `500 Internal Server Error` 或 `410 Resource Gone` ？**

请重试你的请求。 有关详细信息，请参阅 [暂时性故障处理](/azure/architecture/best-practices/transient-faults)。 如果问题仍然存在，请在 VM 的 Azure 门户中创建支持问题。

**这是否适用于虚拟机规模集实例？**

是的，IMDS 适用于虚拟机规模集实例。

**我更新了虚拟机规模集中的标记，但它们不会在实例中出现 (与单实例 Vm) 不同。我怎么办？**

当前，虚拟机规模集的标记仅向 VM 显示对实例的重新启动、重置映像或磁盘更改。

**为什么我的请求对服务的调用超时？**

必须从分配给 VM 的主要网卡的主 IP 地址进行元数据调用。 此外，如果你更改了路由，则 VM 的本地路由表中必须有 169.254.169.254/32 地址的路由。

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
1. 验证是否存在路由 `169.254.169.254` ，并记下相应的网络接口 (示例 `172.16.69.7`) 。
1. 转储接口配置并查找与路由表中所引用的接口相对应的接口，并注明 MAC (物理) 地址。
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
1. 确认该接口对应于 VM 的主 NIC 和主 IP。 可以通过查看 Azure 门户中的网络配置，或通过查找 Azure CLI 来查找主 NIC 和 IP。 如果使用的是 CLI) ，请注意公共和专用 Ip (和 MAC 地址。 下面是 PowerShell CLI 示例：
    ```powershell
    $ResourceGroup = '<Resource_Group>'
    $VmName = '<VM_Name>'
    $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json |Foreach-Object { $_.  id.Split('/')[-1] }
    foreach($NicName in $NicNames)
    {
        $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName ConvertFrom-Json
        Write-Host $NicName, $Nic.primary, $Nic.macAddress
    }
    # Output: wintest767 True 00-0D-3A-E5-1C-C0
    ```
1. 如果二者不匹配，则更新路由表，以便将主 NIC 和 IP 作为目标。

## <a name="support"></a>支持

如果多次尝试后无法获取元数据响应，则可以在 Azure 门户中创建支持问题。

## <a name="product-feedback"></a>产品反馈

你可以在 "虚拟机" 下的 "虚拟机" 下 > 实例元数据服务提供产品反馈和想法： https://feedback.azure.com/forums/216843-virtual-machines?category_id=394627

## <a name="next-steps"></a>后续步骤

[获取 VM 的访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[计划事件](scheduled-events.md)

