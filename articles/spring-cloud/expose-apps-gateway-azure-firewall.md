---
title: 使用应用程序网关和 Azure 防火墙向 Internet 公开应用程序
description: 如何使用应用程序网关和 Azure 防火墙向 Internet 公开应用程序
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: e87ccabeb2d0e0cd837a835c5ac637bebc68b8b4
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861955"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>使用应用程序网关和 Azure 防火墙向 Internet 公开应用程序

本文介绍如何使用应用程序网关和 Azure 防火墙向 Internet 公开应用程序。 在虚拟网络中部署 Azure Spring Cloud 服务实例时，只能在专用网络中访问该服务实例上的应用程序。 若要使应用程序可在 Internet 上访问，需要与 Azure 应用程序网关集成，或者选择性地与 Azure 防火墙集成 。

## <a name="prerequisites"></a>先决条件

- [Azure CLI 2.0.4 版或更高版本](/cli/azure/install-azure-cli)。

## <a name="define-variables"></a>定义变量

按照[在 Azure 虚拟网络中部署 Azure Spring Cloud（VNet 注入）](./how-to-deploy-in-azure-virtual-network.md)中的说明，为创建的资源组和虚拟网络定义变量。 根据真实环境自定义值。  定义 SPRING_APP_PRIVATE_FQDN 时，请从 uri 中删除“https”。

```bash
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 Azure CLI 并选择你的有效订阅。

```azurecli
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>创建网络资源

要创建的 Azure 应用程序网关将加入与 Azure Spring Cloud 服务实例相同或对等的虚拟网络。 首先使用 `az network vnet subnet create` 为虚拟网络中的应用程序网关创建新的子网，再使用 `az network public-ip create` 创建一个公共 IP 地址作为应用程序网关的前端。

```azurecli
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>创建应用程序网关

使用 `az network application-gateway create` 创建应用程序网关，并指定应用程序的专用完全限定的域名 (FQDN) 作为后端池中的服务器。 然后使用 `az network application-gateway http-settings update` 更新 HTTP 设置以使用后端池中的主机名。

```azurecli
APPLICATION_GATEWAY_NAME='my-app-gw'
APPLICATION_GATEWAY_PROBE_NAME='my-probe'
APPLICATION_GATEWAY_REWRITE_SET_NAME='my-rewrite-set'
APPLICATION_GATEWAY_REWRITE_RULE_NAME='remove-request-header'
APPLICATION_GATEWAY_RULE_NAME='rule1'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway probe create \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PROBE_NAME} \
    --protocol https \
    --host-name-from-http-settings true \
    --path /
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true \
    --probe ${APPLICATION_GATEWAY_PROBE_NAME}
az network application-gateway rewrite-rule set create \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_REWRITE_SET_NAME}
az network application-gateway rewrite-rule create \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --rule-set-name ${APPLICATION_GATEWAY_REWRITE_SET_NAME} \
    --name ${APPLICATION_GATEWAY_REWRITE_RULE_NAME} \
    --request-headers X-Forwarded-Proto="https"
az network application-gateway rule update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_RULE_NAME} \
    --rewrite-rule-set ${APPLICATION_GATEWAY_REWRITE_SET_NAME}
```

Azure 可能需要长达 30 分钟的时间来创建应用程序网关。 创建后，使用 `az network application-gateway show-backend-health` 检查后端运行状况。  这将检查应用程序网关是否通过其专用 FQDN 到达应用程序。

```azurecli
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

输出指示后端池的运行状况。

```output
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>使用应用程序网关的前端公共 IP 访问应用程序

使用 `az network public-ip show` 获取应用程序网关的公共 IP 地址。

```azurecli
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。

![公共 IP 中的应用](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>请参阅

- [在 VNET 中排除 Azure Spring Cloud 的故障](./troubleshooting-vnet.md)
- [在 VNET 中运行 Azure Spring Cloud 的客户责任](./vnet-customer-responsibilities.md)
