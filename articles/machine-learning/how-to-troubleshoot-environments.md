---
title: 对环境映像进行故障排除
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
ms.openlocfilehash: 71061c056b499f79727f70fb855db7a81a65f3bd
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572164"
---
# <a name="troubleshoot-environment-image-builds"></a>对环境映像生成进行故障排除

了解如何排查 Docker 环境映像生成和包安装问题。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 试用[免费版或付费版 Azure 机器学习](https://aka.ms/AMLFree)。
* [Azure 机器学习 SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)。
* [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
* 若要在本地调试，则必须在本地系统上安装一个有效的 Docker。

## <a name="docker-image-build-failures"></a>Docker 映像生成失败
 
对于大多数映像生成失败，你会在映像生成日志中找到根本原因。
从 Azure 机器学习门户中查找 (20 \_ 映像 \_ 生成 \_log.txt) 或从 Azure 容器注册表任务运行日志中查找映像生成日志。
 
通常在本地重现错误更容易。 检查错误种类，并尝试使用以下 `setuptools` 之一：

- 在本地安装 conda 依赖项： `conda install suspicious-dependency==X.Y.Z` 。
- 在本地安装 pip 依赖项： `pip install suspicious-dependency==X.Y.Z` 。
- 尝试具体化整个环境： `conda create -f conda-specification.yml` 。

> [!IMPORTANT]
> 请确保本地计算群集上的平台和解释器与远程计算群集上的相同。 

### <a name="timeout"></a>超时 
 
以下网络问题可能导致超时错误：

- 低 Internet 带宽
- 服务器问题
- 无法用给定的 conda 或 pip 超时设置下载的大型依赖项
 
类似于以下示例的消息将指示问题：
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

如果收到错误消息，请尝试以下可能的解决方案之一：
 
- 对于依赖关系，尝试使用其他源（如镜像、Azure Blob 存储或其他 Python 源）。
- 更新 conda 或 pip。 如果使用的是自定义 Docker 文件，请更新超时设置。
- 某些 pip 版本存在已知问题。 请考虑向环境依赖项添加特定的 pip 版本。

### <a name="package-not-found"></a>找不到包

以下错误最常见的是映像生成失败：

- 找不到 Conda 包：

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- 找不到指定的 pip 包或版本：

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- 错误的嵌套 pip 依赖关系：

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

请检查包是否存在于指定的源。 使用 [pip 搜索](https://pip.pypa.io/en/stable/reference/pip_search/) 来验证 pip 依赖关系：

- `pip search azureml-core`

对于 conda 依赖项，请使用 [conda 搜索](https://docs.conda.io/projects/conda/en/latest/commands/search.html)：

- `conda search conda-forge::numpy`

有关更多选项，请尝试执行以下操作：
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>安装程序说明

请确保指定的平台和 Python 解释器版本存在所需的发行版。

对于 pip 依赖项，请访问以 `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` 查看所需版本是否可用。 若要 https://pypi.org/project/azureml-core/1.11.0/#files 查看示例，请参阅。

对于 conda 依赖项，请查看通道存储库上的包。
对于 Anaconda，Inc. 维护的通道，请检查 [Anaconda 包页](https://repo.anaconda.com/pkgs/)。

### <a name="pip-package-update"></a>Pip 包更新

在安装或更新 pip 包的过程中，解析程序可能需要更新已安装的包以满足新的要求。
由于与 pip 版本或依赖项的安装方式相关的各种原因，卸载可能会失败。
最常见的情况是 conda 安装的依赖项无法由 pip 卸载。
对于这种情况，请考虑使用卸载依赖项 `conda remove mypackage` 。

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>安装程序问题

某些安装程序版本在包解析程序中存在可能会导致生成失败的问题。

如果使用的是自定义基本映像或 Dockerfile，建议使用 conda 版本4.5.4 或更高版本。

安装 pip 依赖项需要 pip 包。 如果未在环境中指定版本，将使用最新版本。
建议使用已知版本的 pip 以避免暂时性问题或最新版本的工具可能导致的重大更改。

如果看到以下消息，请考虑在您的环境中固定 pip 版本：

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Pip 子进程错误：
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

如果依赖关系中存在无法解决的冲突，则 Pip 安装可能会停滞无限循环。 如果在本地工作，请将 pip 版本降级为 < 20.3。 在通过 YAML 文件创建的 conda 环境中，仅当 conda 是最高优先级通道时才会出现此问题。 若要缓解此问题，请在 conda 规范文件中显式指定 pip < 20.3（!=20.3 或 =20.2.4 会固定到其他版本）作为 conda 依赖项。

## <a name="service-side-failures"></a>服务端故障

请参阅以下方案来解决可能的服务端故障。

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>无法从容器注册表中提取映像，或者无法为容器注册表解析地址

可能的问题：
- 容器注册表的路径名称可能无法正确解析。 检查映像名称是否使用双斜杠，以及 Linux 与 Windows 主机上的斜杠方向是否正确。
- 如果虚拟网络后面的容器注册表使用 [不受支持的区域](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)中的专用终结点，请使用服务终结点配置容器注册表 (公共访问) 门户中，然后重试。
- 在虚拟网络后面放置容器注册表后，请运行 [Azure 资源管理器模板](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) ，使工作区可以与容器注册表实例通信。

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>工作区容器注册表出现401错误

使用 [ws.sync_keys ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)重新同步存储密钥。

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>环境继续引发 "正在等待其他 conda 操作完成 ..."条

当映像生成操作正在进行时，conda 会被 SDK 客户端锁定。 如果该进程崩溃或被用户错误取消，conda 将保持锁定状态。 若要解决此问题，请手动删除锁定文件。 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>自定义 Docker 映像不在注册表中

请检查是否使用了[正确的标记](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment)且 `user_managed_dependencies = True`。 `Environment.python.user_managed_dependencies = True` 禁用 conda 并使用用户的已安装包。

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>你将获得以下常见的虚拟网络问题之一

- 检查存储帐户、计算群集和容器注册表是否都位于虚拟网络的同一子网中。
- 如果容器注册表位于虚拟网络后面，则它不能直接用于生成映像。 需要使用计算群集来构建映像。
- 如果出现以下情况，则可能需要将存储置于虚拟网络后面：
    - 使用推断或专用轮。
    - 请参阅 403 (未经授权的) 服务错误。
    - 无法从 Azure 容器注册表获取映像详细信息。

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>尝试访问受保护的网络存储时映像生成失败

- Azure 容器注册表任务不能在虚拟网络后工作。 如果用户在虚拟网络后面有容器注册表，则需要使用计算群集来构建映像。
- 存储应该位于虚拟网络后面，以便从该网络中提取依赖项。

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>当存储启用了网络安全功能时，无法运行试验

如果要使用默认 Docker 映像并启用用户管理的依赖项，请使用 MicrosoftContainerRegistry 和 AzureFrontDoor [服务标记](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) 来允许列表 Azure 容器注册表及其依赖项。

 有关详细信息，请参阅 [启用虚拟网络](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry)。

### <a name="you-need-to-create-an-icm"></a>需要创建 ICM

为元存储创建/分配 ICM 时，请包括 CSS 支持票证，以便我们可以更好地了解问题。

## <a name="next-steps"></a>后续步骤

- [训练机器学习模型来对花卉分类](how-to-train-scikit-learn.md)
- [使用自定义 Docker 映像训练机器学习模型](how-to-train-with-custom-image.md)