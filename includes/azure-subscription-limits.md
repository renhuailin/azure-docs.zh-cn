---
title: 包含文件
description: include 文件
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563227"
---
| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 每个[订阅](https://azure.microsoft.com/pricing/)的 vCPU 数量 <sup>1</sup> |20 |10,000 |
| 每个订阅的[协同管理员数](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |200 |200 |
| 每个订阅的[存储帐户数](../articles/storage/common/storage-account-create.md)<sup>2</sup> |100 |100 |
| 每个订阅的[云服务数](../articles/cloud-services/cloud-services-choose-me.md) |20 |200 |
| 每个订阅的[本地网络数](/previous-versions/azure/reference/jj157100(v=azure.100)) |10 |500 |
| 每个订阅的 DNS 服务器 |9 |100 |
| 每个订阅的保留的 IP |20 |100 |
| 每个订阅的[地缘组数](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) |256 |256 |
| 订阅名称长度（字符） | 64 | 64 |

<sup>1</sup>特小实例作为一个 vCPU 至 vCPU 限制计数，即使使用了部分 CPU 核心。

<sup>2</sup>此存储帐户限制包括标准和高级存储帐户。