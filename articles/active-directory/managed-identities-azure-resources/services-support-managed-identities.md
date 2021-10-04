---
title: 支持托管标识的 Azure 服务 - Azure AD
description: 支持 Azure 资源托管标识和 Azure AD 身份验证的服务列表
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 07/13/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: c9167111a1799575b5dc0d06cb26bf7cc20002e9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124826806"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>支持 Azure 资源托管标识的服务

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 使用托管标识，可对支持 Azure AD 身份验证的任何服务进行身份验证，而无需在代码中插入凭据。 我们正在跨 Azure 将 Azure 资源托管标识与 Azure AD 身份验证集成。 请经常再回来看看，确定这部分内容是否有更新。

> [!NOTE]
> Azure 资源托管标识是以前称为托管服务标识 (MSI) 的服务的新名称。

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>支持 Azure 资源托管标识的 Azure 服务

以下 Azure 服务支持 Azure 资源托管标识：


### <a name="azure-api-management"></a>Azure API 管理

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |
| 用户分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |

请参阅以下列表来配置 Azure API 管理的托管标识（在可用的区域中）：

- [Azure Resource Manager 模板](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure 应用配置

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |
| 用户分配 | ![可用][check] | ![可用][check]  | 不可用  | ![可用][check] |

请参阅以下列表来配置 Azure 应用配置的托管标识（在可用的区域中）：

- [Azure CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure 应用服务

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |
| 用户分配 | ![可用][check] | ![可用][check]  | ![可用][check]  | ![可用][check] |

请参阅以下列表来配置 Azure 应用服务的托管标识（在可用的区域中）：

- [Azure 门户](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager 模板](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>已启用 Azure Arc 的 Kubernetes

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | 预览 | 不可用 | 不可用 | 不可用 |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

已启用 Azure Arc 的 Kubernetes 目前[支持系统分配的标识](../../azure-arc/kubernetes/quickstart-connect-cluster.md)。 托管服务标识证书由所有已启用 Azure Arc 的 Kubernetes 代理用于与 Azure 进行通信。

### <a name="azure-arc-enabled-servers"></a>已启用 Azure Arc 的服务器

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | 不可用 |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

所有启用了 Azure Arc 的服务器都有系统分配的标识。 无法在启用了 Azure Arc 的服务器上禁用或更改系统分配的标识。 请参阅以下资源，详细了解如何在启用了 Azure Arc 的服务器上使用托管标识：

- [使用已启用 Azure Arc 的服务器对 Azure 资源进行身份验证](../../azure-arc/servers/managed-identity-authentication.md)
- [将托管标识用于已启用 Azure Arc 的服务器](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-azure-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure 自动管理

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | 预览 | 不可用 | 不可用 | 不可用 |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

如果已将订阅移到新租户，请参阅以下文档以重新配置托管标识：

* [修复损坏的 Automanage 帐户](../../automanage/repair-automanage-account.md)

### <a name="azure-automation"></a>Azure 自动化

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | 预览 | 预览 | 不可用 | 预览 |
| 用户分配 | 预览 | 预览 | 不可用 | 预览 |

若要通过[Azure 自动化](../../automation/automation-intro.md)托管标识，请参阅以下文档：

* [自动化帐户身份验证概述 - 托管标识](../../automation/automation-security-overview.md#managed-identities-preview)
* [启用和使用托管标识来实现自动化](../../automation/enable-managed-identity-for-automation.md)

### <a name="azure-blueprints"></a>Azure 蓝图

|托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | 不可用 |
| 用户分配 | ![可用][check] | ![可用][check] | 不可用 | 不可用 |

若要将托管标识用于 [Azure 蓝图](../../governance/blueprints/overview.md)，请参阅以下列表：

- [Azure 门户 - 蓝图分配](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - 蓝图分配](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure 认知搜索

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

### <a name="azure-cognitive-services"></a>Azure 认知服务

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

### <a name="azure-container-instances"></a>Azure 容器实例

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | Linux：预览<br>Windows:不可用 | 不可用 | 不可用 | 不可用 |
| 用户分配 | Linux：预览<br>Windows:不可用 | 不可用 | 不可用 | 不可用 |

请参阅以下列表来配置 Azure 容器实例的托管标识（在可用的区域中）：

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager 模板](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure 容器注册表任务

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | 预览 | 不可用 | 预览 |
| 用户分配 | 预览 | 预览 | 不可用 | 预览 |

请参阅以下列表来配置 Azure 容器注册表任务的托管标识（在可用的区域中）：

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure 数据资源管理器

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

### <a name="azure-data-factory-v2"></a>Azure 数据工厂 V2

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

请参阅以下列表来配置 Azure 数据工厂 V2 的托管标识（在可用的区域中）：

- [Azure 门户](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)

### <a name="azure-digital-twins"></a>Azure 数字孪生

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | 不可用 | 不可用 | 不可用 |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

请参阅以下列表，为 Azure 数字孪生配置托管标识（在可用的区域中）：

- [Azure 门户](../../digital-twins/how-to-route-with-managed-identity.md)

### <a name="azure-event-grid"></a>Azure 事件网格

托管标识类型 |所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | 预览 | 预览 | 不可用 | 预览 |
| 用户分配 | 预览 | 预览 | 不可用 | 预览 |

### <a name="azure-firewall-policy"></a>Azure 防火墙策略

托管标识类型 |所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | 不可用 | 不可用 | 不可用 | 不可用 |
| 用户分配 | 预览 | 不可用  | 不可用  | 不可用 |

### <a name="azure-functions"></a>Azure Functions

托管标识类型 |所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |
| 用户分配 | ![可用][check] | ![可用][check]  | ![可用][check]  | ![可用][check]  |

请参阅以下列表来配置 Azure Functions 的托管标识（在可用的区域中）：

- [Azure 门户](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager 模板](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT 中心

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |
| 用户分配 | ![可用][check] | 不可用 | 不可用 | 不可用 |

请参阅以下列表，为 Azure IoT 中心配置托管标识（在可用的区域中）：

- 有关详细信息，请参阅 [Azure IoT 中心对托管标识的支持](../../iot-hub/iot-hub-managed-identity.md)。

### <a name="azure-importexport"></a>Azure 导入/导出

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | --- | --- | --- | --- |
| 系统分配 | 在提供 Azure 导入/导出服务的区域中可用 | 预览 | 可用 | 可用 |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes 服务 (AKS)

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | 不可用 |
| 用户分配 | 预览 | ![可用][check] | 不可用 | 不可用 |


有关详细信息，请参阅[在 Azure Kubernetes 服务中使用托管标识](../../aks/use-managed-identity.md)。

### <a name="azure-log-analytics-cluster"></a>Azure Log Analytics 群集

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |
| 用户分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |

有关详细信息，请参阅[标识在 Azure Monitor 中的工作原理](../../azure-monitor/logs/customer-managed-keys.md)

### <a name="azure-logic-apps"></a>Azure 逻辑应用

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |
| 用户分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |


请参阅以下列表来配置 Azure 逻辑应用的托管标识（在可用的区域中）：

- [Azure 门户](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager 模板](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure 机器学习

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | 预览 | 不可用 | 不可用 | 不可用 |
| 用户分配 | 预览 | 不可用 | 不可用 | 不可用 |

有关详细信息，请参阅[将托管标识与 Azure 机器学习配合使用](../../machine-learning/how-to-use-managed-identities.md)。

### <a name="azure-media-services"></a>Azure 媒体服务

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | 不可用 | ![可用][check] |
| 用户分配 | 不可用  | 不可用  | 不可用  | 不可用  |

请参阅以下列表来配置 Azure 媒体服务的托管标识（在可用的区域中）：

- [Azure CLI](../../media-services/latest/security-access-storage-managed-identity-cli-tutorial.md)

### <a name="azure-policy"></a>Azure Policy

|托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

请参阅以下列表来配置 Azure Policy 的托管标识（在可用的区域中）：

- [Azure 门户](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment#az_policy_assignment_create)
- [Azure 资源管理器模板](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/policy/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Service Fabric 应用程序的托管标识](../../service-fabric/concepts-managed-identity.md)在所有区域中均可用。

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | 不可用 | 不可用 | 不可用 |
| 用户分配 | ![可用][check] | 不可用 | 不可用 |不可用 |

请参考下表为所有区域中的 Azure Service Fabric 应用程序配置托管标识：

- [Azure Resource Manager 模板](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | 不可用 | 不可用 | ![可用][check] |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |


有关详细信息，请参阅[如何为 Azure Spring Cloud 应用程序启用系统分配的托管标识](../../spring-cloud/how-to-enable-system-assigned-managed-identity.md)。

### <a name="azure-stack-edge"></a>Azure Stack Edge

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | --- | --- | --- | --- |
| 系统分配 | 在提供 Azure Stack Edge 服务的区域中可用 | 不可用 | 不可用 | 不可用 |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

### <a name="azure-virtual-machine-scale-sets"></a>Azure 虚拟机规模集

|托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |
| 用户分配 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |

请参阅以下列表来配置 Azure 虚拟机规模集的托管标识（在可用的区域中）：

- [Azure 门户](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 资源管理器模板](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure 虚拟机

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |
| 用户分配 | ![可用][check] | ![可用][check] | ![可用][check] | ![可用][check] |

请参阅以下列表来配置 Azure 虚拟机的托管标识（在可用的区域中）：

- [Azure 门户](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 资源管理器模板](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure SDK](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM 映像生成器

| 托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | 不可用 | 不可用 | 不可用 | 不可用 |
| 用户分配 | [在支持的区域中可用](../../virtual-machines/image-builder-overview.md#regions) | 不可用 | 不可用 | 不可用 |

要了解如何配置 Azure VM 映像生成器的托管标识（在可用的区域中），请参阅[映像生成器概述](../../virtual-machines/image-builder-overview.md#permissions)。
### <a name="azure-signalr-service"></a>Azure SignalR 服务

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | 预览 | 预览 | 不可用 | 预览 |
| 用户分配 | 预览 | 预览 | 不可用 | 预览 |

请参阅以下列表来配置 Azure SignalR 服务的托管标识（在可用的区域中）：

- [Azure Resource Manager 模板](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure 资源移动器

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | 在提供 Azure 资源移动服务的区域中可用 | 不可用 | 不可用 | 不可用 |
| 用户分配 | 不可用 | 不可用 | 不可用 | 不可用 |

请参阅以下文档来使用 Azure 资源移动服务：

- [Azure 资源移动器](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>支持 Azure AD 身份验证的 Azure 服务

以下服务支持 Azure AD 身份验证，已通过使用 Azure 资源托管标识的客户端服务进行测试。

### <a name="azure-resource-manager"></a>Azure 资源管理器

请参阅以下列表配置对 Azure 资源管理器的访问权限：

- [通过 Azure 门户分配访问权限](howto-assign-access-portal.md)
- [通过 PowerShell 分配访问权限](howto-assign-access-powershell.md)
- [通过 Azure CLI 分配访问权限](howto-assign-access-CLI.md)
- [通过 Azure 资源管理器模板分配访问权限](../../role-based-access-control/role-assignments-template.md)

| 云 | 资源 ID | 状态 |
|--------|------------|:-:|
| Azure 全球 | `https://management.azure.com/`| ![可用][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![可用][check] |
| Azure 德国 | `https://management.microsoftazure.de/` | ![可用][check] |
| Azure 中国世纪互联 | `https://management.chinacloudapi.cn` | ![可用][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| 云 | 资源 ID | 状态 |
|--------|------------|:-:|
| Azure 全球 | `https://vault.azure.net`| ![可用][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![可用][check] |
| Azure 德国 |  `https://vault.microsoftazure.de` | ![可用][check] |
| Azure 中国世纪互联 | `https://vault.azure.cn` | ![可用][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| 云 | 资源 ID | 状态 |
|--------|------------|:-:|
| Azure 全球 | `https://datalake.azure.net/` | ![可用][check] |
| Azure Government |  | 不可用 |
| Azure 德国 |   | 不可用 |
| Azure 中国世纪互联 |  | 不可用 |

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

| 云 | 资源 ID | 状态 |
|--------|------------|:-:|
| Azure 全球 | `https://<account>.documents.azure.com/`<br/><br/>`https://cosmos.azure.com` | ![可用][check] |
| Azure Government | `https://<account>.documents.azure.us/`<br/><br/>`https://cosmos.azure.us` | ![可用][check] |
| Azure 德国 | `https://<account>.documents.microsoftazure.de/`<br/><br/>`https://cosmos.microsoftazure.de` | ![可用][check] |
| Azure 中国世纪互联 | `https://<account>.documents.azure.cn/`<br/><br/>`https://cosmos.azure.cn` | ![可用][check] |

### <a name="azure-sql"></a>Azure SQL

| 云 | 资源 ID | 状态 |
|--------|------------|:-:|
| Azure 全球 | `https://database.windows.net/` | ![可用][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![可用][check] |
| Azure 德国 | `https://database.cloudapi.de/` | ![可用][check] |
| Azure 中国世纪互联 | `https://database.chinacloudapi.cn/` | ![可用][check] |

### <a name="azure-data-explorer"></a>Azure 数据资源管理器

| 云 | 资源 ID | 状态 |
|--------|------------|:-:|
| Azure 全球 | `https://<account>.<region>.kusto.windows.net` | ![可用][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![可用][check] |
| Azure 德国 | `https://<account>.<region>.kusto.cloudapi.de` | ![可用][check] |
| Azure 中国世纪互联 | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![可用][check] |

### <a name="azure-event-hubs"></a>Azure 事件中心

| 云 | 资源 ID | 状态 |
|--------|------------|:-:|
| Azure 全球 | `https://eventhubs.azure.net` | ![可用][check] |
| Azure Government | `https://eventhubs.azure.net` | ![可用][check] |
| Azure 德国 | `https://eventhubs.azure.net` | ![可用][check] |
| Azure 中国世纪互联 | `https://eventhubs.azure.net` | ![可用][check] |

### <a name="azure-service-bus"></a>Azure 服务总线

| 云 | 资源 ID | 状态 |
|--------|------------|:-:|
| Azure 全球 | `https://servicebus.azure.net`  | ![可用][check] |
| Azure Government | `https://servicebus.azure.net`  | ![可用][check] |
| Azure 德国 |  `https://servicebus.azure.net`  | ![可用][check] |
| Azure 中国世纪互联 | `https://servicebus.azure.net`  | ![可用][check] |


### <a name="azure-storage-blobs-and-queues"></a>Azure 存储 blob 和队列

| 云 | 资源 ID | 状态 |
|--------|------------|:-:|
| Azure 全球 | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![可用][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![可用][check] |
| Azure 德国 | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![可用][check] |
| Azure 中国世纪互联 | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![可用][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| 云 | 资源 ID | 状态 |
|--------|------------|:-:|
| Azure 全球 | `https://*.asazure.windows.net` | ![可用][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![可用][check] |
| Azure 德国 | `https://*.asazure.cloudapi.de` | ![可用][check] |
| Azure 中国世纪互联 | `https://*.asazure.chinacloudapi.cn` | ![可用][check] |

### <a name="azure-communication-services"></a>Azure 通信服务

托管标识类型 | 所有正式发布版<br>全球 Azure 区域 | Azure Government | Azure 德国 | Azure 中国世纪互联 |
| --- | :-: | :-: | :-: | :-: |
| 系统分配 | ![可用][check] | 不可用 | 不可用 | 不可用 |
| 用户分配 | ![可用][check] | 不可用 | 不可用 | 不可用 |

> [!NOTE]
> 使用托管标识对[发送到 Power BI 的 Azure 流分析作业](../../stream-analytics/powerbi-output-managed-identity.md)进行身份验证。


[check]: media/services-support-managed-identities/check.png "可用"
