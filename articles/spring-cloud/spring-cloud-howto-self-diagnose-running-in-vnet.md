---
title: 如何自行诊断 Azure 春季 Cloud VNET
description: 了解如何在 VNET 中运行的 Azure 春季云中自行诊断和解决问题。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125033"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>在 VNET 中运行 Azure 春季云的自我诊断
Azure 春季云诊断支持无需配置即可在虚拟网络中运行的交互式故障排除应用。 Azure 春季云诊断可识别问题并指导你了解有助于排查和解决这些问题的信息。

## <a name="navigate-to-the-diagnostics-page"></a>导航到 "诊断" 页
以下过程将启动网络应用程序的诊断。
1. 登录到 Azure 门户。
1. 转到 Azure Spring Cloud 的“概览”页。
1. 选择左侧导航窗格中的 " **诊断和解决问题** "。
1. 选择第三个类别，" **网络**"。

   ![自行诊断标题](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>查看诊断报告
单击 " **网络** " 类别后，你可以查看与特定于你的 VNet 的特定网络相关的两个问题： **DNS 解析** 和 **所需的出站流量**。

   ![自行诊断选项](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

选择目标问题以查看诊断报告。 将显示诊断摘要，例如： 

* *资源已被删除。*
* *资源未部署在你自己的虚拟网络中*。

某些结果包含相关文档。 不同的子网将单独显示结果。

## <a name="dns-resolution"></a>DNS 解析 
如果选择 " **DNS 解析**"，则结果将指示应用程序是否存在 DNS 问题。  正常应用按如下方式列出：

* *DNS 问题解决，子网 "subnet01" 中没有问题*。
* *DNS 问题解决，子网 "subnet02" 中没有问题*。

以下诊断报告示例说明应用程序的运行状况未知。 报告时间范围不包括报告运行状况状态的时间。  假定上下文结束时间为 *2021-03-03T04：20： 00Z*。 **DNS 解析表呈现** 中的最新时间戳是 *2021-03-03T03：39： 00Z*，前一天。 运行状况检查日志可能由于网络被阻止而未发出。 

未知的运行状况状态结果包含相关文档。  您可以单击左尖括号以查看下拉显示。

   ![DNS 未知](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

如果对专用 DNS 区域记录集进行了错误配置，将获得如下所示的关键结果： `Failed to resolve the Private DNS in subnet xxx` 。 

在下拉 **DNS 解析表呈现** 中，可以找到详细信息消息信息，你可以从中检查配置。

## <a name="required-outbound-traffic"></a>必需的出站流量 

如果选择 " **所需的出站流量**"，结果将指示应用程序是否存在出站流量问题。  正常应用按如下方式列出：

* * 所需的出站流量解析后，子网 "subnet01" 中没有问题。
* * 所需的出站流量解析后，子网 "subnet02" 中没有问题。

如果任何子网被 NSG 或防火墙规则阻止，并且如果你未阻止日志，则会发现以下故障。 你可以检查是否忽略了任何 [客户责任](spring-cloud-vnet-customer-responsibilities.md)。
    
   ![终结点失败](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

如果在 `Required Outbound Traffic Table Renderings` 30 分钟内没有任何数据，则结果将为 `health status unknown` 。 可能是网络被阻止或日志服务已关闭。

   ![诊断终结点未知](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>另请参阅
* [如何自行诊断 Azure 春季云](spring-cloud-howto-self-diagnose-solve.md)