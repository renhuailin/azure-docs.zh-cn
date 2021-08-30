---
title: 使用 PowerShell 连接到 Azure 德国 | Microsoft Docs
description: 有关使用 PowerShell 管理 Azure 德国中的订阅的信息
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs, devx-track-azurepowershell
ms.openlocfilehash: 3f5caa51b992d348fa077a0dbb96e210aa18f4de
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2021
ms.locfileid: "117029098"
---
# <a name="connect-to-azure-germany-by-using-powershell"></a>使用 PowerShell 连接到 Azure 德国

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

要将 Azure PowerShell 与 Azure 德国配合使用，你需要连接到 Azure 德国而不是全球 Azure。 可使用 Azure PowerShell 通过脚本管理大型订阅或访问 Azure 门户中当前不可用的功能。 如果已在全球 Azure 中使用过 PowerShell，会发现它们之间基本相同。 Azure 德国的区别如下：

* 连接帐户
* 区域名称

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> 如果尚未使用 PowerShell，请查看 [Azure PowerShell 简介](/powershell/azure/)。

启动 PowerShell 时，需要通过指定环境参数指示 Azure PowerShell 连接到 Azure 德国。 该参数确保 PowerShell 连接到正确的终结点。 连接到帐户后，会确定终结点的集合。 不同的 API 需要不同版本的环境交换机：

| 连接类型 | Command |
| --- | --- |
| [Azure（经典部署模型）](/powershell/azure)命令 |`Add-AzureAccount -Environment AzureGermanCloud` |
| [Azure（Resource Manager 部署模型）](/powershell/azure)命令 |`Connect-AzAccount -EnvironmentName AzureGermanCloud` |
| [Azure Active Directory（经典部署模型）](/previous-versions/azure/jj151815(v=azure.100))命令 |`Connect-MsolService -AzureEnvironment AzureGermanyCloud` |
| [Azure Active Directory（Resource Manager 部署模型）](../azure-resource-manager/management/deployment-models.md)命令 |`Connect-AzureAD -AzureEnvironmentName AzureGermanyCloud` |

使用 `New-AzStorageContext` 连接到存储帐户时，还可使用 `Environment` 开关，然后指定 `AzureGermanCloud`。

## <a name="determining-region"></a>确定区域
连接后，还存在一个不同之处：用于定位服务的区域。 每个 Azure 云服务都具有不同的区域。 可以在服务可用性页面上查看其列表。 通常使用命令的 `Location` 参数中的区域。


| 公用名 | 显示名称 | 位置名称 |
| --- | --- | --- |
| 德国中部 |`Germany Central` | `germanycentral` |
| 德国东北部 |`Germany Northeast` | `germanynortheast` |


> [!NOTE]
> 正如全球 Azure 的 PowerShell 一样，可对 `Location` 参数使用显示名称或位置名称。
>
>

如果想要验证 Azure 德国中的可用区域，可以运行以下命令，并打印当前列表。 对于经典部署，请使用第一个命令。 对于资源管理器部署，请使用第二个命令。

```azurepowershell
Get-AzureLocation
Get-AzLocation
```

如果对 Azure 的可用环境感兴趣，可以运行：

```azurepowershell
Get-AzureEnvironment
Get-AzEnvironment
```

## <a name="next-steps"></a>后续步骤
有关连接到 Azure 德国的详细信息，请参阅以下资源：

* [使用 Azure CLI 连接到 Azure 德国](./germany-get-started-connect-with-cli.md)
* [使用 Visual Studio 连接到 Azure 德国](./germany-get-started-connect-with-vs.md)
* [使用 Azure 门户连接到 Azure 德国](./germany-get-started-connect-with-portal.md)
