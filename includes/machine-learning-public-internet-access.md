---
title: include 文件
description: include 文件
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 07/21/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: b0b46da23de802d9f26e53d7b3acc96f166e78d0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443378"
---
Azure 机器学习需要对公共 Internet 进行入站和出站访问。 下表概述了需要的访问权限及其用途。 所有项的协议都是 TCP 。 对于以 `.region` 结尾的服务标记，请将 `region` 替换为包含你的工作区的 Azure 区域。 例如，`Storage.westus`：

| 方向 | 端口 | 服务标记 | 目的 |
| ----- |:-----:| ----- | ----- |
| 入站 | 29876-29877 | BatchNodeManagement | Azure 机器学习计算实例和计算群集。 |
| 入站 | 44224 | AzureMachineLearning | Azure 机器学习计算实例。 |
| 出站 | * | AzureActiveDirectory | Azure Active Directory 身份验证。 |
| 出站 | 443 | AzureMachineLearning | Azure 机器学习。 |
| 出站 | 443 | AzureResourceManager | Azure 资源管理器。 |
| 出站 | 443 | Storage.region | Azure 存储帐户。 |
| 出站 | 443 | AzureFrontDoor.FrontEnd</br>* Azure 中国区域不需要。 | Azure Front Door。 | 
| 出站 | 443 | ContainerRegistry.region | Azure 容器注册表。 仅在使用自定义 Docker 映像时需要。 包括对 Microsoft 提供的基本映像的少量修改（例如额外的包）。 |
| 出站 | 443 | MicrosoftContainerRegistry.region | 仅当使用 Microsoft 提供的 Docker 映像并启用用户管理的依赖项时才需要。 |

> [!TIP]
> 如果需要 IP 地址而不是服务标记，请使用下列选项之一：
> * 从 [Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)下载列表。
> * 使用 Azure CLI [az network list-service-tags](/cli/azure/network#az_network_list_service_tags) 命令。
> * 使用 Azure PowerShell [Get-AzNetworkServiceTag](/powershell/module/az.network/get-aznetworkservicetag) 命令。
> 
> IP 地址可能会定期改变。

可能还需要允许出站流量发往 Visual Studio Code 和非 Microsoft 站点，以便安装机器学习项目所需的包。 下表列出了用于机器学习的常用存储库：

| 主机名 | 目的 |
| ----- | ----- |
| **anaconda.com**</br>**\*.anaconda.com** | 用于安装默认包。 |
| \*.anaconda.org | 用于获取存储库数据。 |
| **pypi.org** | 用于列出默认索引的依赖项（如果有），索引不会被用户设置覆盖。 如果索引被覆盖，则还必须允许“\*.pythonhosted.org”。 |
| **cloud.r-project.org** | 在安装用于 R 开发的 CRAN 包时使用。 |
| **\*pytorch.org** | 由基于 PyTorch 的一些示例使用。 |
| **\*.tensorflow.org** | 由基于 Tensorflow 的一些示例使用。 |
| **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | 用于检索通过安装脚本安装在计算实例上的 VS Code 服务器位。|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | 用于检索安装在计算实例上的 websocket 服务器位。 websocket 服务器用于将来自 Visual Studio Code 客户端（桌面应用程序）的请求传输到计算实例上运行的 Visual Studio Code 服务器。|