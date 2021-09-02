---
title: 管理 Azure 区块链服务联盟成员 - PowerShell
description: 了解如何使用 Azure PowerShell 管理 Azure 区块链服务联盟成员。
ms.date: 10/14/2019
ms.topic: how-to
ms.reviewer: zeyadr
ms.openlocfilehash: 72f8e1fc5de0b48b23627f8590a08e06865cac12
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "122653209"
---
# <a name="manage-consortium-members-in-azure-blockchain-service-using-powershell"></a>使用 PowerShell 管理 Azure 区块链服务中的联盟成员

可以使用 PowerShell 来管理 Azure 区块链服务的区块链联盟成员。

[!INCLUDE [Retirement note](./includes/retirement.md)]

具有管理员权限的成员可以邀请、添加、删除和更改区块链联盟中所有参与者的角色。 具有用户权限的成员可以查看区块链联盟中的所有参与者并更改其成员显示名称。

## <a name="prerequisites"></a>先决条件

* 使用 [Azure门户](create-member.md)创建一个区块链成员。
* 有关联盟、成员和节点的详细信息，请参阅 [Azure 区块链服务联盟](consortium.md)。

## <a name="open-azure-cloud-shell"></a>打开 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的操作步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

也可以在单独的浏览器选项卡中通过转到 [shell.azure.com/powershell](https://shell.azure.com/powershell) 打开 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后选择“Enter”来运行它 。

## <a name="install-the-powershell-module"></a>安装 PowerShell 模块

从 PowerShell 库安装 Microsoft.AzureBlockchainService.ConsortiumManagement.PS 包。

```powershell-interactive
Install-Module -Name Microsoft.AzureBlockchainService.ConsortiumManagement.PS -Scope CurrentUser
Import-Module Microsoft.AzureBlockchainService.ConsortiumManagement.PS
```

## <a name="set-the-information-preference"></a>设置信息首选项

通过设置信息首选项变量，可以在执行 cmdlet 时获取更多信息。 默认情况下，$InformationPreference 设置为 SilentlyContinue 。

若要通过 cmdlet 获取更多详细信息，请按如下所示在 PowerShell 中设置首选项：

```powershell-interactive
$InformationPreference = 'Continue'
```

## <a name="establish-a-web3-connection"></a>建立 Web3 连接

要管理联盟成员，请与区块链服务成员终结点建立 Web3 连接。 可以使用此脚本设置全局变量，以调用联盟管理 cmdlet。

```powershell-interactive
$Connection = New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
$MemberAccount = Import-Web3Account -ManagedAccountAddress '<Member account address>' -ManagedAccountPassword '<Member account password>'
$ContractConnection = Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>' -Web3Client $Connection
```

将 \<Member account password\> 替换为在创建成员时使用的成员帐户密码。

在 Azure 门户中查找其他值：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到默认区块链服务成员的“概述”页。

    ![成员概述](./media/manage-consortium-powershell/member-overview.png)

    将 \<Member account\> 和 \<RootContract address\> 替换为门户中的值 。

1. 对于终结点地址，请选择“事务节点”，然后选择“默认事务节点” 。 默认节点的名称与区块链成员的名称相同。
1. 选择“连接字符串”。

    ![连接字符串](./media/manage-consortium-powershell/connection-strings.png)

    将 \<Endpoint address\> 替换为 HTTPS (访问密钥 1) 或 HTTPS (访问密钥 2) 中的值 。

## <a name="manage-the-network-and-smart-contracts"></a>管理网络和智能合同

使用网络和智能合同 cmdlet 与负责联盟管理的区块链终结点的智能合同建立连接。

### <a name="import-consortiummanagementcontracts"></a>Import-ConsortiumManagementContracts

使用此 cmdlet 可连接到联盟管理的智能合同。 这些合同用于管理和强制联盟中的成员。

`Import-ConsortiumManagementContracts -RootContractAddress <String> -Web3Client <IClient>`

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| RootContractAddress | 联盟管理智能合同的根合同地址 | 是 |
| Web3Client | 从 New-Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
Import-ConsortiumManagementContracts -RootContractAddress '<RootContract address>'  -Web3Client $Connection
```

### <a name="import-web3account"></a>Import-Web3Account

使用此 cmdlet 可创建一个对象，用于保存有关远程节点的管理帐户的信息。

`Import-Web3Account -ManagedAccountAddress <String> -ManagedAccountPassword <String>`

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| ManagedAccountAddress | 区块链成员帐户地址 | 是 |
| ManagedAccountPassword | 帐户地址密码 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
Import-Web3Account -ManagedAccountAddress '<Member account address>'  -ManagedAccountPassword '<Member account password>'
```

### <a name="new-web3connection"></a>New-Web3Connection

使用此 cmdlet 可与事务节点的 RPC 终结点建立连接。

`New-Web3Connection [-RemoteRPCEndpoint <String>]`

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| RemoteRPCEndpoint | 区块链成员终结点地址 | 是 |

#### <a name="example"></a>示例

```powershell-interactive
New-Web3Connection -RemoteRPCEndpoint '<Endpoint address>'
```

## <a name="manage-the-consortium-members"></a>管理联盟成员

使用联盟成员管理 cmdlet 来管理联盟中的成员。 可用操作取决于你的联盟角色。

### <a name="get-blockchainmember"></a>Get-BlockchainMember

使用此 cmdlet 可获取成员详细信息或列出联盟的成员。

`Get-BlockchainMember [[-Name] <String>] -Members <IContract> -Web3Client <IClient>`

| 参数 | 说明 | 必须 |
|-----------|-------------|:--------:|
| 名称 | 要检索其详细信息的区块链服务成员的名称。 输入名称后，它将返回成员的详细信息。 省略名称时，它将返回所有联盟成员的列表。 | 否 |
| 成员 | 从 Import-ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Client | 从 New-Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 变量。

```powershell-interactive
$ContractConnection | Get-BlockchainMember -Name <Member Name>
```

#### <a name="example-output"></a>示例输出

```
Name           : myblockchainmember
CorrelationId  : 0
DisplayName    : myCompany
SubscriptionId : <Azure subscription ID>
AccountAddress : 0x85b911c9e103d6405573151258d668479e9ebeef
Role           : ADMIN
```

### <a name="remove-blockchainmember"></a>Remove-BlockchainMember

使用此 cmdlet 可删除区块链成员。

`Remove-BlockchainMember -Name <String> -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>`

| 参数 | 说明 | 必须 |
|-----------|-------------|:--------:|
| 名称 | 要删除的成员名称 | 是 |
| 成员 | 从 Import-ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Account | 从 Import-Web3Account 中获取的 Web3Account 对象 | 是 |
| Web3Client | 从 New-Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 和 $MemberAccount 变量。

```powershell-interactive
$ContractConnection | Remove-BlockchainMember -Name <Member Name> -Web3Account $MemberAccount
```

### <a name="set-blockchainmember"></a>Set-BlockchainMember

使用此 cmdlet 可设置区块链成员属性，包括显示名称和联盟角色。

联盟管理员可以为所有成员设置“DisplayName”和“Role” 。 具有用户角色的联盟成员只能更改其自己的成员显示名称。

```
Set-BlockchainMember -Name <String> [-DisplayName <String>] [-AccountAddress <String>] [-Role <String>]
 -Members <IContract> -Web3Account <IAccount> -Web3Client <IClient>
```

| 参数 | 说明 | 必须 |
|-----------|-------------|:--------:|
| 名称 | 区块链成员的名称 | 是 |
| DisplayName | 新显示名称 | 否 |
| AccountAddress | Account address | 否 |
| 成员 | 从 Import-ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Account | 从 Import-Web3Account 中获取的 Web3Account 对象 | 是 |
| Web3Client |  从 New-Web3Connection 中获取的 Web3Client 对象| 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 和 $MemberAccount 变量。

```powershell-interactive
$ContractConnection | Set-BlockchainMember -Name <Member Name> -DisplayName <Display name> -Web3Account $MemberAccount
```

## <a name="manage-the-consortium-members-invitations"></a>管理联盟成员的邀请

使用联盟成员邀请管理 cmdlet 来管理联盟成员的邀请。 可用操作取决于你的联盟角色。

### <a name="new-blockchainmemberinvitation"></a>New-BlockchainMemberInvitation

使用此 cmdlet 可邀请新成员加入联盟。

```
New-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 Azure 订阅 ID | 是 |
| 角色 | 联盟角色。 值可以是 ADMIN 或 USER。 ADMIN 是联盟管理员角色。 USER 是联盟成员角色。 | 是 |
| 成员 | 从 Import-ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Account | 从 Import-Web3Account 中获取的 Web3Account 对象 | 是 |
| Web3Client | 从 New-Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 和 $MemberAccount 变量。

```powershell-interactive
$ContractConnection | New-BlockchainMemberInvitation -SubscriptionId <Azure Subscription ID> -Role USER -Web3Account $MemberAccount
```

### <a name="get-blockchainmemberinvitation"></a>Get-BlockchainMemberInvitation

使用此 cmdlet 可检索或列出联盟成员的邀请状态。

`Get-BlockchainMemberInvitation [[-SubscriptionId] <String>] -Members <IContract> -Web3Client <IClient>`

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 Azure 订阅 ID。 如果提供了订阅 ID，它将返回订阅 ID 的邀请详细信息。 如果省略订阅 ID，它将返回所有成员邀请的列表。 | 否 |
| 成员 | 从 Import-ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Client | 从 New-Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 变量。

```powershell-interactive
$ContractConnection | Get-BlockchainMemberInvitation –SubscriptionId <Azure subscription ID>
```

#### <a name="example-output"></a>示例输出

```
SubscriptionId                       Role CorrelationId
--------------                       ---- -------------
<Azure subscription ID>              USER             2
```

### <a name="remove-blockchainmemberinvitation"></a>Remove-BlockchainMemberInvitation

使用此 cmdlet 可撤消联盟成员的邀请。

```
Remove-BlockchainMemberInvitation -SubscriptionId <String> -Members <IContract> -Web3Account <IAccount>
 -Web3Client <IClient>
```

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| SubscriptionId | 要撤消的成员的 Azure 订阅 ID | 是 |
| 成员 | 从 Import-ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Account | 从 Import-Web3Account 中获取的 Web3Account 对象 | 是 |
| Web3Client | 从 New-Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 和 $MemberAccount 变量。

```powershell-interactive
$ContractConnection | Remove-BlockchainMemberInvitation -SubscriptionId <Subscription ID> -Web3Account $MemberAccount
```

### <a name="set-blockchainmemberinvitation"></a>Set-BlockchainMemberInvitation

使用此 cmdlet 可为现有邀请设置“Role”。 只有联盟管理员才能更改邀请。

```
Set-BlockchainMemberInvitation -SubscriptionId <String> -Role <String> -Members <IContract>
 -Web3Account <IAccount> -Web3Client <IClient>
```

| 参数 | 说明 | 必需 |
|-----------|-------------|:--------:|
| SubscriptionId | 要邀请的成员的 Azure 订阅 ID | 是 |
| 角色 | 邀请的新联盟角色。 值可以是 USER 或 ADMIN 。 | 是 |
| 成员 |  从 Import-ConsortiumManagementContracts 获取的成员对象 | 是 |
| Web3Account | 从 Import-Web3Account 中获取的 Web3Account 对象 | 是 |
| Web3Client | 从 New-Web3Connection 中获取的 Web3Client 对象 | 是 |

#### <a name="example"></a>示例

[建立 Web3 连接](#establish-a-web3-connection)以设置 $ContractConnection 和 $MemberAccount 变量。

```powershell-interactive
$ContractConnection | Set-BlockchainMemberInvitation -SubscriptionId <Azure subscription ID> -Role USER -Web3Account $MemberAccount
```

## <a name="next-steps"></a>后续步骤

有关联盟、成员和节点的详细信息，请参阅 [Azure 区块链服务联盟](consortium.md)
