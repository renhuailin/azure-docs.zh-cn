---
title: 对环境映像进行故障排除
titleSuffix: Azure Machine Learning
description: 了解如何排查环境映像生成和包安装问题。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 07/27/2021
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: 5b1a0f5890f55e2dc114801a8f4fd058ef4db69b
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660450"
---
# <a name="troubleshoot-environment-image-builds"></a>对环境映像生成进行故障排除

了解如何排查 Docker 环境映像生成和包安装问题。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 试用[免费版或付费版 Azure 机器学习](https://azure.microsoft.com/free/)。
* [Azure 机器学习 SDK](/python/api/overview/azure/ml/install)。
* [Azure CLI](/cli/azure/install-azure-cli)。
* [用于 Azure 机器学习的 CLI 扩展](reference-azure-machine-learning-cli.md)。
* 若要在本地调试，则必须在本地系统上安装一个有效的 Docker。

## <a name="docker-image-build-failures"></a>Docker 映像生成失败
 
对于大多数映像生成失败，你会在映像生成日志中找到根本原因。
可以在 Azure 机器学习门户中找到映像生成日志 (20\_image\_build\_log.txt)，也可以在 Azure 容器注册表任务运行日志中找到该日志。
 
通常可以更轻松地在本地重现错误。 检查错误种类，并尝试使用以下 `setuptools` 之一：

- 在本地安装 Conda 依赖项：`conda install suspicious-dependency==X.Y.Z`。
- 在本地安装 pip 依赖项：`pip install suspicious-dependency==X.Y.Z`。
- 尝试具体化整个环境：`conda create -f conda-specification.yml`。

> [!IMPORTANT]
> 请确保本地计算群集上的平台和解释器与远程计算群集上的平台和解释器匹配。 

### <a name="timeout"></a>超时 
 
以下网络问题可能会导致超时错误：

- 低 Internet 带宽
- 服务器问题
- 无法使用给定的 conda 或 pip 超时设置下载大型依赖项
 
类似于以下示例的消息会指示此问题：
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

如果收到错误消息，请尝试以下可能的解决方案之一：
 
- 尝试不同的依赖项源，例如镜像、Azure Blob 存储或其他 Python 源。
- 更新 conda 或 pip。 如果使用了自定义 Docker 文件，请更新超时设置。
- 某些 pip 版本存在已知问题。 考虑将特定版本的 pip 添加到环境依赖项中。

### <a name="package-not-found"></a>找不到包

下面是映像生成失败最常见的错误：

- 找不到 conda 包：

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- 找不到指定的 pip 包或版本：

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- 错误的嵌套 pip 依赖项：

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

请检查包是否存在于指定的源上。 使用 [pip search](https://pip.pypa.io/en/stable/reference/pip_search/) 验证 pip 依赖项：

- `pip search azureml-core`

对于 conda 依赖项，请使用 [conda search](https://docs.conda.io/projects/conda/en/latest/commands/search.html)：

- `conda search conda-forge::numpy`

若要了解更多选项，请尝试以下命令：
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>安装程序说明

请确保指定的平台和 Python 解释器版本存在所需的发行版。

对于 pip 依赖项，请转到 `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` 来查看所需版本是否可用。 请转到 https://pypi.org/project/azureml-core/1.11.0/#files 来查看示例。

对于 conda 依赖项，请在通道存储库中检查包。
对于由 Anaconda, Inc. 维护的通道，请查看[“Anaconda 包”页面](https://repo.anaconda.com/pkgs/)。

### <a name="pip-package-update"></a>Pip 包更新

在安装或更新 pip 包的过程中，冲突解决程序可能需要更新已安装的包以满足新的要求。
由于与 pip 版本相关的或与依赖项安装方式相关的各种原因，卸载可能会失败。
最常见的情况是通过 conda 安装的依赖项无法通过 pip 卸载。
对于这种情况，请考虑使用 `conda remove mypackage` 卸载依赖项。

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>安装程序问题

某些安装程序版本在包解析程序中存在可能会导致生成失败的问题。

如果使用自定义基础映像或 Dockerfile，建议使用 conda 4.5.4 或更高版本。

安装 pip 依赖项需要使用某个 pip 包。 如果未在环境中指定版本，则会使用最新版本。
建议使用已知版本的 pip 以避免暂时性问题或最新版工具可能会导致的中断性变更。

如果看到以下消息，请考虑在你的环境中固定 pip 版本：

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Pip 子进程错误：
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

如果依赖项中存在无法解决的冲突，pip 安装可能会陷入无限循环。 如果在本地使用，请将 pip 版本降级到 20.3 以下。 在通过 YAML 文件创建的 conda 环境中，只有在 conda-forge 是最高优先级通道的情况下才出现此问题。 若要缓解此问题，请在 conda 规范文件中显式指定 pip < 20.3（!=20.3 或 =20.2.4 会固定到其他版本）作为 conda 依赖项。

### <a name="modulenotfounderror-no-module-named-distutilsdir_util"></a>ModuleNotFoundError：没有名为“distutils.dir_util”的模块

设置环境时，有时会遇到“ModuleNotFoundError: 没有名为 "distutils.dir_util" 的模块”问题。 若要解决此问题，请运行以下命令：

```bash
apt-get install -y --no-install-recommends python3 python3-distutils && \
ln -sf /usr/bin/python3 /usr/bin/python
```

使用 Dockerfile 时，请将其作为 RUN 命令的一部分运行。

```dockerfile
RUN apt-get update && \
  apt-get install -y --no-install-recommends python3 python3-distutils && \
  ln -sf /usr/bin/python3 /usr/bin/python
```

运行此命令会安装正确的模块依赖项来配置环境。 

### <a name="build-failure-when-using-spark-packages"></a>使用 Spark 包时生成失败

将环境配置为不对包进行预先缓存。 

```python
env.spark.precache_packages = False
```

## <a name="service-side-failures"></a>服务端失败

请参阅以下方案来解决可能的服务端失败。

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>你无法从容器注册表中拉取映像，或者无法为容器注册表解析地址

可能的问题：
- 容器注册表的路径名称可能无法正确解析。 请检查映像名称是否使用了双斜杠，以及 Linux 与 Windows 主机上的斜杠方向是否正确。
- 如果虚拟网络后面的容器注册表在[不受支持的区域](../private-link/private-link-overview.md#availability)中使用专用终结点，请在门户中使用服务终结点（公共访问）配置容器注册表，然后重试。
- 将容器注册表置于虚拟网络后面之后，请运行 [Azure 资源管理器模板](./how-to-network-security-overview.md)，使工作区可以与容器注册表实例通信。

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>你从工作区容器注册表收到 401 错误

使用 [ws.sync_keys()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--) 重新同步存储密钥。

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>环境持续引发“正在等待其他 conda 操作完成...”错误

当映像生成操作正在进行时，conda 会被 SDK 客户端锁定。 如果进程崩溃或被用户错误地取消，conda 会保持锁定状态。 若要解决此问题，请手动删除锁定文件。 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>自定义 Docker 映像不在注册表中

请检查是否使用了[正确的标记](./how-to-use-environments.md#create-an-environment)且 `user_managed_dependencies = True`。 `Environment.python.user_managed_dependencies = True` 禁用 Conda 并使用用户的已安装包。

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>你遇到以下常见虚拟网络问题之一

- 检查存储帐户、计算群集和容器注册表是否都位于虚拟网络的同一子网中。
- 如果容器注册表位于虚拟网络后面，则它不能直接用来生成映像。 你需要使用计算群集来生成映像。
- 如果以下情况属实，则你可能需要将存储置于虚拟网络后面：
    - 使用推理或专用 wheel。
    - 请参阅 403（未经授权）服务错误。
    - 无法从 Azure 容器注册表获取映像详细信息。

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>尝试访问受网络保护的存储时映像生成失败

- Azure 容器注册表任务在虚拟网络后面不能执行。 如果用户在虚拟网络后面有容器注册表，则需要使用计算群集来生成映像。
- 为了能够从存储中拉取依赖项，应当将存储置于虚拟网络后面。

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>当存储启用了网络安全时，无法运行试验

如果你使用默认 Docker 映像并启用了用户管理的依赖项，请使用 MicrosoftContainerRegistry 和 AzureFrontDoor.FirstParty [服务标记](./how-to-network-security-overview.md)将 Azure 容器注册表及其依赖项加入允许列表。

 有关详细信息，请参阅[启用虚拟网络](./how-to-network-security-overview.md)。


## <a name="next-steps"></a>后续步骤

- [训练机器学习模型来对花卉分类](how-to-train-scikit-learn.md)
- [使用自定义 Docker 映像训练机器学习模型](how-to-train-with-custom-image.md)
