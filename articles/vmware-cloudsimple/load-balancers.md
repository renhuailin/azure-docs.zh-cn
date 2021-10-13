---
title: Azure VMware Solution by CloudSimple - 为 CloudSimple 私有云选择负载均衡解决方案
description: 介绍在私有云中部署应用程序的负载均衡选项
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36ed6b55b3f97f53a0b8be4829dff97bd0d9c120
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616372"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>为 CloudSimple 私有云选择负载均衡解决方案

在 CloudSimple 私有云中部署应用程序时，可以选择任一负载均衡选项。

可以在 CloudSimple 私有云中选择虚拟或基于软件的负载均衡器，甚至可以使用 Azure 订阅中运行的 Azure L7 负载均衡器，以在前端管理 CloudSimple 私有云中运行的 Web 层 VM。 此处列出了几个选项：

## <a name="virtual-load-balancers"></a>虚拟负载均衡器

可以通过 vCenter 接口在 VMware 环境中部署虚拟负载均衡器设备，并将其配置为在前端管理应用程序流量。

一些热门供应商包括：NginX： http://nginx.org/en/docs/http/load_balancing.html F5- BigIP - 流量管理器： https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC： https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 负载均衡器

针对私有云中运行的应用程序使用 Azure 应用程序网关作为 L7 负载均衡器时，无需管理负载均衡器软件。 负载均衡器软件由 Azure 管理。 私有云中的所有 Web 层 VM 均使用专用 IP 地址，无需其他 NAT 规则或公共 IP 地址来解析名称。 Web 层 VM 通过专用、低延迟、高带宽的连接与 Azure 应用程序网关进行通信。

若要详细了解如何配置此解决方案，请参阅《将 Azure 应用程序网关用作 L7 负载均衡器》的解决方案指南。

## <a name="azure-internal-load-balancer"></a>Azure 内部负载均衡器

如果选择在混合部署（其中 Web 前端层在 Azure 订阅的 Azure vNet 中运行，应用程序的 DB 层在 CloudSimple 私有云的 VMware VM 中运行）中运行应用程序，则可以在 DB 层 VM 前面使用 Azure 内部负载均衡器（L4 负载均衡器）进行流量管理。

若要了解详细信息，请参阅 Azure [内部负载均衡器](../load-balancer/components.md#frontend-ip-configurations)文档。

## <a name="global-server-load-balancer"></a>全局服务器负载均衡器

如果你正在寻找基于 DNS 的负载均衡器，则可以使用 Azure 市场中提供的第三方解决方案，也可以使用本机 Azure 解决方案。

Azure 流量管理器是一种基于 DNS 的流量负载均衡器，可以在全球 Azure 区域内和本地以最佳方式向服务分发流量，同时提供高可用性和响应性。 若要了解详细信息，请参阅 Azure [流量管理器](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)文档。
