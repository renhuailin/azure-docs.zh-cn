---
title: 使用应用程序网关启用基于 Cookie 的相关性
description: 本文介绍如何使用应用程序网关启用基于 Cookie 的相关性。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397410"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>使用应用程序网关启用基于 Cookie 的相关性
如 [Azure 应用程序网关文档](./application-gateway-components.md#http-settings)中所述，应用程序网关支持基于 Cookie 的相关性，这意味着它可以将后续流量从用户会话定向到同一服务器进行处理。

## <a name="example"></a>示例
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```