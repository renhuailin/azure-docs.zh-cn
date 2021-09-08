---
title: include 文件
description: include 文件
author: lobrien
ms.service: machine-learning
services: machine-learning
ms.topic: include
ms.date: 08/27/2021
ms.author: larryfr
ms.custom: include file
ms.openlocfilehash: 18d6da8c9156a66a16be7590603ae71b85abb9a4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105491"
---
Azure 机器学习需要对公共 Internet 进行入站和出站访问。 下表概述了需要的访问权限及其用途。 所有项的协议都是 TCP 。 对于以 `.region` 结尾的服务标记，请将 `region` 替换为包含你的工作区的 Azure 区域。 例如，`Storage.westus`：

| 方向 | 端口 | 服务标记 | 目的 |
| ----- |:-----:| ----- | ----- |
| 入站 | 29876-29877 | BatchNodeManagement | 创建、更新和删除 Azure 机器学习计算实例和计算群集。 |
| 入站 | 44224 | AzureMachineLearning | 创建、更新和删除 Azure 机器学习计算实例。 |
| 出站 | * | AzureActiveDirectory | 使用 Azure AD 进行身份验证。 |
| 出站 | 443 | AzureMachineLearning | 使用 Azure 机器学习服务。 |
| 出站 | 443 | AzureResourceManager | 使用 Azure 机器学习创建 Azure 资源。 |
| 出站 | 443 | Storage.region | 访问 Azure 存储帐户中存储的 Azure Batch 服务数据。 |
| 出站 | 443 | AzureFrontDoor.FrontEnd</br>* Azure 中国区域不需要。 | [Azure 机器学习工作室](https://ml.azure.com)的全球入口点。 | 
| 出站 | 443 | ContainerRegistry.region | 访问由 Microsoft 提供的 docker 映像。 |
| 出站 | 443 | MicrosoftContainerRegistry.region | 访问由 Microsoft 提供的 docker 映像。 为 Azure Kubernetes 服务设置 Azure 机器学习路由器。 |

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

将 Azure Kubernetes Service (AKS) 与 Azure 机器学习一起使用时，请允许将以下流量发送到 AKS VNet：

* AKS 的一般入站/出站要求见[限制 Azure Kubernetes 服务中的出口流量](/azure/aks/limit-egress-traffic)一文中所述。
* 发往 mcr.microsoft.com 的出站流量。
* 将模型部署到 AKS 群集时，请遵循[将 ML 模型部署到 Azure Kubernetes 服务](/azure/machine-learning/how-to-deploy-azure-kubernetes-service#connectivity)一文中的指导。