---
title: 关于 Azure Bastion 配置设置
description: 了解 Azure Bastion 的可用配置设置。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: cherylmc
ms.openlocfilehash: 0f73cc08b2e6e44508a6535ba0d6c420e998a011
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732019"
---
# <a name="about-bastion-configuration-settings"></a>关于 Bastion 配置设置

本文中的各个部分介绍 Azure Bastion 的资源和设置。

## <a name="skus"></a><a name="skus"></a>SKU

SKU 也称为“层”。 Azure Bastion 支持两种 SKU 类型：“基本”和“标准”。 SKU 是在执行工作流期间配置 Bastion 时在 Azure 门户中配置的。 可以[将基本 SKU 升级到标准 SKU](#upgradesku)。

* 基本 SKU 提供基本功能，使 Azure Bastion 能够管理与虚拟机 (VM) 的 RDP/SSH 连接，而无需在目标应用程序 VM 上公开公共 IP 地址。 
* 标准 SKU 目前为预览版 。 标准 SKU 可启用高级功能，使 Azure Bastion 能够更大规模地管理远程连接。 

下表显示了各种功能及对应的 SKU。 

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

### <a name="configuration-methods"></a>配置方法

在预览期，如果你要指定标准 SKU，则必须使用 Azure 门户。 如果使用 Azure CLI 或 Azure PowerShell 来配置 Bastion，则无法指定 SKU，默认会使用基本 SKU。

| 方法 | 值 | 链接 |
| --- | --- | --- |
| Azure 门户 | 层 - 基本或 <br>标准（预览） | [快速入门 - 从 VM 设置配置 Bastion](quickstart-host-portal.md)<br>[教程 - 配置 Bastion](tutorial-create-host-portal.md) |
| Azure PowerShell | 仅基本 - 无设置 |[配置 Bastion - PowerShell](bastion-create-host-powershell.md) |
| Azure CLI |  仅基本 - 无设置 | [配置 Bastion - CLI](create-host-cli.md) |

### <a name="upgrade-a-sku"></a><a name="upgradesku"></a>升级 SKU

Azure Bastion支持从基本 SKU 升级到标准 SKU。 标准 SKU 目前为预览版。 

> [!NOTE]
> 不支持从标准 SKU 降级到基本 SKU。 若要降级，必须删除并重新创建 Azure Bastion。
>

#### <a name="configuration-methods"></a>配置方法

可使用以下方法配置此设置：

| 方法 | 值 | 链接 |
| --- | --- | --- |
| Azure 门户 |层  | [升级 SKU - 预览版](upgrade-sku.md)|

## <a name="instances-and-host-scaling-preview"></a><a name="instance"></a>实例和主机缩放（预览版）

实例是配置 Azure Bastion 时创建的已优化 Azure VM。 它完全由 Azure 管理，运行 Azure Bastion 所需的所有进程。 实例也称为缩放单元。 通过 Azure Bastion 实例连接到客户端 VM。 使用基本 SKU 配置 Azure Bastion 时，将创建两个实例。 如果使用标准 SKU，则可以指定实例数。 这称为“主机缩放”。 

每个实例可以支持 10-12 个并发 RDP/SSH 连接。 每个实例的连接数取决于连接到客户端 VM 时执行的操作。 例如，如果执行某种数据密集型操作，则会创建较大的负载供实例处理。 超过并发会话数后，需要创建额外的缩放单元（实例）。 

实例是在 AzureBastionSubnet 中创建的。 要进行主机缩放，AzureBastionSubnet 应为 /26 或更大的值。 使用较小的子网会限制可创建的实例数。 有关 AzureBastionSubnet 的详细信息，请参阅本文中的[子网](#subnet)部分。

### <a name="configuration-methods"></a>配置方法

可使用以下方法配置此设置：

| 方法 | 值 | 链接 |
| --- | --- | --- |
| Azure 门户 |实例计数  | [配置主机缩放 - 预览版](configure-host-scaling.md)|


## <a name="azure-bastion-subnet"></a><a name="subnet"></a>Azure Bastion 子网

Azure Bastion 需要一个专用子网：AzureBastionSubnet。 需要在 Azure Bastion 所部署到的同一个虚拟网络中创建此子网。 该子网必须采用以下配置：

* 子网名称必须是 AzureBastionSubnet。
* 子网大小必须是 /27 或更大的值（/26、/25 等）。
* 要进行主机缩放，建议使用 /26 或更大的子网。 使用较小的子网空间会限制缩放单元的数量。 有关详细信息，请参阅本文的[主机缩放](#instance)部分。
* 该子网必须与堡垒主机位于同一 VNet 和资源组中。
* 该子网不能包含其他资源。

### <a name="configuration-methods"></a>配置方法

可使用以下方法配置此设置：

| 方法 | 值 | 链接 |
| --- | --- |--- |
| Azure 门户 | 子网  |[快速入门 - 从 VM 设置配置 Bastion](quickstart-host-portal.md)<br>[教程 - 配置 Bastion](tutorial-create-host-portal.md)|
| Azure PowerShell | -subnetName|[cmdlet](/powershell/module/az.network/new-azbastion#parameters) |
| Azure CLI |  --subnet-name | [command](/cli/azure/network/vnet#az_network_vnet_create) |

## <a name="public-ip-address"></a><a name="public-ip"></a>公共 IP 地址

Azure Bastion 需要一个公共 IP 地址。 该公共 IP 必须采用以下配置：

* 公共 IP 地址 SKU 必须为“标准”。
* 公共 IP 地址分配方法必须为“静态”。
* 公共 IP 地址名称是要用于引用此公共 IP 地址的资源名称。
* 可以选择使用已创建的公共 IP 地址，前提是该地址符合 Azure Bastion 所需的条件，并且未被使用。

### <a name="configuration-methods"></a>配置方法

可使用以下方法配置此设置：

| 方法 | 值 | 链接 |
| --- | --- |--- |
| Azure 门户 | 公共 IP 地址 |[Azure 门户](https://portal.azure.com)|
| Azure PowerShell | -PublicIpAddress| [cmdlet](/powershell/module/az.network/new-azbastion#parameters)  |
| Azure CLI | --public-ip create |[command](/cli/azure/network/public-ip)
|

## <a name="next-steps"></a>后续步骤

有关常见问题解答，请参阅 [Azure Bastion 常见问题解答](bastion-faq.md)。
