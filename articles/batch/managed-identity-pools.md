---
title: 在 Batch 池中配置托管标识
description: 了解如何在 Batch 池上启用用户分配的托管标识，以及如何在节点内使用托管标识。
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 903e173a6028e6bb574dfba618661da802702c2d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444803"
---
# <a name="configure-managed-identities-in-batch-pools"></a>在 Batch 池中配置托管标识

[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)可为 Azure Active Directory (Azure AD) 中的 Azure 资源提供标识并使用它来获取 Azure Active Directory (Azure AD) 令牌，从而使开发人员无需管理凭据。

本主题介绍如何在 Batch 池上启用用户分配的托管标识，以及如何在节点内使用托管标识。

> [!IMPORTANT]
> 必须使用[虚拟机配置](nodes-and-pools.md#virtual-machine-configuration)来配置池，才能使用托管标识。
>
> 可以使用 [Batch .NET 管理库](/dotnet/api/overview/azure/batch#management-library)创建具有托管标识的池，但 [Batch .NET 客户端库](/dotnet/api/overview/azure/batch#client-library)当前不支持创建具有托管标识的池。

## <a name="create-a-user-assigned-identity"></a>创建用户分配的标识

首先，在与 Batch 帐户相同的租户中[创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)。 此托管标识不需要位于同一资源组中，甚至不需要位于同一订阅中。

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>创建包含用户分配的托管标识的 Batch 池

创建一个或多个用户分配的托管标识后，可以使用 [Batch .NET 管理库](/dotnet/api/overview/azure/batch#management-library)创建包含该托管标识的 Batch 池。

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        },
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, UserAssignedIdentities>
            {
                ["Your Identity Resource Id"] =
                    new UserAssignedIdentities()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>在 Batch 节点中使用用户分配的托管标识

许多用于访问其他 Azure 资源（如 Azure 存储或 Azure 容器注册表）的 Azure Batch 技术均支持托管标识。 如需进一步了解如何在 Azure Batch 中使用托管标识，请参阅以下链接：

- [资源文件](resource-files.md)
- [输出文件](batch-task-output-files.md#specify-output-files-using-managed-identity)
- [Azure 容器注册表](batch-docker-container-workloads.md#managed-identity-support-for-acr)
- [Azure Blob 容器文件系统](virtual-file-mount.md#azure-blob-container)

你还可以手动配置任务，以便托管标识可以直接访问[支持托管标识的 Azure 资源](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)。

在 Batch 节点中，你可以获取托管标识令牌，并使用它们通过 [Azure 实例元数据服务](../virtual-machines/windows/instance-metadata-service.md)进行 Azure AD 身份验证。

对于 Windows，用于获取进行身份验证的访问令牌的 PowerShell 脚本是：

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

对于 Linux，Bash 脚本是：

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

有关详细信息，请参阅[如何在 Azure VM 上使用 Azure 资源的托管标识获取访问令牌](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。
- 了解如何配合使用[客户管理的密钥和用户管理的标识](batch-customer-managed-key.md)。
- 了解如何[在 Batch 池中启用自动证书轮换](automatic-certificate-rotation.md)。
