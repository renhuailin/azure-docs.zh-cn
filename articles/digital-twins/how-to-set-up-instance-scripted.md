---
title: 设置实例和身份验证（已编写脚本）
titleSuffix: Azure Digital Twins
description: 了解如何通过运行自动部署脚本来设置 Azure 数字孪生服务实例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 6344e83593509b2828b41b8e9074211aa83fc2d3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114440185"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>设置 Azure 数字孪生实例和身份验证（已编写脚本）

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文介绍“设置新 Azure 数字孪生实例”的步骤，包括创建实例和设置身份验证。 完成本文操作后，即可对 Azure 数字孪生实例编程。

本文的此版本通过运行[自动部署脚本示例](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)来完成这些步骤，由此可简化过程。 
* 若要查看脚本在后台运行的手动 CLI 步骤，请在[设置实例和身份验证 (CLI)](how-to-set-up-instance-cli.md) 中查看本文的 CLI 版本。
* 若要按照 Azure 门户查看手动步骤，请在[设置实例和身份验证（门户）](how-to-set-up-instance-portal.md)中查看本文的门户版本。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

## <a name="prerequisites-permission-requirements"></a>先决条件：权限要求
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>先决条件：下载脚本

示例脚本是在 PowerShell 中编写的。 这是 [Azure 数字孪生端到端示例](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)的一部分，你可导航到此示例链接并选择标题下面的“浏览代码”按钮，将其下载到计算机。 此时会转到这些示例的 GitHub 存储库，你可以通过选择“代码”按钮和“下载 ZIP”将其下载为 .zip 。

:::image type="content" source="media/includes/download-repo-zip.png" alt-text="GitHub 上的 digital-twins-samples 存储库的屏幕截图，突出显示了将该存储库下载为 zip 的步骤。" lightbox="media/includes/download-repo-zip.png":::

这会将 .zip 文件夹以 digital-twins-samples-master.zip 形式下载到计算机上。 导航到计算机上的文件夹，并对其解压缩以提取文件。

在解压缩的文件夹中，部署脚本位于“digital-twins-samples-master”>“脚本” >“deploy.ps1”中。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>运行部署脚本

本文使用 Azure 数字孪生代码示例，半自动部署 Azure 数字孪生实例和所需的身份验证。 此外，还可将其用作编写自己脚本交互的起点。

