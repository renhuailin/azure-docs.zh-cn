---
title: 部署 Azure 监控范围中适用于 SAP R3 系列的元数据提取 ABAP 函数模块
description: 本文概述了在 SAP Server 中部署 ABAP 函数模块的步骤
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 1d9f1c5beafb7b54c5fd0189dd738ff8e346a3e8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695922"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>部署适用于 SAP R3 系列桥的元数据提取 ABAP 函数模块 
本文概述了在 SAP Server 中部署 ABAP 函数模块的步骤
## <a name="overview"></a>概述 

SAP Business Suite 4 HANA (S/4HANA) 、ECC 和 R/3 ERP 桥可用于从 SAP 服务器中提取元数据。 这是通过将 ABAP 函数模块置于 SAP 服务器上来实现的。 桥可远程访问此函数模块，以便查询和下载 () 包含在 SAP 服务器中的元数据的文本文件。
执行后，桥接器将执行以下任一操作：

1.  从以前的桥接执行的现有文件中导入元数据。

2.  调用 ABAP 模块 API，等待下载，然后从该文件导入元数据。

本文档详细介绍了部署此模块所需的步骤。

> [!Note] 
>以下说明基于 SAP GUI v2.0 进行编译

## <a name="deployment-of-the-module"></a>部署模块 

### <a name="create-a-package"></a>创建包 

此步骤是可选的，可以使用现有的包。

1.  登录到 SAP S/4HANA 或 SAP ECC 服务器，并打开 \" 对象导航器 \" (SE80 transaction) 。

2.  \"从列表中选择 "选项包 \" "，然后输入新包的名称 (例如，Z \_ MITI) 然后按 "Display" 按钮

3.  在 "创建包" 窗口中按 "是"。 因此， \" 将打开一个窗口包生成器： "创建包" \" 。 在 "简短说明" 字段中输入值，然后按 " \" 继续" \" 图标。

4.  在 "本地工作台请求提示" 窗口中按 "自己的请求"。 选择 "开发" 请求。

### <a name="create-a-function-group"></a>创建函数组 

在对象导航器中， \" 从列表中选择函数组 \" ，然后在下面的输入字段中键入其名称 (例如，Z \_ MITI \_ FGROUP) 。 按 "查看图标"。

1.  在 \" "创建对象 \" " 窗口中，按 "是" 以创建新的函数组。

2.  在 "短文本" 字段中指定适当的说明 \" \" ，并按 "保存" 按钮 \" \" 。

3.  选择上一步骤中准备的包 \" 创建包 \" ，并单击 "保存" 图标 \" \" 。

4.  按图标 "继续" 确认 \" 请求 \" 。

5.  激活此函数组。

### <a name="create-the-abap-function-module"></a>创建 ABAP 函数模块 

创建并选择函数组后，在 "存储库浏览器" 中右键单击其名称，然后选择 " \" 创建 = \> 函数模块" \" 。

输入 \" Z \_ MITI "将 \_ \" 其下载到 \" 函数模块" \" 字段，并 \" \" 通过正确的说明填充短文本输入。

创建该模块后，指定以下信息：

1.  导航到 " \" 属性" \" 选项卡。

2.  选择 "处理类型 = Remote-Enabled 函数模块"。

    :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="注册源选项" border="true":::

3.  导航到 " \" 源代码 \" " 选项卡。可以通过两种方式来部署函数的代码：

    a.  在主菜单中，选择 " \_ \_ 实用工具 = \> 更多实用工具 = \> 上传/下载 = \> 上传" 以上传 Z MITIDOWNLOAD.txt 文件。

    b.  或者，打开文件，复制其内容并粘贴到 \" 源代码 \" 区域。

4.  导航到 " \" 导入" \" 选项卡并创建以下参数：

    a.  P \_ AREA TYPE DD02L-TABNAME (Optional = True) 

    b.  *P \_本地 \_ 路径类型字符串* (Optional = True) 

    c.  *P \_ LANGUAGE 类型 L001TAB-数据默认值 \' E\'*

    d.  *ROWSKIPS 类型，因此 \_ INT 默认值为0*

    e.  *ROWCOUNT 类型，因此 \_ INT 默认值为0*

    > [!Note]
    > 选择 \" \" 所有这些值的传递值

    :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="注册源选项" border="true":::

5.  导航到 "表" 选项卡，并定义以下内容：

    *导出 \_ 表（如 TAB512）*

    :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="注册源选项" border="true":::

6.  导航到 " \" 异常" \" 选项卡，并定义以下异常：

    *E \_ EXP \_ GUI \_ DOWNLOADFAILED*

    :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="注册源选项" border="true":::

7.  保存函数 (按 ctrl + S 或 \> 在主菜单) 中选择 "Function Module = Save"。

8.  单击 \" \" 工具栏上的 "激活" 图标 (Ctrl + F3) 并按 \" \" 对话框窗口中的 "继续" 按钮。 如果出现提示，则应选择要随 main function 模块一起激活的生成的包含。

### <a name="testing-the-function"></a>测试函数 

完成前面的所有步骤后，请按照以下步骤测试函数：

1.  打开 Z \_ MITI \_ 下载函数模块。

2.  \" \> 从主菜单中选择 Function module = test = \> test Function module \" (或按 F8) 。

3.  输入本地文件系统上的文件夹的路径到参数 P \_ 本地 \_ 路径，然后按 \" 工具栏上的 "执行 \" " 图标 (或按 F8) 。

4.  \_如果必须下载或更新包含元数据的文件，请将感兴趣的区域的名称放入 "P area" 字段。 函数完成工作后，在 P 本地路径参数中指示的文件夹 \_ \_ 必须包含多个包含元数据的文件。 可在 P AREA 字段中指定的文件的名称 \_ 。

此函数将完成其执行，如果在与 SAP S/4HANA 或 ECC 服务器建立高速网络连接的计算机上启动该函数，则将以更快的速度下载元数据。

## <a name="next-steps"></a>后续步骤

- [注册和扫描 SAP ECC 源](register-scan-sapecc-source.md)
- [注册和扫描 SAP S/4HANA 源](register-scan-saps4hana-source.md)