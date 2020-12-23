---
title: Azure 网络观察程序的数据驻留 |Microsoft Docs
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
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 2e6a92a4d08f1603f480a990ad437a90302a8189
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966082"
---
# <a name="data-residency-for-azure-network-watcher"></a>Azure 网络观察程序的数据驻留
Azure 网络观察程序不会存储客户数据，连接监视器（预览版）服务除外。


## <a name="connection-monitor-preview-data-residency"></a>连接监视器（预览版）数据驻留
连接监视器（预览版）服务会存储客户数据。 这些数据由网络观察程序自动存储在单个区域中。 因此，连接监视器（预览版）会自动满足区域内数据驻留要求，包括在[信任中心](https://azuredatacentermap.azurewebsites.net/)指定的要求。

## <a name="data-residency"></a>数据驻留
在 Azure 中，启用将客户数据存储在单个区域中的功能当前仅在东南亚区域 (新加坡) ，亚太地域和巴西南部 (圣保罗州) 地区地区。 对于其他所有区域，客户数据存储在以下地域。 有关详细信息，请参阅 [信任中心](https://azuredatacentermap.azurewebsites.net/)。

## <a name="next-steps"></a>后续步骤

* 阅读[网络观察程序](./network-watcher-monitoring-overview.md)的概述。