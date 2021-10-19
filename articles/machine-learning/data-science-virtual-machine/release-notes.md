---
title: Data Science Virtual Machine 的新增功能
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine 的发行说明
author: michalmar
ms.service: data-science-vm
ms.author: mimarusa
ms.date: 10/11/2021
ms.topic: reference
ms.openlocfilehash: c20d7e93bdf507db7c6ac43634c06c20c79b1786
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129811194"
---
# <a name="azure-data-science-virtual-machine-release-notes"></a>Azure Data Science Virtual Machine 发行说明

本文介绍 Azure Data Science Virtual Machine 的发布。 如需获取随附工具的完整列表以及版本号，请查看[此页面](./tools-included.md)。

如需了解已知 bug 和解决方法，请参阅[已知问题列表](reference-known-issues.md)。

## <a name="2021-10-07"></a>2021-10-07

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) 的新映像。

版本：21.10.07

主要更改：
 - 将 pytorch 更改为版本 1.9.1
 - 将 Docker 更改为版本 20.10.9
 - 将 Intellijidea 更改为版本 2021.2.2
 - 将 Nodejs 更改为版本 v14.18.0
 - 将 Pycharm 更改为版本 2021.2.2
 - 将 VS Code 更改为版本 1.60.2
 - 修复了 AutoML 环境 (azureml_py36_automl)
 - 修复了 Azure 存储资源管理器稳定性
 - 改进了稳定性和次要 bug 修补程序 


## <a name="2021-08-11"></a>2021-08-11

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) 的新映像。

版本：21.08.11

主要更改：

- Windows 安全更新
- 将 Nvidia CuDNN 更新到 8.1.0
- 将 Jupyter Lab 更新到 3.0.16
- 添加了用于试验跟踪的 MLFLow
- 改进了稳定性和次要 bug 修补程序 



## <a name="2021-07-12"></a>2021-07-12

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) 的新映像。

主要更改：

- 已更新到 PyTorch 1.9.0
- 已将 Azure CLI 更新到 2.26.1
- 已将 Azure CLI Azure 机器学习扩展更新到 1.29.0
- 更新 VS Code 版本 1.58.1
- 改进了稳定性和次要 bug 修补程序 


## <a name="2021-06-22"></a>2021-06-22

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) 的新映像。

版本：21.06.22

主要更改：

- 已更新到 PyTorch 1.9.0
- 修复了 git 不可用的 bug


## <a name="2021-06-01"></a>2021-06-01

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) 的新映像。

版本：21.06.01

主要更改如下：

- 默认情况下启用 Docker。
- 默认情况下，JupyterHub 使用 JupyterLab
- 更新了 Python 版本以修复 [CVE-2020-15523](https://nvd.nist.gov/vuln/detail/CVE-2020-15523)
- 将 IntelliJ IDEA 更新到版本 2021.1 以修复 [CVE-2021-25758](https://nvd.nist.gov/vuln/detail/CVE-2021-25758)
- 将 PyCharm 社区更新为 2021.1
- 将 TensorFlow 更新为版本 2.5.0

<br/>
从桌面中删除了几个图标。

## <a name="2021-05-22"></a>2021-05-22

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) 的新映像。

版本：21.05.22

所选版本更新为：
- CUDA 11.1
- Python 3.8
- PyTorch 1.8.1
- TensorFlow 2.5.0
- Spark 3.1.1
- Java 11
- R 4.1.0
- Julia 1.0.5
- NodeJS 16.2.0
- Visual Studio Code 1.56.2，包括 Azure ML 扩展
- PyCharm 社区版 2021.1.1
- Jupyter Lab 2.2.6
- RStudio 1.4.1106
- Visual Community Edition 2019（版本 16.9.6）
- Azure CLI 2.23.0
- 存储资源管理器 1.19.1
- AzCopy 10.10.0
- Power BI Desktop 2.93.641.0 64 位（2021 年 5 月）
- Azure Data Studio 1.28.0
- Microsoft Edge 浏览器

<br/>
删除了 Firefox、Apache Drill 和 Microsoft Integration Runtime。

<br/>
深色模式、桌面上图标的更改、墙纸背景更改。

## <a name="2021-05-12"></a>2021-05-12

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) 的新映像。

所选版本更新为：
- CUDA 11.3、cuDNN 8、NCCL2
- Python 3.8
- R 4.0.5
- Spark 3.1，包括 mmlspark、指向 Blob 存储、数据湖和 CosmosDB 的连接器
- Java 11 (OpenJDK)
- Jupyter Lab 3.0.14
- PyTorch 1.8.1 包括 torchaudio torchtext torchvision 和 torch-tb-profiler
- TensorFlow 2.4.1，包括 TensorBoard
- dask 2021.01.0
- VS.Code 1.56
- Azure Data Studio 1.22.1
- Azure CLI 2.23.0
- Azure 存储资源管理器 1.19.1
- azcopy 10.10
- Microsoft Edge 浏览器（beta 版本）

<br/>
添加了 docker。 为节省资源，默认情况下不会启动 docker 服务。 要启动 docker 服务，请运行以下命令行命令：

```
sudo systemctl start docker
```

> [!NOTE]
> 如果计算机具有 GPU，则可以通过向 docker 命令中添加 `--gpus` 参数，而在容器内使用 GPU。
>
> 例如，运行 
>
> `sudo docker run --gpus all -it --rm -v local_dir:container_dir nvcr.io/nvidia/pytorch:18.04-py3`
>
> 将运行预先安装了 PyTorch 且已启用所有 GPU 的 Ubuntu 18.04 容器。 还将在容器中的 container_dir 目录下提供本地文件夹 local_dir 以供使用。 
>


## <a name="2020-02-24"></a>2020-02-24

现在提供 [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) 和 [Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) 映像的 Data Science Virtual Machine 映像。

## <a name="2019-10-08"></a>2019-10-08

### <a name="updates-to-software-on-the-windows-dsvm"></a>Windows DSVM 上的软件更新

- Azure 存储资源管理器 1.10.1
- Power BI Desktop 2.73.55xx
- Firefox 69.0.2
- PyCharm 19.2.3
- RStudio 1.2.50xx

### <a name="default-browser-for-windows-updated"></a>Windows 的默认浏览器已更新

默认浏览器在之前设置为 Internet Explorer。 用户在初次登录时会提示选择默认浏览器。
