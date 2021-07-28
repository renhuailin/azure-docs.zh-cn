---
title: 使用防火墙
titleSuffix: Azure Machine Learning
description: 使用 Azure 防火墙控制对 Azure 机器学习工作区的访问。 了解必须允许通过防火墙的主机。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/03/2021
ms.custom: devx-track-python
ms.openlocfilehash: ff6e4f0a3c2b79f63376a480986f15014d20f9ae
ms.sourcegitcommit: 89c889a9bdc2e72b6d26ef38ac28f7a6c5e40d27
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111565349"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>将防火墙后的工作区用于 Azure 机器学习

本文介绍如何配置 Azure 防火墙以控制对 Azure 机器学习工作区和公共 Internet 的访问。 若要详细了解如何保护 Azure 机器学习，请参阅 [Azure 机器学习的企业安全性](concept-enterprise-security.md)。

## <a name="azure-firewall"></a>Azure 防火墙

> [!IMPORTANT]
> Azure 防火墙是一项 Azure 服务，可为 Azure 虚拟网络资源提供安全性。 其他一些 Azure 服务（如 Azure 存储帐户）有自己的防火墙设置，适用于该特定服务实例的公共终结点。 本文档中的信息针对 Azure 防火墙。
> 
> 有关服务实例防火墙设置的信息，请参阅 [在虚拟网络中使用工作室](how-to-enable-studio-virtual-network.md#firewall-settings)。

使用 Azure 防火墙时，请使用目标网络地址转换 (DNAT) 为入站流量创建 NAT 规则。 对于出站流量，请创建“网络”和/或“应用程序”规则 。 [一些 Azure 防火墙概念](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)中更详细地介绍了这些规则集合。

### <a name="inbound-configuration"></a>入站配置

如果使用“计算实例”或“计算群集”Azure 机器学习，请为包含 Azure 机器学习资源的子网添加[用户定义的路由 (UDR)](../virtual-network/virtual-networks-udr-overview.md) 。 此路由将流量从 `BatchNodeManagement` 和 `AzureMachineLearning` 资源的 IP 地址强制发送到计算实例和计算群集的公共 IP。

借助这些 UDR，Batch 服务可以与计算节点进行通信，以便进行任务计划编制。 还要添加 Azure 机器学习服务的 IP 地址，因为这是访问计算实例所必需的。 添加 Azure 机器学习服务的 IP 时，必须同时添加主要和次要 Azure 区域的 IP。 主要区域是工作区所在的区域。

若要查找次要区域，请参阅[使用 Azure 配对区域确保业务连续性和灾难恢复](../best-practices-availability-paired-regions.md#azure-regional-pairs)。 例如，如果 Azure 机器学习服务位于“美国东部 2”，则次要区域是“美国中部”。 

若要获取 Batch 服务和 Azure 机器学习服务的 IP 地址列表，请使用以下方法之一：

* 下载 [Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=56519)，并在文件中搜索 `BatchNodeManagement.<region>` 和 `AzureMachineLearning.<region>`（其中 `<region>` 是你的 Azure 区域）。

* 使用 [Azure CLI](/cli/azure/install-azure-cli) 下载信息。 下面的示例下载 IP 地址信息，并筛选出“美国东部 2”区域（主要）和“美国中部”区域（次要）的信息：

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > 如果你使用的是美国-弗吉尼亚、美国-亚利桑那区域或中国东部 2 区域，则这些命令不会返回任何 IP 地址。 而如果使用以下链接之一下载 IP 地址列表：
    >
    > * [适用于 Azure 政府的 Azure IP 范围和服务标记](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [适用于 Azure 中国的 Azure IP 范围和服务标记](https://www.microsoft.com//download/details.aspx?id=57062)

添加 UDR 时，请为每个相关的 Batch IP 地址前缀定义路由，并将“下一跃点类型”设置为“Internet”。  下图显示了 Azure 门户中此 UDR 的示例：

![地址前缀的 UDR 示例](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> IP 地址可能会随时间推移而改变。

有关详细信息，请参阅[在虚拟网络中创建 Azure Batch 池](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)。

### <a name="outbound-configuration"></a>出站配置

1. 添加网络规则，允许来自以下服务标记中的流量流向 和 ：  

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Storage.region
    * KeyVault.region
    * ContainerRegistry.region

    如果计划使用 Microsoft 提供的默认 Docker 映像并启用用户管理的依赖项，则还必须添加以下服务标记：

    * MicrosoftContainerRegistry.region
    * AzureFrontDoor.FirstParty

    对于包含 `region` 的条目，请替换为所使用的 Azure 区域。 例如，`keyvault.westus`。

    对于“协议”，请选择“`TCP`”。 对于源和目标的“端口”，请选择“`*`”。

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

    对于“协议:端口”，请选择“http, https” 。

    有关配置应用程序规则的详细信息，请参阅[部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)。

1. 若要限制对部署到 Azure Kubernetes Service (AKS) 的模型的访问，请参阅[限制 Azure Kubernetes Service 中的出口流量](../aks/limit-egress-traffic.md)。

### <a name="diagnostics-for-support"></a>针对支持的诊断

如果在使用 Microsoft 支持时需要收集诊断信息，请执行以下步骤：

1. 添加网络规则，以允许传入和传出 `AzureMonitor` 标记的流量。
1. 为以下主机添加应用程序规则。 为以下主机的“协议: 端口”选择“http, https” ：

    + **dc.applicationinsights.azure.com**
    + **dc.applicationinsights.microsoft.com**
    + **dc.services.visualstudio.com**

    有关 Azure Monitor 主机的 IP 地址列表，请参阅 [Azure Monitor 使用的 IP 地址](../azure-monitor/app/ip-addresses.md)。
## <a name="other-firewalls"></a>其他防火墙

本部分中的指南是通用的，因为每个防火墙都有自己的术语和特定配置。 如果你对如何允许通过防火墙进行通信有疑问，请查阅你正在使用的防火墙的相关文档。

如果未正确配置，则防火墙可能会在使用工作区时导致问题。 Azure 机器学习工作区使用各种主机名。 以下部分列出了 Azure 机器学习所需的主机。

### <a name="microsoft-hosts"></a>Microsoft 主机

本部分中的主机归 Microsoft 所有，它们提供工作区正常工作所需的服务。 下表列出了 Azure 公共、Azure 政府和 Azure 中国世纪互联区域的主机名。

**通用 Azure 主机**

| **要求** | **Azure 公共** | **Azure Government** | **Azure 中国世纪互联** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure 门户 | management.azure.com | management.azure.us | management.azure.cn |
| Azure 资源管理器 | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure 机器学习主机**

| **要求** | **Azure 公共** | **Azure Government** | **Azure 中国世纪互联** |
| ----- | ----- | ----- | ----- |
| Azure 机器学习工作室 | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| 集成笔记本 | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| 集成笔记本 | \*.file.core.windows.net | \*.file.core.usgovcloudapi.net | \*.file.core.chinacloudapi.cn |
| 集成笔记本 | \*.dfs.core.windows.net | \*.dfs.core.usgovcloudapi.net | \*.dfs.core.chinacloudapi.cn |
| 集成笔记本 | \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | \*.blob.core.chinacloudapi.cn |
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
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure 容器注册表 | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft 容器注册表 | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> 如果计划使用联合标识，请按照[保护 Active Directory 联合身份验证服务的最佳做法](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs)一文的说明进行操作。

另外，请使用[强制隧道](how-to-secure-training-vnet.md#forced-tunneling)中的信息添加 `BatchNodeManagement` 和 `AzureMachineLearning` 的 IP 地址。

如需了解限制对部署到 Azure Kubernetes Service (AKS) 的模型的访问，请参阅[限制 Azure Kubernetes Service 中的出口流量](../aks/limit-egress-traffic.md)。

> [!TIP]
> 如果你正在与 Microsoft 支持部门合作收集诊断信息，则必须允许到 Azure Monitor 主机使用的 IP 地址的出站流量。 有关 Azure Monitor 主机的 IP 地址列表，请参阅 [Azure Monitor 使用的 IP 地址](../azure-monitor/app/ip-addresses.md)。
### <a name="python-hosts"></a>Python 主机

本部分中的主机用于安装 Python 包。 开发、训练和部署过程中需要使用它们。 

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

本部分中的主机用于安装 R 包。 开发、训练和部署过程中需要使用它们。

> [!NOTE]
> 这不是 Internet 上所有 R 资源所需的完整主机列表，这只是最常使用的主机列表。 例如，如果需要访问 GitHub 存储库或其他主机，则必须标识并添加该方案所需的主机。

| **主机名** | **用途** |
| ---- | ---- |
| **cloud.r-project.org** | 在安装 CRAN 包时使用。 |

> [!IMPORTANT]
> 在内部，Azure 机器学习的 R SDK 使用 Python 包。 因此，还必须允许 Python 主机通过防火墙。
## <a name="next-steps"></a>后续步骤

* [教程：使用 Azure 门户部署和配置 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md)
* [保护 Azure 虚拟网络中的 Azure ML 试验和推理作业](how-to-network-security-overview.md)
