---
title: 排查映射数据流中的连接器和格式问题
description: 了解如何排查 Azure 数据工厂中与连接器和格式相关的数据流问题。
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/20/2021
ms.openlocfilehash: 1991436e2cc890b5c6a339f79e42536ced2fbd36
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739454"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>排查 Azure 数据工厂中映射数据流中的连接器和格式问题


本文探讨了 Azure 数据工厂 (ADF) 中与映射数据流的连接器和格式相关的故障排除方法。


## <a name="cosmos-db--json"></a>Cosmos DB 和 JSON

### <a name="support-customized-schemas-in-the-source"></a>支持源中的自定义架构

#### <a name="symptoms"></a>症状
如果要使用 ADF 数据流将数据从 Cosmos DB/JSON 移动或传输到其他数据存储，则可能会缺失源数据的某些列。 

#### <a name="cause"></a>原因 
对于自由架构连接器（与其他行进行比较时，每行的列号、列名和列数据类型可能不同），默认情况下，ADF 使用样本行（例如，前 100 或 1000 行数据）来推断架构，并且推断结果会用作架构来读取数据。 因此，如果数据存储具有未出现在样本行中的额外列，则这些额外列的数据不会被读取、移动或传输到接收器数据存储中。

#### <a name="recommendation"></a>建议
为了覆盖默认行为并引入其他字段，ADF 提供了用于自定义源架构的选项。 可以在数据流源投影中指定架构推断结果中可能缺失的额外/缺失列来读取数据，并且可以应用以下选项之一来设置自定义架构。 通常情况下，选项 1 更为可取。

- 选项 1：与可能是包含数百万行并具有复杂架构的一个大型文件、表或容器的原始源数据进行比较时，可以使用包含要读取的所有列的几行创建临时表/容器，然后继续执行以下操作： 

    1. 使用数据流源“调试设置”使具有样本文件/表的“导入投影”获取完整架构 。 可以执行下图中的步骤：<br/>

        ![屏幕截图，显示用于自定义源架构的第一个选项的第一部分。](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. 在数据流画布中选择“调试设置”。
         1. 在弹出窗口中，选择“cosmosSource”选项卡下的“样本表”，然后在“表”块中输入表的名称  。
         1. 选择“保存”以保存设置。
         1. 选择“导入投影”。<br/>  
    
    1. 改回“调试设置”，以将源数据集用于其余数据移动/转换。 可以继续执行下图中的步骤：<br/>

        ![屏幕截图，显示用于自定义源架构的第一个选项的第二部分。](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. 在数据流画布中选择“调试设置”。
         1. 在弹出窗口中，选择“cosmosSource”选项卡下的“源数据集” 。
         1. 选择“保存”以保存设置。<br/>
    
    之后，ADF 数据流运行时会接受并使用自定义架构从原始数据存储读取数据。 <br/>

- 选项 2：如果熟悉源数据的架构和 DSL 语言，则可以手动更新数据流源脚本，以添加额外/缺失列来读取数据。 下图显示了一个示例： 

    ![屏幕截图，显示用于自定义源架构的第二个选项。](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>后续步骤
在故障排除时如需更多帮助，请参阅以下资源：

*  [排查 Azure 数据工厂中的映射数据流问题](data-flow-troubleshoot-guide.md)
*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)