---
title: 使用防火墙
titleSuffix: Azure Machine Learning
description: 使用 Azure 防火墙控制对 Azure 机器学习工作区的访问。 了解必须允许通过防火墙的主机。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 08/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: 790b5a3e34d36d674511507bc5e9ed452c5ba74e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745301"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>将防火墙后的工作区用于 Azure 机器学习

本文介绍如何配置 Azure 防火墙以控制对 Azure 机器学习工作区和公共 Internet 的访问。 若要详细了解如何保护 Azure 机器学习，请参阅 [Azure 机器学习的企业安全性](concept-enterprise-security.md)。

> [!NOTE]
> 本文中的信息适用于 Azure 机器学习工作区，无论该工作区使用专用终结点还是服务终结点。

> [!TIP]
> 本文是介绍如何保护 Azure 机器学习工作流的系列文章的一部分。 请参阅本系列中的其他文章：
>
> * [虚拟网络概述](how-to-network-security-overview.md)
> * [保护工作区资源](how-to-secure-workspace-vnet.md)
> * [保护训练环境](how-to-secure-training-vnet.md)
> * [保护推理环境](how-to-secure-inferencing-vnet.md)
> * [启用工作室功能](how-to-enable-studio-virtual-network.md)
> * [使用自定义 DNS](how-to-custom-dns.md)

## <a name="required-public-internet-access"></a>所需的公共 Internet 访问

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

## <a name="azure-firewall"></a>Azure 防火墙

