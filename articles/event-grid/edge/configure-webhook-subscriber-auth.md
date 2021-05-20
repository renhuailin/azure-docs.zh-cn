---
title: 配置 Webhook 订阅者身份验证 - Azure 事件网格 IoT Edge |Microsoft Docs
description: 配置 webhook 订阅服务器身份验证
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d78b6e80005e9533ccd5ebfaea853f35ae6004be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171646"
---
# <a name="configure-webhook-subscriber-authentication"></a>配置 webhook 订阅服务器身份验证

本指南提供了事件网格模块可能的 Webhook 订阅者配置示例。 默认情况下，Webhook 订阅者只接受 HTTPS 终结点。 如果订阅者提供自签名证书，则事件网格模块将拒绝。

## <a name="allow-only-https-subscriber"></a>只允许 HTTPS 订阅者

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>允许使用自签名证书的 HTTPS 订阅者

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>仅在测试环境中将属性 `outbound__webhook__allowUnknownCA` 设置为 `true`，因为你通常可能会使用自签名证书。 对于生产工作负载，建议将属性设置为 false。

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>允许 HTTPS 订阅者，但跳过证书验证

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>仅在测试环境中将属性 `outbound__webhook__skipServerCertValidation` 设置为 `true`，因为你可能不会提供需要验证的证书。 对于生产工作负载，建议将属性设置为 false

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>允许使用自签名证书的 HTTP 和 HTTPS 订阅者

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>仅在测试环境中将属性 `outbound__webhook__httpsOnly` 设置为 `false`，因为你可能希望首先启动 HTTP 订阅者。 对于生产工作负载，建议将属性设置为 true
