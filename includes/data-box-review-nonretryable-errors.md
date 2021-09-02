---
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/07/2021
ms.author: alkohli
ms.openlocfilehash: c8a749a1b0e14859b0eb2c81b4bfeafd1dba0913
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868093"
---
若要查看不可重试的错误并继续处理订单，请执行以下操作：

1. 在 Azure 门户中打开订单。  

   如果任何不可重试的错误阻止上传文件，你会看到以下通知。 当前订单状态将为“数据复制完成，但存在错误。设备挂起的数据已删除。”

   ![Azure 门户中的上传过程中的复制错误通知](media/data-box-review-nonretryable-errors/copy-errors-in-upload-01.png)

   记下“数据复制详细信息”中的“复制日志路径” 。 你需要查看数据复制日志中的错误。

   > [!NOTE]
   > [!INCLUDE [data-box-copy-logs-behind-firewall](data-box-copy-logs-behind-firewall.md)]

2. 选择“确认设备删除”以打开查看面板。

   ![在 Azure 门户中查看面板并继续处理上传错误](media/data-box-review-nonretryable-errors/copy-errors-in-upload-02.png)

3. 使用之前记下的复制日志路径来查看数据复制日志中的错误。 可以根据需要选择“关闭”以再次显示该路径。 

   在尝试通过网络传输或新的导入订单尝试再次上传之前，你需要解决所有配置问题。 <!--For guidance, see [Review copy errors in uploads from Azure Data Box and Azure Data Box Heavy devices](../articles/databox/data-box-troubleshoot-data-upload.md). - To make the Include, I needed to move this reference out of the main procedure.-->

4. 查看错误后，请选中相应的复选框以确认你已准备好继续进行数据删除。 然后选择“继续”。

   ![确认你已准备好继续进行数据删除](media/data-box-review-nonretryable-errors/copy-errors-in-upload-03.png)

   从设备中安全删除数据后，订单状态会更新为“复制完成，但存在错误”。

   ![已完成但存在错误的 Data Box 导入订单的状态显示](media/data-box-review-nonretryable-errors/copy-errors-in-upload-04.png)

   如果不执行任何操作，则该订单会在 14 天后自动完成。

