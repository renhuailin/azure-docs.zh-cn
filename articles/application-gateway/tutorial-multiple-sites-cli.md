---
title: 使用 CLI 托管多个网站
titleSuffix: Azure Application Gateway
description: 了解如何使用 Azure CLI 创建托管多个网站的应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c637f22a21b73450746a90b83ea7c87249da1d45
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113507404"
---
# <a name="create-an-application-gateway-that-hosts-multiple-web-sites-using-the-azure-cli"></a>使用 Azure CLI 创建托管多个网站的应用程序网关

创建[应用程序网关](overview.md)时，可以使用 Azure CLI [配置多个网站的托管](multiple-site-overview.md)。 本文使用虚拟机规模集定义后端地址池。 然后，基于所拥有的域配置侦听器和规则，以确保 Web 流量可到达池中的相应服务器。 本文假定你拥有多个域，并使用示例 www\.contoso.com 和 www\.fabrikam.com。

在本文中，学习如何：

* 设置网络
* 创建应用程序网关
* 创建后端侦听器
* 创建路由规则
* 使用后端池创建虚拟机规模集
* 在域中创建 CNAME 记录

:::image type="content" source="./media/tutorial-multiple-sites-cli/scenario.png" alt-text="多站点应用程序网关":::

如果需要，可以使用 [Azure PowerShell](tutorial-multiple-sites-powershell.md) 完成此过程。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - 本教程需要 Azure CLI 版本 2.0.4 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 [az group create](/cli/azure/group) 创建资源组。

以下示例在 eastus 位置创建名为 myResourceGroupAG 的资源组 。

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>创建网络资源

使用 [az network vnet create](/cli/azure/network/vnet) 创建虚拟网络和名为 *myAGSubnet* 的子网。 然后，可以使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet) 添加后端服务器所需的子网。 使用 [az network public-ip create](/cli/azure/network/public-ip) 创建名为 *myAGPublicIPAddress* 的公共 IP 地址。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-application-gateway"></a>创建应用程序网关

