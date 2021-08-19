---
title: include 文件
description: include 文件
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 11/19/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c37e2c28eb6aee9edf4bdd97066ce5f15e7447c1
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413885"
---
## <a name="message-headers"></a>消息标头
以下是在消息标头中接收的属性：

| 属性名称 | 说明 |
| ------------- | ----------- | 
| aeg-subscription-name | 事件订阅的名称。 |
| aeg-delivery-count | 针对该事件进行尝试的次数。 |
| aeg-event-type | <p>事件的类型。</p><p>可以为下列值之一：</p><ul><li>SubscriptionValidation</li><li>通知</li><li>SubscriptionDeletion</li></ul> | 
| aeg-metadata-version | <p>事件的元数据版本。<p> 对于“事件网格事件架构”，此属性表示元数据版本；对于“云事件架构”，此属性表示规范版本。 </p>|
| aeg-data-version | <p>事件的数据版本。</p><p>对于“事件网格事件架构”，此属性表示数据版本；对于“云事件架构”，此属性不适用。</p> |
| aeg-output-event-id | 事件网格事件的 ID。 |


