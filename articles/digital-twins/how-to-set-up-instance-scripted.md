---
title: 设置实例和身份验证（已编写脚本）
titleSuffix: Azure Digital Twins
description: 请参阅如何通过运行自动部署脚本来设置 Azure 数字孪生服务的实例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1667c21b9a35b8e93feffb8cf1b37d4409da73c3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044299"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>设置 Azure 数字孪生实例和身份验证 (脚本) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文介绍 **设置新的 Azure 数字孪生实例** 的步骤，包括创建实例和设置身份验证。 完成本文后，你将拥有一个可开始对进行编程的 Azure 数字孪生实例。

本文的此版本通过运行可简化过程的 [**自动化部署脚本** 示例](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) 来完成这些步骤。 
* 若要查看脚本在幕后运行的手动 CLI 步骤，请参阅本文的 CLI 版本： how [*to： Set a instance and authentication (CLI)*](how-to-set-up-instance-cli.md)。
* 若要根据 Azure 门户查看手动步骤，请参阅本文的门户版本： [*如何：) 门户 (设置实例和身份验证*](how-to-set-up-instance-portal.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>必备组件：下载脚本

示例脚本用 PowerShell 编写。 这是 [**Azure 数字孪生端到端示例**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)的一部分，可通过导航到此示例链接并选择标题下面的 " *下载 ZIP* " 按钮，将其下载到计算机。

这会将示例项目下载到你的计算机上 _**Azure_Digital_Twins_end_to_end_samples.zip**_。 导航到计算机上的文件夹并将其解压缩以提取文件。

在解压缩文件夹中，部署脚本位于 _Azure_Digital_Twins_end_to_end_samples > 脚本 > **deploy.ps1**_。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>运行部署脚本

本文使用 Azure 数字孪生代码示例来完全自动部署 Azure 数字孪生实例和所需的身份验证。 它还可用作编写您自己的脚本交互的起点。

下面是在 Cloud Shell 中运行部署脚本的步骤。
1. 在浏览器中转到 [Azure Cloud Shell](https://shell.azure.com/) 窗口。 使用以下命令登录：
    ```azurecli-interactive
    az login
    ```
    如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。 否则，请在浏览器中打开 *https://aka.ms/devicelogin*，然后输入终端中显示的授权代码。
 
2. 在 Cloud Shell 图标栏中，确保 Cloud Shell 设置为运行 PowerShell 版本。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="显示 PowerShell 版本选择的 Cloud Shell 窗口":::

1. 选择 "上传/下载文件" 图标，然后选择 "上传"。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="显示 &quot;上传&quot; 图标选择的 Cloud Shell 窗口":::

    在计算机上导航到 _**deploy.ps1**_ 文件 (_Azure_Digital_Twins_end_to_end_samples > 脚本 > **deploy.ps1**_) 并单击 "打开"。 这会将文件上传到 Cloud Shell，以便可以在 "Cloud Shell" 窗口中运行该文件。

4. 通过 `./deploy.ps1` 在 "Cloud Shell" 窗口中发送命令来运行该脚本。 你可以复制下面的命令 (回忆要粘贴到 Cloud Shell 中，可以在 Windows 和 Linux 上使用 **Ctrl + shift + v** ，或在 macOS 上使用 **Cmd + shift + v** 。 还可以使用右键单击菜单) 。

    ```azurecli-interactive
    ./deploy.ps1
    ```

    此脚本将创建一个 Azure 数字孪生实例，并向 Azure 用户分配该实例上的 *Azure 数字孪生数据所有者* 角色。

    当脚本通过自动安装步骤运行时，系统会要求您传递以下值：
    * 对于实例：要使用的 Azure 订阅的 *订阅 ID*
    * 对于实例：要在其中部署实例的 *位置* 。 若要查看哪些区域支持 Azure 数字孪生，请访问 [*按区域提供的 azure 产品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
    * 对于实例： *资源组* 名称。 你可以使用现有资源组，或者输入一个新名称来创建。
    * 对于实例： Azure 数字孪生实例的 *名称* 。 如果你的订阅在已使用指定名称的区域中有另一个 Azure 数字孪生实例，则系统将要求你选择其他名称。

下面是该脚本的输出日志摘录：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="显示通过运行部署脚本的输入和输出日志的 Cloud Shell 窗口" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

如果脚本成功完成，最终打印输出将显示 `Deployment completed successfully` 。 否则，请解决错误消息，并重新运行该脚本。 它将跳过已完成的步骤，并在你离开的点再次开始请求输入。

> [!NOTE]
> 脚本当前将 Azure 数字孪生中所需的管理角色分配给从 Cloud Shell 运行脚本的同一用户)  (*azure 数字孪生数据所有者* 。 如果需要将此角色分配给将管理实例的其他人，可以通过 Azure 门户 ([指令](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 或 CLI ([说明](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) 来完成此操作。

>[!NOTE]
>目前有一个 **已知** 的脚本化安装问题，其中某些用户 (专用 Microsoft 帐户上的用户 [ (msa)](https://account.microsoft.com/account)) 可能会发现 **未创建 _Azure 数字孪生数据所有者_ 的角色分配**。
>
>你可以在本文后面的 " [*验证用户角色分配*](#verify-user-role-assignment) " 部分中验证角色分配，并且如果需要，还可以使用 [Azure 门户](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 或 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)手动设置角色分配。
>
>有关此问题的更多详细信息，请参阅 [*故障排除：Azure 数字孪生中的已知问题*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup)。

## <a name="verify-success-and-collect-important-values"></a>验证成功并收集重要值

若要验证由脚本创建的资源和权限的创建，可以使用以下说明在 [Azure 门户](https://portal.azure.com) 中查看这些资源和权限。 如果无法验证任何步骤是否成功，请重试此步骤。 您可以使用 [Azure 门户](how-to-set-up-instance-portal.md) 或 [CLI](how-to-set-up-instance-cli.md) 说明单独执行这些步骤。

本部分还介绍了如何从由脚本设置的资源中查找重要值，因为你可以继续使用 Azure 数字孪生实例。 应将这些值保存在安全的位置，或返回此部分，以后在需要时进行查找。 如果其他用户要对该实例进行编程，则也应该与它们共享这些值。

### <a name="verify-instance"></a>验证实例

若要验证是否已创建实例，请在 Azure 门户中转到 [Azure 数字孪生页面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 。 可以通过在门户搜索栏中搜索 " *Azure 数字孪生* " 自行访问此页。

此页列出了所有 Azure 数字孪生实例。 在列表中查找新创建的实例的名称。

如果验证失败，可以使用 [门户](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) 或 [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)重试创建实例。

### <a name="collect-instance-values"></a>收集实例值

从 [Azure 数字孪生页](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) 中选择实例的名称，以打开该实例的 " *概述* " 页。 注意其 *名称*、 *资源组* 和 *主机名*。 稍后可能需要用到它们来标识并连接到实例。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="突出显示实例的 &quot;概述&quot; 页中的重要值":::

### <a name="verify-user-role-assignment"></a>验证用户角色分配

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> 回忆一下，该脚本当前将此必需角色分配给运行脚本的同一用户 Cloud Shell。 如果需要将此角色分配给将管理实例的其他人，可以通过 Azure 门户 ([指令](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) 或 CLI ([说明](how-to-set-up-instance-cli.md#set-up-user-access-permissions)) 来完成此操作。

如果验证不成功，还可以使用 [门户](how-to-set-up-instance-portal.md#set-up-user-access-permissions) 或 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)重做自己的角色分配。

## <a name="next-steps"></a>后续步骤

使用 Azure 数字孪生 CLI 命令测试实例上的单个 REST API 调用： 
* [az dt reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*操作说明：使用 Azure 数字孪生 CLI*](how-to-use-cli.md)

或者，请参阅如何使用身份验证代码将客户端应用程序连接到实例：
* [*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)