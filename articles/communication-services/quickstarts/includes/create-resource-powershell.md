---
ms.openlocfilehash: a04d7dde8b6f2a492538d2012de5222686fd7fcf
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113762391"
---
## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/dotnet/)。
- 安装 [Azure Az PowerShell 模块](/powershell/azure/)

## <a name="create-azure-communication-services-resource"></a>创建 Azure 通信服务资源

若要创建 Azure 通信服务资源，请先[登录 Azure CLI](/cli/azure/authenticate-azure-cli)。 你可以通过使用命令 ```Connect-AzAccount``` 和提供凭据在终端完成此操作。 运行以下命令以创建资源：

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

如果要选择特定订阅，你还可以指定 ```--subscription``` 标志并提供订阅 ID。
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

可使用以下选项来配置通信服务资源：

* 资源组
* Azure 通信服务资源的名称
* 将要与资源关联的地理位置

在下一步中，可以为资源分配标记。 使用标记组织 Azure 资源。 有关标记的详细信息，请参阅[资源标记文档](../../../azure-resource-manager/management/tag-resources.md)。

## <a name="manage-your-communication-services-resource"></a>管理通信服务资源

若要向通信服务资源添加标记，请运行以下命令： 你也可以指向特定的订阅。

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

若要列出给定订阅中的所有 Azure 通信服务资源，请使用以下命令：

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

若要列出给定资源的所有信息，请使用以下命令：

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```