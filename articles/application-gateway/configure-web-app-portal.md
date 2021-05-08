---
title: 使用门户管理多租户应用的流量
titleSuffix: Azure Application Gateway
description: 本文提供有关如何在现有或新的应用程序网关上将 Azure 应用服务 Web 应用配置为后端池成员的指导。
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/02/2021
ms.author: victorh
ms.openlocfilehash: aadd4904ff218613c0dd24daff784ad5b8b90fbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97854904"
---
# <a name="configure-app-service-with-application-gateway"></a>使用应用程序网关配置应用服务

由于应用服务是多租户服务而不是专用部署，因此会使用传入请求中的主机标头来解析发给正确应用服务终结点的请求。 通常，应用程序的 DNS 名称（也是与面向应用服务的应用程序网关关联的 DNS 名称）不同于后端应用服务的域名。 因此，应用程序网关收到的原始请求中的主机标头不同于后端服务的主机名。 正因如此，除非从应用程序网关发往后端的请求中的主机标头已更改为后端服务的主机名，否则多租户后端无法将请求解析为正确的终结点。

应用程序网关提供一个名为 `Pick host name from backend target` 的开关，将请求从应用程序网关路由到后端时，该开关将使用后端的主机名来替代请求中的主机标头。 使用此功能可以支持 Azure 应用服务和 API 管理等多租户后端。 

在本文中，学习如何：

- 编辑后端池并将应用服务添加到其中
- 在启用“选取主机名”开关的情况下编辑 HTTP 设置

## <a name="prerequisites"></a>先决条件

- 应用程序网关：创建不带后端池目标的应用程序网关。 有关详细信息，请参阅[快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure 门户](quick-create-portal.md)

- 应用服务：如果没有应用服务，请参阅[应用服务文档](../app-service/index.yml)。

## <a name="add-app-service-as-backend-pool"></a>将应用服务添加为后端池

1. 在 Azure 门户中，选择你的应用程序网关。

2. 在“后端池”下，选择后端池。

4. 在“目标类型”下，选择“应用服务”。 

5. 在“目标”下，选择你的应用服务。

   :::image type="content" source="./media/configure-web-app-portal/backend-pool.png" alt-text="应用服务后端":::
   
   > [!NOTE]
   > 下拉列表中只填充了与应用程序网关位于同一订阅中的应用服务。 若要使用与应用程序网关不同的订阅中的应用服务，请在“目标”下拉列表中选择“应用服务”，选择“IP 地址或主机名”选项，然后输入应用服务的主机名（例如   应用服务的 azurewebsites.net)。
1. 选择“保存”。 

## <a name="edit-http-settings-for-app-service"></a>编辑应用服务的 HTTP 设置

1. 在“HTTP 设置”下，选择现有 HTTP 设置。

2. 在“使用新主机名进行替代”下，选择“是”。
3. 在“主机名替代”下，选择“从后端目标选取主机名”。
4. 选择“保存”。 

   :::image type="content" source="./media/configure-web-app-portal/http-settings.png" alt-text="从后端 http 设置中选取主机名":::

## <a name="additional-configuration-in-case-of-redirection-to-app-services-relative-path"></a>重定向到应用服务的相对路径时的其他配置

当应用服务向客户端发送重定向响应以重定向到其相对路径（例如，从 `contoso.azurewebsites.net/path1` 重定向到 `contoso.azurewebsites.net/path2`）时，它会在其响应的位置标头中使用从应用程序网关收到的请求中的相同主机名。 因此，客户端将直接向 `contoso.azurewebsites.net/path2` 发出请求，而不是通过应用程序网关 (`contoso.com/path2`) 发出请求。 不应该绕过应用程序网关。

如果在你的用例中，应用服务有时需要将重定向响应发送到客户端，请执行[重写位置标头的附加步骤](./troubleshoot-app-service-redirection-app-service-url.md#sample-configuration)。

## <a name="restrict-access"></a>限制访问

这些示例中部署的 Web 应用使用可通过 Internet 直接访问的公共 IP 地址。 这有助于在了解新功能和尝试新事物时获取疑难解答。 但若想将功能部署至生产环境，将需要添加更多限制。

若要限制对 Web 应用的访问权限，一种方法是使用 [Azure 应用服务静态 IP 限制](../app-service/app-service-ip-restrictions.md)。 例如，可以限制 Web 应用，使其仅接收来自应用程序网关的流量。 使用应用服务 IP 限制功能列出应用程序网关 VIP，作为具有访问权限的唯一地址。

## <a name="next-steps"></a>后续步骤

若要详细了解应用服务和应用程序网关的其他多租户支持，请参阅[应用程序网关的多租户服务支持](./application-gateway-web-app-overview.md)。
