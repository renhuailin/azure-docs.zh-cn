---
title: 在防火墙或代理服务器上允许 Azure 门户 URL
description: 为了优化你的网络与 Azure 门户及其服务之间的连接，建议将这些 URL 添加到允许列表。
ms.date: 06/21/2021
ms.topic: conceptual
ms.openlocfilehash: 70e2f55a381c38b0a4244f742b7b2c51e6ca81f6
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516354"
---
# <a name="allow-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>在防火墙或代理服务器上允许 Azure 门户 URL

为了优化你的网络与 Azure 门户及其服务之间的连接，建议将特定的 Azure 门户 URL 添加到允许列表。 此操作可改进局域网或广域网和 Azure 云之间的性能和连接性。

网络管理员会经常部署代理服务器、防火墙或其他设备，这样有助于确保用户访问 Internet 时的安全性并控制其访问方式。 旨在保护用户的规则有时候可能会阻止合法的与业务相关的 Internet 流量或降低其速度。 此流量包括你与 Azure 之间通过此处列出的 URL 进行的通信。

> [!TIP]
> 若要帮助诊断这些域的网络连接问题，请检查 https://portal.azure.com/selfhelp 。

## <a name="azure-portal-urls-for-proxy-bypass"></a>用于跳过代理的 Azure 门户 URL

可用于 Azure 门户的 URL 终结点特定于部署组织的 Azure 云。 若要允许发送到这些终结点的网络流量绕过限制，请选择你的云，然后将 URL 列表添加到代理服务器或防火墙。 除了列表里列出的，建议不要再添加任何其他与门户相关的 URL，不过你可能想要添加与其他 Microsoft 产品和服务相关的 URL。

#### <a name="public-cloud"></a>[公有云](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[美国政府云](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[中国政府云](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> 发往这些终结点的流量使用标准的 TCP 端口：80 (HTTP) 和 443 (HTTPS)。
