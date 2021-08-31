---
title: 如何在 Azure Spring Cloud 中标识出站公共 IP 地址
description: 如何查看与外部资源（例如，数据库、存储、Key Vault 等）通信的静态出站公共 IP 地址。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: cc3050cb7c92e0e46c02a4a12f218364b94b2d1f
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015543"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>如何在 Azure Spring Cloud 中标识出站公共 IP 地址

本页说明如何查看 Azure Spring Cloud 应用程序的静态出站公共 IP 地址。  公共 IP 用于与外部资源（例如，数据库、存储和密钥保管库）通信。

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>IP 地址在 Azure Spring Cloud 中的工作原理

Azure Spring Cloud 服务有一个或多个出站公共 IP 地址。 出站公共 IP 地址数可能因层级和其他因素而异。

出站公共 IP 地址通常是固定不变的，但有例外。

## <a name="when-outbound-ips-change"></a>出站 IP 更改时

每个 Azure Spring Cloud 实例在任何给定时间都有一定数量的出站公共 IP 地址。 从应用程序发起的任何出站连接（例如，与后端数据库的连接）都使用出站公共 IP 地址之一作为源 IP 地址。 该 IP 地址是在运行时随机选择的，因此后端服务必须对所有出站 IP 地址打开其防火墙。

执行以下操作之一时，出站公共 IP 数会更改：

- 升级层间的 Azure Spring Cloud 实例。
- 提交支持票证以根据业务需求获取更多出站公共 IP。

## <a name="find-outbound-ips"></a>查找出站 IP

若要在 Azure 门户中查找服务实例当前使用的出站公共 IP 地址，请在实例的左侧导航窗格中选择“网络”。 这些 IP 地址会列在“出站 IP 地址”字段中。

在 Cloud Shell 中运行以下命令可以找到相同的信息

```azurecli
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIps.publicIps --output tsv
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
* [了解有关 Azure 资源的托管标识的详细信息](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [详细了解 Azure Spring Cloud 中的密钥保管库](./tutorial-managed-identities-key-vault.md)
