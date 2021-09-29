---
title: 使用 Azure IoT Central 资源管理器监视设备连接性
description: 监视设备消息，并通过 IoT Central 资源管理器 CLI 观察设备孪生更改。
author: dominicbetts
ms.author: dobett
ms.date: 08/30/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
ms.openlocfilehash: 9c257b9df42af31c443ae3e609d578db6adeb1c8
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154824"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>使用 Azure CLI 监视设备连接

使用 Azure CLI IoT 扩展，了解如何将设备发送到 IoT Central，并观察设备孪生中的更改。 可以使用此工具来调试和观察设备连接性，并诊断设备消息不到达云或者设备不响应孪生更改的问题。

[如需更多详细信息，请访问 Azure CLI 扩展参考](/cli/azure/iot/central)

## <a name="prerequisites"></a>先决条件

Azure 中的工作或学校帐户，在 IoT Central 应用程序中作为用户添加。

[!INCLUDE [azure-cli-prepare-your-environment-h3](../../../includes/azure-cli-prepare-your-environment-h3.md)]

## <a name="install-the-iot-central-extension"></a>安装 IoT Central 扩展

从命令行运行以下命令以安装：

```azurecli
az extension add --name azure-iot
```

检查扩展的版本，方法是运行：

```azurecli
az --version
```

应该会看到 azure-iot extension 扩展为 0.9.9 或更高版本。 如果不是，请运行：

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>使用扩展

以下部分介绍常用的命令和选项，它们可以在你运行 `az iot central` 时使用。 若要查看完整的命令和选项集，请将 `--help` 传递到 `az iot central` 或其任何子命令。

### <a name="login"></a>登录

从登录 Azure CLI 着手。 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>获取 IoT Central 应用的应用程序 ID
在“管理/应用程序设置”中，复制 **应用程序 ID**。 可以在以后的步骤中使用此值。

### <a name="monitor-messages"></a>监视消息
监视从设备发送到 IoT Central 应用的消息。 输出包含所有标头和批注。

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>查看设备属性
查看某个给定设备当前的读取和读取/写入设备属性。

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>后续步骤

建议下一步是了解 [Azure IoT Central 中的设备连接](./concepts-get-connected.md)。