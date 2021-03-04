---
title: 为包管理创建自定义 Conda 通道
description: 了解如何创建用于包管理的自定义 Conda 通道
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107363"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>为包管理创建自定义 Conda 通道 
安装 Python 包时，Conda 包管理器使用通道查找包。 出于各种原因，可能需要创建自定义 Conda 通道。 例如，你可能会发现：

- 你的工作区是数据渗透保护的，而出站连接被阻止。  
- 你有不想要上传到公共存储库的程序包。
- 需要为工作区中的用户设置 am 备用存储库。

在本文中，我们将提供一个循序渐进的指南，帮助你在 Azure Data Lake Storage 帐户中创建自定义 Conda 通道。

## <a name="set-up-your-local-machine"></a>设置本地计算机

1. 在本地计算机上安装 Conda。可以参考 [Azure Synapse Spark 运行时](./apache-spark-version-support.md) 来识别在同一运行时中使用的 Conda 版本。
   
2. 若要创建自定义通道，请安装 conda。
```
conda install conda-build
```
3. 为要服务的平台组织中的所有包。 在此示例中，我们将在本地计算机上安装 Anaconda 存档。

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>将存储帐户装载到计算机上
接下来，我们将 Azure Data Lake Storage Gen2 帐户装载到本地计算机上。 还可以使用 WASB 帐户完成此过程;但是，我们将通过一个示例来了解 ADLSg2 帐户 
 
有关如何在本地计算机上装载存储帐户的详细信息，可以访问 [此页](https://github.com/Azure/azure-storage-fuse#blobfuse )。 

1. 你可以从 Microsoft 产品的 Linux 软件存储库中安装 blobfuse。

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. 使用 blobfuse 创建装入点 (```mkdir /path/to/mount```) 并装载 Blob 容器。 在此示例中，让我们将值 **privatechannel** 用于 **mycontainer** 变量。
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>创建通道
在下一组步骤中，我们将创建一个自定义 Conda 通道。 

1. 在本地计算机上，创建一个目录来组织自定义通道的所有包。
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. 将所有 ```tar.bz2``` 包从组织 https://repo.anaconda.com/pkgs/main/linux-64/ 到子目录中。 还要确保同时包含所有相关的 bz2 包。 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

有关详细信息，还可以 [访问 Conda 用户指南](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html) 来创建自定义通道。 

## <a name="storage-account-permissions"></a>存储帐户权限
现在，我们需要验证对存储帐户的权限。 若要设置这些权限，请导航到将在其中创建自定义通道的路径。 然后，为提供 " ```privatechannel``` 读取"、"列出" 和 "执行" 权限的 SAS 令牌。 

通道名称现在将是从此进程生成的 blob SAS URL。  

## <a name="create-a-sample-conda-environment-configuration-file"></a>创建示例 Conda 环境配置文件
最后，通过创建一个示例 Conda 文件来验证安装过程 ```environment.yml``` 。 如果已启用 DEP 的工作区，则必须 ``nodefaults`` 在环境文件中指定该通道。

下面是一个 Conda 配置文件示例：
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
创建示例 Conda 文件后，可以创建虚拟 Conda 环境。 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
验证自定义通道后，可以使用 [Python 池管理](./apache-spark-manage-python-packages.md) 过程来更新 Apache Spark 池上的库。

## <a name="next-steps"></a>后续步骤
- 查看默认库： [Apache Spark 版本支持](apache-spark-version-support.md)
- 管理 Python 包： [python 包管理](./apache-spark-manage-python-packages.md)

