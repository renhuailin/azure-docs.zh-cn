---
title: 配置 Azure Red Hat OpenShift 4 的 DNS 转发
description: 配置 Azure Red Hat OpenShift 4 的 DNS 转发
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 68cc5dbee5b96472d11bdfb3a8bf35d118638dcd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633863"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 群集上配置 DNS 转发

若要在 Azure Red Hat OpenShift 群集上配置 DNS 转发，需要修改 DNS 操作员。 此修改将允许在群集内运行的应用程序盒解析群集外专用 DNS 服务器上托管的名称。 [此处](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)的 OpenShift 4.6 介绍了这些步骤。

例如，如果你想要将 *. example.com 的所有 DNS 请求转发给 DNS 服务器192.168.100.10 进行解析，则可以通过运行以下内容来编辑操作员配置：
 
```bash
oc edit dns.operator/default
```
 
这将启动一个编辑器，你可以将 `spec: {}` 其替换为：
 
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

保存文件并退出编辑器。

## <a name="next-steps"></a>后续步骤
在 [此处](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)查看有关 OpenShift 4.6 的 DNS 转发的详细信息。