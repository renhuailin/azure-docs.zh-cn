---
title: 使用应用程序网关和 Azure 防火墙向 Internet 公开应用程序
description: 如何使用应用程序网关和 Azure 防火墙向 Internet 公开应用程序
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738712"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>使用应用程序网关和 Azure 防火墙向 Internet 公开应用程序

本文档说明如何使用应用程序网关和 Azure 防火墙向 Internet 公开应用程序。 在虚拟网络中部署 Azure 春季云服务实例时，服务实例上的应用程序只能在专用网络中访问。 若要使应用程序可在 Internet 上访问，需要与 **Azure 应用程序网关** 集成，还可以选择与 **Azure 防火墙** 集成。

## <a name="prerequisites"></a>先决条件

- [Azure CLI 2.0.4 版或更高版本](/cli/azure/install-azure-cli)。

## <a name="define-variables"></a>定义变量

按照在 [azure 虚拟网络中部署 Azure 春季 Cloud (VNet 注入) ](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)中的说明，为创建的资源组和虚拟网络定义变量。 根据真实环境自定义值。

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>登录到 Azure

登录到 Azure CLI 并选择你的有效订阅。

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>创建网络资源

要创建的 **Azure 应用程序网关** 会将与--或对等互连虚拟网络相同的虚拟网络加入到 Azure 春季云服务实例。 首先使用为虚拟网络中的应用程序网关创建新的子网 `az network vnet subnet create` ，并使用创建公共 IP 地址作为应用程序网关的前端 `az network public-ip create` 。

```
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

使用创建应用程序网关 `az network application-gateway create` ，并指定应用程序的专用完全限定域名 (FQDN) 为后端池中的服务器。 然后使用更新 HTTP 设置 `az network application-gateway http-settings update` 以使用后端池中的主机名。

```
APPLICATION_GATEWAY_NAME='my-app-gw'
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
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Azure 可能需要长达 30 分钟的时间来创建应用程序网关。 创建后，请使用检查后端运行状况 `az network application-gateway show-backend-health` 。  这会检查应用程序网关是否通过其专用 FQDN 到达了应用程序。

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

输出指示后端池的正常状态。

```
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

使用获取应用程序网关的公共 IP 地址 `az network public-ip show` 。

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。

  ![公共 IP 中的应用](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>请参阅

- [在 VNET 中排除 Azure Spring Cloud 的故障](spring-cloud-troubleshooting-vnet.md)
- [在 VNET 中运行 Azure Spring Cloud 的客户责任](spring-cloud-vnet-customer-responsibilities.md)