---
title: 什么是工作区？ -Azure 医疗保健 Api
description: 本文介绍 Azure 医疗保健 Api 工作区的概述。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 07/12/2021
ms.author: ginle
ms.openlocfilehash: 6eb5fa5186b7f28954724f27186b972f7efb08b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783049"
---
# <a name="what-is-healthcare-apis-preview-workspace"></a>什么是医疗保健 Api (预览) 工作区？

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Azure 医疗保健 Api 工作区是适用于所有医疗保健服务实例的逻辑容器，如快速医疗保健互操作性资源 (FHIR®) 服务、医学中的数字图像和通信 (DICOM®) 服务以及物联网 (IoT) 连接器。 该工作区还会创建一个符合性边界 (HIPAA、HITRUST) ，受保护的健康信息可在该范围内传输。

你可以在工作区中设置多个数据服务，并且可以通过设计使它们相互无缝地工作。 通过工作区，你可以组织所有的医疗保健 Api 实例，并管理在适用的所有基础数据集和服务之间共享的某些配置设置。

## <a name="workspace-provisioning-process"></a>工作区预配过程
 
一个或多个工作区可以在 Azure 门户的资源组中创建，也可以使用部署脚本创建。 在创建一个或多个子服务实例之前，必须先创建一个医疗保健 Api 工作区，作为分层服务树中的父项。   
 
除非已删除工作区中的所有子服务实例，否则无法删除工作区。 此功能可帮助防止意外删除服务实例。 但是，在删除工作区资源组时，工作区资源组中的所有工作区和子服务实例都将被删除。 

## <a name="workspace-and-azure-region-selection"></a>工作区和 Azure 区域选择 
 
创建工作区时，必须为 Azure 区域配置该工作区，该区域可以与资源组相同或不同。 创建工作区后，不能更改区域。 在每个工作区中，必须在工作区的区域中创建所有医疗保健 api 服务 (FHIR service、DICOM 服务和 IoT Connector 服务) ，并且不能将其移动到其他工作区。 

## <a name="workspace-and-azure-healthcare-apis-service-instances"></a>工作区和 Azure 医疗保健 Api 服务实例 

创建 Azure 医疗保健 Api 工作区后，便可以从 Azure 门户创建一个或多个服务实例。 可以在一个工作区中创建同一类型或不同类型的多个服务实例。 在工作区中，你可以将共享配置设置应用到 "工作区和配置设置" 部分中介绍的子服务实例。

[![Azure 资源组 ](media/azure-resource-group.png) ](media/azure-resource-group.png#lightbox)

此外，还可以使用 Azure 资源管理器部署模板创建工作区，该过程通常称为基础结构即代码 (IaC) 。 此选项提供了自定义 ARM 模板并在组合步骤中完成工作区创建和服务实例创建的功能。 

可以使用 PowerShell、CLI、Terraform 脚本或 .NET SDK 来部署医疗保健 Api 服务。 若要在工作区中创建服务实例，请选择 " **创建** (FHIR service"、"DICOM 服务" 或 "IoT 连接器") ，然后输入正在创建的该服务实例的帐户详细信息。


## <a name="fhir-service"></a>FHIR 服务

FHIR 服务包含 Azure 中的 FHIR Api 和终结点，用于 FHIR 数据格式的数据访问和存储。 FHIR 服务在安全且合规的云环境中 (PHI) 管理受保护的健康信息。 部署 FHIR 服务后，你可以根据 HL7 发布的 [FHIR](https://www.hl7.org/fhir/index.html) 互操作数据标准，将多个系统中的临床 heath 数据整合到 Azure 云中。 有关详细信息，请参阅 [关于 FHIR 服务](./fhir/overview.md)。

## <a name="dicom-service"></a>DICOM 服务

部署 DICOM 服务，从任何启用 DICOMwebTM 的系统中将医疗图像数据引入到云中。 有关详细信息，请参阅 [DICOM 服务概述](dicom/dicom-services-overview.md)。

## <a name="iot-connector"></a>IoT 连接器

IoT Connector 服务使你能够以一种可缩放、安全且合规的方式将高频率 IoT 设备数据引入 FHIR 服务。 有关详细信息，请参阅 [IoT 连接器文档页](./iot/index.yml)。
 
## <a name="workspace-configuration-settings"></a>工作区配置设置

某些功能是在工作区级别配置的，并应用于该工作区中的所有子服务。

### <a name="application-monitoring"></a>应用程序监视

Azure Monitor 可帮助最大程度地提高应用程序和服务的可用性和性能。 它提供了一个全面的解决方案，用于从云和本地环境收集、分析和处理遥测数据。 此信息可让你深入了解你的应用程序的执行情况，并使你能够主动识别并解决影响它们的问题及其依赖的资源。 有关 Azure Monitor 的信息，请参阅 [Azure Monitor 概述](../azure-monitor/index.yml) 文档。

### <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)

基于 Azure 角色的访问控制 (Azure RBAC) 是一个系统，它为 Azure 资源提供精细的访问管理。 使用 Azure RBAC，可以在团队中实现职责分离，并且可以仅向用户授予执行作业所需的访问权限。 此外，它还可帮助你管理有权访问 Azure 资源的人员、他们可以对这些资源执行哪些操作以及他们有权访问哪些区域。 有关详细信息，请参阅 [AZURE RBAC](../role-based-access-control/index.yml) 文档。


## <a name="next-steps"></a>后续步骤

若要开始使用 Azure 医疗保健 Api，请遵循5分钟的快速入门来部署工作区。

>[!div class="nextstepaction"]
>[在 Azure 门户中部署工作区](healthcare-apis-quickstart.md)