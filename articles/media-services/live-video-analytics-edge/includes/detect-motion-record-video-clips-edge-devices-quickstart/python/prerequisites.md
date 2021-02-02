---
ms.openlocfilehash: f031843243e64515549eaa4e850efcddca58d1f2
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633282"
---
* 具有活动订阅的 Azure 帐户。 如果没有帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
  > [!NOTE]
  > 你将需要一个具有服务主体创建权限（所有者角色提供此权限）的 Azure 订阅。 如果你没有正确的权限，请联系帐户管理员，让其授予你适当的权限。 
* 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
        > [!TIP]
        > 在安装 Azure IoT Tools 时，系统可能会提示安装 Docker。 可以忽略此提示。
    * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
* [Python 3](https://www.python.org/downloads/)（3.6.9 或更高版本）、[Pip 3](https://pip.pypa.io/en/stable/installing/) 和可选的 [venv](https://docs.python.org/3/library/venv.html)。
* 如果尚未完成[检测运动并发出事件](../../../detect-motion-emit-events-quickstart.md)快速入门，请按照以下步骤操作：
     1. [设置 Azure 资源](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [设置开发环境](../../../detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [生成并部署 IoT Edge 部署清单](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [准备监视事件](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> 如果在创建 Azure 资源时遇到问题，请查看[故障排除指南](../../../troubleshoot-how-to.md#common-error-resolutions)来解决一些常见问题。
