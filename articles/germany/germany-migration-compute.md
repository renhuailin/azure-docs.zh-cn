---
title: 将 Azure 计算资源从 Azure 德国迁移至全球 Azure
description: 本文介绍如何将 Azure 计算资源从 Azure 德国迁移至全球 Azure。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 3662039dad5d85c87c2598fb59b7719ab9251090
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029083"
---
# <a name="migrate-compute-resources-to-global-azure"></a>将计算资源迁移至全球 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

本文中的信息可帮助你将 Azure 计算资源从 Azure 德国迁移至全球 Azure。

## <a name="compute-iaas"></a>计算 IaaS

不能直接将 Azure 计算基础结构即服务 (IaaS) 资源从 Azure 德国迁移至全球 Azure。 但是，可以通过多种方式“复制”VM。

### <a name="duplicate-by-using-site-recovery"></a>使用 Site Recovery 进行复制

Azure Site Recovery 可帮助你将 VM 从 Azure 德国迁移至全球 Azure。 源和目标在从 Azure 德国迁移至全球 Azure 时位于不同的租户中，因此，不能使用适用于 VM 的正常 Azure 灾难恢复选项。 技巧是在目标环境（全球 Azure）中设置 Site Recovery 保管库，并像将物理服务器迁移至 Azure 一样继续操作。 在 Azure 门户中选择标记为“未虚拟化”的复制路径。 复制完成后，执行故障转移。

> [!NOTE]
> 以下步骤与将在本地运行的物理服务器迁移至 Azure 的步骤相同。

若要了解详细信息，请查看此[有用的 Site Recovery 教程](../site-recovery/physical-azure-disaster-recovery.md)。 下面是略经调整的简短过程版本，供你快速地大致了解：

在源环境中安装配置/进程服务器以生成服务器映像。 然后，将映像复制到目标环境中的 Azure 恢复服务保管库。 这项工作完全由配置服务器完成。 无需处理每个服务器。

1. 登录到 Azure 德国门户。
1. 将要迁移的 VM 的 OS 版本与[支持矩阵](../site-recovery/vmware-physical-secondary-support-matrix.md)进行比较。
1. 在源 Azure 虚拟网络实例中设置一个新的 VM 充当配置服务器：
   1. 选择 DS4v3 或更高版本（4 到 8 核，16-GB 内存）。
   1. 附加至少具有 1 TB 可用空间的附加磁盘（VM 映像）。
   1. 使用 Windows Server 2012 R2 或更高版本。
1. 请确保同时在两个方向为子网打开端口 443 和 9443。
1. 登录到新 VM (ConfigurationServer)。
1. 在远程桌面会话中，使用全球 Azure 凭据登录到全球 Azure 门户。
1. 设置将在其中运行复制的 VM 的虚拟网络。
1. 创建 Azure 存储帐户。
1. 设置恢复服务保管库。
1. 定义“保护目标”（“到 Azure” > “未虚拟化/其他”）。  。
1. 下载恢复统一安装程序安装文件（“准备基础结构” > “源”）。  从 ConfigurationServer 中打开门户 URL 时，该文件将下载到正确的服务器。 从 ConfigurationServer 外部，将该安装文件上传到 ConfigurationServer。
1. 下载保管库注册密钥（必要时将其上传到 ConfigurationServer，如上一步）。
1. 在 ConfigurationServer 上运行恢复统一安装程序。
1. 设置目标环境（检查是否仍登录到目标门户）。
1. 定义复制策略。
1. 启动复制。

复制最初成功后，通过执行测试故障转移来测试方案。 验证并删除测试。 最后一步是执行真正的故障转移。

> [!CAUTION]
> 同步回源 VM 的情况不会发生。 若要再次迁移，请清理所有内容，从头开始！

### <a name="duplicate-by-using-resource-manager-template-exportimport"></a>使用资源管理器模板导出/导入进行复制

可以导出用于部署到本地计算机的 Azure 资源管理器模板。 编辑该模板以更改位置和其他参数或变量。 然后，在全球 Azure 中重新部署。 

> [!IMPORTANT]
> 更改位置、Azure Key Vault 机密、证书和其他 GUID，以与新区域保持一致。

