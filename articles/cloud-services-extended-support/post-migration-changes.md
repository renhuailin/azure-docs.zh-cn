---
title: Azure 云服务（外延支持）迁移后更改
description: 迁移到云服务（外延支持）后的迁移后更改概述
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: df9a41b6832c9ca93aa7078b0d06082f1441a5e5
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438986"
---
# <a name="post-migration-changes"></a>迁移后更改
云服务（经典）部署将转换为云服务（外延支持）部署。 有关详细信息，请参阅[云服务（外延支持）文档](deploy-prerequisite.md)。  

## <a name="changes-to-deployment-files"></a>对部署文件的更改 

将对客户的 .csdef 和 .cscfg 文件进行次要更改，使部署文件符合 Azure 资源管理器和云服务（外延支持）要求。 迁移后，检索新的部署文件或更新现有文件。 对于更新/删除操作需要采取此措施。  

- 虚拟网络 .cscfg 文件的 NetworkConfiguration 节中使用完整的 Azure 资源管理器资源 ID，而不只是使用资源名称。 例如，`/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name`。 对于属于云服务所在的同一资源组的虚拟网络，可以选择将 .cscfg 文件重新更新为仅使用虚拟网络名称。  

- 经典大小（例如“小”、“大”、“超大”）将替换为其新的大小名称 Standard_A*。 需要在 .csdef 文件中将这些大小名称更改为其新名称。 有关详细信息，请参阅[云服务（外延支持）部署先决条件](deploy-prerequisite.md#required-service-definition-file-csdef-updates)

- 使用“获取”API 获取部署文件的最新副本。 
    - 使用[门户](../azure-resource-manager/templates/export-template-portal.md)、[PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)、[CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) 和 [REST API](/rest/api/resources/resourcegroups/exporttemplate) 获取模板 
    - 使用 [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) 或 [REST API](/rest/api/compute/cloudservices/rest-get-package) 获取 .csdef 文件。 
    - 使用 [PowerShell](/powershell/module/az.cloudservice/?preserve-view=true&view=azps-5.4.0#cloudservice) 或 [REST API](/rest/api/compute/cloudservices/rest-get-package) 获取 .cscfg 文件。 
    
 

## <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>对客户的自动化、CI/CD 管道、自定义脚本、自定义仪表板、自定义工具等的更改  

客户需要更新其工具和自动化，以开始使用新的 API/命令来管理其部署。 在进行此项更改的过程中，客户可以轻松采用 Azure 资源管理器/云服务（外延支持）的新特性和功能。 

- 迁移后对资源和资源组名称的更改
    - 在迁移过程中，少量资源（例如云服务、公共 IP 地址等）的名称会更改。 在更新云服务之前，可能需要在部署文件中反映这些更改。 [详细了解资源名称更改](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration)。  

- 重新创建管理和缩放云服务所需的规则与策略 
    - [自动缩放规则](configure-scaling.md)不会迁移。 迁移后，请重新创建自动缩放规则。  
    - [警报](enable-alerts.md)不会迁移。 迁移后，请重新创建警报。
    - 将创建没有任何访问策略的密钥保管库。 在密钥保管库上[创建适当的策略](../key-vault/general/assign-access-policy-portal.md)以查看或管理证书。 证书将显示在名为“机密”的选项卡上的“设置”下。


## <a name="changes-to-certificate-management-post-migration"></a>迁移后对证书管理的更改 

作为管理证书的标准做法，所有有效的 .pfx 证书文件都应该添加到密钥保管库中的证书存储中，并且更新将通过任何客户端（门户、Powershell 或 Rest API）完美运行。

目前，Azure 门户执行验证以检查是否在密钥保管库中的证书存储中上传了所有所需的证书，并在找不到证书时发出警告。 但是，如果计划将证书用作机密，则无法验证这些证书的指纹，并且任何涉及机密添加的更新操作都无法通过门户进行。 建议客户使用 PowerShell 或 RestAPI 继续涉及机密的更新。


## <a name="changes-for-update-via-visual-studio"></a>通过 Visual Studio 更新的更改
如果通过 Visual Studio 直接发布更新，则需要先从迁移后部署下载最新的 CSCFG 文件。 使用此文件作为参考，将网络配置详细信息添加到 Visual Studio 项目中的当前 CSCFG 文件。 然后生成并发布解决方案。 可能需要选择此更新的密钥保管库和资源组。


## <a name="next-steps"></a>后续步骤
- [平台支持的从经典部署模型到 Azure 资源管理器部署模型的 IaaS 资源迁移概述](../virtual-machines/migration-classic-resource-manager-overview.md)
- 使用 [Azure 门户](in-place-migration-portal.md)迁移到云服务（外延支持）
- 使用 [PowerShell](in-place-migration-powershell.md) 迁移到云服务（外延支持）
