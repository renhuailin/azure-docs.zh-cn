---
title: 部署 Azure 云服务（外延支持）的先决条件
description: 部署 Azure 云服务（外延支持）的先决条件
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f21dc3251a64a3a432320e1d7a1f64abc124b61e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752079"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>部署 Azure 云服务（外延支持）的先决条件

若要确保云服务（外延支持）成功部署，请查看以下步骤，并在尝试任何部署之前完成每一项。 

## <a name="required-service-configuration-cscfg-file-updates"></a>所需的服务配置 (.cscfg) 文件更新

### <a name="1-virtual-network"></a>1) 虚拟网络
云服务（外延支持）部署必须在虚拟网络中。 可以通过 [Azure 门户](../virtual-network/quick-create-portal.md)、[PowerShell](../virtual-network/quick-create-powershell.md)、[Azure CLI](../virtual-network/quick-create-cli.md) 或 [ARM 模板](../virtual-network/quick-create-template.md)创建虚拟网络。 还必须在服务配置文件 (.cscfg) 的 [NetworkConfiguration](schema-cscfg-networkconfiguration.md) 部分下引用虚拟网络和子网。 

对于属于云服务所在资源组的虚拟网络，仅引用服务配置 (.cscfg) 文件中的虚拟网络名称就已经足够。 如果虚拟网络和云服务位于两个不同的资源组中，则需要在服务配置 (.cscfg) 文件中指定虚拟网络的完整 Azure 资源管理器 ID。
 
#### <a name="virtual-network-located-in-same-resource-group"></a>位于同一资源组中的虚拟网络
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
  </AddressAssignments> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>位于其他资源组中的虚拟网络
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
   </AddressAssignments>
```
### <a name="2-remove-the-old-plugins"></a>2) 删除旧插件

从服务配置 (.cscfg) 文件中删除旧的远程桌面设置。  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```
从服务配置 (.cscfg) 文件中的每个角色删除旧的诊断设置。

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
```

## <a name="required-service-definition-file-csdef-updates"></a>所需的服务定义文件 (.csdef) 更新

> [!NOTE]
> 更改服务定义文件 (.csdef) 需要重新生成包文件 (.cspkg)。 在 .csdef 文件中进行以下更改后，请生成并重新打包 .cspkg，以获取云服务的最新设置

### <a name="1-virtual-machine-sizes"></a>1) 虚拟机大小
Azure 资源管理器中已弃用以下大小。 但是，如果要继续使用它们，请使用关联的 Azure 资源管理器命名约定更新名称 `vmsize`。  

| 以前的大小名称 | 更新的大小名称 | 
|---|---|
| 特小型 | Standard_A0 | 
| 小 | Standard_A1 |
| 中型 | Standard_A2 | 
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
> 若要检索可用大小列表，请参阅[资源 SKU - 列表](/rest/api/compute/resourceskus/list)并应用以下筛选器： <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) 删除旧远程桌面插件
利用旧远程桌面插件的部署需要将模块从服务定义 (.csdef) 文件以及任何关联的证书中删除。 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```
利用旧诊断插件的部署需要为服务定义 (.csdef) 文件中的每个角色删除设置

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```
## <a name="access-control"></a>访问控制

对于云服务（外延支持），包含网络资源的订阅需要具有[网络参与者](../role-based-access-control/built-in-roles.md#network-contributor)访问权限或更高权限。 有关详细信息，请参阅 [RBAC 内置角色](../role-based-access-control/built-in-roles.md)

## <a name="key-vault-creation"></a>创建密钥保管库 

Key Vault 用于存储与云服务（外延支持）关联的证书。 将证书添加到密钥保管库，然后引用服务配置文件中的证书指纹。 还需要为“用于部署的 Azure 虚拟机”启用 Key Vault“访问策略（在门户中），以便云服务（外延支持）资源可从 Key Vault 检索存储为机密的证书。 可以在 [ Azure 门户](../key-vault/general/quick-create-portal.md)中创建密钥保管库，也可以使用 [PowerShell](../key-vault/general/quick-create-powershell.md) 创建。 该密钥保管库必须在云服务所在的区域和订阅中创建。 有关详细信息，请参阅[在 Azure 云服务（外延支持）中使用证书](certificates-and-key-vault.md)。

## <a name="next-steps"></a>后续步骤 
- 查看云服务（外延支持）的[部署先决条件](deploy-prerequisite.md)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 访问[云服务（外延支持）示例存储库](https://github.com/Azure-Samples/cloud-services-extended-support)
