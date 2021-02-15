---
title: 在 Batch 池中配置托管标识
description: 了解如何在批处理池上启用用户分配的托管标识，以及如何在节点内使用托管标识。
ms.topic: conceptual
ms.date: 02/10/2021
ms.custom: references_regions
ms.openlocfilehash: 4a59383d119f88bb3ee180f629ba0a6ea6ac2f44
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417088"
---
# <a name="configure-managed-identities-in-batch-pools"></a>在 Batch 池中配置托管标识

[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md) 使开发人员无需通过在 Azure Active Directory (Azure AD) 中提供 Azure 资源的标识并使用它来获取 Azure Active Directory (Azure AD 令牌来管理凭据。

本主题介绍如何在批处理池上启用用户分配的托管标识，以及如何在节点内使用托管标识。

> [!IMPORTANT]
> 对具有用户分配的托管标识的 Azure Batch 池的支持目前以公共预览版提供给以下地区：美国西部2、美国中南部、美国东部、US Gov 亚利桑那州和 US Gov 弗吉尼亚州。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-a-user-assigned-identity"></a>创建用户分配的标识

首先，在与 Batch 帐户相同的租户中 [创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) 。 此托管标识不需要位于同一资源组中，甚至不需要位于同一订阅中。

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>使用用户分配的托管标识创建 Batch 池

创建一个或多个用户分配的托管标识后，可以使用 [batch .net 管理库](/dotnet/api/overview/azure/batch#management-library)创建具有该管理标识的 batch 池。

> [!IMPORTANT]
> 必须使用 [虚拟机配置](nodes-and-pools.md#virtual-machine-configuration) 来配置池，才能使用托管标识。

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
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
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

> [!NOTE]
> [Batch .net 客户端库](/dotnet/api/overview/azure/batch#client-library)当前不支持创建具有托管标识的池。

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>在 Batch 节点中使用用户分配的托管标识

创建池后，用户分配的托管标识可通过安全外壳 (SSH) 或远程桌面 (RDP) 来访问池节点。 你还可以配置任务，以便托管标识可以直接访问 [支持托管标识的 Azure 资源](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)。

在批处理节点中，你可以获取托管标识令牌，并使用它们通过 [Azure 实例元数据服务](../virtual-machines/windows/instance-metadata-service.md)进行 Azure AD 身份验证进行身份验证。

对于 Windows，用于获取用于身份验证的访问令牌的 PowerShell 脚本是：

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

对于 Linux，Bash 脚本是：

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

有关详细信息，请参阅 [如何在 azure 虚拟机上使用 azure 资源的托管标识获取访问令牌](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。
- 了解如何结合使用 [客户管理的密钥和用户管理的标识](batch-customer-managed-key.md)。
