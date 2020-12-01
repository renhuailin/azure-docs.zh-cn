---
title: 适用于 Windows 的 Azure 实例元数据服务
description: 了解 Azure 实例元数据服务，以及它如何提供有关 Windows 中当前正在运行的虚拟机实例的信息。
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: cc0d47807101a3cfbb26e7ea69cc7d117d3f9b31
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435223"
---
# <a name="azure-instance-metadata-service"></a>Azure 实例元数据服务

Azure 实例元数据服务 (IMDS) 提供有关当前正在运行的虚拟机实例的信息。 你可以使用它来管理和配置虚拟机。
这些信息包括 SKU、存储、网络配置和即将发生的维护事件。 有关可用数据的完整列表，请参阅 [元数据 api](#metadata-apis)。


IMDS 可用于运行 (Vm) 和虚拟机规模集实例的虚拟机实例。 所有 Api 都支持使用 [Azure 资源管理器](/rest/api/resources/)创建和管理的 vm。 只有证明和网络终结点支持使用经典部署模型创建的 Vm。 证明终结点仅在有限范围内执行此操作。

IMDS 是一个 REST 终结点，该终结点在已知的、不可路由的 IP 地址 (`169.254.169.254`) 中可用。 只能从 VM 中访问。 VM 与 IMDS 之间的通信绝不会离开主机。
查询 IMDS 时，让 HTTP 客户端绕过 VM 中的 web 代理，并 `169.254.169.254` 将其视为相同 [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) 。

## <a name="security"></a>安全性

只能从不可路由的 IP 地址上正在运行的虚拟机实例中访问 IMDS 终结点。 此外，具有标头的任何请求 `X-Forwarded-For` 都将被服务拒绝。
请求还必须包含 `Metadata: true` 标头，以确保实际请求是直接预期的，而不是无意重定向的一部分。

> [!IMPORTANT]
> IMDS 不是敏感数据的通道。 此终结点对于 VM 上的所有进程都是开放的。 将通过此服务公开的信息视为 VM 内运行的所有应用程序的共享信息。

## <a name="usage"></a>使用情况

### <a name="access-azure-instance-metadata-service"></a>访问 Azure 实例元数据服务

若要访问 IMDS，请从 [Azure 资源管理器](/rest/api/resources/) 或 [Azure 门户](https://portal.azure.com)创建 VM，并使用以下示例。
有关更多示例，请参阅 [Azure 实例元数据示例](https://github.com/microsoft/azureimds)。

下面是检索实例的所有元数据的示例代码。 若要访问特定的数据源，请参阅 [元数据 API](#metadata-apis) 部分。 

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-09-01 | ConvertTo-Json
```
> [!NOTE]
> `-NoProxy`标志仅在 PowerShell 6 或更高版本中可用。 如果没有代理设置，则可以省略该标志。

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 通过 cmdlet 将 REST 查询传递 `ConvertTo-Json` 给良好打印。

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

### <a name="data-output"></a>数据输出

默认情况下，IMDS 以 JSON 格式 () 返回数据 `Content-Type: application/json` 。 但是，如果请求，某些 Api 可以返回不同格式的数据。
下表列出了 Api 可能支持的其他数据格式。

API | 默认数据格式 | 其他格式
--------|---------------------|--------------
/attested | json | 无
/identity | json | none
/instance | json | text
/scheduledevents | json | 无

若要访问非默认响应格式，请在请求中将所请求的格式指定为查询字符串参数。 例如：

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> 对于中的叶节点 `/metadata/instance` ， `format=json` 不起作用。 对于这些查询， `format=text` 需要显式指定，因为默认格式为 JSON。

### <a name="version"></a>版本

IMDS 是版本控制的，并且必须在 HTTP 请求中指定 API 版本。

支持的 API 版本有： 
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
> 版本2020-10-01 可能尚未在每个区域中提供。

添加较新的版本时，如果脚本依赖于特定的数据格式，则仍可访问早期版本的兼容性。

如果未指定版本，则会收到一个错误，其中包含最新的受支持版本的列表。

> [!NOTE]
> 此响应是 JSON 字符串。 下面的示例指示未指定版本时的错误条件。 为了便于阅读，响应的效果很好。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**响应**

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

## <a name="metadata-apis"></a>元数据 API

IMDS 包含多个表示不同数据源的 Api。

API | 说明 | 引入的版本
----|-------------|-----------------------
/attested | 请参阅 [证明数据](#attested-data) | 2018-10-01
/identity | 请参阅[获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | 请参阅[实例 API](#instance-api) | 2017-04-02
/scheduledevents | 查看 [计划事件](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>实例 API

实例 API 公开 VM 实例的重要元数据，其中包括 VM、网络和存储。 可以通过以下类别访问以下类别 `instance/compute` ：

数据 | 说明 | 引入的版本
-----|-------------|-----------------------
azEnvironment | 运行 VM 的 Azure 环境。 | 2018-10-01
customData | 此功能目前已禁用。 | 2019-02-01
isHostCompatibilityLayerVm | 标识 VM 是否在主机兼容性层上运行。 | 2020-06-01
licenseType | [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit)的许可证类型。 请注意，这仅适用于启用 AHB 的 Vm。 | 2020-09-01
location | 正在运行 VM 的 Azure 区域。 | 2017-04-02
name | VM 的名称。 | 2017-04-02
offer | 为 VM 映像提供信息。 这仅适用于从 Azure 映像库部署的映像。 | 2017-04-02
osProfile.adminUsername | 指定管理员帐户的名称。 | 2020-07-15
osProfile | 指定计算机的名称。 | 2020-07-15
osProfile. disablePasswordAuthentication | 指定是否禁用密码身份验证。 请注意，这仅适用于 Linux Vm。 | 2020-10-01
osType | Linux 或 Windows。 | 2017-04-02
placementGroupId | 虚拟机规模集的[放置组](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)。 | 2017-08-01
计划 | 包含 VM 的名称、产品和发布者的[计划](/rest/api/compute/virtualmachines/createorupdate#plan)（如果它是 Azure Marketplace 映像）。 | 2018-04-02
platformUpdateDomain |  [更新](../manage-availability.md) 运行 VM 的域。 | 2017-04-02
platformFaultDomain | 运行 VM 的[容错域](../manage-availability.md)。 | 2017-04-02
provider | VM 的提供程序。 | 2018-10-01
publicKeys | 分配给 VM 和路径[的公钥的集合](/rest/api/compute/virtualmachines/createorupdate#sshpublickey)。 | 2018-04-02
publisher | VM 映像的发布者。 | 2017-04-02
resourceGroupName | VM 的[资源组](../../azure-resource-manager/management/overview.md)。 | 2017-08-01
ResourceId | 资源的 [完全限定](/rest/api/resources/resources/getbyid) ID。 | 2019-03-11
sku | VM 映像的特定 SKU。 | 2017-04-02
securityProfile.secureBootEnabled | 标识是否在 VM 上启用了 UEFI 安全启动。 | 2020-06-01
securityProfile.virtualTpmEnabled | 确定虚拟受信任的平台模块 (是否在 VM 上启用了 TPM) 。 | 2020-06-01
storageProfile | 请参阅 [存储配置文件](#storage-metadata)。 | 2019-06-01
subscriptionId | VM 的 Azure 订阅。 | 2017-08-01
tags | VM 的[标记](../../azure-resource-manager/management/tag-resources.md)。  | 2017-08-01
tagsList | 格式化为 JSON 数组的标记，便于编程分析。  | 2019-06-04
版本 | VM 映像的版本。 | 2017-04-02
vmId | VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)。 | 2017-04-02
vmScaleSetName | 虚拟机规模集的虚拟机规模集[名称](../../virtual-machine-scale-sets/overview.md)。 | 2017-12-01
vmSize | 请参阅 [VM 大小](../sizes.md)。 | 2017-04-02
区域 | VM 的[可用性区域](../../availability-zones/az-overview.md)。 | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>示例1：跟踪在 Azure 上运行的 VM

作为服务提供商，可能需要跟踪运行软件的 Vm 的数量，或者需要跟踪 VM 的唯一性的代理。 若要获取 VM 的唯一 ID，请使用 `vmId` IMDS 中的字段。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**响应**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>示例2：放置不同的数据副本

对于某些方案，不同数据副本的放置至关重要。 例如，通过[orchestrator](https://kubernetes.io/docs/user-guide/node-selection/)的[HDFS 副本放置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)或容器放置可能要求你知道 `platformFaultDomain` ，并且 VM 正在 `platformUpdateDomain` 上运行。
你还可以使用实例的[可用性区域](../../availability-zones/az-overview.md)来做出这些决策。
可以通过 IMDS 直接查询此数据。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**响应**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>示例3：在支持案例期间获取有关 VM 的详细信息

作为服务提供商，你可能会收到一个支持调用，你需要了解有关 VM 的详细信息。 在这种情况下，要求客户共享计算元数据会很有用。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01
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

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>示例4：获取运行 VM 的 Azure 环境

Azure 具有各种主权云，例如 [Azure 政府](https://azure.microsoft.com/overview/clouds/government/)。 有时，你需要 Azure 环境来做出某些运行时决策。 以下示例显示了如何实现此行为。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**响应**

```text
AzurePublicCloud
```

此处列出了 Azure 环境的云和值。

 云   | Azure 环境
---------|-----------------
[所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure 中国世纪互联](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>网络元数据 

网络元数据是实例 API 的一部分。 可以通过终结点使用以下网络类别 `instance/network` 。

数据 | 说明 | 引入的版本
-----|-------------|-----------------------
ipv4/privateIpAddress | VM 的本地 IPv4 地址。 | 2017-04-02
ipv4/publicIpAddress | VM 的公共 IPv4 地址。 | 2017-04-02
subnet/address | VM 的子网地址。 | 2017-04-02
subnet/prefix | 子网前缀。 示例：24 | 2017-04-02
ipv6/ipAddress | VM 的本地 IPv6 地址。 | 2017-04-02
macAddress | VM mac 地址。 | 2017-04-02

> [!NOTE]
> 所有 API 响应都是 JSON 字符串。 以下所有示例响应显示清晰，可供阅读。

#### <a name="sample-1-retrieve-network-information"></a>示例1：检索网络信息

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
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

#### <a name="sample-2-retrieve-public-ip-address"></a>示例2：检索公共 IP 地址

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>存储元数据

存储元数据是此终结点下的实例 API 的一部分 `instance/compute/storageProfile` 。
它提供与 VM 关联的存储磁盘的详细信息。 

VM 的存储配置文件分为三个类别：映像引用、操作系统磁盘和数据磁盘。

Image reference 对象包含有关操作系统映像的下列信息：

数据    | 说明
--------|-----------------
id      | 资源 ID
offer   | 平台或映像的产品/服务
publisher | 映像发布者
sku     | 映像 SKU
版本 | 平台或映像的版本

操作系统磁盘对象包含有关 VM 使用的操作系统磁盘的以下信息：

数据    | 说明
--------|-----------------
caching | 缓存要求
createOption | 有关 VM 创建方式的信息
diffDiskSettings | 临时磁盘设置
diskSizeGB | 磁盘大小 (GB)
encryptionSettings | 磁盘的加密设置
image   | 源用户映像虚拟硬盘
managedDisk | 托管磁盘参数
name    | 磁盘名称
osType  | 磁盘中包含的操作系统类型
vhd     | 虚拟硬盘
writeAcceleratorEnabled | `writeAccelerator`磁盘上是否已启用

数据磁盘阵列包含附加到 VM 的数据磁盘列表。 每个数据磁盘对象包含以下信息：

数据    | 说明
--------|-----------------
caching | 缓存要求
createOption | 有关 VM 创建方式的信息
diffDiskSettings | 临时磁盘设置
diskSizeGB | 磁盘大小 (GB)
image   | 源用户映像虚拟硬盘
lun     | 磁盘的逻辑单元号
managedDisk | 托管磁盘参数
name    | 磁盘名称
vhd     | 虚拟硬盘
writeAcceleratorEnabled | `writeAccelerator`磁盘上是否已启用

以下示例演示如何查询 VM 的存储信息。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
```

**响应**

> [!NOTE]
> 此响应是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

```json
{
    "dataDisks": [
      {
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
      }
    ],
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
}
```

## <a name="vm-tags"></a>VM 标记

VM 标记包含在终结点下的实例 API 中 `instance/compute/tags` 。
标记可能已应用到 Azure VM 以逻辑方式将它们组织到分类。 可以使用以下请求检索分配给 VM 的标记。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**响应**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` 字段是带有用分号分隔的标记的字符串。 如果标记本身使用了分号，则此输出可能会出现问题。 如果编写分析程序以编程方式提取标记，则应该依赖于 `tagsList` 字段。 该 `tagsList` 字段是不带分隔符的 JSON 数组，因此更易于分析。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
```

**响应**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>证明数据

IMDS 帮助确保提供的数据来自 Azure。 Microsoft 签署此信息的一部分，因此可以确认 Azure Marketplace 中的映像是在 Azure 上运行的映像。

### <a name="sample-1-get-attested-data"></a>示例1：获取证明的数据

> [!NOTE]
> 所有 API 响应都是 JSON 字符串。 以下示例响应显示清晰，可供阅读。

**请求**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> 由于 IMDS 的缓存机制， `nonce` 可能会返回以前缓存的值。

`Api-version` 是必填字段。 有关支持的 API 版本的信息，请参阅[使用部分](#usage)。
`Nonce` 是一个可选的10位字符串。 如果未提供此项，IMDS 将返回当前协调世界时的时间戳。

**响应**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

签名 blob 是文档的 [pkcs7](https://aka.ms/pkcs7)签名版本。 它包含用于签名的证书，以及特定于 VM 的特定详细信息。 

对于使用 Azure 资源管理器创建的 vm，这包括 `vmId` 、、、 `sku` `nonce` `subscriptionId` 、 `timeStamp` 用于创建和过期文档以及有关映像的计划信息。 该计划信息只针对 Azure 市场映像进行填充。 

对于使用经典部署模型创建的 Vm，仅 `vmId` 保证填充。 你可以从响应中提取证书，并使用它来确认响应是否有效以及是否来自 Azure。

该文档包含以下字段：

数据 | 说明 | 引入的版本
-----|-------------|-----------------------
licenseType | [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit)的许可证类型。 请注意，这仅适用于启用 AHB 的 Vm。 | 2020-09-01
nonce | 可以随请求提供的可选字符串。 如果未 `nonce` 提供，则使用当前的协调世界时时间戳。 | 2018-10-01
plan | [Azure 市场映像计划](/rest/api/compute/virtualmachines/createorupdate#plan)。 包含计划 ID (名称) 、产品图像或产品/服务 (产品) ，发布者 ID (发布者) 。 | 2018-10-01
timestamp/createdOn | 创建签名文档时的协调通用时间时间戳。 | 2018-20-01
timestamp/expiresOn | 签名文档过期时的协调世界时时间戳。 | 2018-10-01
vmId |  VM 的[唯一标识符](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)。 | 2018-10-01
subscriptionId | VM 的 Azure 订阅。 | 2019-04-30
sku | VM 映像的特定 SKU。 | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>示例2：验证 VM 是否正在 Azure 中运行

Azure Marketplace 中的供应商需要确保其软件只能在 Azure 中运行。 如果有人将 VHD 复制到本地环境，则供应商需要能够检测到这一点。 通过 IMDS，这些供应商可以获得仅保证来自 Azure 的响应的签名数据。

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

> [!NOTE]
> 由于 IMDS 的缓存机制， `nonce` 可能会返回以前缓存的值。

`nonce`如果在 `nonce` 初始请求中提供了参数，则可以比较签名文档中的。

> [!NOTE]
> 公有云和每个主权云的证书将不同。

云 | 证书
------|------------
[所有公开上市的 Azure 区域](https://azure.microsoft.com/regions/) | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *.metadata.azure.us
[Azure 中国世纪互联](https://azure.microsoft.com/global-infrastructure/china/)     | *.metadata.azure.cn
[Azure 德国](https://azure.microsoft.com/overview/clouds/germany/)                | *.metadata.microsoftazure.de

> [!NOTE]
> 对于公有云，证书可能不完全匹配 `metadata.azure.com` 。 出于此原因，证书验证应允许来自任何子域的公用名称 `.metadata.azure.com` 。

如果在验证期间由于网络限制而无法下载中间证书，则可以固定中间证书。 请注意，Azure 会滚动证书，这是标准的 PKI 实践。 发生滚动更新时，需要更新固定证书。 每当计划对更新中间证书的更改时，都会更新 Azure 博客，并通知 Azure 客户。 

可以在 [PKI 存储库](https://www.microsoft.com/pki/mscorp/cps/default.htm)中找到中间证书。 每个区域的中间证书可能不同。

> [!NOTE]
> Azure 中国世纪互联的中间证书来自 DigiCert Global Root CA，而不是巴尔的摩。
如果将 Azure 中国区的中间证书固定为根链证书颁发机构的一部分，则必须更新中间证书。

## <a name="failover-clustering-in-windows-server"></a>Windows Server 中的故障转移群集

使用故障转移群集查询 IMDS 时，有时需要将路由添加到路由表。 方法如下：

1. 使用管理员特权打开命令提示符。

1. 运行以下命令，并记下 `0.0.0.0` IPv4 路由表中用于网络目标 () 的接口地址。

```bat
route print
```

> [!NOTE]
> 以下示例输出来自启用了故障转移群集的 Windows Server VM。 为简单起见，输出只包含 IPv4 路由表。

```text
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

运行以下命令，并使用网络目标 () 的接口地址，该接口 `0.0.0.0` `10.0.1.10` 在此示例中 () 。

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity"></a>托管标识

可以在 VM 上启用由系统分配的托管标识。 你还可以将一个或多个用户分配的托管标识分配给 VM。
然后，可以从 IMDS 请求托管标识的令牌。 使用这些令牌对其他 Azure 服务（如 Azure Key Vault）进行身份验证。

有关启用此功能的详细步骤，请参阅[获取访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

## <a name="scheduled-events"></a>计划事件
您可以使用 IMDS 获取计划事件的状态。 然后，用户可以指定要对这些事件运行的一组操作。 有关详细信息，请参阅 [计划事件](scheduled-events.md)。 

## <a name="regional-availability"></a>区域可用性

服务在所有 Azure 云中公开发布。

## <a name="sample-code-in-different-languages"></a>不同语言的示例代码

下表列出了在 VM 内使用不同语言调用 IMDS 的示例：

语言      | 示例
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Go            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="errors-and-debugging"></a>错误和调试

如果找不到数据元素或请求格式不正确，则 IMDS 将返回标准 HTTP 错误。 例如：

HTTP 状态代码 | Reason
-----------------|-------
200 正常 |
400 错误的请求 | `Metadata: true`查询叶节点时缺少标头或缺少参数 `format=json` 。
404 未找到  | 请求的元素不存在。
不允许 405 方法 | 仅 `GET` 支持请求。
410 不存在 | 请在一段时间后重试，最大为70秒。
429 请求次数过多 | API 当前支持每秒最多5个查询。
500 服务错误     | 请稍后重试。

### <a name="frequently-asked-questions"></a>常见问题

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
   * <details>
        <summary>验证路由表</summary>

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
        1. 验证是否存在路由 `169.254.169.254` ，并记下相应的网络接口 (例如 `172.16.69.7`) 。
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
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. 如果二者不匹配，则更新路由表，以便将主 NIC 和 IP 作为目标。
    </details>

## <a name="support"></a>支持

如果多次尝试后无法获取元数据响应，则可以在 Azure 门户中创建支持问题。
对于 " **问题类型**"，选择 " **管理**"。 对于 " **类别**"，选择 " **实例元数据服务**"。

![实例元数据服务支持的屏幕截图](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>后续步骤

[获取 VM 的访问令牌](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[计划事件](scheduled-events.md)
