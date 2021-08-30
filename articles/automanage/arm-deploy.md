---
title: 使用 ARM 模板将计算机加入 Azure Automanage
description: 了解如何使用 Azure 资源管理器模板将计算机加入 Azure Automanage。
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 34f20a50fc62cff98362380222822ac016b76a48
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746765"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>使用 Azure 资源管理器 (ARM) 模板将计算机加入 Automanage


## <a name="overview"></a>概述
按照下面的步骤将计算机加入 Automanage 最佳做法。 下面的 ARM 模板将创建一个 `configurationProfileAssignment` 对象，这是一种 Azure 资源，用于表示已加入 Automanage 的计算机。

## <a name="prerequisites"></a>必备知识
* 必须已创建一个现有的 Automanage 帐户，并为其分配了正确的权限。 请参阅[此文档](./automanage-account.md)，详细了解 Automanage 帐户及其创建方法和分配权限方法。
* 如果你有一个已分配权限的现有 Automanage 帐户，还必须在包含要加入 Automanage 的计算机的资源组上具有参与者角色。


## <a name="arm-template-overview"></a>ARM 模板概述
下面的 ARM 模板会将指定的计算机加入 Azure Automanage 最佳做法。 请参阅下面的 [ARM 模板部署](#arm-template-deployment)部分，详细了解 ARM 模板及其部署步骤。
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>ARM 模板部署
上面的 ARM 模板将使用指定的 Automanage 帐户为指定计算机创建一个配置文件分配。 如果你尚未创建 Automanage 帐户，请参阅[此文档](./automanage-account.md)了解详细信息。

`configurationProfileAssignment` 值可以是以下值之一：
* “Production”
* “DevTest”

请遵照以下步骤部署 ARM 模板：
1. 将上面的 ARM 模板另存为 `azuredeploy.json`
1. 使用 `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json` 运行 ARM 模板部署
1. 当系统发出提示时，提供 machineName、automanageAccountName 和 configurationProfileAssignment 的值
1. 完成！

与任何 ARM 模板一样，可以将参数分解成单独的 `azuredeploy.parameters.json` 文件，然后在进行部署时将该文件用作参数。

## <a name="next-steps"></a>后续步骤
详细了解用于 [Linux](./automanage-linux.md) 和 [Windows](./automanage-windows-server.md) 的 Automanage