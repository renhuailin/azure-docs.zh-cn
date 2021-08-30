---
title: 利用流量管理器跨多个终结点进行故障转移
titleSuffix: Azure Content Delivery Network
description: 了解如何使用 Azure 流量管理器跨多个 Azure 内容传送网络终结点配置故障转移。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 3eea73559fdf00e8c74f58ea51b1efce5eac694d
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113651292"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>利用流量管理器跨多个终结点进行故障转移

配置 Azure 内容分发网络 (CDN) 时，可根据需要选择最佳的提供程序和定价层。 

Azure CDN 采用全局分布式基础结构，默认会创建本地和地理冗余以及全局负载均衡，以提高服务可用性和性能。 

如果某个位置无法用于提供内容，则请求会自动路由到另一个位置。 最佳存在点 (POP) 用于为每个客户端请求提供服务。 自动路由基于请求位置和服务器负载等因素。
 
如果你有多个 CDN 配置文件，可以使用 Azure 流量管理器进一步提高可用性和性能。 

结合 Azure CDN 使用 Azure 流量管理器在多个 CDN 终结点之间进行负载均衡，以实现以下目的：
 
* 故障转移
* 地理负载均衡 

在典型的故障转移场景中，所有客户端请求都将定向到主 CDN 配置文件。 

如果这个配置文件不可用，则会将请求定向到辅助配置文件。  请求在重新联机时会恢复到主配置文件。

以这种方式使用 Azure 流量管理器可确保 Web 应用程序始终可用。 

本文提供了有关如何使用配置文件配置故障转移的指南和示例： 

* **来自 Verizon 的 Azure CDN 标准版**
* **来自 Akamai 的 Azure CDN 标准版**

还支持 Microsoft 的 Azure CDN。

## <a name="create-azure-cdn-profiles"></a>创建 Azure CDN 配置文件
使用不同的提供程序创建两个或更多个 Azure CDN 配置文件与终结点。

1. 创建两个 CDN 配置文件：
    * **来自 Verizon 的 Azure CDN 标准版**
    * **来自 Akamai 的 Azure CDN 标准版** 

    按照[创建新的 CDN 配置文件](cdn-create-new-endpoint.md#create-a-new-cdn-profile)中的步骤创建配置文件。
 
   ![CDN 的多个配置文件](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. 在每个新配置文件中，遵循[创建新 CDN 终结点](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)中的步骤至少创建一个终结点。

## <a name="create-traffic-manager-profile"></a>创建流量管理器配置文件
创建 Azure 流量管理器配置文件，并跨 CDN 终结点设置负载均衡。 

1. 遵循[创建流量管理器配置文件](../traffic-manager/quickstart-create-traffic-manager-profile.md)中的步骤创建 Azure 流量管理器配置文件。 

    * 对于“路由方法”，请选择“优先级”。 

2. 遵循[添加流量管理器终结点](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints)中的步骤，在流量管理器配置文件中添加 CDN 终结点

    * 对于“类型”，请选择“外部终结点”。 
    * 对于“优先级”，请输入一个数字。

    例如，创建优先级为 **1** 的 **cdndemo101akamai.azureedge.net**，以及优先级为 **2** 的 **cdndemo101verizon.azureedge.net**。

   ![CDN 的流量管理器终结点](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>在 Azure CDN 和 Azure 流量管理器中配置自定义域
配置 CDN 和流量管理器配置文件后，遵循以下步骤在 CDN 终结点中添加 DNS 映射并注册自定义域。 在此示例中，自定义域名为 **cdndemo101.dustydogpetcare.online**。

1. 转到自定义域提供商（例如 GoDaddy）的网站，并创建两个 DNS CNAME 条目。 

    a. 对于第一个 CNAME 条目，请将具有 cdnverify 子域的自定义域映射到 CDN 终结点。 将自定义域注册到在步骤 2 中添加到流量管理器的 CDN 终结点时，需要使用此条目。

      例如： 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. 对于第二个 CNAME 条目，请将没有 cdnverify 子域的自定义域映射到 CDN 终结点。 此条目将自定义域映射到流量管理器。 

      例如： 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > 如果你的域当前已上线且不能中断，请在最后执行此步骤。 在将自定义域 DNS 更新到流量管理器之前，请验证 CDN 终结点和流量管理器域是否已上线。
    >
   
    > [!NOTE]
    > 若要实现此故障转移方案，这两个终结点需要位于不同的配置文件中，且不同的配置文件应分属不同的 CDN 提供程序，以避免域名冲突。
    > 

2.  在 Azure CDN 配置文件中，选择第一个 CDN 终结点 (Akamai)。 选择“添加自定义域”，然后输入“cdndemo101.dustydogpetcare.online”。 确认表示验证自定义域的复选标记是否显示为绿色。 

    Azure CDN 使用 **cdnverify** 子域来验证 DNS 映射，以完成此注册过程。 有关详细信息，请参阅[创建 CNAME DNS 记录](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)。 此步骤使 Azure CDN 能够识别自定义域，以便对其请求做出响应。
    
    > [!NOTE]
    > 要在“Akamai 的 Azure CDN”上启用 TLS，必须直接将自定义域的别名映射到你的终结点。 尚不支持用于启用 TLS 的 cdnverify。 
    >

3.  回到自定义域的域提供商的网站。 更新已创建的第一个 DNS 映射。 将自定义域映射到第二个 CDN 终结点。
                             
    例如： 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. 在 Azure CDN 配置文件中，选择第二个 CDN 终结点 (Verizon) 并重复步骤 2。 选择“添加自定义域”，然后输入“cdndemo101.dustydogpetcare.online”。
 
完成这些步骤后，即已使用 Azure 流量管理器配置了具有故障转移功能的多 CDN 服务。 

你可以从自定义域访问测试 URL。 

若要测试功能，请禁用主要 CDN 终结点，并验证是否能够正确地将请求转移到辅助 CDN 终结点。 

## <a name="next-steps"></a>后续步骤
可以配置其他路由方法（例如地理路由），以便在不同的 CDN 终结点之间进行负载均衡。 

有关详细信息，请参阅[使用流量管理器配置地理流量路由方法](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)。
