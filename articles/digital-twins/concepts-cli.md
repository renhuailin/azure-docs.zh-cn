---
title: Azure 数字孪生 CLI 命令集
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生 CLI 命令集。
author: baanders
ms.author: baanders
ms.date: 8/25/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 087a7bc7fda1c54e0a9e2c7f896b06dda78fa11e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221148"
---
# <a name="azure-digital-twins-cli-command-set"></a>Azure 数字孪生 CLI 命令集

除了在 Azure 门户中管理 Azure 数字孪生实例外，还可以使用 Azure 数字孪生为 [Azure CLI](/cli/azure/what-is-azure-cli) 提供的命令集对服务执行大多数主要操作，包括：
* 管理 Azure 数字孪生实例
* 管理模型
* 管理数字孪生体
* 管理孪生关系
* 配置终结点
* 管理[路由](concepts-route-events.md)
* 通过 Azure 基于角色的访问控制 (Azure RBAC) 配置[安全性](concepts-security.md)

此命令集称为“az dt”，是[适用于 Azure CLI 的 Azure IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)的一部分。 可以在 `az iot` 命令集的参考文档中查看完整的命令列表及其用法：az dt 命令参考。

## <a name="uses-deploy-and-validate"></a>使用（部署和验证）

除了通常情况下对实例进行管理外，CLI 还是一个用于部署和验证的有用工具。
* 可使用控制平面命令重复部署或自动部署新的实例。
* 可使用数据平面命令快速检查实例中的值，并验证操作是否按预期完成。

## <a name="get-the-command-set"></a>获取命令集

Azure 数字孪生命令是[适用于 Azure CLI 的 Azure IoT 扩展 (azure-iot)](https://github.com/Azure/azure-iot-cli-extension) 的一部分，因此请按照以下步骤操作，以确保使用 az dt 命令获得最新 `azure-iot` 扩展。

### <a name="cli-version-requirements"></a>CLI 版本要求

如果要将 Azure CLI 与 PowerShell 配合使用，则扩展包的要求是，Azure CLI 版本应为 2.3.1 或更高版本。

可以使用以下 CLI 命令检查 Azure CLI 的版本：
```azurecli
az --version
```

有关如何将 Azure CLI 安装或更新为更新版本的说明，请参阅安装 Azure CLI。

### <a name="get-the-extension"></a>获取扩展

首次使用需要扩展的命令时，Azure CLI 会自动提示你安装该扩展。

否则，可以使用以下命令随时自行安装扩展（或在你已有较旧版本的情况下对其进行更新）。 命令可以在 [Azure Cloud Shell](../cloud-shell/overview.md) 或[本地 Azure CLI](/cli/azure/install-azure-cli) 中运行。

```azurecli-interactive
az extension add --upgrade --name azure-iot
```

## <a name="next-steps"></a>后续步骤

通过参考文档了解 CLI 及其完整的命令集：
* [az dt 命令参考](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)