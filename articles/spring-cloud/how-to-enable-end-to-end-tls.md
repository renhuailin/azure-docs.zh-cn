---
title: 启用端到端传输层安全性
titleSuffix: Azure Spring Cloud
description: 如何为应用程序启用端到端传输层安全性。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227800"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>为应用程序启用端到端 TLS

本主题演示如何启用端到端 SSL/TLS，以保护从入口控制器到支持 HTTPS 的应用程序的流量。 启用端到端 TLS 并从 keyvault 加载证书后，Azure Spring Cloud 中的所有通信都使用 TLS 进行保护。

   ![受 TLS 保护的通信关系图。](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>先决条件 

- 部署的 Azure Spring Cloud 实例。 按[有关如何通过 Azure CLI 进行部署的快速入门](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli)操作即可入门。
- 如果不熟悉端到端 TLS，请参阅[端到端 TLS 示例](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl)。
- 若要安全地将所需证书加载到 Spring Boot 应用中，可以使用 [keyvault spring boot 起动器](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates)。


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>在现有应用上启用端到端 TLS

使用命令 `az spring-cloud app update --enable-end-to-end-tls` 为应用启用或禁用端到端 TLS。

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>绑定自定义域时启用端到端 TLS

使用命令 `az spring-cloud app custom-domain update --enable-end-to-end-tls` 或 `az spring-cloud app custom-domain bind --enable-end-to-end-tls` 为应用启用或禁用端到端 TLS。

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="verify-end-to-end-tls-status"></a>验证端到端 TLS 状态

使用命令 `az spring-cloud app show` 来检查 `enableEndToEndTls` 的值。
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>后续步骤
* [访问 Config Server 和服务注册表](how-to-access-data-plane-azure-ad-rbac.md)
