---
title: Azure 实验室服务的 Azure PowerShell 模块 | Microsoft Docs
description: 本文介绍了 PowerShell 模块，可帮助管理 Azure 实验室服务中的项目。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4f990b35a41f040d34fab156d3f3d450ad7561a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94646519"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell 模块（预览版）
Az LabServices 是用于简化 Azure 实验室服务管理的 PowerShell 模块。 它提供的可编写函数用于创建、查询、更新和删除实验室帐户、实验室、VM 和映像。 有关详细信息，请参阅 [GitHub 上的 Az.LabServices 主页](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

> [!NOTE]
> 此模块为“预览版”。 

## <a name="example-command"></a>示例命令
以下是使用 PowerShell 命令停止所有实验室中全部正在运行的 VM 的示例。

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>入门
1. 安装 [Azure PowerShell](/powershell/azure/)（如果计算机上未安装）。 
2. 将 [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) 下载到你的计算机。
3. 导入模块：

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. 运行以下命令，列出订阅中的所有实验室。

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>后续步骤
请参阅 [GitHub 上的 Az.LabServices 主页](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。