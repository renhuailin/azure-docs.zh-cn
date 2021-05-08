---
title: 为 Azure Red Hat OpenShift 4 配置 DNS 转发
description: 为 Azure Red Hat OpenShift 4 配置 DNS 转发
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 68cc5dbee5b96472d11bdfb3a8bf35d118638dcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633863"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 群集上配置 DNS 转发

若要在 Azure Red Hat OpenShift 群集上配置 DNS 转发，需要修改 DNS 操作员。 此修改将允许群集内运行的应用程序 Pod 解析群集外专用 DNS 服务器上托管的名称。 [此处](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)为 OpenShift 4.6 记录了这些步骤。

例如，如果要转发 *. example.com 的所有 DNS 请求以便由 DNS 服务器 192.168.100.10 进行解析，可通过运行以下内容来编辑操作员配置：
 
```bash
oc edit dns.operator/default
```
 
这样将会启动一个编辑器，你可以将 `spec: {}` 其替换为：
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

保存该文件并退出编辑器。

## <a name="next-steps"></a>后续步骤
请在[此处](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)查看有关针对 OpenShift 4.6 进行 DNS 转发的详细信息。