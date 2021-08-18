---
title: Azure AD Connect：ADSync PowerShell 参考 | Microsoft Docs
description: 本文档提供 ADSync.psm1 PowerShell 模块的参考信息。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19d3b2dc72e31cef290bd5253e7b173d624488af
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228732"
---
# <a name="azure-ad-connect--adsync-powershell-reference"></a>Azure AD Connect：ADSync PowerShell 参考
以下文档提供了 Azure AD Connect 随附的 ADSync.psm1 PowerShell 模块的参考信息。


## <a name="add-adsyncaddsconnectoraccount"></a>Add-ADSyncADDSConnectorAccount

 ### <a name="synopsis"></a>摘要
 此 cmdlet 重置服务帐户的密码，并在 Azure AD 和同步引擎中更新该密码。

 ### <a name="syntax"></a>SYNTAX
  #### <a name="byidentifier"></a>byIdentifier
   ```
     Add-ADSyncADDSConnectorAccount [-Identifier] <Guid> [-EACredential <PSCredential>] [<CommonParameters>]
   ```

 #### <a name="byname"></a>byName
   ```
     Add-ADSyncADDSConnectorAccount [-Name] <String> [-EACredential <PSCredential>] [<CommonParameters>]
   ```

 ### <a name="description"></a>DESCRIPTION
 此 cmdlet 重置服务帐户的密码，并在 Azure AD 和同步引擎中更新该密码。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
   ```powershell
     PS C:\> Add-ADSyncADDSConnectorAccount -Name contoso.com -EACredential $EAcredentials
   ```

 重置连接到 contoso.com 的服务帐户的密码。

 ### <a name="parameters"></a>PARAMETERS

   #### <a name="-eacredential"></a>-EACredential
   Active Directory 中企业管理员帐户的凭据。

   ```yaml
     Type: PSCredential
     Parameter Sets: (All)
     Aliases:

     Required: False
     Position: Named
     Default value: None
     Accept pipeline input: False
     Accept wildcard characters: False
   ```

  #### <a name="-identifier"></a>-Identifier
  需要重置其服务帐户密码的连接器的标识符。

   ```yaml
     Type: Guid
     Parameter Sets: byIdentifier
     Aliases:

     Required: True
     Position: 0
     Default value: None
     Accept pipeline input: True (ByValue)
     Accept wildcard characters: False
   ```

  #### <a name="-name"></a>-Name
  连接器的名称。

   ```yaml
     Type: String
     Parameter Sets: byName
     Aliases:
 
     Required: True
     Position: 1
     Default value: None
     Accept pipeline input: True (ByValue)
     Accept wildcard characters: False
   ```

  #### <a name="commonparameters"></a>CommonParameters
  此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

  #### <a name="systemguid"></a>System.Guid

  #### <a name="systemstring"></a>System.String

 ### <a name="outputs"></a>输出

  #### <a name="systemobject"></a>System.Object



## <a name="disable-adsyncexportdeletionthreshold"></a>Disable-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>摘要
 禁用导出阶段的删除阈值功能。

 ### <a name="syntax"></a>SYNTAX
   
   ```
     Disable-ADSyncExportDeletionThreshold [[-AADCredential] <PSCredential>] [-WhatIf] [-Confirm]
     [<CommonParameters>]
   ```

 ### <a name="description"></a>DESCRIPTION
 禁用导出阶段的删除阈值功能。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
   ```powershell
     PS C:\> Disable-ADSyncExportDeletionThreshold -AADCredential $aadCreds
   ```

 使用提供的 AAD 凭据禁用导出删除阈值功能。

 ### <a name="parameters"></a>PARAMETERS

  #### <a name="-aadcredential"></a>-AADCredential
  AAD 凭据。

   ```yaml
     Type: PSCredential
     Parameter Sets: (All)
     Aliases:

     Required: False
     Position: 1
     Default value: None
     Accept pipeline input: True (ByPropertyName)
     Accept wildcard characters: False
  ```

  #### <a name="-confirm"></a>-Confirm
  用于提示确认的参数开关。

 ```yaml
     Type: SwitchParameter
     Parameter Sets: (All)
     Aliases: cf

     Required: False
     Position: Named
     Default value: None
     Accept pipeline input: False
     Accept wildcard characters: False
 ```

  #### <a name="-whatif"></a>-WhatIf
  显示运行该 cmdlet 时会发生什么情况。
