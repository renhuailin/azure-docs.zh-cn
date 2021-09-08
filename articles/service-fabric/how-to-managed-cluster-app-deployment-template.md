---
title: 使用 Azure 资源管理器将应用程序部署到托管群集
description: 了解如何使用 Azure 资源管理器在 Azure Service Fabric 托管群集上部署、升级或删除 Service Fabric 应用程序
ms.topic: how-to
ms.date: 8/23/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f2f2f47e9cdcef54be9c78513fbb57cd20ddde5f
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122864774"
---
# <a name="manage-application-lifecycle-on-a-managed-cluster-using-azure-resource-manager"></a>使用 Azure 资源管理器管理托管群集上的应用程序生命周期

有多个选项可用于在 Service Fabric 托管群集上部署 Azure Service Fabric 应用程序。 建议使用 Azure 资源管理器。 如果使用资源管理器，可以采用 JSON 描述应用程序和服务，然后将其部署到群集所在的同一资源管理器模板中。 不同于使用 PowerShell 或 Azure CLI 来部署和管理应用程序，如果使用资源管理器，则不必等待群集准备就绪；应用程序注册、预配和部署都可以在一个步骤中进行。 使用资源管理器是在群集中管理应用程序生命周期的最佳方式。 有关详细信息，请参阅[最佳做法：基础结构即代码](service-fabric-best-practices-infrastructure-as-code.md#service-fabric-resources)。

在资源管理器中将应用程序作为资源进行管理有助于在以下方面获得改进：

* 审核线索：资源管理器审核每个操作并保留详细的活动日志。 活动日志可帮助你跟踪对应用程序和群集所做的任何更改。
* 基于角色的访问控制：可以使用相同资源管理器模板来管理对群集的访问以及对群集上部署的应用程序的访问。
* 管理效率：使用资源管理器可以在单个位置（Azure 门户）管理群集和关键应用程序部署。

本文档将介绍以下操作：

> [!div class="checklist"]
>
> * 使用资源管理器部署 Service Fabric 应用程序资源。
> * 使用资源管理器升级 Service Fabric 应用程序资源。
> * 删除 Service Fabric 应用程序资源。

## <a name="deploy-service-fabric-application-resources"></a>部署 Service Fabric 应用程序资源

可以使用资源管理器应用程序资源模型来部署应用程序及其服务，所需采用的概要步骤如下：
1. 将应用程序代码打包。
1. 上传该包。
1. 在资源管理器模板中引用该包的位置作为应用程序资源。 

有关详细信息，请查看[将应用程序打包](service-fabric-package-apps.md#create-an-sfpkg)。

然后，创建资源管理器模板，使用应用程序详细信息更新参数文件，并将模板部署到 Service Fabric 托管群集上。 [浏览示例](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy/ARM-Managed-Cluster)。

### <a name="create-a-storage-account"></a>创建存储帐户

若要从资源管理器模板部署应用程序，必须有一个存储帐户。 存储帐户用于暂存应用程序映像。 

可以重复使用现有的存储帐户或创建新的存储帐户来暂存应用程序。 如果使用现有的存储帐户，则可以跳过此步骤。 

![创建存储帐户][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>配置存储帐户

创建存储帐户后，需要创建一个可在其中暂存应用程序的 Blob 容器。 在 Azure 门户中，转到要存储应用程序的 Azure 存储帐户。 选择“Blob” > “添加容器”。 

可以通过将公共访问级别设置为“专用”来保护群集中的资源。 可以通过多种方式授予访问权限：

* 使用 [Azure Active Directory](../storage/common/storage-auth-aad-app.md) 授予对 Blob 和队列的访问权限。
* [在 Azure 门户中使用 Azure RBAC](../storage/blobs/assign-azure-role-data-access.md) 授予对 Azure Blob 和队列数据的访问权限。
* 使用[共享访问签名](/rest/api/storageservices/delegate-access-with-shared-access-signature)委派访问权限。

以下屏幕截图中的示例使用对 Blob 的匿名读取访问。

![创建 Blob][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>在存储帐户中暂存应用程序

在部署应用程序之前，必须在 Blob 存储中暂存该应用程序。 在本教程中，我们将手动创建应用程序包。 请记住，此步骤可以自动执行。 有关详细信息，请参阅[将应用程序打包](service-fabric-package-apps.md#create-an-sfpkg)。 

在本教程中，我们将使用[投票示例应用程序](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart)。

1. 在 Visual Studio 中右键单击 Voting 项目并选择“打包”。 

   ![将应用程序打包][PackageApplication]  
1. 转到 .\service-fabric-dotnet-quickstart\Voting\pkg\Debug 目录。 将内容压缩到名为 Voting.zip 的文件中。 *Applicationmanifest.xml* 文件应位于 zip 文件中的根位置。

   ![Zip 应用程序][ZipApplication]  
1. 重命名该文件，以将扩展名从 .zip 更改为 .sfpkg。

1. 在 Azure 门户上你的存储帐户的 apps 容器中，选择“上传”，然后上传 Voting.sfpkg。 

   ![上传应用包][UploadAppPkg]

应用程序现已暂存，你可以创建资源管理器模板来部署应用程序了。

### <a name="create-the-resource-manager-template"></a>创建 Resource Manager 模板

示例应用程序包含可用于部署该应用程序的 [Azure 资源管理器模板](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)。 模板文件名为 UserApp.json 和 UserApp.Parameters.json。

> [!NOTE]
> 必须使用群集名称更新 *UserApp.Parameters.json* 文件。
>
>

| 参数              | 说明                                 | 示例                                                      | 注释                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | 要部署到的群集的名称 | sf-cluster123                                                |                                                              |
| application            | 应用程序的名称                 | Voting                                                       |
| 版本    | 该应用的资源 ID、应用程序类型和版本。       | /providers/Microsoft.ServiceFabric/managedClusters/sf-cluster-123/applicationTypes/VotingType/versions/1.0.0                                               | 必须匹配 ApplicationManifest.xml                 |
| serviceName            | 服务的名称         | VotingWeb                                             | 必须采用 ServiceType 格式            |
| serviceTypeName        | 服务的类型名称                | VotingWebType                                                    | 必须匹配 ServiceManifest.xml                 |
| appPackageUrl          | 应用程序的 Blob 存储 URL     | https:\//servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Blob 存储中应用程序包的 URL（设置该 URL 的过程在本文中的后面部分介绍） |

```json
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2021-05-01",
    "type": "Microsoft.ServiceFabric/managedclusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-service-fabric-application"></a>部署 Service Fabric 应用程序

运行 **New-AzResourceGroupDeployment** cmdlet，以将应用程序部署到包含群集的资源组：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>使用资源管理器升级 Service Fabric 应用程序

> [!IMPORTANT]
> 必须从相应 ApplicationManifest.xml 文件的“DefaultServices”部分删除通过 Azure 资源管理器 (ARM) 模板部署的任何服务。


你可能会出于以下某一原因升级已部署到 Service Fabric 群集的应用程序：

* 一个新服务将添加到应用程序。 当服务添加到应用程序时，一个服务定义必须添加到 service-manifest 和 application-manifest 文件中。 若要反映应用程序的新版本，还必须在 [UserApp.parameters.json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json) 中将应用程序类型版本从 1.0.0 更改为 1.0.1：

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

* 现有服务的新版本将添加到应用程序中。 例如，对应用程序代码的更改，以及对应用类型版本和名称的更新。 对于此升级，请更新 UserApp.Parameters.json，如下所示：

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```
## <a name="delete-service-fabric-application-resources"></a>删除 Service Fabric 应用程序资源
> [!NOTE]
> 不应通过 Azure 资源管理器 (ARM) 模板删除应用程序，因为没有声明性的方法可以清理单项资源

删除使用资源管理器中的应用程序资源模型部署的 Service Fabric 应用程序：

1. 使用 [Get-AzResource](/powershell/module/az.resources/get-azresource) cmdlet 获取应用程序的资源 ID：

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. 使用 [Remove-AzResource](/powershell/module/az.resources/remove-azresource) cmdlet 删除应用程序资源：

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```


## <a name="migration-from-classic-to-managed-clusters"></a>从经典群集迁移到托管群集

如果要将应用程序从经典群集迁移到托管群集，则需要确保正确指定验证类型，否则会遇到错误。 

以下各项因使用频率而被特别调用，但这并不是一个完整的差异列表。 

* upgradeReplicaSetCheckTimeout 对于托管现在是一个整数，但在典型 SFRP 上是一个字符串。 

有关完整的属性和类型列表，请参阅[托管群集应用程序资源类型](/azure/templates/microsoft.servicefabric/managedclusters/applications?tabs=json)

## <a name="next-steps"></a>后续步骤

详细了解托管群集应用程序部署：

* [部署托管群集应用程序机密](how-to-managed-cluster-application-secrets.md)
* [使用托管标识部署托管群集应用程序](how-to-managed-cluster-application-managed-identity.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png