---
title: 版本发布历史记录
titleSuffix: Microsoft Genomics
description: 关于 Microsoft 基因组学 Python 客户端更新以便提供修补程序和新功能的版本发布历史记录。
services: genomics
author: vigunase
manager: cgronlun
ms.author: vigunase
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 3023f01b9a9ccbc3a56c509b1edc86885f2af41e
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255688"
---
# <a name="version-release-history"></a>版本发布历史记录
Microsoft 基因组学团队定期更新 Microsoft 基因组学 Python 客户端，以便提供修补程序和新的功能。 

## <a name="latest-release"></a>最新发布
当前 Python 客户端的版本是 0.9.0。 它已于 2019 年 2 月 6 日发布，支持使用 GATK3.5 和 GATK4 运行工作流。 它支持 gVCF 输出，并且支持选择参数进行输出压缩。


## <a name="release-history"></a>版本历史记录 
Microsoft 基因组学 Python 客户端的新版本每年约发布一次。 当 Microsoft 基因组学 Python 客户端发布新版本时，此处会更新修补程序和功能列表。 发布新版本后，对早期版本的支持应至少持续 90 日。 当早期版本不再受支持时，此页面将进行说明。 

### <a name="version-090"></a>版本 0.9.0
版本0.9.0 包括对输出压缩的支持。 这等效于对 vcf 或 gvcf 输出运行 `-bgzip` 之后再运行 `-tabix`。 有关详细信息，请参阅[常见问题解答](frequently-asked-questions-genomics.yml)。 

### <a name="version-081"></a>版本 0.8.1
版本 0.8.1 包括小 bug 修复。  

### <a name="version-080"></a>版本 0.8.0
版本 0.8.0 包括对 GATK4 和输出 gVCF 的支持。  

### <a name="version-074"></a>版本 0.7.4
版本 0.7.4 包括对于接受 `config.txt` 输入中的 SAS 令牌（而不是帐户密钥）的支持。 有关详细信息，请参阅[输入 SAS 令牌快速入门](quickstart-input-sas.md)。 

### <a name="version-073"></a>版本 0.7.3
版本 0.7.3 包括小 bug 修复。

### <a name="version-072"></a>版本 0.7.2
版本 0.7.2 是初始版本。 它已于 2017 年 11 月 1 发布。
