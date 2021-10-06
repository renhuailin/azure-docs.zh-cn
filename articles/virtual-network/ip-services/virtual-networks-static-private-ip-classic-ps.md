---
title: 为 VM（经典）配置专用 IP 地址 - Azure PowerShell | Microsoft 文档
description: 了解如何使用 PowerShell 为虚拟机（经典）配置专用 IP 地址。
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: allensu
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28291444583f197bb43a77ac063dc1495242fabb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367338"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>使用 PowerShell 为虚拟机（经典）配置专用 IP 地址

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../../includes/azure-arm-classic-important-include.md)]

本文介绍经典部署模型。 还可以 [管理 Resource Manager 部署模型中的静态专用 IP 地址](virtual-networks-static-private-ip-arm-ps.md)。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../../includes/virtual-networks-static-ip-scenario-include.md)]

下面的示例 PowerShell 命令需要已创建简单的环境。 若要运行本文档中所显示的命令，请首先构建[创建 VNet](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-netcfg-ps) 中所述的测试环境。

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>如何验证特定 IP 地址是否可用：
若要验证 IP 地址 *192.168.1.101* 在名为 *TestVNet* 的 VNet 中是否可用，请运行以下 PowerShell 命令并验证 *IsAvailable* 的值：

```azurepowershell
Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 
```

预期输出：

```output
IsAvailable          : True
AvailableAddresses   : {}
OperationDescription : Test-AzureStaticVNetIP
OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
OperationStatus      : Succeeded
```

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>如何在创建 VM 时指定静态专用 IP 地址
下面的 PowerShell 脚本将创建名为 *TestService* 的全新云服务，然后从 Azure 中检索映像，在新的云服务中使用检索到的映像创建名为 *DNS01* 的 VM，对该 VM 进行设置，使之位于名为 *FrontEnd* 的子网中，最后再将 *192.168.1.7* 设置为该 VM 的静态专用 IP 地址：

```azurepowershell
New-AzureService -ServiceName TestService -Location "Central US"
$image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
    Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
    Set-AzureSubnet –SubnetNames FrontEnd |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    New-AzureVM -ServiceName TestService –VNetName TestVNet
```

预期输出：

```output
WARNING: No deployment found in service: 'TestService'.
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  
```

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>如何检索 VM 的静态专用 IP 地址信息
要查看使用上述脚本创建的 VM 静态专用 IP 地址信息，请运行以下 PowerShell 命令，并观察 *IpAddress* 的值：

```azurepowershell
Get-AzureVM -Name DNS01 -ServiceName TestService
```

预期输出：

```output
DeploymentName              : TestService
Name                        : DNS01
Label                       : 
VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
InstanceStatus              : Provisioning
IpAddress                   : 192.168.1.7
InstanceStateDetails        : Windows is preparing your computer for first use...
PowerState                  : Started
InstanceErrorCode           : 
InstanceFaultDomain         : 0
InstanceName                : DNS01
InstanceUpgradeDomain       : 0
InstanceSize                : Small
HostName                    : rsR2-797
AvailabilitySetName         : 
DNSName                     : http://testservice000.cloudapp.net/
Status                      : Provisioning
GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
PublicIPAddress             : 
PublicIPName                : 
NetworkInterfaces           : {}
ServiceName                 : TestService
OperationDescription        : Get-AzureVM
OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
OperationStatus             : OK
```

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>如何从 VM 中删除静态专用 IP 地址
若要删除使用上述脚本添加到 VM 的静态专用 IP 地址，请运行以下 PowerShell 命令：

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Remove-AzureStaticVNetIP |
    Update-AzureVM
```

预期输出：

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded
```

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>如何将静态专用 IP 地址添加到现有 VM
若要向使用上述脚本创建的 VM 添加静态专用 IP 地址，请运行以下命令：

```azurepowershell
Get-AzureVM -ServiceName TestService -Name DNS01 |
    Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
    Update-AzureVM
```

预期输出：

```output
OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 
```

## <a name="set-ip-addresses-within-the-operating-system"></a>在操作系统中设置 IP 地址

我们建议，除非有必要，否则不要以静态方式在 VM 的操作系统中分配已分配给 Azure 虚拟机的专用 IP。 如果确实需要在操作系统中手动设置该专用 IP 地址，请确保它是分配给 Azure VM 的同一专用 IP 地址，否则可能会丢失与虚拟机的连接。 切勿在虚拟机的操作系统中手动分配已分配给 Azure 虚拟机的公共 IP 地址。

## <a name="next-steps"></a>后续步骤
* 了解[保留公共 IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) 地址。
* 了解[实例层级公共 IP (ILPIP) 地址](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip)。
* 查阅[保留 IP REST API](/previous-versions/azure/reference/dn722420(v=azure.100))。