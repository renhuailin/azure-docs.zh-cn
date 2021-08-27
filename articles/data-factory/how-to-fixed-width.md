---
title: 在 Azure 数据工厂中使用映射数据流处理定长文本文件
description: 了解如何使用映射数据流在 Azure 数据工厂中处理定长文本文件。
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 689a9d34dc796516aa281964552e11d70c74c1de
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637882"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>通过使用数据工厂映射数据流来处理定长文本文件

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通过在 Microsoft Azure 数据工厂中使用映射数据流，可以从定宽的文本文件转换数据。 在以下任务中，我们将为不带分隔符的文本文件定义数据集，然后基于序号位置设置子字符串拆分。

## <a name="create-a-pipeline"></a>创建管道

1. 选择“+新建管道”以创建新管道。

2. 添加一个数据流活动，该活动将用于处理定宽的文件：

    ![定宽管道](media/data-flow/fwpipe.png)

3. 在数据流活动中，选择“新建映射数据流”。

4. 添加源、派生列、选择和接收器转换：

    ![定宽数据流](media/data-flow/fw2.png)

5. 配置源转换以使用新的数据集，该数据集将是带分隔符的文本类型。

6. 不要设置任何列分隔符或标头。

   现在，我们将为该文件的内容设置字段起点和长度：

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. 在源转换的“投影”选项卡上，应该可以看到名为“Column_1”的字符串列。

8. 在派生列中，创建一个新列。

9. 我们将为列指定简单名称，如 col1。

10. 在表达式生成器中，键入以下内容：

    ```substring(Column_1,1,4)```

    ![派生列 (derived column)](media/data-flow/fwderivedcol1.png)

11. 对需要解析的所有列重复步骤 10。

12. 选择“检查”选项卡以查看将要生成的新列：

    ![检查](media/data-flow/fwinspect.png)

13. 使用选择转换来删除不需要转换的任何列：

    ![选择转换](media/data-flow/fwselect.png)

14. 使用接收器将数据输出到文件夹：

    ![定宽接收器](media/data-flow/fwsink.png)

    下面是输出的效果：

    ![定宽输出](media/data-flow/fxdoutput.png)

  现在，将定宽数据拆分，每个有四个字符，并分配到 Col1、Col2、Col3、Col4 等。 根据前面的示例，将数据拆分为四列。

## <a name="next-steps"></a>后续步骤

* 使用映射数据流[转换](concepts-data-flow-overview.md)来生成数据流逻辑的其余部分。
