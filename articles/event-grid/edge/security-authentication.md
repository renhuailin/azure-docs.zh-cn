---
title: 安全性和身份验证 - Azure 事件网格 IoT Edge | Microsoft Docs
description: IoT Edge 上事件网格中的安全性和身份验证。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: cfdefe642e7e0f428680340747f825641d19b391
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378277"
---
# <a name="security-and-authentication"></a>安全性和身份验证

安全性和身份验证是一个高级概念，需要熟悉事件网格基础知识才能理解此概念。 如果你不熟悉 IoT Edge 上的事件网格，请从[此处](concepts.md)着手。 事件网格模块构建在 IoT Edge 中现有安全基础结构的基础之上。 有关详细信息和设置，请参阅[此文档](../../iot-edge/security.md)。

以下部分详细介绍了如何对这些设置进行保护和身份验证：

* TLS 配置
* 入站客户端身份验证
* 出站服务器身份验证
* 出站客户端身份验证

>[!IMPORTANT]
>事件网格模块安全性和身份验证利用 IoT Edge 上可用的现有基础结构。 假设是 IoT Edge 子系统很安全。

>[!IMPORTANT]
>事件网格配置默认是安全的。 以下子部分介绍可用于替代身份验证各个方面的所有选项和可能值。 在做出任何更改之前请了解其影响。 要使任何更改生效，需要重新部署事件网格模块。

## <a name="tls-configuration-aka-server-authentication"></a>TLS 配置（即服务器身份验证）

事件网格模块承载 HTTP 和 HTTPS 终结点。 IoT Edge 的安全守护程序为每个 IoT Edge 模块分配一个服务器证书。 我们将使用该服务器证书来保护终结点。 该证书过期后，模块将自动使用 IoT Edge 安全守护程序提供的新证书进行刷新。

默认只允许 HTTPS 通信。 可以通过 inbound__serverAuth__tlsPolicy 配置替代此行为。 下表汇总了此属性的可能值。

| 可能值 | 说明 |
| ---------------- | ------------ |
| 严格 | 默认。 仅启用 HTTPS
| Enabled | 启用 HTTP 和 HTTPS
| 已禁用 | 仅启用 HTTP

## <a name="inbound-client-authentication"></a>入站客户端身份验证

客户端是执行管理和/或运行时操作的实体。 客户端可以是其他 IoT Edge 模块或非 IoT 应用程序。

事件网格模块支持两种类型的客户端身份验证：

* 基于共享访问签名 (SAS) 密钥
* 基于证书

默认情况下，事件网格模块配置为仅接受基于证书的身份验证。 在启动时，事件网格模块将从 IoT Edge 安全守护程序检索“TrustBundle”，并使用它来验证任何客户端证书。 无法解析为此链的客户端证书将被拒绝并出现 `UnAuthorized` 错误。

### <a name="certificate-based-client-authentication"></a>基于证书的客户端身份验证

基于证书的身份验证默认已启用。 可以选择通过属性 inbound__clientAuth__clientCert__enabled 禁用基于证书的身份验证。 下表汇总了可能值。

| 可能值 | 说明 |
| ----------------  | ------------ |
| 是 | 默认。 要求发送到事件网格模块的所有请求提供客户端证书。 此外，还需要配置 inbound__clientAuth__clientCert__source。
| false | 不强制客户端提供证书。

下表汇总了 inbound__clientAuth__clientCert__source 的可能值

| 可能值 | 说明 |
| ---------------- | ------------ |
| IoT Edge | 默认。 使用 IoT Edge 的 Trustbundle 验证所有客户端证书。

如果客户端提供自签名证书，则默认情况下，事件网格模块将拒绝此类请求。 可以选择通过 inbound__clientAuth__clientCert__allowUnknownCA 属性允许自签名的客户端证书。 下表汇总了可能值。

| 可能值 | 说明 |
| ----------------  | ------------|
| 是 | 默认。 允许成功提供自签名证书。
| false | 如果提供自签名证书，则使请求失败。

>[!IMPORTANT]
>在生产方案中，可能需要将 inbound__clientAuth__clientCert__allowUnknownCA 设置为 false 。

### <a name="sas-key-based-client-authentication"></a>基于 SAS 密钥的客户端身份验证

除了基于证书的身份验证以外，事件网格模块还可以执行基于 SAS 密钥的身份验证。 SAS 密钥类似于事件网格模块中配置的、应该用来验证所有传入调用的机密。 客户端需要在 HTTP 头“aeg-sas-key”中指定机密。 如果机密不匹配，请求将被拒绝并出现 `UnAuthorized` 错误。

