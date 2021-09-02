---
title: Azure 德国 IoT 服务 | Microsoft Docs
description: 本文为 Azure 德国的 Azure IoT 套件的入门概要。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 16259a816413bcf7f57475b12f46c9410cb3ca44
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "117029190"
---
# <a name="azure-germany-iot-services"></a>Azure 德国 IoT 服务

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="iot-solution-accelerators"></a>IoT 解决方案加速器
Azure IoT 套件所需的全部服务现均已在 Azure 德国中推出。 

### <a name="variations"></a>变体
在 Azure 德国中，Azure IoT 套件的主页与全球 Azure 的主页不同。

## <a name="solution-accelerators"></a>解决方案加速器
你可从以下解决方案加速器之一开始入手。 

### <a name="remote-monitoring"></a>远程监视
远程监视解决方案加速器针对远程位置中运行的多台计算机实现端到端监视解决方案。 该解决方案结合了关键 Azure 服务来提供业务方案的通用实现。 你可从此处入手来开始实施，并根据特定的业务要求自定义该解决方案。

### <a name="predictive-maintenance"></a>预测性维护
预测性维护解决方案加速器是一个用于商业应用场景的端到端解决方案，可预测可能发生故障的时间点。 你可以主动使用此解决方案加速器来优化维护等活动。 该解决方案结合了关键的 Azure IoT 套件服务，例如 Azure IoT 中心、流分析和机器学习工作区。 此工作区包含基于公用示例数据集的模型，用于预测飞机引擎的剩余使用寿命 (RUL)。 此解决方案全面实施了完整的 IoT 商业应用场景，你可从此入手，规划和实施解决方案，进而满足自己特定的业务需求。


## <a name="deploying-the-solution-accelerator"></a>部署解决方案加速器

这两种解决方案可以通过网站或 PowerShell 这两种方式部署。

### <a name="deploy-via-website"></a>通过网站部署

使用前面提到的主页，按照[预配置解决方案教程](/previous-versions/azure/iot-accelerators/about-iot-accelerators)中的说明操作。

### <a name="deploy-via-powershell"></a>通过 PowerShell 部署

你可在此查看完整版的远程监视解决方案（使用 Azure 资源管理器模板和 Visual Studio）。 从 [GitHub 上的 Azure-IoT-Remote-Monitoring 存储库](https://github.com/Azure/azure-iot-remote-monitoring)下载。 PowerShell 部署已适用于包括 Azure 德国在内的其他环境。 提供环境参数“AzureGermanCloud”，如下所示：

```powershell
build.cmd cloud debug AzureGermanCloud
```

必应地图当前尚未适用于 Azure 德国，因此无法自动订阅。 可以通过在全球 Azure 中订阅该服务并在那里使用该服务来解决此问题。 

> [!NOTE]
> 通过此处所述方式使用必应地图时，将保留 Azure 德国环境。

操作方法如下：

1. 单击“+ 新建”，搜索用于企业的必应地图 API，然后按照提示在全球 Azure 门户中创建必应地图 API。
2. 从全球 Azure 门户获取企业版必应地图 API 的密钥： 
    1. 在全球 Azure 门户中，浏览企业版必应地图 API 所在的资源组。
    2. 单击“所有设置” > “密钥管理”。 
    3. 可以看到两个密钥：主密钥和查询密钥。 复制查询密钥的值。
3. 从 [GitHub 上的 Azure-IoT-Remote-Monitoring 存储库](https://github.com/Azure/azure-iot-remote-monitoring)下拉最新代码。
4. 在 `/docs/` 存储库文件夹中按照命令行部署指南在环境中运行本地部署。 
5. 运行了部署之后，在根文件夹中查找在部署过程中创建的 .user.config 文件。 在文本编辑器中打开此文件。 
6. 更改以下行，以包含为查询密钥复制的值：`<setting name="MapApiQueryKey" value="" />`
7. 通过重复步骤 4 重新部署解决方案。
 


## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 [Azure 德国博客](/archive/blogs/azuregermany/)。