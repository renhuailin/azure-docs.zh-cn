---
title: 针对 Azure 导入/导出作业创建支持票证或案例 | Microsoft Docs
description: 了解如何针对导入/导出作业相关的问题记录支持请求。
services: storsimple
author: alkohli
ms.service: storage
ms.topic: conceptual
ms.date: 07/30/2021
ms.author: alkohli
ms.subservice: common
ms.custom: contperf-fy22q1
ms.openlocfilehash: 7b0153f56efa4a78f06d2b8182c64b1c5316fc20
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729903"
---
# <a name="open-a-support-ticket-for-an-importexport-job"></a>针对导入/导出作业开具支持票证

如果在使用导入/导出服务时遇到任何问题，可创建服务请求来获取技术支持。  

本文介绍：

* 如何创建支持请求 
* 如何从门户内部管理支持请求生命周期

## <a name="create-a-support-request"></a>创建支持请求

若要创建支持请求，请执行以下步骤：

1. 转到导入/导出作业。 在“支持 + 故障排除”下，选择“新建支持请求” 。
     
    ![基础](./media/storage-import-export-contact-microsoft-support/import-export-support-01.png)
   
1. 在“基本信息”选项卡上输入所需的基本信息。 系统已填充与导入/导出订单相关的基本信息。

     |选项|说明|
     |------|-----------|
     |**摘要** | 简单描述你的问题。|
     |**问题类型**|选择“技术”。|
     |**订阅**|为导入/导出订单选择订阅。 |
     |**服务**|选择“我的服务”。|
     | **服务类型**| 选择“Azure 导入/导出服务”。|
     |**资源**|选择导入/导出订单。|
     |**问题类型**|从导入/导出订单的问题类型中选择一个问题类型。|
     |**问题子类型**|为问题类型选择适当的子类型。|
 
     ![屏幕截图显示了导入/导出服务支持请求的“基本信息”屏幕上的信息。 其中突出显示了用于打开“解决方案”选项卡的按钮。](./media/storage-import-export-contact-microsoft-support/import-export-support-02.png)

   选择“下一步: 解决方案>>”以继续。

1. “解决方案”选项卡显示所述问题的专家解决方案。 如果未找到相关问题的解决方案，请选择“下一步: 详细信息>>”，以创建支持请求。

    ![屏幕截图显示了导入/导出服务支持请求的“解决方案”选项卡。 “解决方案”选项卡显示所选问题的专家解决方案。 其中突出显示了用于打开“解决方案”选项卡的按钮。](./media/storage-import-export-contact-microsoft-support/import-export-support-03.png)

1. 使用“详细信息”选项卡提供支持工单信息。

    |分组|选项|
    |--------|-------------------|
    |**问题详细信息**|<ul><li>**该问题是何时开始的？** 使用本地时间，输入问题开始的时间。</li><li>**详细信息**：描述所遇到的问题。</li><li>**文件**：可视情况附加一个或多个文件。 选择文件夹图标。 然后浏览到要上传的任何文件。</li></ul>|
    |**共享诊断信息**|<ul><li>请选择“否”。 Azure 支持人员会在需要时让你提供更多信息。</li></ul>|
    |**支持方法**|<ul><li>将“严重性”保留为“C - 轻微影响” 。 Azure 支持人员将根据你提供的信息调整严重性级别。</li><li>否则，请告知你的联系方式 - 通过电子邮件还是电话，采用何种语言？</li></ul>|
    |**联系人信息**|<ul><li>你可以视情况编辑联系人信息。</li></ul>|

    ![屏幕截图显示了导入/导出服务支持请求的“详细信息”选项卡。 其中突出显示了用于打开“查看 + 创建”选项卡的按钮。](./media/storage-import-export-contact-microsoft-support/import-export-support-04.png)

    选择“下一步: 查看 + 创建 >>”以继续。

1. 在“查看 + 创建”选项卡中查看支持请求。视情况进行修改。 然后选择“创建”。 

    ![屏幕截图显示了新的 Azure 支持请求的“查看 + 创建”选项卡。 其中突出显示了“创建”按钮。](./media/storage-import-export-contact-microsoft-support/import-export-support-05.png)


## <a name="manage-a-support-request"></a>管理支持请求

创建支持请求之后，可以在门户内管理请求的生命周期。

#### <a name="to-manage-your-support-requests"></a>管理支持请求

1. 若要访问“帮助和支持”页，请导航到“浏览”>“帮助 + 支持”。

    ![屏幕截图显示了如何在 Azure 门户主页上选择“帮助 + 支持”。](./media/storage-import-export-contact-microsoft-support/manage-support-ticket-01.png)

2. “帮助 + 支持”中会显示“最近的支持请求”表格。 

    ![屏幕截图显示了“帮助 + 支持”页面，页面中突出显示了“打开支持请求"。](./media/storage-import-export-contact-microsoft-support/manage-support-ticket-02.png) 

3. 选择并单击某个支持请求。 可以查看此请求的状态和详细信息。 如果想要跟进此请求，请选择“+ 新建消息”。

    ![屏幕截图显示了为 Azure 支持请求选择的新消息。](./media/storage-import-export-contact-microsoft-support/manage-support-ticket-03.png)


## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure 导入/导出将数据传入和传出 Azure 存储](storage-import-export-service.md)。
