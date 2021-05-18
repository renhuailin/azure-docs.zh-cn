---
title: Azure Red Hat OpenShift 3.11 支持的资源
description: 了解 Microsoft Azure Red Hat OpenShift 支持哪些 Azure 区域和虚拟机大小。
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ad0837ae110b84cdff690fe76e13923a0ab60996
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100635602"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift 资源

本主题列出了 Microsoft Azure Red Hat OpenShift 3.11 服务支持的 Azure 区域和虚拟机大小。

## <a name="azure-regions"></a>Azure 区域

有关可在其中部署 Azure Red Hat OpenShift 群集的区域的当前列表，请查看[按区域列出的可用产品](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)。

## <a name="virtual-machine-sizes"></a>虚拟机大小

下面是可在 Azure Red Hat OpenShift 群集中为计算节点指定的受支持的虚拟机大小。

> [!Important]
> 每个 VM 可附加不同数量的驱动器。 这可能并不像内存或 CPU 大小那样直接一目了然。
> 并非所有 VM 大小都可在所有区域中使用。 即使 API 支持指定的大小，如果该大小在指定的区域中不可用，你也可能遇到错误。
> 有关详细信息，请查看[每个区域支持的 VM 大小的当前列表](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。

## <a name="compute-node-sizes"></a>计算节点大小

Azure Red Hat OpenShift REST API 支持以下计算节点大小：

|大小|vCPU|RAM|
|-|-|-|
|标准 D4s v3|4|16 GB|
|标准 D8s v3|8|32 GB|
|标准 D16s v3|16|64 GB|
|标准 D32s v3|32|128 GB|
|-|-|-|
|标准 E4s v3|4|32 GB|
|标准 E8s v3|8|64 GB|
|标准 E16s v3|16|128 GB|
|标准 E32s v3|32|256 GB|
|-|-|-|
|标准 F8s v2|8|16 GB|
|标准 F16s v2|16|32 GB|
|标准 F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>主节点大小

Azure Red Hat OpenShift REST API 支持以下主/基础结构节点大小：

|大小|vCPU|RAM|
|-|-|-|
|标准 D4s v3|4|16 GB|
|标准 D8s v3|8|32 GB|
|标准 D16s v3|16|64 GB|
|标准 D32s v3|32|128 GB|

## <a name="next-steps"></a>后续步骤

请尝试[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程。
