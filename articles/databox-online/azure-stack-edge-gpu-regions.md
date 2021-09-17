---
title: 选择适用于 Microsoft Azure Stack Edge Pro with GPU 的区域 | Microsoft Docs
description: 说明如何选择适用于 Azure Stack Edge 服务、数据存储、Azure Stack Edge Pro with GPU 专用设备、Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 的区域。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/18/2021
ms.author: alkohli
ms.openlocfilehash: 7b585c61dd46339be71e66be66b402a0e107c194
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429821"
---
# <a name="choosing-a-region-for-azure-stack-edge"></a>选择适用于 Azure Stack Edge 的区域

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

本文介绍 Azure Stack Edge 服务、数据存储和 Azure Stack Edge 设备发货的区域可用性，并说明区域选择将会如何影响服务、性能和计费。


## <a name="overview"></a>概述

Azure 数据中心在全球多个地理位置运营，目的在于满足客户在性能、要求和数据位置偏好方面的需求。 Azure 地理位置指至少包含一个 Azure 区域的划定世界区域。 Azure 区域是包含一个或多个数据中心的地理位置内的某个区域。

选择 Azure 区域非常重要，并且区域的选择受各种因素影响，例如数据驻留和数据主权、服务可用性、性能、成本和冗余。 有关如何选择区域的详细信息，请参阅[哪一个 Azure 区域适合我？](https://azure.microsoft.com/overview/datacenters/how-to-choose/)。

对于 Azure Stack Edge Pro GPU 设备，区域的选择取决于以下因素：

- 提供 Azure Stack Edge 服务的区域。
- 存储 Azure Stack Edge 数据的存储帐户应位于性能最佳的区域。
- 可以派送 Azure Stack Edge 设备的国家/地区。


## <a name="region-availability-for-the-service"></a>服务的区域可用性

目前，美国东部、欧洲西部和东南亚公共区域均支持提供 Azure Stack Edge 服务。 这三个区域支持世界各地的地理位置。

服务区域指分配给 Azure Stack Edge 管理资源的国家或地区。 管理资源使用 Azure Stack Edge 服务来激活、部署和返还 Azure Stack Edge 设备。

Azure Stack Edge 服务还可监视设备的运行状况，包括问题、错误、警报以及设备是否“处于活动状态”。 通过查看设备上的控制面板信息，该服务可监视设备的使用情况和损耗计费表。 分配给管理资源的区域即为发生计费的区域。

设备必须连接到要激活的 Azure Stack Edge 服务。 如果不希望与服务进一步交互，可以将设备切换为断开连接模式。 

数据不会流经 Azure Stack Edge 服务。 数据将会在设备与存储帐户（部署在客户的数据来源区域）之间流动。 

一般情况下，请选择离部署设备的地理区域最近的位置。 不过，设备和服务也可部署在不同的位置。

## <a name="region-availability-for-data-storage"></a>数据存储的区域可用性

Azure Stack Edge 数据存储在 Azure 存储帐户中，并且这些帐户在所有 Azure 区域均可用。 创建 Azure 存储帐户时，选择该存储帐户的主要位置。 该选择将会确定数据的驻留区域。

请[在设备上创建共享时](azure-stack-edge-gpu-deploy-add-shares.md#add-a-share)选择存储帐户。 Azure Stack Edge 服务和 Azure 存储可位于两个不同的位置。

通常情况下，选择离存储帐户的服务最近的区域。 不过，最近的 Microsoft Azure 区域实际上不一定是延迟最低的区域。 延迟会影响网络服务的性能，因而决定设备的性能。 因此，如果是在不同的区域选择存储帐户，则一定要了解服务与关联存储帐户的区域之间的延迟情况。

## <a name="region-of-device"></a>设备区域

若要了解提供不同 Azure Stack Edge 型号的国家/地区和地理区域，请参阅产品概述：

- [适用于 Azure Stack Edge Pro with GPU 的区域可用性](azure-stack-edge-gpu-overview.md#region-availability)
- [适用于 Azure Stack Edge Pro R 的区域可用性](azure-stack-edge-pro-r-overview.md#region-availability)
- [适用于 Azure Stack Edge Mini R 的区域可选性](azure-stack-edge-mini-r-overview.md#region-availability)

Microsoft 可以向这些地理区域运送 Azure Stack Edge 物理硬件并提供用于替换的硬件备用部件。

> [!IMPORTANT]
> 不要将 Azure Stack Edge 物理设备放置在不支持 Azure Stack Edge 的区域。 Microsoft 不会向不支持 Azure Stack Edge 的国家/地区寄送任何替换部件。


## <a name="next-steps"></a>后续步骤

* 详细了解[不同 Azure Stack Edge 型号的定价](https://azure.microsoft.com/pricing/details/azure-stack/edge/)。
* [准备部署 Azure Stack Edge Pro 设备](azure-stack-edge-gpu-deploy-prep.md)。
