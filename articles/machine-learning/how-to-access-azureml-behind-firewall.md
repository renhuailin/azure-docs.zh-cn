---
title: 配置入站和出站网络流量
titleSuffix: Azure Machine Learning
description: 如何在使用安全的 Azure 机器学习工作区时配置所需的入站和出站网络流量。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/14/2021
ms.custom: devx-track-python
ms.openlocfilehash: 50c8a38a5acbfea119770a5ea81a21d51fd4ea83
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424533"
---
# <a name="configure-inbound-and-outbound-network-traffic"></a>配置入站和出站网络流量

本文介绍在虚拟网络 (VNet) 中保护 Azure 机器学习工作区时的网络通信要求。 其中包括如何配置 Azure 防火墙以控制对 Azure 机器学习工作区和公共 Internet 的访问。 若要详细了解如何保护 Azure 机器学习，请参阅 [Azure 机器学习的企业安全性](concept-enterprise-security.md)。

> [!NOTE]
> 本文中的信息适用于配置了专用终结点的 Azure 机器学习工作区。

> [!TIP]
> 本文是介绍如何保护 Azure 机器学习工作流系列文章的一部分。 请参阅本系列中的其他文章：
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
    | Keyvault.region | TCP | 443 |

    > [!TIP]
    > * 对于自定义 Docker 映像，仅需要 ContainerRegistry.region。 这包括对 Microsoft 提供的基本映像进行的小幅度修改（例如附加包）。
    > * 仅在计划使用 Microsoft 提供的默认 Docker 映像并启用用户管理的依赖项时，才需要 MicrosoftContainerRegistry.region 。
    > * 仅当工作区是在启用 [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 标志的情况下创建时，才需要 Keyvault.region。
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
    | **dc.applicationinsights.azure.com** | 用于在与 Microsoft 支持人员合作时收集指标和诊断信息。 |
    | **dc.applicationinsights.microsoft.com** | 用于在与 Microsoft 支持人员合作时收集指标和诊断信息。 |
    | **dc.services.visualstudio.com** | 用于在与 Microsoft 支持人员合作时收集指标和诊断信息。 | 
    

    对于“协议:端口”，请选择“http, https” 。

    有关配置应用程序规则的详细信息，请参阅[部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)。

1. 若要限制部署到 Azure Kubernetes 服务 (AKS) 的模型的出站流量，请参阅[限制 Azure Kubernetes 服务中的出口流量](../aks/limit-egress-traffic.md)和[将 ML 模型部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md#connectivity)文章。

### <a name="azure-kubernetes-services"></a>Azure Kubernetes 服务

将 Azure Kubernetes 服务与 Azure 机器学习配合使用时，必须允许以下流量：

* AKS 的常规入站/出站要求，详见[限制 Azure Kubernetes 服务中的出口流量](../aks/limit-egress-traffic.md)一文。
* 发往 mcr.microsoft.com 的出站流量。
* 将模型部署到 AKS 群集时，请遵循[将 ML 模型部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md#connectivity)一文中的指导。

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
| 计算群集/实例 | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| 计算实例 | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| 计算实例 | \*.instances.azureml.ms |  |  |
| Azure 存储帐户 | \*.blob.core.windows.net</br>\*.table.core.windows.net</br>\*.queue.core.windows.net | \*.blob.core.usgovcloudapi.net</br>\*.table.core.usgovcloudapi.net</br>\*.queue.core.usgovcloudapi.net | \*blob.core.chinacloudapi.cn</br>\*.table.core.chinacloudapi.cn</br>\*.queue.core.chinacloudapi.cn |
| Azure Key Vault | \*.vault.azure.net | \*.vault.usgovcloudapi.net | \*.vault.azure.cn |

> [!IMPORTANT]
> 你的防火墙必须允许通过 TCP 端口 18881、443 和 8787 与 .instances.azureml.ms 通信。\* 

> [!TIP]
> 仅当工作区是在启用 [hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) 标志的情况下创建时，才需要 Azure Key Vault 的 FQDN。

**通过 Azure 机器学习维护的 Docker 映像**

| **要求** | **Azure 公共** | **Azure Government** | **Azure 中国世纪互联** |
| ----- | ----- | ----- | ----- |
| Microsoft 容器注册表 | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |
| Azure 机器学习预生成映像 | viennaglobal.azurecr.io | viennaglobal.azurecr.io | viennaglobal.azurecr.io |

> [!TIP]
> * 所有自定义 Docker 映像都需要 Azure 容器注册表。 这包括对 Microsoft 提供的基本映像进行的小幅度修改（例如附加包）。
> * 仅在计划使用 Microsoft 提供的默认 Docker 映像并启用用户管理的依赖项时，才需要 Microsoft Container Registry 。
> * 如果计划使用联合标识，请按照[保护 Active Directory 联合身份验证服务的最佳做法](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)一文的说明进行操作。

此外，使用[入站配置](#inbound-configuration)部分中的信息为 `BatchNodeManagement` 和 `AzureMachineLearning` 添加 IP 地址。

如需了解限制访问部署到 AKS 的模型的相关信息，请参阅[限制 Azure Kubernetes Service 中的出口流量](../aks/limit-egress-traffic.md)。

**支持诊断**

为了让 Microsoft 支持部门能够诊断你在工作区中遇到的任何问题，你必须允许到以下主机的出站流量：

* **dc.applicationinsights.azure.com**
* **dc.applicationinsights.microsoft.com**
* **dc.services.visualstudio.com**

有关这些主机的 IP 地址列表，请参阅 [Azure Monitor 使用的 IP 地址](../azure-monitor/app/ip-addresses.md)。

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

### <a name="azure-kubernetes-services"></a>Azure Kubernetes 服务

将 Azure Kubernetes 服务与 Azure 机器学习配合使用时，必须允许以下流量：

* AKS 的常规入站/出站要求，详见[限制 Azure Kubernetes 服务中的出口流量](../aks/limit-egress-traffic.md)一文。
* 发往 mcr.microsoft.com 的出站流量。
* 将模型部署到 AKS 群集时，请遵循[将 ML 模型部署到 Azure Kubernetes 服务](how-to-deploy-azure-kubernetes-service.md#connectivity)一文中的指导。

### <a name="visual-studio-code-hosts"></a>Visual Studio Code 主机

本部分中的主机用于安装 Visual Studio Code 包，以在 Visual Studio Code 和 Azure 机器学习工作区中的计算实例之间建立远程连接。

> [!NOTE]
> 这不是 Internet 上所有 Visual Studio Code 资源所需的完整主机列表，这只是最常使用的主机列表。 例如，如果需要访问 GitHub 存储库或其他主机，则必须标识并添加该方案所需的主机。

| **主机名** | **用途** |
| ---- | ---- |
|  **update.code.visualstudio.com**</br></br>**\*.vo.msecnd.net** | 用于检索通过安装脚本安装在计算实例上的 VS Code 服务器位。|
| **raw.githubusercontent.com/microsoft/vscode-tools-for-ai/master/azureml_remote_websocket_server/\*** |用于检索安装在计算实例上的 websocket 服务器位。 websocket 服务器用于将来自 Visual Studio Code 客户端（桌面应用程序）的请求传输到计算实例上运行的 Visual Studio Code 服务器。 |

## <a name="next-steps"></a>后续步骤

本文是介绍如何保护 Azure 机器学习工作流系列文章的一部分。 请参阅本系列中的其他文章：

* [虚拟网络概述](how-to-network-security-overview.md)
* [保护工作区资源](how-to-secure-workspace-vnet.md)
* [保护训练环境](how-to-secure-training-vnet.md)
* [保护推理环境](how-to-secure-inferencing-vnet.md)
* [启用工作室功能](how-to-enable-studio-virtual-network.md)
* [使用自定义 DNS](how-to-custom-dns.md)

有关配置 Azure 防火墙的详细信息，请参阅[教程：使用 Azure 门户部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)。
