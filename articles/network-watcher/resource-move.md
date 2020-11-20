---
title: 移动 Azure 网络观察程序资源 |Microsoft Docs
description: 跨区域移动 Azure 网络观察程序资源
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
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 84764e73ec5b4ada8c204147def310326a3c7bdd
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948419"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>跨区域移动 Azure 网络观察程序资源

## <a name="moving-the-network-watcher-resource"></a>移动网络观察程序资源
网络观察程序资源代表网络观察程序的后端服务，由 Azure 完全托管。 客户无需管理它。 此资源不支持移动操作。

## <a name="moving-child-resources-of-network-watcher"></a>移动网络观察程序的子资源
对于 `*networkWatcher*` 资源类型的任何子资源，当前都不支持跨区域移动资源。

## <a name="next-steps"></a>后续步骤
* 阅读[网络观察程序概述](./network-watcher-monitoring-overview.md)
* 查看[网络观察程序常见问题解答](./frequently-asked-questions.md)