---
title: Azure Stack Edge Pro GPU/Pro R/Mini R 的数据驻留和复原能力
description: 介绍 Azure Stack Edge 的数据驻留状态。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: alkohli
ms.openlocfilehash: 6edb7aafdee046f2dd198d54e566c69b791cc1eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778150"
---
# <a name="data-residency-and-resiliency-for-azure-stack-edge-preview"></a>Azure Stack Edge 的数据驻留和复原能力（预览）

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍的信息可帮助了解 Azure Stack Edge 的数据驻留和复原能力以及如何在服务中启用数据驻留。  

## <a name="about-data-residency-for-azure-stack-edge"></a>关于 Azure Stack Edge 的数据驻留 

Azure Stack Edge 服务在服务可用的所有地理位置存储和处理客户数据时，使用 [Azure 区域对](../best-practices-availability-paired-regions.md#azure-regional-pairs)。 对于东南亚（新加坡）区域，该服务当前与中国香港特别行政区配对。 Azure 区域配对意味着存储在新加坡的任何数据都会复制到香港特别行政区。 新加坡已制定法律，要求客户数据不得离开其国家边界。 

为了确保客户数据仅驻留在单个区域，Azure Stack Edge 服务中启用了一个新选项。 选择此选项后，服务将仅在新加坡区域存储和处理客户数据。 客户数据不会复制到香港特别行政区。 特定于服务的元数据（非敏感数据）仍会复制到配对区域。  

启用此选项后，服务可以灵活应对局部区域范围的中断，但不能应对全区域范围的中断。 如果应对全区域范围的中断对你很重要，则应该选择基于区域对的复制。

单一区域数据驻留选项仅适用于东南亚（新加坡）。 对于所有其他区域，Azure Stack Edge 在客户指定的地理位置存储和处理客户数据。

Azure Stack Edge 服务的数据驻留状态可以概括为服务的以下几个方面：

- 现有的 Azure Stack Edge 订购和管理服务。
- 新的 Azure Edge Hardware Center（预览版），用于今后的新订单。
<!--- Telemetry for the device and the service.
- Proactive Support log collection where any logs that the service generates are stored in a single region and are not replicated to the paired region.-->

Azure Stack Edge 服务还与以下相关服务集成，并总结了它们的行为： 

- 已启用 Azure Arc 的 Kubernetes
- Azure IoT 中心和 Azure IoT Edge
<!--- Azure Key Vault -->


## <a name="azure-stack-edge-classic-ordering-and-management-resource"></a>Azure Stack Edge 经典订购和管理资源 

如果使用经典体验订购 Azure Stack Edge，则该服务当前使用 Azure 区域对来实现针对全区域范围中断的数据复原。 对于新加坡现有的 Azure Stack Edge 资源，数据将复制到香港特别行政区。

如果要创建新的 Azure Stack Edge 资源，可以选择仅在新加坡启用数据驻留。 选择此选项后，数据不会复制到香港。 如果发生全区域范围的服务中断，你有两种选择：

- 等待新加坡区域恢复。

- 在另一区域创建资源、重置设备，并通过新资源管理设备。 有关详细说明，请参阅[重置并重新激活 Azure Stack Edge 设备](azure-stack-edge-reset-reactivate-device.md)。

## <a name="azure-edge-hardware-center-ordering-and-management-resource"></a>Azure Edge Hardware Center 订购和管理资源 

新的 Azure Edge Hardware Center 服务（预览版）现已推出，可用于创建和管理 Azure Stack Edge 资源。 在东南亚区域下单时，可以选择让数据仅驻留在新加坡而不进行复制。 

如果发生全区域范围的中断，你将无法访问订单资源。 你将无法返回、取消或删除资源。 如果在服务中断期间请求更新订单状态或需要紧急启动设备退货，Microsoft 支持部门将处理这些请求。

有关详细说明，请参阅[通过 Azure Edge Hardware Center 创建订单](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)。


<!--## Azure Stack Edge telemetry

As Azure Stack Edge is a first-party Microsoft device, the telemetry from the device is automatically collected (without the user consent) and sent to Microsoft. This telemetry is stored in a common central location. This gathered telemetry provides valuable insights into enterprise deployments of Azure Stack Edge. This telemetry is also used for security, health, quality, and performance analysis.

- Microsoft collects telemetry for the infrastructure VMs (for example, Kubernetes master VM and Kubernetes worker VM) deployed on your Azure Stack Edge device and hosts. Telemetry is also gathered for other services that run on Azure Stack Edge device (for example, local Azure Resource Manager, Kubernetes dashboard). 
- The telemetry data is encrypted-in-transit as well at rest.
- Raw telemetry data sent to Microsoft is retained for 90 days. Aggregated data is retained for longer.
- For all the containerized workloads (deployed via IoT Edge and Kubernetes) and VM workloads, the application data is considered as the customer data. This data can only be accessed by the customer unless it pertains to the underlying infrastructure. 

For more information, see [Use the Kubernetes dashboard to monitor the Kubernetes cluster health on your Azure Stack Edge Pro device](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).-->

## <a name="azure-stack-edge-dependent-services"></a>Azure Stack Edge 相关服务

已启用 Azure Arc 的 Kubernetes、Azure IoT 中心和 Azure IoT Edge 以及 Azure Key Vault 是与 Azure Stack Edge 集成的服务。

### <a name="azure-arc-enabled-kubernetes"></a>已启用 Azure Arc 的 Kubernetes 

已启用 Azure Arc 的 Kubernetes 可作为 Azure Stack Edge 的附加产品使用。 对于新加坡（东南亚），Azure Arc 数据仅驻留在新加坡，不会复制到香港特别行政区。 <!--If there is a region-wide outage, the service is not resilient.-->

<!--For all other regions, Azure Arc supports Azure Regional Pair and is resilient to any region-wide outages.--> 
<!--For more information, see [Data residency and resiliency for Azure Arc enabled Kubernetes clusters]().-->


### <a name="azure-iot"></a>Azure IoT

Azure IoT 可作为 Azure Stack Edge 的附加产品使用。 对于新加坡（东南亚），Azure IoT 使用配对区域并将数据复制到香港特别行政区。 这意味着 Azure IoT 可灵活应对全区域范围的中断。 

<!--For more information, see [Data residency and resiliency for Azure IoT]().-->


<!--### Azure Key Vault

Azure Key Vault currently uses Azure Regional Pair for region outage resiliency. For new Azure Key Vault resources, an option is now available that can be enabled at the subscription level. When enabled, if your service is deployed in Singapore (Southeast Asia), you can control the data replication to Hong Kong. 

If you choose to store and process the data only in Singapore region, then the service will not be resilient to region-wide outages. -->
<!--For more information, see [Data residency and resiliency for Azure Key Vault]().-->

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 数据驻留要求](https://azure.microsoft.com/global-infrastructure/data-residency/)。