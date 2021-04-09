---
title: include 文件
description: include 文件
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2f52e8a89ec9dd78a1951836053cb2c698310bbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100645575"
---
以下限制适用于 Azure 事件网格主题（系统、自定义和合作伙伴主题）。 

| 资源 | 限制 |
| --- | --- |
| 每个 Azure 订阅的自定义主题数 | 100 |
| 每个主题的事件订阅数 | 500 |
| 自定义或合作伙伴主题的发布速率（流入量） | 5,000 个事件/秒或 5 MB/秒（以先达到者为准） |
| 事件大小 | 1 MB  |
| 每个主题的专用终结点连接数  | 64 | 
| 每个主题的 IP 防火墙规则数 | 16 | 

以下限制适用于 Azure 事件网格域。 

| 资源 | 限制 |
| --- | --- |
| 每个事件域的主题数 | 100,000 |
| 域中每个主题的事件订阅数 | 500 |
| 域范围事件订阅数 | 50 |
| 事件域（入口）的发布速率 | 5,000 个事件/秒或 5 MB/秒（以先达到者为准） |
| 每个 Azure 订阅的事件域数 | 100 |
| 每个域的专用终结点连接数 | 64 | 
| 每个域的 IP 防火墙规则数 | 16 | 