可以使用 [az network application-gateway create](/cli/azure/network/application-gateway#az_network_application_gateway_create) 创建应用程序网关。 使用 Azure CLI 创建应用程序网关时，请指定配置信息，例如容量、sku 和 HTTP 设置。 将应用程序网关分配给之前创建的 *myAGSubnet* 和 *myAGPublicIPAddress*。 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

创建应用程序网关可能需要几分钟时间。 创建应用程序网关后，可以看到它的这些新功能：

- *appGatewayBackendPool* - 应用程序网关必须至少具有一个后端地址池。
- *appGatewayBackendHttpSettings* - 指定将端口 80 和 HTTP 协议用于通信。
- *appGatewayHttpListener* - 与 *appGatewayBackendPool* 关联的默认侦听器。
- *appGatewayFrontendIP* - 将 *myAGPublicIPAddress* 分配给 *appGatewayHttpListener*。
- *rule1* - 与 *appGatewayHttpListener* 关联的默认路由规则。

### <a name="add-the-backend-pools"></a>添加后端池

使用 [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool#az_network_application_gateway_address-pool_create) 添加包含后端服务器所需的后端池
```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name contosoPool

az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name fabrikamPool
```

### <a name="add-listeners"></a>添加侦听器

使用 [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az_network_application_gateway_http_listener_create) 添加路由流量所需的侦听器。

>[!NOTE]
> 通过应用程序网关或 WAF v2 SKU，你还可为每个侦听器配置最多 5 个主机名，并且可在主机名中使用通配符。 要了解详细信息，请参阅[侦听器中的通配符主机名](multiple-site-overview.md#wildcard-host-names-in-listener-preview)。
>若要通过 Azure CLI 在侦听器中使用多个主机名和通配符，则必须使用 `--host-names` 而不是 `--host-name`。 使用 host-names 时，可通过空格分隔值的形式提及最多 5 个主机名。 例如： `--host-names "*.contoso.com *.fabrikam.com"`

```azurecli-interactive
az network application-gateway http-listener create \
  --name contosoListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.contoso.com

az network application-gateway http-listener create \
  --name fabrikamListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port appGatewayFrontendPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway \
  --host-name www.fabrikam.com   
  ```

### <a name="add-routing-rules"></a>添加路由规则

如果未使用规则优先级字段，则按照规则列出顺序处理规则。 使用第一条匹配的规则来定向流量，而不考虑特殊性。 例如，如果在同一端口上同时有使用基本侦听器的规则和使用多站点侦听器的规则，则使用多站点侦听器的规则必须在使用基本侦听器的规则之前列出，多站点规则才能正常运行。

在此示例中，将创建两个新规则并删除在部署应用程序网关时创建的默认规则。 可以使用 [az network application-gateway rule create](/cli/azure/network/application-gateway/rule#az_network_application_gateway_rule_create) 添加规则。

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name contosoRule \
  --resource-group myResourceGroupAG \
  --http-listener contosoListener \
  --rule-type Basic \
  --address-pool contosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name fabrikamRule \
  --resource-group myResourceGroupAG \
  --http-listener fabrikamListener \
  --rule-type Basic \
  --address-pool fabrikamPool

az network application-gateway rule delete \
  --gateway-name myAppGateway \
  --name rule1 \
  --resource-group myResourceGroupAG
```
### <a name="add-priority-to-routing-rules"></a>向路由规则添加优先级

为了确保先处理更具体的规则，请使用规则优先级字段，以确保这些规则具有更高的优先级。 必须为所有现有的请求路由规则设置规则优先级字段，并且以后创建的任何新规则也必须具有规则优先级值。
```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name wccontosoRule \
  --resource-group myResourceGroupAG \
  --http-listener wccontosoListener \
  --rule-type Basic \
  --priority 200 \
  --address-pool wccontosoPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name shopcontosoRule \
  --resource-group myResourceGroupAG \
  --http-listener shopcontosoListener \
  --rule-type Basic \
  --priority 100 \
  --address-pool shopcontosoPool

```

## <a name="create-virtual-machine-scale-sets"></a>创建虚拟机规模集

在此示例中，将创建三个虚拟机规模集以支持应用程序网关中的三个后端池。 创建的规模集分别名为 *myvmss1*、*myvmss2* 和 *myvmss3*。 每个规模集包含两个在其上安装了 IIS 的虚拟机实例。

```azurecli-interactive
for i in `seq 1 2`; do

  if [ $i -eq 1 ]
  then
    poolName="contosoPool"
  fi

  if [ $i -eq 2 ]
  then
    poolName="fabrikamPool"
  fi

  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>安装 NGINX

```azurecli-interactive
for i in `seq 1 2`; do

  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'

done
```

## <a name="create-a-cname-record-in-your-domain"></a>在域中创建 CNAME 记录

使用其公共 IP 地址创建应用程序网关后，可以获取 DNS 地址并使用它在域中创建 CNAME 记录。 可以使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 获取应用程序网关的 DNS 地址。 复制 DNSSettings 的 *fqdn* 值并使用它作为所创建的 CNAME 记录的值。 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [dnsSettings.fqdn] \
  --output tsv
```

建议不要使用 A 记录，因为应用程序网关重启后 VIP 可能会变化。

## <a name="test-the-application-gateway"></a>测试应用程序网关

在浏览器的地址栏中输入域名。 例如，http:\//www.contoso.com。

![在应用程序网关中测试 contoso 站点](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest1.png)

将地址更改为其他域，应看到类似下例所示的内容：

![在应用程序网关中测试 fabrikam 站点](./media/tutorial-multiple-sites-cli/application-gateway-nginxtest2.png)

## <a name="clean-up-resources"></a>清理资源

当不再需要资源组、应用程序网关以及所有相关资源时，请将其删除。

```azurecli-interactive
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>后续步骤

[使用基于 URL 路径的路由规则创建应用程序网关](./tutorial-url-route-cli.md)
