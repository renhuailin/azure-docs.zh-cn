---
title: 分析 Azure Data Lake Storage Gen1 中的数据 - Power BI
description: 了解如何使用 Power BI Desktop 分析和可视化 Azure Data Lake Storage Gen1 中存储的数据。
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: normesta
ms.openlocfilehash: a2187bead4c8aa6e205888bbaaac84853fa88c27
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654283"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>使用 Power BI 分析 Azure Data Lake Storage Gen1 中的数据
本文介绍如何使用 Power BI Desktop 分析和可视化 Azure Data Lake Storage Gen1 中存储的数据。

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，必须具有：

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Data Lake Storage Gen1 帐户**。 请遵循[通过 Azure 门户开始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) 中的说明进行操作。 本文假定已创建名为“myadlsg1”的 Data Lake Storage Gen1 帐户，且已向其上传了示例数据文件 (Drivers.txt) 。 可从 [Azure Data Lake Git Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)（Azure Data Lake Git 存储库）下载此示例文件。
* **Power BI Desktop**。 可从 [Microsoft 下载中心](https://www.microsoft.com/en-us/download/details.aspx?id=45331) 进行下载。 

## <a name="create-a-report-in-power-bi-desktop"></a>在 Power BI Desktop 中创建报表
1. 在计算机上启动 Power BI Desktop。
2. 在“主页”功能区上，单击“获取数据”，并单击“详细信息”。 在“获取数据”对话框中，单击“Azure”，单击“Azure Data Lake Store”，然后单击“连接”。
   
    ![“获取数据”对话框的屏幕截图，其中突出显示了“Azure Data Lake Store”选项并标注了“连接”选项。](./media/data-lake-store-power-bi/get-data-lake-store-account.png "连接到 Data Lake Storage Gen1")
3. 如果出现一个对话框显示连接器处于开发阶段，选择继续。
4. 在“Azure Data Lake Store”对话框中，提供 Data Lake Storage Gen1 帐户的 URL，并单击“确定”。
   
    ![Data Lake Storage Gen1 的 URL](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Data Lake Storage Gen1 的 URL")
5. 在下一个对话框中，单击“登录”以登录到 Data Lake Storage Gen1 帐户。 将重定向到组织的登录页面。 按照提示登录到该帐户。
   
    ![登录 Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "登录 Data Lake Storage Gen1")
6. 登录成功后，单击“连接”。
   
    ![“Azure Data Lake Store”对话框的屏幕截图，其中标注了“连接”选项。](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "连接到 Data Lake Storage Gen1")
7. 下一个对话框中会显示已上传至 Data Lake Storage Gen1 帐户的文件。 验证信息，并单击“加载”。
   
    ![从 Data Lake Storage Gen1 加载数据](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "从 Data Lake Storage Gen1 加载数据")
8. 数据成功加载到 Power BI 后，“字段”选项卡会出现以下字段。
   
    ![已导入的字段](./media/data-lake-store-power-bi/imported-fields.png "已导入的字段")
   
    但是，若要可视化和分析数据，最好以下每个字段都可用该数据
   
    ![所需字段](./media/data-lake-store-power-bi/desired-fields.png "所需字段")
   
    后续步骤会更新查询，以按所需格式转换已导入的数据。
9. 在“主页”功能区上，单击“编辑查询”。
   
    ![“主页”功能区的屏幕截图，其中标注了“编辑查询”选项。](./media/data-lake-store-power-bi/edit-queries.png "编辑查询")
10. 在“查询编辑器”中的“内容”列，单击“二进制”。
    
    ![“查询编辑器”的屏幕截图，其中标注了“内容”列。](./media/data-lake-store-power-bi/convert-query1.png "编辑查询")
11. 会出现一个文件图标，该图标表示已上传的 **Drivers.txt** 文件。 右键单击该文件，并单击“CSV”。    
    
    ![“查询编辑器”的屏幕截图，其中标注了“CSV”选项。](./media/data-lake-store-power-bi/convert-query2.png "编辑查询")
12. 会出现如下所示的输出。 数据现在已是可用于创建可视化的格式。
    
    ![“查询编辑器”的屏幕截图，其中的输出按预期显示。](./media/data-lake-store-power-bi/convert-query3.png "编辑查询")
13. 在“主页”功能区上，单击“关闭并应用”，并再次单击“关闭并应用”。
    
    ![“主页”功能区的屏幕截图，其中标注了“关闭”和“应用”选项。](./media/data-lake-store-power-bi/load-edited-query.png "编辑查询")
14. 查询更新后，“字段”选项卡会显示可用于可视化的新字段。
    
    ![已更新的字段](./media/data-lake-store-power-bi/updated-query-fields.png "已更新的字段")
15. 我们来创建一个饼图，表示特定国家/地区的每个城市中的司机。 为此，请如下进行选择。
    
    1. 在“可视化”选项卡中，单击饼图符号。
       
        ![创建饼图](./media/data-lake-store-power-bi/create-pie-chart.png "创建饼图")
    2. 将使用到的列是“列 4”（城市名称）和“列 7”（国家/地区名称）。 如下所示，从“字段”选项卡将这些列拖动到“可视化”选项卡。
       
        ![创建可视化效果](./media/data-lake-store-power-bi/create-visualizations.png "创建视觉效果")
    3. 此时饼图应如下所示。
       
        ![饼图](./media/data-lake-store-power-bi/pie-chart.png "创建视觉效果")
16. 通过在页面级别筛选器中选择一个特定的国家/地区，可看到所选国家/地区的每个城市中的司机数量。 例如，在“可视化”选项卡中的“页面级别筛选器”中，选择“巴西”。
    
    ![选择国家/地区](./media/data-lake-store-power-bi/select-country.png "选择国家/地区")
17. 饼图会自动更新，显示巴西各个城市中的司机。
    
    ![某个国家/地区中的司机](./media/data-lake-store-power-bi/driver-per-country.png "每个国家/地区的司机")
18. 在“文件”菜单上，单击“保存”将可视化对象另存为 Power BI Desktop 文件。

## <a name="publish-report-to-power-bi-service"></a>将报表发布到 Power BI 服务
在 Power BI Desktop 中创建可视化后，可将其发布到 Power BI 服务中与他人共享。 有关如何发布的说明，请参阅[从 Power BI Desktop 发布](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/)。

## <a name="see-also"></a>另请参阅
* [使用 Data Lake Analytics 分析 Data Lake Storage Gen1 中的数据](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