此 cmdlet 未运行。

 ```yaml
     Type: SwitchParameter
     Parameter Sets: (All)
     Aliases: wi

     Required: False
     Position: Named
     Default value: None
     Accept pipeline input: False
     Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

  #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 ### <a name="outputs"></a>输出

  #### <a name="systemobject"></a>System.Object

## <a name="enable-adsyncexportdeletionthreshold"></a>Enable-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>摘要
 启用导出删除阈值功能并设置阈值。

 ### <a name="syntax"></a>SYNTAX

 ```
 Enable-ADSyncExportDeletionThreshold [-DeletionThreshold] <UInt32> [[-AADCredential] <PSCredential>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 启用导出删除阈值功能并设置阈值。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Enable-ADSyncExportDeletionThreshold -DeletionThreshold 777 -AADCredential $aadCreds
 ```

 启用导出删除阈值功能并将删除阈值设置为 777。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 AAD 凭据。

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-confirm"></a>-Confirm
 提示你在运行 cmdlet 之前进行确认。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-deletionthreshold"></a>-DeletionThreshold
 删除阈值。

 ```yaml
 Type: UInt32
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 显示运行该 cmdlet 时会发生什么情况。
此 cmdlet 未运行。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="systemuint32"></a>System.UInt32
 
 #### <a name="sytemmanagementautomationpscredential"></a>Sytem.Management.Automation.PSCredential

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncautoupgrade"></a>Get-ADSyncAutoUpgrade

 ### <a name="synopsis"></a>摘要
 获取安装的 AutoUpgrade 的状态。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncAutoUpgrade [-Detail] [<CommonParameters>]
 ``` 

 ### <a name="description"></a>DESCRIPTION
 获取安装的 AutoUpgrade 的状态。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Get-ADSyncAutoUpgrade -Detail
 ```

 返回安装的 AutoUpgrade 状态并在 AutoUpgrade 暂停时显示暂停原因。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-detail"></a>-Detail
 如果 AutoUpgrade 状态为暂停，则使用此参数显示暂停原因。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsynccsobject"></a>Get-ADSyncCSObject

 ### <a name="synopsis"></a>摘要
 获取指定的连接器空间对象。

 ### <a name="syntax"></a>SYNTAX
 
 #### <a name="searchbyidentifier"></a>SearchByIdentifier
 ```
 Get-ADSyncCSObject [-Identifier] <Guid> [<CommonParameters>]
 ```
 
 #### <a name="searchbyconnectoridentifierdistinguishedname"></a>SearchByConnectorIdentifierDistinguishedName
 ```
 Get-ADSyncCSObject [-ConnectorIdentifier] <Guid> [-DistinguishedName] <String> [-SkipDNValidation] [-Transient]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectoridentifier"></a>SearchByConnectorIdentifier
 ```
 Get-ADSyncCSObject [-ConnectorIdentifier] <Guid> [-Transient] [-StartIndex <Int32>] [-MaxResultCount <Int32>]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectornamedistinguishedname"></a>SearchByConnectorNameDistinguishedName
 ```
 Get-ADSyncCSObject [-ConnectorName] <String> [-DistinguishedName] <String> [-SkipDNValidation] [-Transient]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectorname"></a>SearchByConnectorName
 ```
 Get-ADSyncCSObject [-ConnectorName] <String> [-Transient] [-StartIndex <Int32>] [-MaxResultCount <Int32>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 获取指定的连接器空间对象。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Get-ADSyncCSObject -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DC=contoso,DC=com"
 ```

 获取 contoso.com 域中用户 fabrikam 的 CS 对象。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 连接的标识符。

 ```yaml
 Type: Guid
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorIdentifier 
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 连接器的名称。

 ```yaml
 Type: String
 Parameter Sets: SearchByConnectorNameDistinguishedName, SearchByConnectorName
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 连接器空间对象的可分辨名称。

 ```yaml
 Type: String
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorNameDistinguishedName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 连接器空间对象的标识符。

 ```yaml
 Type: Guid
 Parameter Sets: SearchByIdentifier
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-maxresultcount"></a>-MaxResultCount
 结果集的最大计数。

 ```yaml
 Type: Int32
 Parameter Sets: SearchByConnectorIdentifier, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-skipdnvalidation"></a>-SkipDNValidation
 参数开关，用于跳过 DN 验证。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorNameDistinguishedName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-startindex"></a>-StartIndex
 要从其返回计数的起始索引。

 ```yaml
 Type: Int32
 Parameter Sets: SearchByConnectorIdentifier, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-transient"></a>-Transient
 参数开关，用于获取瞬时 CS 对象。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorIdentifier, SearchByConnectorNameDistinguishedName, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsynccsobjectlog"></a>Get-ADSyncCSObjectLog

 ### <a name="synopsis"></a>摘要
 获取连接器空间对象日志条目。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncCSObjectLog [-Identifier] <Guid> [-Count] <UInt32> [<CommonParameters>]
 ```
 
 ### <a name="description"></a>DESCRIPTION
 获取连接器空间对象日志条目。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Get-ADSyncCSObjectLog -Identifier "00000000-0000-0000-0000-000000000000" -Count 1
 ```

 返回一个具有指定标识符的对象。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-count"></a>-Count
 要检索的连接器空间对象日志条目的预期最大数量。

 ```yaml
 Type: UInt32
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 连接器空间对象标识符。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncdatabaseconfiguration"></a>Get-ADSyncDatabaseConfiguration

 ### <a name="synopsis"></a>摘要
 获取 ADSync 数据库的配置。
 
 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncDatabaseConfiguration [<CommonParameters>]
 ```
 
 ### <a name="description"></a>DESCRIPTION
 获取 ADSync 数据库的配置。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Get-ADSyncDatabaseConfiguration
 ```

 获取 ADSync 数据库的配置。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncexportdeletionthreshold"></a>Get-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>摘要
 从 AAD 获取导出删除阈值。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncExportDeletionThreshold [[-AADCredential] <PSCredential>] [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 从 AAD 获取导出删除阈值。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Get-ADSyncExportDeletionThreshold -AADCredential $aadCreds
 ```

 使用指定的 AAD 凭据从 AAD 获取导出删除阈值。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 AAD 凭据。

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-confirm"></a>-Confirm
 提示你在运行 cmdlet 之前进行确认。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 显示运行该 cmdlet 时会发生什么情况。
此 cmdlet 未运行。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncmvobject"></a>Get-ADSyncMVObject

 ### <a name="synopsis"></a>摘要
 获取 metaverse 对象。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncMVObject -Identifier <Guid> [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 获取 metaverse 对象。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Get-ADSyncMVObject -Identifier "00000000-0000-0000-0000-000000000000"
 ```

 获取具有指定标识符的 metaverse 对象。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-identifier"></a>-Identifier
 metaverse 对象的标识符。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncrunprofileresult"></a>Get-ADSyncRunProfileResult

 ### <a name="synopsis"></a>摘要
 处理来自客户端的输入并检索运行配置文件结果。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncRunProfileResult [-RunHistoryId <Guid>] [-ConnectorId <Guid>] [-RunProfileId <Guid>]
 [-RunNumber <Int32>] [-NumberRequested <Int32>] [-RunStepDetails] [-StepNumber <Int32>] [-WhatIf] [-Confirm]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 处理来自客户端的输入并检索运行配置文件结果。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Get-ADSyncRunProfileResult -ConnectorId "00000000-0000-0000-0000-000000000000"
 ```

 检索指定连接器的所有同步运行配置文件结果。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-confirm"></a>-Confirm
 提示你在运行 cmdlet 之前进行确认。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorid"></a>-ConnectorId
 连接器标识符。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-numberrequested"></a>-NumberRequested
 返回的最大数。

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runhistoryid"></a>-RunHistoryId
 特定运行的标识符。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runnumber"></a>-RunNumber
 特定运行的运行编号。

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runprofileid"></a>-RunProfileId
 特定运行的运行配置文件标识符。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runstepdetails"></a>-RunStepDetails
 运行步骤详细信息的参数开关。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stepnumber"></a>-StepNumber
 按步骤编号进行筛选。

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 显示运行该 cmdlet 时会发生什么情况。
此 cmdlet 未运行。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncrunstepresult"></a>Get-ADSyncRunStepResult

 ### <a name="synopsis"></a>摘要
 获取 AD Sync 运行步骤结果。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncRunStepResult [-RunHistoryId <Guid>] [-StepHistoryId <Guid>] [-First] [-StepNumber <Int32>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 获取 AD Sync 运行步骤结果。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Get-ADSyncRunStepResult -RunHistoryId "00000000-0000-0000-0000-000000000000"
 ```

 获取指定运行的 AD Sync 运行步骤结果。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-confirm"></a>-Confirm
 提示你在运行 cmdlet 之前进行确认。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-first"></a>-First
 仅获取第一个对象的参数开关。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runhistoryid"></a>-RunHistoryId
 特定运行的 ID。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stephistoryid"></a>-StepHistoryId
 特定运行步骤的 ID。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stepnumber"></a>-StepNumber
 步骤编号。

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 显示运行该 cmdlet 时会发生什么情况。
此 cmdlet 未运行。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object



## <a name="get-adsyncscheduler"></a>Get-ADSyncScheduler

 ### <a name="synopsis"></a>摘要
 获取同步计划程序的当前同步周期设置。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncScheduler [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 获取同步计划程序的当前同步周期设置。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Get-ADSyncScheduler
 ```

 获取同步计划程序的当前同步周期设置。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncschedulerconnectoroverride"></a>Get-ADSyncSchedulerConnectorOverride

 ### <a name="synopsis"></a>摘要
 获取指定连接器的 AD 同步计划程序替代值。

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncSchedulerConnectorOverride [-ConnectorIdentifier <Guid>] [-ConnectorName <String>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 获取指定连接器的 AD 同步计划程序替代值。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1 
 ```powershell
 PS C:\> Get-ADSyncSchedulerConnectorOverride -ConnectorName "contoso.com"
 ```

 获取“contoso.com”连接器的 AD 同步计划程序替代值。

 #### <a name="example-2"></a>示例 2
 ```powershell
 PS C:\> Get-ADSyncSchedulerConnectorOverride
 ```

 获取所有 AD 同步计划程序替代值。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 连接器标识符。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 连接器名称。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="invoke-adsynccsobjectpasswordhashsync"></a>Invoke-ADSyncCSObjectPasswordHashSync

 ### <a name="synopsis"></a>摘要
 同步给定 AD 连接器空间对象的密码哈希。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="searchbydistinguishedname"></a>SearchByDistinguishedName
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-ConnectorName] <String> [-DistinguishedName] <String>
 [<CommonParameters>]
 ```

 #### <a name="searchbyidentifier"></a>SearchByIdentifier
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-Identifier] <Guid> [<CommonParameters>]
 ```

 #### <a name="csobject"></a>CSObject
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-CsObject] <CsObject> [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 同步给定 AD 连接器空间对象的密码哈希。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Invoke-ADSyncCSObjectPasswordHashSync -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DN=contoso,DN=com"
 ```

 同步指定对象的密码哈希。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectorname"></a>-ConnectorName
 连接器的名称。

 ```yaml
 Type: String
 Parameter Sets: SearchByDistinguishedName
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-csobject"></a>-CsObject
 连接器空间对象。

 ```yaml
 Type: CsObject
 Parameter Sets: CSObject
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 连接器空间对象的可分辨名称。

 ```yaml
 Type: String
 Parameter Sets: SearchByDistinguishedName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 连接器空间对象的标识符。

 ```yaml
 Type: Guid
 Parameter Sets: SearchByIdentifier
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile

 ### <a name="synopsis"></a>摘要
 调用特定的运行配置文件。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectorname"></a>ConnectorName
 ```
 Invoke-ADSyncRunProfile -ConnectorName <String> -RunProfileName <String> [-Resume] [<CommonParameters>]
 ```

 #### <a name="connectoridentifier"></a>ConnectorIdentifier
 ```
 Invoke-ADSyncRunProfile -ConnectorIdentifier <Guid> -RunProfileName <String> [-Resume] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 调用特定的运行配置文件。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Invoke-ADSyncRunProfile -ConnectorName "contoso.com" -RunProfileName Export
 ```

 调用“contoso.com”连接器上的导出。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 连接器的标识符。

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 连接器的名称。

 ```yaml
 Type: String
 Parameter Sets: ConnectorName
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-resume"></a>-Resume
 参数开关，用于尝试恢复先前停滞/完成了一半的 RunProfile。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runprofilename"></a>-RunProfileName
 要在所选连接器上调用的运行配置文件的名称。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="systemstring"></a>System.String

 #### <a name="systemguid"></a>System.Guid

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="remove-adsyncaadserviceaccount"></a>Remove-ADSyncAADServiceAccount

 ### <a name="synopsis"></a>摘要
 删除 AAD 租户中的一个/所有现有 AAD 服务帐户（与指定的凭据关联）。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="serviceaccount"></a>ServiceAccount
 ```
 Remove-ADSyncAADServiceAccount [-AADCredential] <PSCredential> [-Name] <String> [-WhatIf] [-Confirm]
 [<CommonParameters>]
 ```

 #### <a name="serviceprincipal"></a>服务主体
 ```
 Remove-ADSyncAADServiceAccount [-ServicePrincipal] [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 删除 AAD 租户中的一个/所有现有 AAD 服务帐户（与指定的凭据关联）。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Remove-ADSyncAADServiceAccount -AADCredential $aadcreds -Name contoso.com
 ```

 删除 contoso.com 中的所有现有 AAD 服务帐户。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 AAD 凭据。

 ```yaml
 Type: PSCredential
 Parameter Sets: ServiceAccount
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-confirm"></a>-Confirm
 提示你在运行 cmdlet 之前进行确认。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-name"></a>-Name
 帐户的名称。

 ```yaml
 Type: String
 Parameter Sets: ServiceAccount
 Aliases:

 Required: True
 Position: 2
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-serviceprincipal"></a>-ServicePrincipal
 帐户的服务主体。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: ServicePrincipal
 Aliases:

 Required: True
 Position: 3
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 显示运行该 cmdlet 时会发生什么情况。
此 cmdlet 未运行。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 #### <a name="systemstring"></a>System.String

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncautoupgrade"></a>Set-ADSyncAutoUpgrade

 ### <a name="synopsis"></a>摘要
 在“启用”和“禁用”之间更改安装上的 AutoUpgrade 状态。

 ### <a name="syntax"></a>SYNTAX

 ```
 Set-ADSyncAutoUpgrade [-AutoUpgradeState] <AutoUpgradeConfigurationState> [[-SuspensionReason] <String>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 设置安装上的 AutoUpgrade 状态。 此 cmdlet 应仅用于在“启用”和“禁用”之间更改AutoUpgrade 状态。 只有系统才能将状态设置为“暂停”。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Set-ADSyncAutoUpgrade -AutoUpgradeState Enabled
 ```

 将 AutoUpgrade 状态设置为“启用”。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-autoupgradestate"></a>-AutoUpgradeState
 AutoUpgrade 状态。 接受的值：“暂停”、“启用”、“禁用”。

 ```yaml
 Type: AutoUpgradeConfigurationState
 Parameter Sets: (All)
 Aliases:
 Accepted values: Suspended, Enabled, Disabled

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-suspensionreason"></a>-SuspensionReason
 暂停原因。 只有系统才能将 AutoUpgrade 的状态设置为“暂停”。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncscheduler"></a>Set-ADSyncScheduler

 ### <a name="synopsis"></a>摘要
 设置同步计划程序的当前同步周期设置。

 ### <a name="syntax"></a>SYNTAX

 ```
 Set-ADSyncScheduler [[-CustomizedSyncCycleInterval] <TimeSpan>] [[-SyncCycleEnabled] <Boolean>]
 [[-NextSyncCyclePolicyType] <SynchronizationPolicyType>] [[-PurgeRunHistoryInterval] <TimeSpan>]
 [[-MaintenanceEnabled] <Boolean>] [[-SchedulerSuspended] <Boolean>] [-Force] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 设置同步计划程序的当前同步周期设置。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Set-ADSyncScheduler -SyncCycleEnabled $true
 ```

 将 SyncCycleEnabled 的当前同步周期设置设置为 True。
 
 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-customizedsynccycleinterval"></a>-CustomizedSyncCycleInterval
 请为要设置的自定义同步间隔指定时间跨度值。
如果要在允许的最低设置上运行，请将此参数设置为 null。

 ```yaml
 Type: TimeSpan
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-force"></a>-Force
 用于强制设置值的参数开关。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 6
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-maintenanceenabled"></a>-MaintenanceEnabled
 用于设置 MaintenanceEnabled 的参数。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 4
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-nextsynccyclepolicytype"></a>-NextSyncCyclePolicyType
 用于设置 NextSyncCyclePolicyType 的参数。 接受的值：“未指定”、“Delta”、“初始值”。

 ```yaml
 Type: SynchronizationPolicyType
 Parameter Sets: (All)
 Aliases:
 Accepted values: Unspecified, Delta, Initial

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-purgerunhistoryinterval"></a>-PurgeRunHistoryInterval
 用于设置 PurgeRunHistoryInterval 的参数。

 ```yaml
 Type: TimeSpan
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 3
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-schedulersuspended"></a>-SchedulerSuspended
 用于设置 SchedulerSuspended 的参数。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 5
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-synccycleenabled"></a>-SyncCycleEnabled
 用于设置 SyncCycleEnabled 的参数。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="systemnullable1systemtimespan-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.TimeSpan, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 #### <a name="systemnullable1systemboolean-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 #### <a name="systemnullable1microsoftidentitymanagementpowershellobjectmodelsynchronizationpolicytype-microsoftidentitymanagementpowershellobjectmodel-version1400-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.IdentityManagement.PowerShell.ObjectModel.SynchronizationPolicyType, Microsoft.IdentityManagement.PowerShell.ObjectModel, Version=1.4.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncschedulerconnectoroverride"></a>Set-ADSyncSchedulerConnectorOverride

 ### <a name="synopsis"></a>摘要
 设置同步计划程序的当前同步周期设置。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectoridentifier"></a>ConnectorIdentifier
 ```
 Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> [-FullImportRequired <Boolean>]
 [-FullSyncRequired <Boolean>] [<CommonParameters>]
 ```

 #### <a name="connectorname"></a>ConnectorName
 ```
 Set-ADSyncSchedulerConnectorOverride -ConnectorName <String> [-FullImportRequired <Boolean>]
 [-FullSyncRequired <Boolean>] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 设置同步计划程序的当前同步周期设置。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Set-ADSyncSchedulerConnectorOverride -Connectorname "contoso.com" -FullImportRequired $true
 -FullSyncRequired $false
 ```

 将“contoso.com”连接器的同步周期设置设置为需要完全导入且无需完全同步。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 连接器标识符。

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 连接器名称。

 ```yaml
 Type: String
 Parameter Sets: ConnectorName
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-fullimportrequired"></a>-FullImportRequired
 设置为 true，以要求在下一个周期完全导入。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-fullsyncrequired"></a>-FullSyncRequired
 设置为 true，以要求在下一个周期完全同步。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="systemguid"></a>System.Guid

 #### <a name="systemstring"></a>System.String

 #### <a name="systemnullable1systemboolean-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="start-adsyncpurgerunhistory"></a>Start-ADSyncPurgeRunHistory

 ### <a name="synopsis"></a>摘要
 Cmdlet，用于清除早于指定时间跨度的运行历史记录。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="online"></a>联机
 ```
 Start-ADSyncPurgeRunHistory [[-PurgeRunHistoryInterval]  <TimeSpan>] [<CommonParameters>]
 ```

 #### <a name="offline"></a>offline
 ```
 Start-ADSyncPurgeRunHistory [-Offline] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Cmdlet，用于清除早于指定时间跨度的运行历史记录。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Start-ADSyncPurgeRunHistory -PurgeRunHistoryInterval (New-Timespan -Hours 5)
 ```

 清除所有超过 5 小时的运行历史记录。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-offline"></a>-Offline
 当服务处于脱机状态时，从数据库中清除所有运行历史记录。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: offline
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-purgerunhistoryinterval"></a>-PurgeRunHistoryInterval
 要保留其历史记录的时间间隔。

 ```yaml
 Type: TimeSpan
 Parameter Sets: online
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="systemtimespan"></a>System.TimeSpan

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object

## <a name="start-adsyncsynccycle"></a>Start-ADSyncSyncCycle

 ### <a name="synopsis"></a>摘要
 触发同步周期。

 ### <a name="syntax"></a>SYNTAX

 ```
 Start-ADSyncSyncCycle [[-PolicyType] <SynchronizationPolicyType>] [[-InteractiveMode] <Boolean>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 触发同步周期。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Start-ADSyncSyncCycle -PolicyType Initial
 ```

 触发具有初始策略类型的同步周期。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-interactivemode"></a>-InteractiveMode
 区分交互（命令行）模式和脚本/代码模式（来自其他代码的调用）。

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-policytype"></a>-PolicyType
 要运行的策略类型。 接受的值：“未指定”、“Delta”、“初始值”。

 ```yaml
 Type: SynchronizationPolicyType
 Parameter Sets: (All)
 Aliases:
 Accepted values: Unspecified, Delta, Initial

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="systemnullable1microsoftidentitymanagementpowershellobjectmodelsynchronizationpolicytype-microsoftidentitymanagementpowershellobjectmodel-version1400-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.IdentityManagement.PowerShell.ObjectModel.SynchronizationPolicyType, Microsoft.IdentityManagement.PowerShell.ObjectModel, Version=1.4.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemboolean"></a>System.Boolean

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="stop-adsyncrunprofile"></a>Stop-ADSyncRunProfile

 ### <a name="synopsis"></a>摘要
 查找并停止所有或指定的繁忙连接器。

 ### <a name="syntax"></a>SYNTAX

 ```
 Stop-ADSyncRunProfile [[-ConnectorName] <String>] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 查找并停止所有或指定的繁忙连接器。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Stop-ADSyncRunProfile -ConnectorName "contoso.com"
 ```

 停止在“contoso.com”上运行的任何同步。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectorname"></a>-ConnectorName
 连接器的名称。
如果没有给出，那么所有繁忙连接器都将停止。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="stop-adsyncsynccycle"></a>Stop-ADSyncSyncCycle

 ### <a name="synopsis"></a>摘要
 指示服务器停止当前运行的同步周期。

 ### <a name="syntax"></a>SYNTAX

 ```
 Stop-ADSyncSyncCycle [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 指示服务器停止当前运行的同步周期。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Stop-ADSyncSyncCycle
 ```

 指示服务器停止当前运行的同步周期。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="sync-adsynccsobject"></a>Sync-ADSyncCSObject

 ### <a name="synopsis"></a>摘要
 在连接器空间对象上运行同步预览。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectorname_objectdn"></a>ConnectorName_ObjectDN
 ```
 Sync-ADSyncCSObject -ConnectorName <String> -DistinguishedName <String> [-Commit] [<CommonParameters>]
 ```

 #### <a name="connectoridentifier_objectdn"></a>ConnectorIdentifier_ObjectDN
 ```
 Sync-ADSyncCSObject -ConnectorIdentifier <Guid> -DistinguishedName <String> [-Commit] [<CommonParameters>]
 ```

 #### <a name="objectidentifier"></a>ObjectIdentifier
 ```
 Sync-ADSyncCSObject -Identifier <Guid> [-Commit] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 在连接器空间对象上运行同步预览。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Sync-ADSyncCSObject -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DC=contoso,DC=com"
 ```

 返回指定对象的同步预览。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-commit"></a>-Commit
 用于提交的参数开关。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 连接的标识符。

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 连接器的名称。

 ```yaml
 Type: String
 Parameter Sets: ConnectorName_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 连接器空间对象的可分辨名称。

 ```yaml
 Type: String
 Parameter Sets: ConnectorName_ObjectDN, ConnectorIdentifier_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 连接器空间对象的标识符。

 ```yaml
 Type: Guid
 Parameter Sets: ObjectIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="none"></a>无

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="test-adsyncazureserviceconnectivity"></a>Test-AdSyncAzureServiceConnectivity

 ### <a name="synopsis"></a>摘要
 调查并确定与 Azure AD 的连接问题。

 ### <a name="syntax"></a>SYNTAX

 #### <a name="byenvironment"></a>ByEnvironment
 ```
 Test-AdSyncAzureServiceConnectivity [-AzureEnvironment] <Identifier> [[-Service] <AzureService>] [-CurrentUser]
 [<CommonParameters>]
 ```

 #### <a name="bytenantname"></a>ByTenantName
 ```
 Test-AdSyncAzureServiceConnectivity [-Domain] <String> [[-Service] <AzureService>] [-CurrentUser]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 调查并确定与 Azure AD 的连接问题。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Test-AdSyncAzureServiceConnectivity -AzureEnvironment Worldwide -Service SecurityTokenService -CurrentUser
 ```

 如果没有连接问题，则返回 True。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-azureenvironment"></a>-AzureEnvironment
 要测试的 Azure 环境。 接受的值：全球、中国、美国政府、德国、AzureUSGovernmentCloud、AzureUSGovernmentCloud2、AzureUSGovernmentCloud3、PreProduction、OneBox、默认值。

 ```yaml
 Type: Identifier
 Parameter Sets: ByEnvironment
 Aliases:
 Accepted values: Worldwide, China, UsGov, Germany, AzureUSGovernmentCloud, AzureUSGovernmentCloud2, AzureUSGovernmentCloud3, PreProduction, OneBox, Default

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-currentuser"></a>-CurrentUser
 运行 cmdlet 的用户。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 3
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-domain"></a>-Domain
 正在测试其连接性的域。

 ```yaml
 Type: String
 Parameter Sets: ByTenantName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-service"></a>-Service
 正在测试其连接性的服务。

 ```yaml
 Type: AzureService
 Parameter Sets: (All)
 Aliases:
 Accepted values: SecurityTokenService, AdminWebService

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="microsoftonlinedeploymentclientframeworkmicrosoftonlineinstanceidentifier"></a>Microsoft.Online.Deployment.Client.Framework.MicrosoftOnlineInstance+Identifier

 #### <a name="systemstring"></a>System.String

 #### <a name="systemnullable1microsoftonlinedeploymentclientframeworkazureservice-microsoftonlinedeploymentclientframework-version1600-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.Online.Deployment.Client.Framework.AzureService, Microsoft.Online.Deployment.Client.Framework, Version=1.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object


## <a name="test-adsyncuserhaspermissions"></a>Test-AdSyncUserHasPermissions

 ### <a name="synopsis"></a>摘要
 Cmdlet，用于检查 ADMA 用户是否具有所需的权限。

 ### <a name="syntax"></a>SYNTAX

 ```
 Test-AdSyncUserHasPermissions [-ForestFqdn] <String> [-AdConnectorId] <Guid>
 [-AdConnectorCredential] <PSCredential> [-BaseDn] <String> [-PropertyType] <String> [-PropertyValue] <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>DESCRIPTION
 Cmdlet，用于检查 ADMA 用户是否具有所需的权限。

 ### <a name="examples"></a>示例

 #### <a name="example-1"></a>示例 1
 ```powershell
 PS C:\> Test-AdSyncUserHasPermissions -ForestFqdn "contoso.com" -AdConnectorId "00000000-0000-0000-000000000000"
 -AdConnectorCredential $connectorAcctCreds -BaseDn "CN=fabrikam,CN=Users,DC=contoso,DC=com" -PropertyType "Allowed-Attributes" -PropertyValue "name"
 ```

 检查 ADMA 用户是否有权访问用户 fabrikam 的 name 属性。

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-adconnectorcredential"></a>-AdConnectorCredential
 AD 连接器帐户凭据。

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 2
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-adconnectorid"></a>-AdConnectorId
 AD 连接器 ID。

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-basedn"></a>-BaseDn
 要检查的对象的基本 DN。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 3
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-confirm"></a>-Confirm
 提示你在运行 cmdlet 之前进行确认。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: cf

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-forestfqdn"></a>-ForestFqdn
 林的名称。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-propertytype"></a>-PropertyType
 要查找的权限类型。 接受的值：Allowed-Attributes、Allowed-Attributes-Effective、Allowed-Child-Classes、Allowed-Child-Classes-Effective。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 4
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-propertyvalue"></a>-PropertyValue
 在 PropertyType 属性中查找的值。

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 5
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-whatif"></a>-WhatIf
 显示运行该 cmdlet 时会发生什么情况。
此 cmdlet 未运行。

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases: wi

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。 有关详细信息，请参阅 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。

 ### <a name="inputs"></a>输入

 #### <a name="systemstring"></a>System.String

 #### <a name="systemguid"></a>System.Guid

 ### <a name="outputs"></a>输出

 #### <a name="systemobject"></a>System.Object

## <a name="next-steps"></a>后续步骤

- [什么是混合标识？](./whatis-hybrid-identity.md)
- [什么是 Azure AD Connect 和 Connect Health？](whatis-azure-ad-connect.md)