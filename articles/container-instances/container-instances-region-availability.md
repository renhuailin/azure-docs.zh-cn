---
title: 资源可用性（按区域）
description: Azure 容器实例服务的计算和内存资源在不同 Azure 区域的可用性。
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 63ec7c9027f957714108263dcbb4300a3cc68388
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661368"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Azure 容器实例在 Azure 区域的资源可用性

本文按目标操作系统详细介绍了 Azure 容器实例计算、内存和存储资源在 Azure 区域中的可用性。 有关 Azure 容器实例常规可用区域的列表，请参阅[可用区域](https://azure.microsoft.com/regions/services/)。

提供的值是指部署一个[容器组](container-instances-container-groups.md)时可以使用的最大资源。 在本文发布时，值是最新的。

> [!NOTE]
> 在这些资源限制内创建的容器组受部署区域内可用性的限制。 某个区域负载较重时，部署实例时可能会失败。 若要减少此类部署失败，请尝试部署具有较低资源设置的实例，或稍后尝试部署，或在具有可用资源的其他区域中进行部署。

若要了解部署中的配额和其他限制，请参阅 [Azure 容器实例的配额和限制](container-instances-quotas.md)。

## <a name="linux-container-groups"></a>Linux 容器组

以下区域和最大资源数适用于使用 Linux 容器的容器组的常规部署、[Azure 虚拟网络](container-instances-vnet.md)部署以及使用 [GPU 资源](container-instances-gpu.md)（预览版）的部署。

> [!IMPORTANT]
> 区域中的最大资源因部署而异。 例如，一个区域在 Azure 虚拟网络部署中可能具有与常规部署中不同的最大 CPU 和内存大小。 对于具有 GPU 资源的部署，同一区域也可能具有一组不同的最大值。 在检查下表中你所在区域的最大值之前，请确定你的部署类型。

| 区域 | 最大 CPU | 最大内存 (GB) | VNET 最大 CPU | VNET 最大内存 (GB)  | 存储器 (GB) | GPU SKU（预览版） |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| 澳大利亚东部 | 4 | 16 | 4 | 16 | 50 | N/A |
| Brazil South | 4 | 16 | 2 | 8 | 50 | N/A |
| 加拿大中部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 印度中部 | 4 | 16 | N/A | N/A | 50 | V100 |
| 美国中部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 东亚 | 4 | 16 | 4 | 16 | 50 | N/A |
| 美国东部 | 4 | 16 | 4 | 16 | 50 | K80、P100、V100 |
| 美国东部 2 | 4 | 16 | 4 | 16 | 50 | N/A |
| 法国中部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 德国中西部 | 3 | 16 | N/A | N/A | 50 | N/A |
| Japan East | 2 | 8 | 4 | 16 | 50 | N/A |
| 韩国中部 | 4 | 16 | N/A | N/A | 50 | N/A |
| 美国中北部 | 2 | 3.5 | 4 | 16 | 50 | K80、P100、V100 |
| 北欧 | 4 | 16 | 4 | 16 | 50 | K80 |
| 美国中南部 | 4 | 16 | 4 | 16 | 50 | N/A |
| Southeast Asia | 4 | 16 | 4 | 16 | 50 | P100、V100 |
| 印度南部 | 4 | 16 | N/A | N/A | 50 | N/A |
| 英国南部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 阿拉伯联合酋长国北部 | 3 | 16 | N/A | N/A | 50 | N/A |
| 美国中西部| 4 | 16 | 4 | 16 | 50 | N/A |
| 西欧 | 4 | 16 | 4 | 16 | 50 | K80、P100、V100 |
| 美国西部 | 4 | 16 | 4 | 16 | 50 | N/A |
| 美国西部 2 | 4 | 16 | 4 | 16 | 50 | K80、P100、V100 |

以下最大资源数适用于使用 [GPU 资源](container-instances-gpu.md)（预览版）部署的容器组。

> [!IMPORTANT]
> 目前，具有 GPU 资源的部署在 Azure 虚拟网络部署中不受支持，并且仅适用于 Linux 容器组。

| GPU SKU | GPU 计数 | 最大 CPU | 最大内存 (GB) | 存储器 (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100、V100 | 1 | 6 | 112 | 50 |
| P100、V100 | 2 | 12 | 224 | 50 |
| P100、V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Windows 容器组

以下区域和最大资源数量适用于具有[受支持和预览版](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server 容器的容器组。

> [!IMPORTANT]
> 目前，Azure 虚拟网络部署中不支持具有 Windows 容器组的部署。

###  <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> 有关 1B、2B 和 3B 主机的详细信息，请参阅[主机和容器版本兼容性](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility)。

| 区域 | 1B/2B 最大 CPU | 1B/2B 最大内存 (GB) |最高 3B CPU | 最大内存 (GB)  | 存储器 (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| 澳大利亚东部 | 2 | 8 | 2 | 8 | 20 |
| Brazil South | 4 | 16 | 4 | 16 | 20 |
| 加拿大中部 | 2 | 8 | 2 | 3.5 | 20 |
| 印度中部 | 2 | 3.5 | 2 | 3.5 | 20 |
| 美国中部 | 2 | 3.5 | 2 | 3.5 | 20 |
| 东亚 | 2 | 3.5 | 2 | 3.5 | 20 |
| 美国东部 | 4 | 16 | 2 | 8 | 20 |
| 美国东部 2 | 2 | 3.5 | 4 | 16 | 20 |
| Japan East | 4 | 16 | 4 | 16 | 20 |
| 韩国中部 | 4 | 16 | 4 | 16 | 20 |
| 美国中北部 | 4 | 16 | 4 | 16 | 20 |
| 北欧 | 2 | 8 | 2 | 8 | 20 |
| 美国中南部 | 2 | 3.5 | 2 | 8 | 20 |
| Southeast Asia | N/A | N/A | 2 | 3.5 | 20 |
| 印度南部 | 2 | 3.5 | 2 | 3.5 | 20 |
| 英国南部 | 2 | 8 | 2 | 3.5 | 20 |
| 美国中西部 | 4 | 16 | 2 | 8 | 20 |
| 西欧 | 4 | 16 | 4 | 16 | 20 |
| 美国西部 | 4 | 16 | 2 | 8 | 20 |
| 美国西部 2 | 2 | 8 | 2 | 3.5 | 20 |


### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> 有关 1B、2B 和 3B 主机的详细信息，请参阅[主机和容器版本兼容性](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility)。

| 区域 | 1B/2B 最大 CPU | 1B/2B 最大内存 (GB) |最高 3B CPU | 最大内存 (GB)  | 存储器 (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| 澳大利亚东部 | 4 | 16 | 4 | 16 | 20 |
| Brazil South | 4 | 16 | 4 | 16 | 20 |
| 加拿大中部 | 4 | 16 | 4 | 16 | 20 |
| 印度中部 | 4 | 16 | 4 | 16 | 20 |
| 美国中部 | 4 | 16 | 4 | 16 | 20 |
| 东亚 | 4 | 16 | 4 | 16 | 20 |
| 美国东部 | 4 | 16 | 4 | 16 | 20 |
| 美国东部 2 | 2 | 3.5 | 2 | 3.5 | 20 |
| 法国中部 | 4 | 16 | 4 | 16 | 20 |
| Japan East | N/A | 不适用 | 4 | 16 | 20 |
| 韩国中部 | 4 | 16 | 4 | 16 | 20 |
| 美国中北部 | 4 | 16 | 4 | 16 | 20 |
| 北欧 | 4 | 16 | 4 | 16 | 20 |
| 美国中南部 | 4 | 16 | 4 | 16 | 20 |
| Southeast Asia | 4 | 16 | 4 | 16 | 20 |
| 印度南部 | 4 | 16 | 4 | 16 | 20 |
| 英国南部 | 4 | 16 | 4 | 16 | 20 |
| 美国中西部 | 4 | 16 | 4 | 16 | 20 |
| 西欧 | 4 | 16 | 4 | 16 | 20 |
| 美国西部 | 4 | 16 | 4 | 16 | 20 |
| 美国西部 2 | 2 | 8 | 4 | 16 | 20 |

## <a name="next-steps"></a>后续步骤

如果希望看到更多的区域，或者希望提高资源可用性，请通过 [aka.ms/aci/feedback](https://aka.ms/aci/feedback) 告知我们的团队。

有关容器实例部署故障排除的信息，请参阅[排查 Azure 容器实例的部署问题](container-instances-troubleshooting.md)。


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
