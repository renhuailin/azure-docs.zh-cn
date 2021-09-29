---
title: 使用托管标识对 Azure 流分析作业的 Power BI 输出进行身份验证
description: 本文介绍如何使用托管标识对 Azure 流分析作业的 Power BI 输出进行身份验证。
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 05/30/2021
ms.openlocfilehash: 2d65b537ef0bad56127fcfcc1e87f1b1cfc15f2d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128583758"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>使用托管标识对 Azure 流分析作业的 Power BI 进行身份验证

通过针对 Power BI 输出的[托管标识身份验证](../active-directory/managed-identities-azure-resources/overview.md)，流分析作业可以直接访问 Power BI 帐户内的工作区。 通过此功能，可以实现流分析作业部署的完全自动化，因为用户不再需要通过 Azure 门户以交互方式登录 Power BI。 此外，还可以为写入 Power BI 的长时间运行的作业提供更好的支持，因为无需定期向作业重新授权。

本文介绍如何通过 Azure 门户以及通过 Azure 资源管理器部署，为流分析作业的 Power BI 输出启用托管标识。

## <a name="prerequisites"></a>先决条件

使用此功能需满足以下条件：

- 有具有 [Pro 许可证](/power-bi/service-admin-purchasing-power-bi-pro)的 Power BI 帐户。

- Power BI 帐户中有已升级的工作区。 如需更多详细信息，请参阅此功能的 [Power BI 公告](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/)。

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>使用 Azure 门户创建流分析作业。

1. 在 Azure 门户中创建新的流分析作业，或打开现有的作业。 在屏幕左侧的菜单栏中，选择“配置”下面的“托管标识”。 确保选择“使用系统分配的托管标识”，然后选择屏幕底部的“保存”按钮。

   ![配置流分析托管标识](./media/common/stream-analytics-enable-managed-identity.png)

2. 在配置输出之前，请按照本文中[向流分析作业授予对 Power BI 工作区的访问权限](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)部分中的说明，为流分析作业授予对 Power BI 工作区的访问权限。

3. 导航到流分析作业的“输出”部分，选择“+添加”，然后选择“Power BI”  。 然后，选择“授权”按钮，然后用 Power BI 帐户登录。

   ![Power BI 帐户授权](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. 获得授权后，将有一个下拉列表，其中填充了你有权访问的所有工作区。 选择在上一步中授权的工作区。 然后为“身份验证模式”选择“托管标识”。 最后选择“保存”按钮。

   ![用托管标识配置 Power BI 输出](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure 资源管理器部署

通过 Azure 资源管理器可实现流分析作业部署的完全自动。 可以使用 Azure PowerShell 或 [Azure CLI](/cli/azure/) 部署资源管理器模板。 以下示例使用 Azure CLI。


1. 可以通过在资源管理器模板的 resource 节中包含以下属性，来创建带有托管标识的 Microsoft.StreamAnalytics/streamingjobs 资源：

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   此属性告知 Azure 资源管理器为流分析作业创建和管理标识。 以下示例资源管理器模板部署一个启用了托管标识的流分析作业，以及一个使用托管标识的 Power BI 输出接收器：

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    使用以下 Azure CLI 命令将上述作业部署到资源组 **ExampleGroup**：

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 创建作业后，使用 Azure 资源管理器检索作业的完整定义。

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    以上命令将返回如下所示的响应：

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    如果计划使用 Power BI 的 REST API 将流分析作业添加到 Power BI 工作区中，请记下返回的“principalId”。

3. 现已创建作业，可接着学习本文的[向流分析作业授予对 Power BI 工作区的访问权限](#give-the-stream-analytics-job-access-to-your-power-bi-workspace)部分。


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>向流分析作业授予对 Power BI 工作区的访问权限

现已创建流分析作业，便可以向其授予对 Power BI 工作区的访问权限。 授予作业访问权限后，请等待几分钟以便标识得到传播。

### <a name="use-the-power-bi-ui"></a>使用 Power BI UI

   > [!Note]
   > 若要使用 UI 将流分析作业添加到 Power BI 工作区，还需要在 Power BI 管理门户的“开发人员设置”中启用服务主体访问。 有关更多详细信息，请参阅[开始使用服务主体](/power-bi/developer/embed-service-principal)。

1. 导航到工作区的访问设置。 有关更多详细信息，请参阅此文章：[提供对工作区的访问权限](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)。

2. 在文本框中键入流分析作业的名称，并为“访问级别”选择“参与者”。

3. 选择“添加”并关闭窗格。

   ![将流分析作业添加到 Power BI 工作区](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>使用 Power BI PowerShell cmdlet

1. 安装 Power BI `MicrosoftPowerBIMgmt` PowerShell cmdlet。

   > [!Important]
   > 请确保使用的是版本 1.0.821 或更高版本的 cmdlet。

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. 登录 Power BI。

```powershell
Login-PowerBI
```

3. 将流分析作业作为参与者添加到工作区。

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>使用 Power BI REST API

还可以通过直接使用“添加组用户”REST API 将流分析作业作为参与者添加到工作区。 有关此 API 的完整文档，请转到[组 - 添加组用户](/rest/api/power-bi/groups/addgroupuser)。

**示例请求**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
请求正文
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

### <a name="use-a-service-principal-to-grant-permission-for-an-asa-jobs-managed-identity"></a>使用服务主体为 ASA 作业的托管标识授予权限

对于自动部署，无法使用交互式登录来授予 ASA 作业访问 Power BI 工作区的权限。 这可以通过使用服务主体为 ASA 作业的托管标识授予权限来完成。 这可以使用 PowerShell 实现：

```powershell
Connect-PowerBIServiceAccount -ServicePrincipal -TenantId "<tenant-id>" -CertificateThumbprint "<thumbprint>" -ApplicationId "<app-id>"
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

## <a name="remove-managed-identity"></a>删除托管标识

仅当删除流分析作业后，才会删除为该作业创建的托管标识。 如果不删除作业，则无法删除其托管标识。 如果不想再使用托管标识，可以更改对输出的身份验证方法。 在删除作业之前，托管标识会始终存在，如果决定再次使用托管标识身份验证，将再次使用该标识。

## <a name="limitations"></a>限制
下面是此功能的相关限制：

- 不支持经典 Power BI 工作区。

- 不包含 Azure Active Directory 的 Azure 帐户。

- 不支持多租户访问。 为给定流分析作业创建的服务主体必须驻留在创建了该作业的同一 Azure Active Directory 租户中，而不可用于驻留在其他 Azure Active Directory 租户中的资源。

- 不支持[用户分配的标识](../active-directory/managed-identities-azure-resources/overview.md)。 这意味着不能输入自己的服务主体供其流分析作业使用。 服务主体必须由 Azure 流分析生成。

## <a name="next-steps"></a>后续步骤

* [Power BI 仪表板与 Azure 流分析的集成](./stream-analytics-power-bi-dashboard.md)
* [了解 Azure 流分析的输出](./stream-analytics-define-outputs.md)
