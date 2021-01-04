---
title: 环境映像疑难解答
titleSuffix: Azure Machine Learning
description: 了解如何排查环境映像生成和包安装问题。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733278"
---
# <a name="troubleshoot-environment-image-builds"></a>排查环境映像生成问题
了解如何排查 Docker 环境映像生成和包安装问题。

## <a name="prerequisites"></a>先决条件

* 一个 **Azure 订阅**。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。
* [Azure 机器学习 SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
* 若要在本地调试，则必须在本地系统上安装一个有效的 Docker。

## <a name="docker-image-build-failures"></a>Docker 映像生成失败
 
对于大多数映像生成故障，可在映像生成日志中找到根本原因。
你可以从 Azure 机器学习门户 (20 \_ 映像 \_ 生成 \_log.txt) 或从 ACR 任务运行日志中找到映像生成日志
 
在大多数情况下，在本地重现错误更为容易。 检查错误类型，并尝试以下 `setuptools` 操作之一：

- 在本地安装 conda 依赖项 `conda install suspicious-dependency==X.Y.Z`
- 在本地安装 pip 依赖项 `pip install suspicious-dependency==X.Y.Z`
- 尝试具体化整个环境 `conda create -f conda-specification.yml`

> [!IMPORTANT]
> 请确保本地计算上的平台和解释器与远程计算机上的平台和解释器匹配。 

### <a name="timeout"></a>超时 
 
对于各种网络问题，可能会出现超时问题：
- 低 internet 带宽
- 服务器问题
- 无法用给定的 conda 或 pip 超时设置下载的大型依赖项
 
类似于下面的消息将指示问题：
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

可能的解决方法：
 
- 如果可用，请为依赖项（如镜像、blob 存储或其他 python 源）尝试不同的源。
- Update conda 或 pip。 如果使用了自定义 docker 文件，则更新超时设置。
- 某些 pip 版本包含已知问题。 考虑将特定版本的 pip 添加到环境依赖项中。

### <a name="package-not-found"></a>找不到包

这是映像生成失败的最常见情况。

- 找不到 Conda 包
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- 找不到指定的 pip 包或版本
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- 错误的嵌套 pip 依赖项
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

检查指定源上是否存在此包。 使用 [pip 搜索](https://pip.pypa.io/en/stable/reference/pip_search/) 来验证 pip 依赖关系。

`pip search azureml-core`

对于 conda 依赖项，请使用 [conda 搜索](https://docs.conda.io/projects/conda/en/latest/commands/search.html)。

`conda search conda-forge::numpy`

有关更多选项：
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>安装程序说明

请确保指定的平台和 Python 解释器版本存在所需的分发。

对于 pip 依赖项，请导航到 `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` 以查看所需版本是否可用。 例如： https://pypi.org/project/azureml-core/1.11.0/#files

对于 conda 依赖项，请查看通道存储库上的包。
对于 Anaconda，Inc. 维护的频道，请查看 [此处](https://repo.anaconda.com/pkgs/)。

### <a name="pip-package-update"></a>Pip 包更新

在安装或更新 pip 包的过程中，冲突解决程序可能需要更新已安装的包以满足新的要求。
由于与 pip 版本或依赖项的安装方式相关的各种原因，卸载可能会失败。
最常见的情况是 conda 安装的依赖项无法由 pip 卸载。
对于这种情况，请考虑使用卸载依赖项 `conda remove mypackage` 。

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>安装程序问题

某些安装程序版本在可能导致生成失败的包解析程序中存在问题。

如果使用的是自定义基本映像或 dockerfile，则建议使用 conda 版本4.5.4 或更高版本。

需要 pip 包才能安装 pip 依赖项，如果环境中未指定版本，将使用最新版本。
建议使用已知版本的 pip 以避免暂时性问题或可能由最新版本的工具导致的重大更改。

如果看到以下任何消息，请考虑在您的环境中固定 pip 版本：

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

此外，如果依赖项中存在无法解决的冲突，则 pip 安装可能会停滞无限循环。 如果在本地工作，请将 pip 版本降级为 < 20.3。 在通过 YAML 文件创建的 conda 环境中，仅当 conda 是最高优先级通道时才会出现此问题。 若要缓解此问题，请在 conda 规范文件中显式指定 pip < 20.3 (！ = 20.3 或 = 20.2.4 固定到其他版本) 作为 conda 依赖项。

## <a name="service-side-failures"></a>服务端故障

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>无法为容器注册表解析 MCR/Address 中的请求映像。
可能的问题：
- 容器注册表的路径名称可能无法正确解析。 检查映像名称是否使用双斜杠，以及 Linux 与 Windows 主机上的斜杠方向是否正确。
- 如果 Vnet 后面的 ACR 正在使用 [不受支持的区域](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)中的专用终结点，请使用服务终结点在 vnet 后面配置 acr， (门户中的公共访问) ，然后重试。
- 在 VNet 后面放置 ACR 后，请确保 [ARM 模板](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) 运行。 这使工作区可以与 ACR 实例通信。

### <a name="401-error-from-workspace-acr"></a>401工作区 ACR 中的错误
使用 ws.sync_keys ( 重新同步存储密钥 [# B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>环境持续引发 "正在等待其他 Conda 操作完成 ..."条
当正在生成映像时，conda 将被 SDK 客户端锁定。 如果进程崩溃或被用户错误取消，conda 将保持锁定状态。 若要解决此问题，请手动删除锁定文件。 

### <a name="custom-docker-image-not-in-registry"></a>不在注册表中的自定义 docker 映像
检查是否使用了 [正确的标记](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) 和 `user_managed_dependencies = True` 。 `Environment.python.user_managed_dependencies = True` 禁用 Conda 并使用用户的已安装包。

### <a name="common-vnet-issues"></a>常见 VNet 问题

1. 检查存储帐户、计算群集和 Azure 容器注册表是否都位于虚拟网络的同一子网中。
2. 当 ACR 位于 VNet 后面时，它不能直接用于生成映像。 计算群集需要用于生成映像。
3. 在以下情况下，可能需要将存储置于 VNet 后面：
    - 使用推断或专用轮
    - 查看 403 (未授权) 服务错误
    - 无法从 ACR/MCR 获取映像详细信息

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>尝试访问受保护的网络存储时映像生成失败
- ACR 任务在 VNet 后面不起作用。 如果用户在 VNet 后面有 ACR，则需要使用计算群集来构建映像。
- 存储应位于 VNet 后面，以便能够从其拉取依赖项。 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>当存储启用网络安全功能时无法运行试验
使用默认 Docker 映像并启用用户管理的依赖项时，必须使用 MicrosoftContainerRegistry 和 AzureFrontDoor [服务标记](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) 来允许列表 MCR 及其依赖项。

 有关详细信息，请参阅 [启用 VNET](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) 。

### <a name="creating-an-icm"></a>创建 ICM

创建/将 ICM 分配到元存储时，请包括 CSS 支持票证，以便我们可以更好地了解问题。

## <a name="next-steps"></a>后续步骤

- [训练机器学习模型来分类花卉](how-to-train-scikit-learn.md)
- [使用自定义 Docker 映像训练机器学习模型](how-to-train-with-custom-image.md)