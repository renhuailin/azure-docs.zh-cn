---
title: Azure SignalR 服务限制表
description: 描述 Azure SignalR 服务的系统限制。
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/11/2020
ms.author: zhshang
ms.openlocfilehash: 328a3d088ea21ec55f5e06cfbfc307fca2b03afa
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2021
ms.locfileid: "113644360"
---
| 资源 | 默认限制 | 最大限制 | 
| --- | --- | --- |
| 免费层每个实例的 Azure SignalR 服务单元数 |1 |1 |
| 标准层每个实例的 Azure SignalR 服务单元数 |100 |100 |
| 免费层每个区域每个订阅的 Azure SignalR 服务单元数|5 |5 |
| 每个区域每个订阅的 Azure SignalR 服务单元总数 |150 |无限制 |
| 免费层每天每个单元的连接数 |20 |20 |
| 标准层每天每个单元的连接数 |1,000 |1,000|
| 免费层每天每个单元包含的消息数|20,000 |20,000 |
| 免费层每天每个单元的其他消息数|0 |0 |
| 标准层每天每个单元包含的消息数|1,000,000 |1,000,000 |
| 标准层每天每个单元的其他消息数|无限制 |无限制 |

若要请求更新订阅的默认限制，请开具支持票证。

有关如何对连接和消息进行计数的详细信息，请参阅 [Azure SignalR 服务中的消息和连接](../articles/azure-signalr/signalr-concept-messages-and-connections.md)。
