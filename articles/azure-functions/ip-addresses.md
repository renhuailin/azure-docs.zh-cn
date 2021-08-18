---
title: Azure Functions 中的 IP 地址
description: 了解如何查找函数应用的入站和出站 IP 地址，以及这些地址发生更改的原因。
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: a884edd23fa1538fcc2b00c80190eab6699e1e47
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414478"
---
# <a name="ip-addresses-in-azure-functions"></a>Azure Functions 中的 IP 地址

本文介绍以下与函数应用的 IP 地址相关的概念：

* 查找函数应用当前正在使用的 IP 地址。
* 导致函数应用 IP 地址更改的条件。
* 限制可以访问函数应用的 IP 地址。
* 定义函数应用的专用 IP 地址。

IP 地址与函数应用而不是单个函数相关联。 传入的 HTTP 请求不能使用入站 IP 地址来调用单个函数；它们必须使用默认域名 (functionappname.azurewebsites.net) 或自定义域名。

## <a name="function-app-inbound-ip-address"></a>函数应用的入站 IP 地址

每个函数应用具有单个入站 IP 地址。 查找该 IP 地址：

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到函数应用。
3. 在“设置”下，选择“属性”   。 入站 IP 地址显示在“虚拟 IP 地址”下面。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

在本地客户端计算机中使用 `nslookup` 实用工具：

```command
nslookup <APP_NAME>.azurewebsites.net
```

---

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>函数应用的出站 IP 地址

每个函数应用具有一组可用的出站 IP 地址。 从某个函数发起的任何出站连接（例如，与后端数据库的连接）使用某个可用的出站 IP 地址作为源 IP 地址。 无法事先知道给定的连接要使用哪个 IP 地址。 因此，后端服务必须向函数应用的所有出站 IP 地址开放其防火墙。