> [!IMPORTANT]
> Azure 防火墙为 Azure 虚拟网络资源提供安全性。 一些 Azure 服务（如 Azure 存储帐户）有自己的防火墙设置，适用于该特定服务实例的公共终结点。 本文档中的信息针对 Azure 防火墙。
> 
> 有关服务实例防火墙设置的信息，请参阅 [在虚拟网络中使用工作室](how-to-enable-studio-virtual-network.md#firewall-settings)。

* 对于流向 Azure 机器学习计算群集和计算实例的入站流量，请使用[用户定义的路由 (UDR)](../virtual-network/virtual-networks-udr-overview.md) 来跳过防火墙。

* 对于出站流量，请创建“网络”和“应用程序”规则  。 

[一些 Azure 防火墙概念](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)中更详细地介绍了这些规则集合。

### <a name="inbound-configuration"></a>入站配置

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

### <a name="outbound-configuration"></a>出站配置

1. 添加网络规则，允许来自以下服务标记中的流量流向 和 ：  

    | 服务标记 | 协议 | 端口 |
    | ----- |:-----:|:-----:|
    | AzureActiveDirectory | TCP | * |
    | AzureMachineLearning | TCP | 443 |
    | AzureResourceManager | TCP | 443 |
    | Storage.region       | TCP | 443 |
    | AzureFrontDoor.FrontEnd</br>* 在 Azure 中国中不需要。 | TCP | 443 | 
    | ContainerRegistry.region  | TCP | 443 |
    | MicrosoftContainerRegistry.region | TCP | 443 |

    > [!TIP]
    > * 对于自定义 Docker 映像，仅需要 ContainerRegistry.region。 这包括对 Microsoft 提供的基本映像进行的小幅度修改（例如附加包）。
    > * 仅在计划使用 Microsoft 提供的默认 Docker 映像并启用用户管理的依赖项时，才需要 MicrosoftContainerRegistry.region 。
    > * 对于包含 `region` 的条目，请替换为所使用的 Azure 区域。 例如 `ContainerRegistry.westus`。

1. 为以下主机添加应用程序规则：

    > [!NOTE]
    > 这不是 Internet 上所有 Python 资源所需的完整主机列表，这只是最常使用的主机列表。 例如，如果需要访问 GitHub 存储库或其他主机，则必须标识并添加该方案所需的主机。

    | **主机名** | **用途** |
    | ---- | ---- |
    | **graph.windows.net** | 由 Azure 机器学习计算实例/群集使用。 |
    | **anaconda.com**</br>**\*.anaconda.com** | 用于安装默认包。 |
    | \*.anaconda.org | 用于获取存储库数据。 |
    | **pypi.org** | 用于列出默认索引的依赖项（如果有），索引不会被用户设置覆盖。 如果索引被覆盖，则还必须允许“\*.pythonhosted.org”。 |
    | **cloud.r-project.org** | 在安装用于 R 开发的 CRAN 包时使用。 |
    | **\*pytorch.org** | 由基于 PyTorch 的一些示例使用。 |
    | **\*.tensorflow.org** | 由基于 Tensorflow 的一些示例使用。 |
    | **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | 用于检索通过安装脚本安装在计算实例上的 VS Code 服务器位。|
    | **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** | 用于检索安装在计算实例上的 websocket 服务器位。 websocket 服务器用于将来自 Visual Studio Code 客户端（桌面应用程序）的请求传输到计算实例上运行的 Visual Studio Code 服务器。|
    

    对于“协议:端口”，请选择“http, https” 。

    有关配置应用程序规则的详细信息，请参阅[部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)。

1. 若要限制部署到 Azure Kubernetes 服务 (AKS) 的模型的出站流量，请参阅[限制 Azure Kubernetes 服务中的出口流量](../aks/limit-egress-traffic.md)和[将 ML 模型部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md#connectivity)文章。

### <a name="diagnostics-for-support"></a>针对支持的诊断

如果在使用 Microsoft 支持时需要收集诊断信息，请执行以下步骤：

1. 添加网络规则，以允许传入和传出 `AzureMonitor` 标记的流量。
1. 为以下主机添加应用程序规则。 为以下主机的“协议: 端口”选择“http, https” ：

    + **dc.applicationinsights.azure.com**
    + **dc.applicationinsights.microsoft.com**
    + **dc.services.visualstudio.com**

    有关 Azure Monitor 主机的 IP 地址列表，请参阅 [Azure Monitor 使用的 IP 地址](../azure-monitor/app/ip-addresses.md)。
## <a name="other-firewalls"></a>其他防火墙

本部分中的指南是通用的，因为每个防火墙都有自己的术语和特定配置。 如果有任何疑问，请查看所用防火墙的文档。

如果未正确配置，则防火墙可能会在使用工作区时导致问题。 Azure 机器学习工作区使用各种主机名。 以下部分列出了 Azure 机器学习所需的主机。

### <a name="microsoft-hosts"></a>Microsoft 主机

下表中的主机归 Microsoft 所有，它们提供工作区正常工作所需的服务。 该表列出了 Azure 公共、Azure 政府和 Azure 中国世纪互联区域的主机。

**通用 Azure 主机**

| **要求** | **Azure 公共** | **Azure Government** | **Azure 中国世纪互联** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure 门户 | management.azure.com | management.azure.us | management.azure.cn |
| Azure 资源管理器 | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure 机器学习主机**

> [!IMPORTANT]
> 在下表中，将 `<storage>` 替换为你的 Azure 机器学习工作区的默认存储帐户的名称。

| **要求** | **Azure 公共** | **Azure Government** | **Azure 中国世纪互联** |
| ----- | ----- | ----- | ----- |
| Azure 机器学习工作室 | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| 集成笔记本 | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| 集成笔记本 | \<storage\>.file.core.windows.net | \<storage\>.file.core.usgovcloudapi.net | \<storage\>.file.core.chinacloudapi.cn |
| 集成笔记本 | \<storage\>.dfs.core.windows.net | \<storage\>.dfs.core.usgovcloudapi.net | \<storage\>.dfs.core.chinacloudapi.cn |
| 集成笔记本 | \<storage\>.blob.core.windows.net | \<storage\>.blob.core.usgovcloudapi.net | \<storage\>.blob.core.chinacloudapi.cn |
| 集成笔记本 | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| 集成笔记本 | \*.aznbcontent.net |  | |

**Azure 机器学习计算实例和计算群集主机**

| **要求** | **Azure 公共** | **Azure Government** | **Azure 中国世纪互联** |
| ----- | ----- | ----- | ----- |
| 计算群集/实例 | \*.batchai.core.windows.net | \*.batchai.core.usgovcloudapi.net |\*.batchai.ml.azure.cn |
| 计算群集/实例 | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| 计算实例 | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| 计算实例 | \*.instances.azureml.ms |  |  |

> [!IMPORTANT]
> 你的防火墙必须允许通过 TCP 端口 18881、443 和 8787 与 .instances.azureml.ms 通信。\* 

**Azure 机器学习使用的关联资源**

| **要求** | **Azure 公共** | **Azure Government** | **Azure 中国世纪互联** |
| ----- | ----- | ----- | ----- |
| Azure 存储帐户 | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure 容器注册表 | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft 容器注册表 | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |
| Azure 机器学习预生成映像 | viennaglobal.azurecr.io | viennaglobal.azurecr.io | viennaglobal.azurecr.io |

> [!TIP]
> * 所有自定义 Docker 映像都需要 Azure 容器注册表。 这包括对 Microsoft 提供的基本映像进行的小幅度修改（例如附加包）。
> * 仅在计划使用 Microsoft 提供的默认 Docker 映像并启用用户管理的依赖项时，才需要 Microsoft Container Registry 。
> * 如果计划使用联合标识，请按照[保护 Active Directory 联合身份验证服务的最佳做法](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)一文的说明进行操作。

此外，使用[入站配置](#inbound-configuration)部分中的信息为 `BatchNodeManagement` 和 `AzureMachineLearning` 添加 IP 地址。

如需了解限制访问部署到 AKS 的模型的相关信息，请参阅[限制 Azure Kubernetes Service 中的出口流量](../aks/limit-egress-traffic.md)。

> [!TIP]
> 如果你正在与 Microsoft 支持部门合作收集诊断信息，则必须允许到 Azure Monitor 主机使用的 IP 地址的出站流量。 有关 Azure Monitor 主机的 IP 地址列表，请参阅 [Azure Monitor 使用的 IP 地址](../azure-monitor/app/ip-addresses.md)。

### <a name="python-hosts"></a>Python 主机

安装 Python 包，以及开发、培训和部署期间需使用本部分中的主机。 

> [!NOTE]
> 这不是 Internet 上所有 Python 资源所需的完整主机列表，这只是最常使用的主机列表。 例如，如果需要访问 GitHub 存储库或其他主机，则必须标识并添加该方案所需的主机。

| **主机名** | **用途** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | 用于安装默认包。 |
| \*.anaconda.org | 用于获取存储库数据。 |
| **pypi.org** | 用于列出默认索引的依赖项（如果有），索引不会被用户设置覆盖。 如果索引被覆盖，则还必须允许“\*.pythonhosted.org”。 |
| **\*pytorch.org** | 由基于 PyTorch 的一些示例使用。 |
| **\*.tensorflow.org** | 由基于 Tensorflow 的一些示例使用。 |

### <a name="r-hosts"></a>R 主机

安装 R 包，以及开发、培训和部署期间需使用本部分中的主机。

> [!NOTE]
> 这不是 Internet 上所有 R 资源所需的完整主机列表，这只是最常使用的主机列表。 例如，如果需要访问 GitHub 存储库或其他主机，则必须标识并添加该方案所需的主机。

| **主机名** | **用途** |
| ---- | ---- |
| **cloud.r-project.org** | 在安装 CRAN 包时使用。 |

### <a name="azure-kubernetes-services-hosts"></a>Azure Kubernetes 服务主机

有关 AKS 需要与之通信的主机的信息，请参阅[限制 Azure Kubernetes 服务中的出口流量](../aks/limit-egress-traffic.md)和[将 ML 模型部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md#connectivity)文章。

### <a name="visual-studio-code-hosts"></a>Visual Studio Code 主机

本部分中的主机用于安装 Visual Studio Code 包，以在 Visual Studio Code 和 Azure 机器学习工作区中的计算实例之间建立远程连接。

> [!NOTE]
> 这不是 Internet 上所有 Visual Studio Code 资源所需的完整主机列表，这只是最常使用的主机列表。 例如，如果需要访问 GitHub 存储库或其他主机，则必须标识并添加该方案所需的主机。

| **主机名** | **用途** |
| ---- | ---- |
|  **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | 用于检索通过安装脚本安装在计算实例上的 VS Code 服务器位。|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** |用于检索安装在计算实例上的 websocket 服务器位。 websocket 服务器用于将来自 Visual Studio Code 客户端（桌面应用程序）的请求传输到计算实例上运行的 Visual Studio Code 服务器。 |

## <a name="next-steps"></a>后续步骤

本文是介绍如何保护 Azure 机器学习工作流的系列文章的一部分。 请参阅本系列中的其他文章：

* [虚拟网络概述](how-to-network-security-overview.md)
* [保护工作区资源](how-to-secure-workspace-vnet.md)
* [保护训练环境](how-to-secure-training-vnet.md)
* [保护推理环境](how-to-secure-inferencing-vnet.md)
* [启用工作室功能](how-to-enable-studio-virtual-network.md)
* [使用自定义 DNS](how-to-custom-dns.md)

有关配置 Azure 防火墙的详细信息，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)。
