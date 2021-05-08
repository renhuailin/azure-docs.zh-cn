---
title: 配置标识 - Azure 事件网格 IoT Edge | Microsoft Docs
description: 配置事件网格模块标识
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2418c8518bbf298a102d4f29b4a973f6121de2eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171680"
---
# <a name="configure-identity-for-the-event-grid-module"></a>配置事件网格模块标识

本文介绍如何为 Edge 网格配置标识。 默认情况下，事件网格模块会根据 IoT 安全性守护程序的配置显示标识证书。 Edge 事件网格在传递事件时，会在传出调用中显示标识证书。 这允许订阅者在接受前，验证其是否为发送事件的事件网格模块。

有关所有可能的配置，请参阅[安全性和身份验证](security-authentication.md)指南。

## <a name="always-present-identity-certificate"></a>始终显示标识证书
下面的示例配置用于对传出调用始终显示标识证书。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>不显示标识证书
下面的示例配置用于对传出调用不显示标识证书。 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
