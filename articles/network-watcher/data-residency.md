---
title: Azure 网络观察程序的数据驻留 | Microsoft Docs
description: 本文介绍 Azure 网络观察程序服务的数据驻留。
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b5aa8167031c3b871c6a6a4d84159c3c284bf241
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018422"
---
# <a name="data-residency-for-azure-network-watcher"></a>Azure 网络观察程序的数据驻留
Azure 网络观察程序不会存储客户数据，连接监视器（预览版）服务除外。


## <a name="connection-monitor-preview-data-residency"></a>连接监视器（预览版）数据驻留
连接监视器（预览版）服务会存储客户数据。 这些数据由网络观察程序自动存储在单个区域中。 因此，连接监视器（预览版）会自动满足区域内数据驻留要求，包括在[信任中心](https://azuredatacentermap.azurewebsites.net/)指定的要求。

## <a name="data-residency"></a>数据驻留
在 Azure 中，支持在单个区域存储客户数据的功能目前仅在亚太地区的东南亚区域（新加坡）和巴西地区的巴西南部（圣保罗州）可用。 对于其他所有区域，客户数据存储在以下地域。 有关详细信息，请参阅[信任中心](https://azuredatacentermap.azurewebsites.net/)。

## <a name="next-steps"></a>后续步骤

* 阅读[网络观察程序](./network-watcher-monitoring-overview.md)的概述。