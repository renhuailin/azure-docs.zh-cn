---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: d4a2c269c2dfe66dd0d3271b3eef8fc8de32e4a5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383677"
---
本教程的先决条件如下：


* 包含活动订阅的 Azure 帐户。 如果没有帐户，可免费[创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!NOTE]    
    > 你将需要 Azure 订阅，可以在其中访问[参与者](../../../../../role-based-access-control/built-in-roles.md#contributor)角色和[用户访问管理员](../../../../../role-based-access-control/built-in-roles.md#user-access-administrator)角色。 如果没有适当的权限，请联系帐户管理员，让其授予此类权限。
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    
    > [!TIP]
    > 在安装 Azure IoT Tools 时，系统可能会提示安装 Docker。 可以忽略此提示。    
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。 

> [!Important]
> 此自定义视觉模块仅支持 Intel x86 和 amd64 体系结构。 请在继续操作之前检查边缘设备的体系结构。

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](../../common-includes/azure-resources.md)]
