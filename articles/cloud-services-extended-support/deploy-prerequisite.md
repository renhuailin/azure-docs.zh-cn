---
title: 部署 Azure 云服务 (扩展支持) 的先决条件
description: 部署 Azure 云服务 (扩展支持) 的先决条件
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 1473305d7da57d1216ef05c0b88a0f69d586784b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728104"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>部署 Azure 云服务 (扩展支持) 的先决条件

> [!IMPORTANT]
> 云服务（外延支持）目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

若要确保云服务成功 (扩展支持) 部署请查看以下步骤，并在尝试任何部署之前完成每个项。 

## <a name="register-the-cloudservices-feature"></a>注册 CloudServices 功能
注册订阅的功能。 注册可能需要几分钟才能完成。 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

使用以下各项检查注册状态：  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>所需的服务配置 ( .cscfg) 文件更新

### <a name="1-virtual-network"></a>1) 虚拟网络
云服务（外延支持）部署必须在虚拟网络中。 可以通过 [Azure 门户](../virtual-network/quick-create-portal.md) [PowerShell](../virtual-network/quick-create-powershell.md)、 [Azure CLI](../virtual-network/quick-create-cli.md) 或 [ARM 模板](../virtual-network/quick-create-template.md)创建虚拟网络。 还必须在 [NetworkConfiguration](schema-cscfg-networkconfiguration.md) 节下的服务配置 ( .cscfg) 中引用虚拟网络和子网。 

对于属于与云服务相同资源组的虚拟网络，只引用服务配置 ( .cscfg) 文件中的虚拟网络名称。 如果虚拟网络和云服务位于两个不同的资源组中，则需要在服务配置 ( .cscfg) 文件中指定虚拟网络的完整 Azure 资源管理器 ID。
 
#### <a name="virtual-network-located-in-same-resource-group"></a>位于同一资源组中的虚拟网络
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>位于不同资源组中的虚拟网络
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) 删除旧插件

从服务配置 ( .cscfg) 文件中删除旧的远程桌面设置。  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```

## <a name="required-service-definition-file-csdef-updates"></a>所需的服务定义文件 () 更新

### <a name="1-virtual-machine-sizes"></a>1) 虚拟机大小
Azure 资源管理器中已弃用以下大小。 但是，如果要继续使用它们，请 `vmsize` 使用关联的 Azure 资源管理器命名约定更新名称。  

| 以前的大小名称 | 更新的大小名称 | 
|---|---|
| 特小型 | Standard_A0 | 
| 小 | Standard_A1 |
| 中 | Standard_A2 | 
| 大 | Standard_A3 | 
| 超大型 | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 例如，`<WorkerRole name="WorkerRole1" vmsize="Medium"` 将变为 `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"`。
 
> [!NOTE]
> 若要检索可用大小列表，请参阅 [资源 Sku 列表](/rest/api/compute/resourceskus/list) 并应用以下筛选器： <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) 删除旧的远程桌面插件
利用旧的远程桌面插件的部署需要将模块从服务定义中删除 () 文件以及任何关联的证书。 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```

## <a name="key-vault-creation"></a>Key Vault 创建 

Key Vault 用于存储与云服务（外延支持）关联的证书。 将证书添加到 Key Vault，并在服务配置文件中引用证书指纹。 还需要启用 Key Vault 来获得适当的权限，以便云服务（外延支持）资源可从 Key Vault 检索存储为机密的证书。 可以通过 [Azure 门户](../key-vault/general/quick-create-portal.md)和  [PowerShell](../key-vault/general/quick-create-powershell.md)创建 Key Vault。 Key Vault 必须与云服务在同一区域和订阅中创建。 有关详细信息，请参阅[在 Azure 云服务（外延支持）中使用证书](certificates-and-key-vault.md)。

## <a name="next-steps"></a>后续步骤 
- 查看云服务的 [部署先决条件](deploy-prerequisite.md) (扩展支持) 。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
- 请参阅云服务（外延支持）的[常见问题解答](faq.md)。
- 访问[云服务（外延支持）示例存储库](https://github.com/Azure-Samples/cloud-services-extended-support)