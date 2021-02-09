---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831191"
---
如果打算继续学习其他 IoT 即插即用文章，则可保留并重复使用在本文中使用的资源。 否则，可删除在本文中创建的资源，以免产生额外的费用。

可通过使用以下 Azure CLI 命令删除整个资源组来一次性删除中心和注册设备。 如果这些资源与你想要保留的其他资源共享一个资源组，则不要使用此命令。

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

若要仅删除 IoT 中心，请使用 Azure CLI 运行以下命令：

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

若要仅删除注册到 IoT 中心的设备标识，请使用 Azure CLI 运行以下命令：

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

你可能还需要从开发计算机中删除克隆的示例文件。