查找函数应用可用的出站 IP 地址：

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 登录到 [Azure 资源浏览器](https://resources.azure.com)。
2. 选择“订阅”> {你的订阅} >“提供程序”>“Microsoft.Web”>“站点”。
3. 在 JSON 面板中，找到其 `id` 属性以函数应用名称结尾的站点。
4. 请参见 `outboundIpAddresses` 和 `possibleOutboundIpAddresses`。 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp show --resource-group <GROUP_NAME> --name <APP_NAME> --query outboundIpAddresses --output tsv
az functionapp show --resource-group <GROUP_NAME> --name <APP_NAME> --query possibleOutboundIpAddresses --output tsv
```
---

`outboundIpAddresses` 的集当前可供函数应用使用。 `possibleOutboundIpAddresses` 的集包括仅当函数应用[缩放到其他定价层](#outbound-ip-address-changes)时才可用的 IP 地址。

> [!NOTE]
> 对按[消耗计划](consumption-plan.md)或[高级计划](functions-premium-plan.md)运行的函数应用进行缩放时，可能会分配新范围的出站 IP 地址。 在上述任一计划中运行时，不能依赖报告的出站 IP 地址来创建最终的允许列表。 为了能够包含动态缩放期间使用的所有潜在出站地址，需要将整个数据中心添加到允许列表。

## <a name="data-center-outbound-ip-addresses"></a>数据中心出站 IP 地址

如果需要将函数应用使用的出站 IP 地址添加到允许列表，可以采用另一种做法，即，将函数应用的数据中心（Azure 区域）添加到允许列表。 可以[下载列出所有 Azure 数据中心 IP 地址的 JSON 文件](https://www.microsoft.com/en-us/download/details.aspx?id=56519)。 然后，找到应用于运行函数应用的区域的 JSON 片段。

例如，下面的 JSON 片段是西欧允许列表的样子：

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 有关此文件何时更新以及 IP 地址何时更改的信息，请展开[下载中心页](https://www.microsoft.com/en-us/download/details.aspx?id=56519)的“详细信息”部分。

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>入站 IP 地址更改

如果执行以下操作，入站 IP 地址 **可能** 会更改：

- 删除函数应用，然后在不同的资源组中重新创建它。
- 删除资源组和区域组合中的最后一个函数应用，然后重新创建它。
- 删除 TLS 绑定（例如，在[证书续订](../app-service/configure-ssl-certificate.md#renew-certificate)期间）。

当函数应用在[消耗计划](consumption-plan.md)或[高级计划](functions-premium-plan.md)中运行时，即使你未执行任何操作（如[上面列出](#inbound-ip-address-changes)的操作），入站 IP 地址也可能会更改。

## <a name="outbound-ip-address-changes"></a>出站 IP 地址更改

出站 IP 地址的相对稳定性取决于托管计划。  

### <a name="consumption-and-premium-plans"></a>消耗计划和高级计划

由于自动缩放行为，在[消耗计划](consumption-plan.md)或[高级计划](functions-premium-plan.md)上运行时，出站 IP 可能会随时更改。 

如果需要控制函数应用的出站 IP 地址（例如需要将其添加到允许列表中时），在高级托管计划中运行时，请考虑实施[虚拟网络 NAT 网关](#virtual-network-nat-gateway-for-outbound-static-ip)。 还可以通过在专用（应用服务）计划中运行来实现此目的。

### <a name="dedicated-plans"></a>专用计划

在专用（应用服务）计划上运行时，若执行以下操作，函数应用的可用出站 IP 地址集可能会更改：

* 执行可能更改入站 IP 地址的任何操作。
* 更改专用（应用服务）计划的定价层。 应用可在所有定价层中使用的所有可能出站 IP 地址列表在 `possibleOutboundIPAddresses` 属性中指定。 请参阅[查找出站 IP](#find-outbound-ip-addresses)。

#### <a name="forcing-an-outbound-ip-address-change"></a>强制进行出站 IP 地址更改

请使用以下过程在专用（应用服务）计划中有意强制出站 IP 地址发生更改：

1. 在标准和高级 v2 定价层之间纵向缩放应用服务计划。

2. 等待 10 分钟。

3. 缩放回到最初的层。

## <a name="ip-address-restrictions"></a>IP 地址限制

可以配置允许或拒绝其访问函数应用的 IP 地址列表。 有关详细信息，请参阅 [Azure 应用服务静态 IP 限制](../app-service/app-service-ip-restrictions.md)。

## <a name="dedicated-ip-addresses"></a>专用 IP 地址

在函数应用需要静态专用 IP 地址时，可以探索多种策略。 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>用于出站静态 IP 的虚拟网络 NAT 网关

可以通过使用虚拟网络 NAT 网关引导流量通过静态公共 IP 地址，从而控制来自函数的出站流量的 IP 地址。 在[高级计划](functions-premium-plan.md)或[专用（应用服务）计划](dedicated-plan.md)中运行时，可以使用此拓扑。 要了解详细信息，请参阅[教程：使用 Azure 虚拟网络 NAT 网关控制 Azure Functions 出站 IP](functions-how-to-use-nat-gateway.md)。

### <a name="app-service-environments"></a>应用服务环境

若要对入站和出站的 IP 地址都进行完全控制，建议使用[应用服务环境](../app-service/environment/intro.md)（应用服务计划的[独立层](https://azure.microsoft.com/pricing/details/app-service/)）。 有关详细信息，请参阅[应用服务环境的 IP 地址](../app-service/environment/network-info.md#ase-ip-addresses)和[如何控制应用服务环境的入站流量](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)。

确定函数应用是否在应用服务环境中运行：

# <a name="azure-porta"></a>[Azure 门户](#tab/portal)

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 导航到函数应用。
3. 选择“概述”选项卡。
4. 应用服务计划层显示在“应用服务计划/定价层”下面。 应用服务环境定价层为“隔离”。

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

---

应用服务环境的 `sku` 为“`Isolated`”。

## <a name="next-steps"></a>后续步骤

IP 发生更改的常见原因之一是函数应用的规模发生更改。 [详细了解函数应用的缩放](functions-scale.md)。
