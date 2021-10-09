---
title: PowerShell 脚本：设置允许的 VM 大小
description: 本文包括示例 PowerShell 脚本，用于在 Azure 实验室服务中设置允许的虚拟机 (VM) 大小。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: ef5e755caf5b5f1477b947798869a67663f71ee6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557659"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>使用 PowerShell 在 Azure 实验室服务中设置允许的 VM 大小

此示例 PowerShell 脚本在 Azure 实验室服务中设置允许的虚拟机 (VM) 大小。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>先决条件
* 实验室。 此脚本要求拥有现有的实验室。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 注释 |
|---|---|
| Find-AzResource | 基于指定参数搜索资源。 |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 获取资源。 |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | 修改资源。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 创建资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure 实验室服务 PowerShell 示例](../samples-powershell.md)中找到其他 Azure 实验室服务 PowerShell 脚本示例。
