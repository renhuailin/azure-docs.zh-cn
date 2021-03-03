---
title: '使用 Azure 实例元数据服务检索负载平衡器元数据 (IMDS) '
titleSuffix: Azure Load Balancer
description: 开始学习如何使用 Azure 实例元数据服务检索负载均衡器元数据。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 9ec217cefb05929ed6f5c7395df5e68891e823ac
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739774"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>使用 Azure 实例元数据服务检索负载平衡器元数据 (IMDS) 

## <a name="prerequisites"></a>先决条件

* 使用 [最新的 API 版本](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#supported-api-versions) 作为你的请求。

## <a name="sample-request-and-response"></a>示例请求和响应
> [!IMPORTANT]
> 此示例会绕过代理。 查询 IMDS 时，必须绕过代理。 有关详细信息，请参阅 [代理](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies)。
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```
> [!NOTE]
> PowerShell 6.0 中引入了-NoProxy 参数。 如果你使用的是较旧版本的 PowerShell，请在请求正文中 NoProxy，并确保在检索 IMDS 信息时未使用代理。 在[此处](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies)了解更多信息。
> 
### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>示例响应

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>后续步骤
[常见错误代码和故障排除步骤](troubleshoot-load-balancer-imds.md)

了解有关[Azure 实例元数据服务](../virtual-machines/windows/instance-metadata-service.md)的详细信息

[检索实例的所有元数据](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#access-azure-instance-metadata-service)

[部署标准负载均衡器](quickstart-load-balancer-standard-public-portal.md)

