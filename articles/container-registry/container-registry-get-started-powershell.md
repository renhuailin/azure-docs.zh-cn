---
title: 快速入门 - 创建注册表 - PowerShell
description: 快速了解如何使用 PowerShell 在 Azure 容器注册表中创建专用 Docker 注册表
ms.date: 06/03/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell - mvc - devx-track-azurepowershell - mode-api
ms.openlocfilehash: b774e75c5df86b35efb466bfffcb8f9afa1961d6
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893810"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建专用容器注册表

Azure 容器注册表是用于生成、存储和管理容器映像和相关项目的专用注册表服务。 在本快速入门中，你将使用 Azure PowerShell 创建一个 Azure 容器注册表实例。 然后，使用 Docker 命令将容器映像推送到注册表中，最终从注册表提取并运行该映像。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本快速入门需要 Azure PowerShell 模块。 运行 `Get-Module -ListAvailable Az` 即可确定已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

还必须在本地安装 Docker。 Docker 提供的包适用于 [macOS][docker-mac]、[Windows][docker-windows] 和 [Linux][docker-linux] 系统。

由于 Azure Cloud Shell 并不包括所有必需的 Docker 组件（`dockerd` 守护程序），因此不能将 Cloud Shell 用于本快速入门。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 [Connect-AzAccount][Connect-AzAccount] 命令登录到 Azure 订阅，然后按屏幕说明操作。

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>创建资源组

通过 Azure 进行身份验证以后，请使用 [New-AzResourceGroup][New-AzResourceGroup] 创建资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>创建容器注册表

接下来，请使用 [New-AzContainerRegistry][New-AzContainerRegistry] 命令在新的资源组中创建容器注册表。

注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 以下示例创建名为“myContainerRegistry007”的注册表。 替换以下命令中的 *myContainerRegistry007*，然后运行该命令以创建注册表：

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

[!INCLUDE [container-registry-quickstart-sku](../../includes/container-registry-quickstart-sku.md)]

## <a name="log-in-to-registry"></a>登录到注册表

在推送和拉取容器映像之前，必须使用 [Connect-AzContainerRegistry][connect-azcontainerregistry] cmdlet 登录到注册表。 下面的示例使用的凭据与你使用 `Connect-AzAccount` cmdlet 向 Azure 进行身份验证时登录所用的凭据相同。

> [!NOTE]
> 在以下示例中，`$registry.Name` 的值是资源名称，而不是完全限定的注册表名称。

```powershell
Connect-AzContainerRegistry -Name $registry.Name
```

该命令在完成后返回 `Login Succeeded`。

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>清理资源

用完在本快速入门中创建的资源后，请使用 [Remove-AzResourceGroup][Remove-AzResourceGroup] 命令删除资源组、容器注册表以及其中存储的容器映像：

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure PowerShell 创建 Azure 容器注册表、推送容器映像，以及提取和运行注册表中的映像。 请继续阅读 Azure 容器注册表教程，以更深入地了解 ACR。

> [!div class="nextstepaction"]
> [Azure 容器注册表教程][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure 容器注册表任务教程][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[connect-azcontainerregistry]: /powershell/module/az.containerregistry/connect-azcontainerregistry