通过选择资源组，在门户中导出资源管理器模板。 选择“部署”，然后导航到最近的部署。 在左侧菜单中选择“模板”，然后下载模板。

此时将下载包含多个文件的 .zip 文件。 PowerShell、Azure CLI、Ruby 或 .NET 脚本有助于部署模板。 parameters.json 文件包含上次部署的所有信息。 你可能需要更改此文件中的某些设置。 如果只想重新部署资源的子集，请编辑 template.json 文件。

参考信息：

- 通过完成 [Site Recovery 教程](../site-recovery/index.yml)学习更多新知识。
- 了解如何[导出资源管理器模板](../azure-resource-manager/templates/export-template-portal.md)或阅读 [Azure 资源管理器](../azure-resource-manager/management/overview.md)概述。
- 详细了解[使用 Site Recovery 执行物理机到 Azure 灾难恢复](../site-recovery/physical-azure-disaster-recovery.md)。
- 阅读 [Azure 位置概述](https://azure.microsoft.com/global-infrastructure/locations/)。
- 详细了解如何[重新部署模板](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="cloud-services"></a>云服务

可以通过再次提供 `.cspkg` 和 `.cscfg` 定义来重新部署 Azure 云服务资源。

### <a name="azure-portal"></a>Azure 门户

若要在 Azure 门户中重新部署云服务：

1. 使用 `.cspkg` 和 `.cscfg` 定义[创建新的云服务](../cloud-services/cloud-services-how-to-create-deploy-portal.md)。
1. 更新 [CNAME 或 A 记录](../cloud-services/cloud-services-custom-domain-name-portal.md)，将流量指向新的云服务。
1. 当流量指向新的云服务时，请删除 Azure 德国中的旧有云服务。

### <a name="powershell"></a>PowerShell

若要使用 PowerShell 重新部署云服务：

1. 使用 `.cspkg` 和 `.cscfg` 定义[创建新的云服务](/powershell/module/servicemanagement/azure.service/new-azureservice)。

    ```powershell
    New-AzureService -ServiceName <yourServiceName> -Label <MyTestService> -Location <westeurope>
    ```

1. 使用 `.cspkg` 和 `.cscfg` 定义[创建新部署](/powershell/module/servicemanagement/azure.service/new-azuredeployment)。

    ```powershell
    New-AzureDeployment -ServiceName <yourServiceName> -Slot <Production> -Package <YourCspkgFile.cspkg> -Configuration <YourConfigFile.cscfg>
    ```

1. 更新 [CNAME 或 A 记录](../cloud-services/cloud-services-custom-domain-name-portal.md)，将流量指向新的云服务。
1. 当流量指向新的云服务时，[请删除 Azure 德国中的旧有云服务](/powershell/module/servicemanagement/azure.service/remove-azureservice)。

    ```powershell
    Remove-AzureService -ServiceName <yourOldServiceName>
    ```

### <a name="rest-api"></a>REST API

若要使用 REST API 重新部署云服务：

1. 在目标环境中[创建新的云服务](/rest/api/compute/cloudservices/rest-create-cloud-service)。

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices
    ```

1. 使用[创建部署 API](/previous-versions/azure/reference/ee460813(v=azure.100)) 创建新部署。 若要找到 `.cspkg` 和 `.cscfg` 定义，可以调用[获取包 API](/previous-versions/azure/reference/jj154121(v=azure.100))。

    ```http
    https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deploymentslots/production
    ```

1. 当流量指向新的云服务时，[请删除 Azure 德国中的旧有云服务](/rest/api/compute/cloudservices/rest-delete-cloud-service)。

    ```http
    https://management.core.cloudapi.de/<subscription-id>/services/hostedservices/<old-cloudservice-name>
    ```

参考信息：

- 查看[云服务概述](../cloud-services/cloud-services-choose-me.md)。

## <a name="service-fabric"></a>Service Fabric

不能将 Azure Service Fabric 资源从 Azure 德国迁移到全球 Azure。 必须在新环境中重新部署 Service Fabric 资源。

可以使用 PowerShell cmdlet 获取有关当前 Service Fabric 环境的信息。 通过在 PowerShell 中输入 `Get-Help *ServiceFabric*` 来访问与 Service Fabric 相关的所有 cmdlet。

参考信息：

- 通过完成 [Service Fabric 教程](../service-fabric/service-fabric-tutorial-create-dotnet-app.md)学习更多新知识。
- 了解如何[创建新群集](../service-fabric/service-fabric-cluster-creation-via-portal.md)。
- 查看 [Service Fabric 概述](../service-fabric/service-fabric-overview.md)。

## <a name="batch"></a>Batch

无法将 Azure Batch 帐户数据从一个区域迁移到另一区域。 该帐户可能正在运行与之关联的 VM，并且正在主动与存储帐户、数据库或其他存储系统中的数据进行交互。

在新区域中重新部署部署脚本、模板或代码。 重新部署包括下列任务：

1. [创建 Batch 帐户](../batch/batch-account-create-portal.md)。
1. [增加 Batch 帐户配额](../batch/batch-quota-limit.md)。
1. 创建 Batch 池。
1. 创建新的存储帐户、数据库和其他用于保存输入及输出数据的服务。
1. 更新配置和代码，以指向新的 Batch 帐户并使用新凭据。

参考信息：

- 通过完成 [Batch 教程](../batch/tutorial-parallel-dotnet.md)学习更多新知识。
- 查看 [Azure Batch 概述](../batch/batch-technical-overview.md)。

## <a name="functions"></a>函数

目前不支持将 Azure Functions 资源从 Azure 德国迁移到全球 Azure。 建议导出资源管理器模板、更改位置，然后重新部署到目标区域。

> [!IMPORTANT]
> 更改位置、密钥保管库机密、证书、应用设置和其他 GUID，以与新区域保持一致。

参考信息：

- 通过完成 [Functions 教程](../azure-functions/index.yml)学习更多新知识。
- 了解如何[导出资源管理器模板](../azure-resource-manager/templates/export-template-portal.md)或阅读 [Azure 资源管理器](../azure-resource-manager/management/overview.md)概述。
- 查看 [Azure Functions 概述](../azure-functions/functions-overview.md)。
- 获取 [Azure 位置概述](https://azure.microsoft.com/global-infrastructure/locations/)。
- 了解如何[重新部署模板](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="virtual-machine-scale-sets"></a>虚拟机规模集

若要将虚拟机规模集迁移至全球 Azure，请导出资源管理器模板，调整以使之适应新环境，然后重新部署到目标区域。 仅导出基础模板，并在新环境中重新部署该模板。 各个虚拟机规模集实例均应相同。

> [!IMPORTANT]
> 更改位置、密钥保管库机密、证书和其他 GUID，以与新区域保持一致。

参考信息：

- 通过完成[虚拟机规模集教程](../virtual-machine-scale-sets/tutorial-create-and-manage-cli.md)学习更多新知识。
- 了解如何[导出 Azure 资源管理器模板](../azure-resource-manager/templates/export-template-portal.md)。
- 查看 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)。
- 获取[虚拟机规模集](../virtual-machine-scale-sets/overview.md)概述。
- 阅读 [Azure 位置概述](https://azure.microsoft.com/global-infrastructure/locations/)。
- 了解如何[重新部署模板](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="web-apps"></a>Web 应用

目前，使用 Azure 应用服务的 Web 应用功能创建的应用无法从 Azure 德国迁移到全球 Azure。 建议将 Web 应用导出为资源管理器模板，然后在将位置属性更改为新区域后重新部署。

> [!IMPORTANT]
> 更改位置、密钥保管库机密、证书和其他 GUID，以与新区域保持一致。

参考信息：

- 通过完成[应用服务教程](../app-service/tutorial-dotnetcore-sqldb-app.md)学习更多新知识。
- 了解如何[导出资源管理器模板](../azure-resource-manager/templates/export-template-portal.md)或阅读 [Azure 资源管理器](../azure-resource-manager/management/overview.md)概述。
- 查看[应用服务概述](../app-service/overview.md)。
- 阅读 [Azure 位置概述](https://azure.microsoft.com/global-infrastructure/locations/)。
- 了解如何[重新部署模板](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="next-steps"></a>后续步骤

了解用于在以下服务类别中迁移资源的工具、方法和建议：

- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [分析](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)