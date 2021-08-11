---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: 3c4a156f745b5c32e238a7865a9c26710baf7ed0
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113735163"
---
本教程的先决条件如下：

* 包含活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    [!INCLUDE [azure-subscription-permissions](../../common-includes/azure-subscription-permissions.md)]
- 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
  * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [Python 3](https://www.python.org/downloads/)（3.6.9 或更高版本）、[Pip 3](https://pip.pypa.io/en/stable/installing/) 和可选的 [venv](https://docs.python.org/3/library/venv.html)。 

> [!Important]
> 此自定义视觉模块仅支持 Intel x86 和 amd64 体系结构。 请在继续操作之前检查边缘设备的体系结构。

## <a name="set-up-azure-resources"></a>设置 Azure 资源

[![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](../../common-includes/azure-resources.md)]


