---
title: 创建用于包管理的自定义 Conda 通道
description: 了解如何创建用于包管理的自定义 Conda 通道
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 26b6adefd2d334c9fe570bfa7e63bb06b55b9d20
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588762"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>创建用于包管理的自定义 Conda 通道 
安装 Python 包时，Conda 包管理器使用通道来查找包。 出于各种原因，你可能需要创建自定义的 Conda 通道。 例如，你可能发现：

- 工作区可以防范数据外泄，并且出站连接已被阻止。  
- 你不希望将某些包上传到公共存储库。
- 你要为工作区中的用户设置备用存储库。

本文将提供循序渐进的指南，帮助你在 Azure Data Lake Storage 帐户中创建自定义 Conda 通道。

## <a name="set-up-your-local-machine"></a>设置本地计算机

1. 在本地计算机上安装 Conda。可以参阅 [Azure Synapse Spark 运行时](./apache-spark-version-support.md)来识别同一运行时中使用的 Conda 版本。
   
2. 若要创建自定义通道，请安装 conda-build。
```
conda install conda-build
```
3. 在要为其提供服务的平台中组织所有包。 在此示例中，我们将在本地计算机上安装 Anaconda 存档。

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>将存储帐户装载到计算机
接下来，我们将 Azure Data Lake Storage Gen2 帐户装载到本地计算机。 也可以使用 WASB 帐户完成此过程；不过，我们演练的示例适用于 ADLSg2 帐户 
 
有关如何在本地计算机上装载存储帐户的详细信息，可访问[此页](https://github.com/Azure/azure-storage-fuse#blobfuse )。 

1. 可以从适用于 Microsoft 产品的 Linux 软件存储库安装 blobfuse。

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. 创建装载点 (```mkdir /path/to/mount```)，并使用 blobfuse 装载一个 Blob 容器。 在此示例中，我们对 **mycontainer** 变量使用值 **privatechannel**。
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>创建通道
在下一组步骤中，我们将创建自定义 Conda 通道。 

1. 在本地计算机上，创建一个目录用于组织自定义通道的所有包。
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. 在子目录中组织来自 https://repo.anaconda.com/pkgs/main/linux-64/ 的所有 ```tar.bz2``` 包。 另请确保包含所有相关的 tar.bz2 包。 

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

有关详细信息，还可[访问有关创建自定义通道的 Conda 用户指南](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html)。 

## <a name="storage-account-permissions"></a>存储帐户权限
现在，我们需要验证对存储帐户的权限。 若要设置这些权限，请导航到要在其中创建自定义通道的路径。 然后，为拥有读取、列出和执行权限的 ```privatechannel``` 创建 SAS 令牌。 

通道名称现在将是在此过程中生成的 Blob SAS URL。  

## <a name="create-a-sample-conda-environment-configuration-file"></a>创建示例 Conda 环境配置文件
最后，通过创建示例 Conda ```environment.yml``` 文件来验证安装过程。 如果你有一个已启用 DEP 的工作区，则必须在环境文件中指定 ``nodefaults`` 通道。

下面是一个示例 Conda 配置文件：
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
验证自定义通道后，可以使用 [Python 池管理](./apache-spark-manage-python-packages.md)过程来更新 Apache Spark 池中的库。

## <a name="next-steps"></a>后续步骤
- 查看默认库：[Apache Spark 版本支持](apache-spark-version-support.md)
- 管理 Python 包：[Python 包管理](./apache-spark-manage-python-packages.md)

