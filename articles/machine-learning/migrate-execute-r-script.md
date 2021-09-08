---
title: ML 工作室（经典）：迁移执行 R 脚本
description: 重新生成工作室（经典）执行 R 脚本模块以在 Azure 机器学习上运行。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: b6b422236b122244d18f0d000329b67213f36a0f
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479024"
---
# <a name="migrate-execute-r-script-modules-in-studio-classic"></a>在工作室（经典）中迁移“执行 R 脚本”模块

[!INCLUDE [ML Studio (classic) retirement](../../includes/machine-learning-studio-classic-deprecation.md)]

本文介绍如何在 Azure 机器学习中重新生成工作室（经典）“执行 R 脚本”模块。

有关从工作室（经典）迁移的详细信息，请参阅[迁移概述一文](migrate-overview.md)。

## <a name="execute-r-script"></a>执行 R 脚本

Azure 机器学习设计器现在在 Linux 上运行。 工作室（经典）在 Windows 上运行。 由于平台更改，你必须在迁移过程中调整“执行 R 脚本”，否则管道会失败。

若要从工作室（经典）中迁移“执行 R 脚本”模块，必须将 `maml.mapInputPort` 和 `maml.mapOutputPort` 接口替换为标准函数。

下表总结了对 R 脚本模块的更改：

|功能|工作室（经典版）|Azure 机器学习设计器|
|---|---|---|
|脚本接口|`maml.mapInputPort` 和 `maml.mapOutputPort`|函数接口|
|平台|Windows|Linux|
|可访问 Internet |否|是|
|内存|14 GB|依赖于计算 SKU|

### <a name="how-to-update-the-r-script-interface"></a>如何更新 R 脚本接口

下面是工作室（经典）中的示例“执行 R 脚本” 模块的内容：
```r
# Map 1-based optional input ports to variables 
dataset1 <- maml.mapInputPort(1) # class: data.frame 
dataset2 <- maml.mapInputPort(2) # class: data.frame 

# Contents of optional Zip port are in ./src/ 
# source("src/yourfile.R"); 
# load("src/yourData.rdata"); 

# Sample operation 
data.set = rbind(dataset1, dataset2); 

 
# You'll see this output in the R Device port. 
# It'll have your stdout, stderr and PNG graphics device(s). 

plot(data.set); 

# Select data.frame to be sent to the output Dataset port 
maml.mapOutputPort("data.set"); 
```

下面是设计器中的更新内容。 请注意，已将 `maml.mapInputPort` 和 `maml.mapOutputPort` 替换为标准函数接口 `azureml_main`。 
```r
azureml_main <- function(dataframe1, dataframe2){ 
    # Use the parameters dataframe1 and dataframe2 directly 
    dataset1 <- dataframe1 
    dataset2 <- dataframe2 

    # Contents of optional Zip port are in ./src/ 
    # source("src/yourfile.R"); 
    # load("src/yourData.rdata"); 

    # Sample operation 
    data.set = rbind(dataset1, dataset2); 


    # You'll see this output in the R Device port. 
    # It'll have your stdout, stderr and PNG graphics device(s). 
    plot(data.set); 

  # Return datasets as a Named List 

  return(list(dataset1=data.set)) 
} 
```
有关详细信息，请参阅设计器[执行 R 脚本模块参考](/azure/machine-learning/algorithm-module-reference/execute-r-script)。

### <a name="install-r-packages-from-the-internet"></a>从 Internet 安装 R 包

Azure 机器学习设计器使你可以直接从 CRAN 安装包。

这是对工作室（经典）的改进。 由于工作室（经典）在不具有 Internet 访问权限的沙盒环境中运行，因此你必须在 zip 包中上传脚本，才能安装更多包。 

使用以下代码在设计器的“执行 R 脚本”模块中安装 CRAN 包：
```r
  if(!require(zoo)) { 
      install.packages("zoo",repos = "http://cran.us.r-project.org") 
  } 
  library(zoo) 
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何将执行 R 脚本模块迁移到 Azure 机器学习。

请参阅工作室（经典）迁移系列中的其他文章：

1. [迁移概述](migrate-overview.md)。
1. [迁移数据集](migrate-register-dataset.md)。
1. [重新生成工作室（经典）训练管道](migrate-rebuild-experiment.md)。
1. [重新生成工作室（经典）Web 服务](migrate-rebuild-web-service.md)。
1. [将机器学习 Web 服务与客户端应用集成](migrate-rebuild-integrate-with-client-app.md)。
1. 迁移“执行 R 脚本”模块。
