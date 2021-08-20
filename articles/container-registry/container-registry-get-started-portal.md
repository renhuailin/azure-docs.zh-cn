---
title: 快速入门 - 在门户中创建注册表
description: 快速了解如何通过 Azure 门户创建专用 Azure 容器注册表。
ms.date: 06/23/2021
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
- contperf-fy21q4
ms.openlocfilehash: 51531b6af5babe256ec89079c7705cfea71ef52a
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895933"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>快速入门：通过 Azure 门户创建 Azure 容器注册表

Azure 容器注册表是用于生成、存储和管理容器映像和相关项目的专用注册表服务。 在本快速入门中，你将使用 Azure 门户创建一个 Azure 容器注册表实例。 然后，使用 Docker 命令将容器映像推送到注册表中，最终从注册表提取并运行该映像。

若要登录到注册表以使用容器映像，本快速入门要求运行 Azure CLI（建议使用 2.0.55 或更高版本）。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

还必须在本地安装 Docker。 Docker 提供的包可在任何 [Mac][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-a-container-registry"></a>创建容器注册表

选择“创建资源” > “容器” > “容器注册表”。  

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="在门户中导航到容器注册表":::

在“基本信息”选项卡中，输入“资源组”和“注册表名称”的值 。 注册表名称在 Azure 中必须唯一，并且包含 5-50 个字母数字字符。 对于本快速入门，在 `West US` 位置创建名为 `myResourceGroup` 的新资源组，对于 **SKU**，选择“基本”。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="在门户中创建容器注册表":::

对于剩余的设置，请接受默认值。 然后选择“查看 + 创建”。 查看设置后，选择“创建”。

[!INCLUDE [container-registry-quickstart-sku](../../includes/container-registry-quickstart-sku.md)]

显示“部署成功”消息时，请在门户中选择容器注册表。 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="门户中的容器注册表概述":::

记下登录服务器的注册表名称和值，这是在 Azure 云中以 `azurecr.io` 结尾的完全限定名称。 使用 Docker 推送和拉取映像时，请在以下步骤中使用这些值。

## <a name="log-in-to-registry"></a>登录到注册表

必须登录到注册表实例才可推送和拉取容器映像。 在本地计算机上[登录到 Azure CLI][get-started-with-azure-cli]，然后运行 [az acr login][az-acr-login] 命令。 使用 Azure CLI 登录时仅指定注册表资源名称。 请勿使用完全限定的登录服务器名称。

```azurecli
az acr login --name <registry-name>
```

示例：

```azurecli
az acr login --name mycontainerregistry
```

该命令在完成后返回 `Login Succeeded`。 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>列出容器映像

若要列出注册表中的映像，请在门户中导航到注册表并选择“存储库”，然后选择使用 `docker push` 创建的 hello-world 存储库 。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="在门户中列出容器映像":::

通过选择 hello-world 存储库，可以在“标记”下看到 `v1` 标记的映像 。

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>清理资源

若要清理资源，请在门户中导航到 **myResourceGroup** 资源组。 加载该资源组后，单击“删除资源组”，删除该资源组、容器注册表以及其中存储的容器映像。

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="在门户中删除资源组":::


## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Azure 门户创建 Azure 容器注册表、推送容器映像，以及提取和运行注册表中的映像。 请继续阅读 Azure 容器注册表教程，以更深入地了解 ACR。

> [!div class="nextstepaction"]
> [Azure 容器注册表教程][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Azure 容器注册表任务教程][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az_acr_login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
