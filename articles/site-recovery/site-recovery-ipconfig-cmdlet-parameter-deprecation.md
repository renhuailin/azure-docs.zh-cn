---
title: 弃用 cmdlet New-AzRecoveryServicesAsrVMNicConfig 的 IPConfig 参数 | Microsoft Docs
description: 有关弃用 cmdlet New-AzRecoveryServicesAsrVMNicConfig 的 IPConfig 参数的详细信息和有关使用新 cmdlet New-AzRecoveryServicesAsrVMNicIPConfig 的信息
services: site-recovery
author: rishjai-msft
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/30/2021
ms.author: rishjai
ms.openlocfilehash: 138dd9d576638cda52ca62e45cdb353920e9b00b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968558"
---
# <a name="deprecation-of-ip-config-parameters-for-the-cmdlet-new-azrecoveryservicesasrvmnicconfig"></a>弃用 cmdlet New-AzRecoveryServicesAsrVMNicConfig 的 IP 配置参数

本文介绍弃用方案、相应影响，以及在以下方案中为客户提供的备用选项：

配置用于故障转移或测试故障转移的主 IP 配置设置。 

此 cmdlet 影响在 Az Powershell 5.9.0 及以上版本中使用 cmdlet New-AzRecoveryServicesAsrVMNicConfig 完成 Azure 到 Azure 灾难恢复方案的所有客户。

> [!IMPORTANT]
> 建议客户尽早执行修正步骤，以免对其环境造成任何中断。 

## <a name="what-changes-should-you-expect"></a>预计应该会有哪些变更？

New-AzRecoveryServicesAsrVMNicConfig 使用以下参数配置用于 FO/TFO 的 IP 配置值：
- RecoveryVMSubnetName
- RecoveryNicStaticIPAddress
- RecoveryPublicIPAddressId
- RecoveryLBBackendAddressPoolId
- TfoVMSubnetName
- TfoNicStaticIPAddress
- TfoPublicIPAddressId
- TfoLBBackendAddressPoolId

cmdlet 将不再接受这些参数。

- 自 2021 年 5 月 4 日起，你将收到 Azure 门户通知和电子邮件，告知你即将弃用 cmdlet New-AzRecoveryServicesAsrVMNicConfig 中的 IP 配置参数。

- 如果你的现有脚本正在使用此参数，则该脚本将不再受支持。
 
## <a name="alternatives"></a>备选方法 

作为备选方法，我们引入新 cmdlet [New-AzRecoveryServicesAsrVMNicIPConfig](/powershell/module/az.recoveryservices/new-azrecoveryservicesasrvmnicipconfig) 来配置 FO/TFO 的 IP 配置设置。 


## <a name="remediation-steps"></a>修正步骤

需要修改脚本才能删除这些参数。 开始改用新 cmdlet New-AzRecoveryServicesAsrVMNicIPConfig 创建 IP 配置对象。 示例如下：

编写现有脚本，如下所示：
```azurepowershell
# Fetching the Protected Item Object (for the Protected VM)
$protectedItemObject = Get-AsrReplicationProtectedItem -ProtectionContainer $primaryContainerObject | where { $_.FriendlyName -eq $VMName };$protectedItemObject

# ID of the NIC whose settings are to be updated.
$nicId = $protectedItemObject.NicDetailsList[0].NicId

$nic1 = New-AzRecoveryServicesAsrVMNicConfig -NicId $nicId -ReplicationProtectedItem $protectedItemObject -RecoveryVMNetworkId <networkArmId> -TfoVMNetworkId <networkArmId> -RecoveryVMSubnetName "default" -TfoVMSubnetName "default" -RecoveryNicStaticIPAddress "10.1.40.223" -TfoNicStaticIPAddress "10.33.0.223"

$nics = @($nic1)
Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $protectedItemObject -ASRVMNicConfiguration $nics
```

修改脚本，如下所示：
```azurepowershell
# Fetching the Protected Item Object (for the Protected VM)
$protectedItemObject = Get-AsrReplicationProtectedItem -ProtectionContainer $primaryContainerObject | where { $_.FriendlyName -eq $VMName };$protectedItemObject

# Create the config object for Primary IP Config
$ipConfig = New-AzRecoveryServicesAsrVMNicIPConfig  -IpConfigName <ipConfigName> -RecoverySubnetName "default" -TfoSubnetName "default" -RecoveryStaticIPAddress "10.1.40.223" -TfoStaticIPAddress "10.33.0.223"

$ipConfigs = @($ipConfig)

# ID of the NIC whose settings are to be updated.
$nicId = $protectedItemObject.NicDetailsList[0].NicId

$nic1 = New-AzRecoveryServicesAsrVMNicConfig -NicId $nicId -ReplicationProtectedItem $protectedItemObject -RecoveryVMNetworkId <networkArmId> -TfoVMNetworkId <networkArmId> -IPConfig $ipConfigs

$nics = @($nic1)
Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $protectedItemObject -ASRVMNicConfiguration $nics
```

## <a name="next-steps"></a>后续步骤
如上所述，修改脚本。 如果你对此有任何疑问，请联系 Microsoft 支持部门