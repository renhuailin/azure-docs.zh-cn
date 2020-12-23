---
title: 如何在 Azure 春季云中识别出站公共 IP 地址
description: 如何查看静态出站公共 IP 地址以与外部资源（例如数据库、存储、Key Vault 等）进行通信。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663479"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>如何在 Azure 春季云中识别出站公共 IP 地址

本页说明如何查看 Azure 春季云应用程序的静态出站公共 IP 地址。  公共 Ip 用于与外部资源（例如数据库、存储和密钥保管库）进行通信。

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Azure 春季云中 IP 地址的工作方式

Azure 春季云服务有一个或多个出站公共 IP 地址。 出站公共 IP 地址的数量可能因层和其他因素而异。 

出站公共 IP 地址通常是固定的并且保持不变，但有一些例外。

## <a name="when-outbound-ips-change"></a>出站 IP 更改时

在任意给定时间，每个 Azure 春季云实例都有一组设置的出站公共 IP 地址。 任何来自应用程序的出站连接（如到后端数据库）都使用一个出站公共 IP 地址作为源 IP 地址。 IP 地址是在运行时随机选择的，因此后端服务必须将其防火墙打开到所有出站 IP 地址。

执行下列操作之一时，出站公共 Ip 更改的数目：

- 在层间升级 Azure 春季云实例。
- 为满足业务需求的更多出站公共 Ip 提出支持票证。

## <a name="find-outbound-ips"></a>查找出站 IP

若要查找 Azure 门户中的服务实例当前使用的出站公共 IP 地址，请在实例的左侧导航窗格中单击 " **网络** "。 它们列在 " **出站 IP 地址** " 字段中。

可以通过在 Cloud Shell 中运行以下命令来查找相同的信息。

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
* [了解有关 Azure 资源的托管标识的详细信息](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [详细了解 Azure 春季云中的密钥保管库](spring-cloud-tutorial-managed-identities-key-vault.md)
