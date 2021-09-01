---
title: 如何自行诊断 Azure Spring Cloud VNET
description: 了解如何自行诊断和解决 VNET 中运行的 Azure Spring Cloud 的问题。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 087533d0cc63af3d2c7cadbb45b65c2c4fa2615f
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015357"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>自行诊断 VNET 中 Azure Spring Cloud 的问题
Azure Spring Cloud 诊断支持以交互方式排查虚拟网络中运行的应用的问题，且无需完成任何配置。 Azure Spring Cloud 诊断会识别问题并指导你获取有助于排查和解决问题的信息。

## <a name="navigate-to-the-diagnostics-page"></a>导航到诊断页
以下过程将启动联网应用程序的诊断。
1. 登录到 Azure 门户。
1. 转到 Azure Spring Cloud 的“概览”页。
1. 在左侧导航窗格上的菜单中选择“诊断并解决问题”。
1. 选择第三个类别：“网络”。

   ![自行诊断标题](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>查看诊断报告

在选择“网络”类别后，你可以查看特定于 VNet 注入 Azure Spring Cloud 的两个网络相关问题：“DNS 解析”和“所需出站流量”。  

   ![自行诊断选项](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

选择目标问题以查看诊断报告。 将显示诊断摘要，例如：

* “资源已删除。”
* “资源未部署在你自己的虚拟网络中”。

某些结果包含相关文档。 不同的子网将单独显示结果。

## <a name="dns-resolution"></a>DNS 解析

如果选择“DNS 解析”，结果将指示应用程序是否存在 DNS 问题。  正常的应用按如下所示列出：

* “DNS 问题已解决，子网 'subnet01' 中没有问题”。
* “DNS 问题已解决，子网 'subnet02' 中没有问题”。

以下诊断报告示例指示应用程序的运行状况未知。 报告时间范围不包括报告运行状况时的时间。  假设上下文结束时间为 *2021-03-03T04:20:00Z*。 “DNS 解析表呈现结果”中的最新时间戳是 *2021-03-03T03:39:00Z*，即前一天。 运行状况检查日志可能由于网络被阻止而未发出。

未知的运行状况结果包含相关文档。  你可以选择左尖括号以查看下拉显示列表。

![DNS 未知](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

如果错误地配置了专用 DNS 区域记录集，则会收到如下所示的严重结果：`Failed to resolve the Private DNS in subnet xxx`。

在“DNS 解析表呈现结果”下拉列表中，将会看到消息的详细信息，从中可以检查配置。

## <a name="required-outbound-traffic"></a>所需出站流量

如果选择“所需出站流量”，结果将指示应用程序是否存在出站流量问题。  正常的应用按如下所示列出：

* *“所需出站流量已解析，子网 'subnet01' 中没有问题。”
* *“所需出站流量已解析，子网 'subnet02' 中没有问题。”

如果 NSG 或防火墙规则阻止了任何子网，并且你未阻止日志，则会发生以下失败。 可以检查你是否疏忽了任何[客户责任](./vnet-customer-responsibilities.md)。

![终结点失败](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

如果 `Required Outbound Traffic Table Renderings` 在 30 分钟内没有任何数据，则结果将是 `health status unknown`。
可能是网络被阻止或日志服务已关闭。

![诊断终结点未知](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>另请参阅

* [如何自行诊断 Azure Spring Cloud](./how-to-self-diagnose-solve.md)
