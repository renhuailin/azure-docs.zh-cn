---
title: Apache Spark 版本支持
description: 支持的 Spark、Scala、Python、.NET 版本
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/22/2021
ms.author: midesa
ms.openlocfilehash: 9f13a0797caa13f0d4367357308809c97bde0b17
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577763"
---
# <a name="azure-synapse-runtimes"></a>Azure Synapse 运行时
Azure Synapse 中的 Apache Spark 池使用运行时将基本组件版本、Azure Synapse 优化、包和连接器与特定版本 Apache Spark 联系在一起。 这些运行时将定期升级，以包含新的改进、功能和修补程序。 

创建无服务器 Apache Spark 池时，可以选择相应的 Apache Spark 版本。 以此为基础，池将随关联的运行时组件和包一起预安装。 这些运行时具有以下优点：

- 会话启动时间更快
- 测试了与特定 Apache Spark 版本的兼容性
- 可访问热门的兼容连接器和开源包

> [!NOTE]
> - 维护更新将自动应用于给定无服务器 Apache Spark 池的新会话。 
> - 使用新的运行时版本时，应该测试并验证应用程序是否正常运行。

## <a name="supported-azure-synapse-runtime-releases"></a>受支持的 Azure Synapse 运行时版本 
下表列出了受支持的 Azure Synapse 运行时版本的运行时名称、Apache Spark 版本和发布日期。

|  运行时名称  | 发布日期 |  发布阶段 |
| ----- | ----- | ----- |
| [适用于 Apache Spark 2.4 的 Azure Synapse 运行时](./apache-spark-24-runtime.md) | 2020 年 12 月 15 日 | GA|
| [适用于 Apache Spark 3.1 的 Azure Synapse 运行时](./apache-spark-3-runtime.md) | 2021 年 5 月 26 日 | GA |

## <a name="runtime-release-stages"></a>运行时发布说明

## <a name="preview-runtimes"></a>预览版运行时
Azure Synapse Analytics 提供预览版，让你有机会在功能正式发布 (GA) 之前对其进行评估并提供反馈。 虽然运行时以预览版提供，但也可能会引入新的依赖项和组件版本。 支持 SLA 不适用于预览版运行时。 

## <a name="generally-available-runtimes"></a>正式发布的运行时
正式发布 (GA) 的运行时可供所有客户使用，并可直接用于生产环境。 运行时正式发布后，安全修补程序和稳定性改进可能会向后移植。 此外，仅当新组件不会更改基本依赖项或组件版本时，才会引入新组件。 