用于控制基于 SAS 密钥的身份验证的配置是 inbound__clientAuth__sasKeys__enabled。

| 可能值 | 说明  |
| ----------------  | ------------ |
| 是 | 允许基于 SAS 密钥的身份验证。 需要 inbound__clientAuth__sasKeys__key1 或 inbound__clientAuth__sasKeys__key2 
| false | 默认。 基于 SAS 密钥的身份验证已禁用。

 inbound__clientAuth__sasKeys__key1 和 Inbound__clientAuth__sasKeys__key2 是在事件网格模块中配置的、用来对传入请求进行检查的密钥 。 至少需要配置其中一个密钥。 发出请求的客户端需要提供该密钥作为请求头“aeg-sas-key”的一部分。 如果配置了这两个密钥，客户端可以提供其中的一个密钥。

> [!NOTE]
>可以对这两种身份验证方法均进行配置。 在这种情况下，将先检查 SAS 密钥，仅当该密钥通不过检查时，才执行基于证书的身份验证。 只需其中一种身份验证方法成功，请求即可成功。

## <a name="outbound-client-authentication"></a>出站客户端身份验证

出站上下文中的客户端是指事件网格模块。 正在执行的操作是将事件传递给订阅服务器。 订阅模块被视为服务器。

IoT Edge 的安全守护程序为每个 IoT Edge 模块分配一个标识证书。 我们将该标识证书用于传出调用。 该证书过期后，模块将自动使用 IoT Edge 安全守护程序提供的新证书进行刷新。

用于控制出站客户端身份验证的配置是 Outbound__clientAuth__clientCert__enabled。

| 可能值 | 说明 |
| ----------------  | ------------ |
| 是 | 默认。 要求来自事件网格模块的所有传出请求提供一个证书。 需要配置 outbound__clientAuth__clientCert__source。
| false | 不要求事件网格模块提供其证书。

用于控制证书源的配置是 outbound__clientAuth__clientCert__source。

| 可能值 | 说明 |
| ---------------- | ------------ |
| IoT Edge | 默认。 使用 IoT Edge 安全守护程序配置的模块标识证书。

### <a name="outbound-server-authentication"></a>出站服务器身份验证

事件网格订阅服务器的目标类型之一是“Webhook”。 默认情况下，对于此类订阅服务器，只会接受 HTTPS 终结点。

用于控制 Webhook 目标策略的配置是 outbound__webhook__httpsOnly。

| 可能值 | 说明 |
| ----------------  | ------------ |
| 是 | 默认。 仅允许具有 HTTPS 终结点的订阅服务器。
| false | 允许具有 HTTP 或 HTTPS 终结点的订阅服务器。

默认情况下，事件网格模块将验证订阅服务器的服务器证书。 可以通过替代 outbound__webhook__skipServerCertValidation 来跳过验证。 可能的值包括：

| 可能值 | 说明 |
| ----------------  | ------------ |
| 是 | 不验证订阅服务器的服务器证书。
| false | 默认。 验证订阅服务器的服务器证书。

如果订阅服务器的证书是自签名证书，则默认情况下，事件网格模块将拒绝此类订阅服务器。 若要允许自签名证书，可以替代 outbound__webhook__allowUnknownCA。 下表汇总了可能值。

| 可能值 | 说明 |
| ----------------  | ------------ |
| 是 | 默认。 允许成功提供自签名证书。
| false | 如果提供自签名证书，则使请求失败。

>[!IMPORTANT]
>在生产方案中，需要将 outbound__webhook__allowUnknownCA 设置为 false 。

> [!NOTE]
>IoT Edge 环境将生成自签名证书。 建议为生产工作负载生成由已授权 CA 颁发的证书，并将入站和出站服务器上的 allowUnknownCA 属性设置为 false 。

## <a name="summary"></a>总结

事件网格模块默认是安全的。 建议为生产部署保留这些默认设置。

下面是配置时要使用的指导原则：

* 仅允许 HTTPS 请求进入模块。
* 仅允许基于证书的客户端身份验证。 仅允许已知 CA 颁发的那些证书。 禁止自签名证书。
* 禁止基于 SAS 密钥的客户端身份验证。
* 始终在传出调用中提供事件网格模块的标识证书。
* 仅允许将 HTTPS 订阅服务器用作 Webhook 目标类型。
* 始终针对 Webhook 目标类型验证订阅服务器的服务器证书。 仅允许已知 CA 颁发的证书。 禁止自签名证书。

默认情况下，将使用以下配置部署事件网格模块：

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
