---
title: 教程 - 将 Azure Spring Cloud 与 Azure 负载均衡解决方案集成
description: 如何将 Azure Spring Cloud 与 Azure 负载均衡解决方案集成
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 058d8ea63c931f60f27b827874726791f79a31db
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015351"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>将 Azure Spring Cloud 与 Azure 负载均衡解决方案集成

本文适用于：✔️ Java ✔️ C#

Azure Spring Cloud 支持 Azure 上的微服务。  不断增长的业务可能需要多个数据中心来管理 Azure Spring Cloud 的多个实例。

Azure 已提供不同的负载均衡解决方案。 将 Azure Spring Cloud 与 Azure 负载均衡解决方案集成有三个选项：

1. 将 Azure Spring Cloud 与 Azure 流量管理器集成
2. 将 Azure Spring Cloud 与 Azure 应用网关集成
3. 将 Azure Spring Cloud 与 Azure Front Door 集成

## <a name="prerequisites"></a>先决条件

* Azure Spring Cloud：[如何创建 Azure Spring Cloud 服务](./quickstart.md)
* Azure 流量管理器：[如何创建流量管理器](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* Azure 应用网关：[如何创建应用程序网关](../application-gateway/quick-create-portal.md)
* Azure Front Door：[如何创建 Front Door](../frontdoor/quickstart-create-front-door.md)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>将 Azure Spring Cloud 与 Azure 流量管理器集成

若要将 Azure Spring Cloud 与流量管理器集成，请将其公共终结点添加为流量管理器的终结点，然后同时为流量管理器和 Azure Spring Cloud 配置自定义域。

### <a name="add-endpoint-in-traffic-manager"></a>在流量管理器中添加终结点

在流量管理器中添加终结点：

1. 将“类型”指定为“外部终结点”。
1. 输入每个 Azure Spring Cloud 公共终结点的完全限定的域名 (FQDN)。
1. 选择“确定”。

    ![流量管理器 1](media/spring-cloud-load-balancers/traffic-manager-1.png) ![流量管理器 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>配置自定义域

若要完成配置，请执行以下操作：

1. 登录到域提供商的网站，创建从自定义域到流量管理器的 Azure 默认域名的 CNAME 记录映射。
1. 按照[如何将自定义域添加到 Azure Spring Cloud](./tutorial-custom-domain.md) 中的说明操作。
1. 将上面的自定义域绑定（绑定到流量管理器）添加到 Azure Spring Cloud 的相应应用服务，并在该处上传 SSL 证书。

    ![流量管理器 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>将 Azure Spring Cloud 与 Azure 应用网关集成

若要与 Azure Spring Cloud 服务集成，请完成以下配置：

### <a name="configure-backend-pool"></a>配置后端池

1. 将“目标类型”指定为“IP 地址”或“FQDN”。
1. 输入 Azure Spring Cloud 公共终结点。

    ![应用网关 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>添加自定义探测

1. 依次选择“运行状况探测”、“添加” 以打开“自定义探测”对话框。
1. 关键点是对“从后端 HTTP 设置选取主机名”选项选择“是”。

    ![应用网关 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>配置 Http 设置

1. 依次选择“Http 设置”、“添加”以添加 HTTP 设置。
1. **使用新主机名进行替代：** 选择“是”。
1. **主机名替代**：选择“从后端目标选取主机名”。
1. **使用自定义探测**：选择“是”并选取上面创建的自定义探测。

    ![应用网关 3](media/spring-cloud-load-balancers/app-gateway-3.png)

### <a name="configure-rewrite-set"></a>配置“重写集”

1. 选择“重写”，然后选择“重写集”来添加重写集。
1. 选择将请求传递到 Azure Spring Cloud 公共终结点的传递规则。
1. 在“重写规则配置”选项卡上，选择“添加重写规则”。
1. **重写类型**：选择“请求头”
1. **操作类型**：选择“删除”
1. **标头名称**：选择“通用标头”
1. **通用标头**：选择“X-Forwarded-Proto”

    ![应用程序网关 4](media/spring-cloud-load-balancers/app-gateway-4.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>将 Azure Spring Cloud 与 Azure Front Door 集成

若要与 Azure Spring Cloud 服务集成并配置后端池，请执行以下步骤：

1. 添加后端池。
1. 通过添加主机指定后端终结点。

    ![Front Door 1](media/spring-cloud-load-balancers/front-door-1.png)

1. 将“后端主机类型”指定为“自定义主机”。
1. 在“后端主机名”中输入 Azure Spring Cloud 公共终结点的 FQDN。
1. 接受默认的“后端主机头”（与“后端主机名”相同）。

    ![Front Door 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>后续步骤

* [如何创建流量管理器](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* [如何创建应用程序网关](../application-gateway/quick-create-portal.md)
* [如何创建 Front Door](../frontdoor/quickstart-create-front-door.md)。