在 Cloud Shell 中运行部署脚本的步骤如下。
1. 在浏览器中打开 [Azure Cloud Shell](https://shell.azure.com/) 窗口。 使用以下命令登录：
    ```azurecli-interactive
    az login
    ```
    如果 CLI 可以打开默认浏览器，它将这样做并加载 Azure 登录页。 否则，请在浏览器中打开 *https://aka.ms/devicelogin*，然后输入终端中显示的授权代码。
 
2. 在 Cloud Shell 图标栏中，确保将 Cloud Shell 设置为运行 PowerShell 版本。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Azure 门户中 Cloud Shell 窗口的屏幕截图，其中显示选择 PowerShell 版本。":::

1. 选择“上传/下载文件”图标，然后选择“上传”。

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Azure 门户中 Cloud Shell 窗口的屏幕截图，其中显示选择“上传”图标。":::

    导航到计算机上的 deploy.ps1 文件（digital-twins-samples-master > scripts > deploy.ps1 中），然后选择“打开” 。 该文件将上传到 Cloud Shell，以便可以在“Cloud Shell”窗口中运行。

4. 通过在“Cloud Shell”窗口中发送 `./deploy.ps1` 命令运行该脚本。 可以复制以下命令（要粘贴到 Cloud Shell 中，在 Windows 和 Linux 上可以使用 Ctrl+Shift+V，在 macOS 上可以使用 Cmd+Shift+V。 还可以使用右键单击菜单）。

    ```azurecli-interactive
    ./deploy.ps1
    ```

    该脚本将创建一个 Azure 数字孪生实例，并为 Azure 用户分配该实例上的“Azure 数字孪生数据所有者”角色。

    当脚本通过自动安装步骤运行时，系统会要求你传递以下值：
    * 对于实例：要使用的 Azure 订阅的订阅 ID
    * 对于实例：要在其中部署实例的位置。 要查看哪些区域支持 Azure 数字孪生，请访问[各区域的 Azure 产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
    * 对于实例：资源组名称。 可以使用现有资源组，也可以输入一个新名称进行创建。
    * 对于实例：Azure 数字孪生实例的名称。 如果订阅在该区域已存在使用指定名称的其他 Azure 数字孪生实例，系统会要求你选择其他的名称。

下面是该脚本输出日志的摘录：

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Azure 门户中 Cloud Shell 窗口的屏幕截图，其中显示通过运行部署脚本得到的输入和输出日志。" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

如果脚本成功完成，最终打印输出将显示 `Deployment completed successfully`。 否则，请解决错误消息，并重新运行该脚本。 它将跳过已完成的步骤，并从中断的位置重新开始请求输入。

> [!NOTE]
> 脚本当前会向通过 Cloud Shell 运行该脚本的同一用户分配 Azure 数字孪生中必需的管理角色（“Azure 数字孪生数据所有者”）。 如果需要将此角色分配给其他要管理实例的人员，可通过 [Azure 门户说明](how-to-set-up-instance-portal.md#set-up-user-access-permissions)或 [CLI 说明](how-to-set-up-instance-cli.md#set-up-user-access-permissions)来完成此操作。

>[!NOTE]
>目前，脚本安装存在一个已知问题，有些用户（特别是使用个人 [Microsoft 帐户 (MSA)](https://account.microsoft.com/account) 的用户）可能会发现“Azure 数字孪生数据所有者的角色分配尚未创建”。
>
>你可以按照本文后面的“[验证用户角色分配](#verify-user-role-assignment)”部分验证角色分配，如果需要，还可以使用 [Azure 门户](how-to-set-up-instance-portal.md#set-up-user-access-permissions)或 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions) 手动设置角色分配。
>
>有关此问题的更多详细信息，请参阅[故障排除：Azure 数字孪生中的已知问题](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup)。

## <a name="verify-success-and-collect-important-values"></a>验证是否成功并收集重要值

要验证脚本创建的资源和权限，可以按照以下说明在 [Azure 门户](https://portal.azure.com)中查看它们。 如果无法验证任何步骤是否成功，可重试该步骤。 可以按照 [Azure 门户](how-to-set-up-instance-portal.md)或 [CLI](how-to-set-up-instance-cli.md) 说明单独执行这些步骤。

此部分还会介绍如何从脚本设置的资源中查找继续使用 Azure 数字孪生实例可能需要的重要值。 应将这些值保存在安全的位置，或在以后需要它们时返回此部分来查找。 如果其他用户要对该实例进行编程，也应与他们共享这些值。

### <a name="verify-instance"></a>验证实例

要验证是否已创建实例，请在 Azure 门户中转到 [Azure 数字孪生页面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)。 可以通过在门户搜索栏中搜索“Azure 数字孪生”来自行访问此页。

此页列出了所有 Azure 数字孪生实例。 在列表中查找新创建的实例的名称。

如果验证失败，可以使用[门户](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance)或 [CLI](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance) 重新尝试创建实例。

### <a name="collect-instance-values"></a>收集实例值

从 [Azure 数字孪生页面](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)选择实例的名称，以打开该实例的“概述”页。 记下它的“名称”、“资源组”和“主机名”。 稍后，可能需要用它们来识别和连接实例。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Azure 门户的屏幕截图，突出显示了 Azure 数字孪生实例的概述页面中的重要值。":::

### <a name="verify-user-role-assignment"></a>验证用户角色分配

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> 切记，脚本当前为从 Cloud Shell 运行脚本的同一用户分配此必需角色。 如果需要将此角色分配给其他要管理实例的人，可以通过 Azure 门户（[说明](how-to-set-up-instance-portal.md#set-up-user-access-permissions)）或 CLI（[说明](how-to-set-up-instance-cli.md#set-up-user-access-permissions)）来完成此操作。

如果验证失败，还可以使用[门户](how-to-set-up-instance-portal.md#set-up-user-access-permissions)或 [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions) 自行重新进行角色分配。

## <a name="next-steps"></a>后续步骤

使用 Azure 数字孪生 CLI 命令在实例上测试各个 REST API 调用： 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Azure 数字孪生 CLI 命令集](concepts-cli.md)

或者，了解如何使用验证码将客户端应用程序连接到实例：
* [编写应用身份验证码](how-to-authenticate-client.md)