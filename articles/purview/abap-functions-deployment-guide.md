---
title: SAP R3 中的元数据提取 ABAP 函数模块 - Azure Purview
description: 本文概述了在 SAP 服务器中部署 ABAP 函数模块的步骤
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: f235e4b293c47c9d2833732fa6333a350a1fd272
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210338"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>部署适用于 SAP R3 系列桥接的元数据提取 ABAP 函数模块

本文概述了在 SAP Server 中部署 ABAP 函数模块的步骤。

## <a name="overview"></a>概述

SAP Business Suite 4 HANA (S/4HANA), ECC, 和 R/3 ERP 桥接可用于从 SAP 服务器中提取元数据。 这可通过将 ABAP 函数模块置于 SAP 服务器上来实现。 桥接可远程访问此函数模块，以查询和下载（文本文件格式的）SAP 服务器中包含的元数据。

执行后，桥接将执行以下任一操作：

1. 从已从以前的桥接执行本地下载的现有文件中导入元数据。

2. 调用 ABAP 模块 API，等待下载，然后从该文件导入元数据。

本文档详细介绍了部署此模块所需的步骤。

> [!Note]
> 以下说明基于 SAP GUI v.7.2 进行编译

## <a name="deployment-of-the-module"></a>部署模块

### <a name="create-a-package"></a>创建包

此步骤是可选的，可以使用现有的包。

1. 登录到 SAP S/4HANA 或 SAP ECC 服务器，打开“对象导航器”（SE80 事务）。

2. 从列表中选择选项“包”，然后输入新包的名称（例如，Z\_MITI），然后按按钮“显示”。

3. 在“创建包”窗口中，选择“是”。 此时将打开“包生成器: 创建包”窗口。 在“简短说明”字段中输入值，并选择“继续”图标。

4. 在“本地工作台请求提示”窗口中选择“自己的请求”。 选择“开发”请求。

### <a name="create-a-function-group"></a>创建函数组

在对象导航器中，从列表中选择“函数组”，然后在下面的输入字段中键入名称（例如，Z\_MITI\_FGROUP）。 选择“视图”图标。

1. 在“创建对象”窗口中，选择“是”以创建新的函数组。

2. 在“短文本”字段中指定适当的说明，并按“保存”按钮。

3. 选择上一步“创建包”中准备的包，然后选择“保存”。

4. 按“继续”图标确认请求。

5. 激活函数组。

### <a name="create-the-abap-function-module"></a>创建 ABAP 函数模块

1. 创建函数组后，选中该函数组。

2. 在存储库浏览器中，选择并按住（或右键单击）函数组名称，选择“创建”，然后选择“函数模块”。

3. 在“函数模块”字段中，输入 `Z_MITI_DOWNLOAD`。 用正确的说明填充“短文本”输入。

创建模块后，指定以下信息：

1. 导航到“属性”选项卡。

2. 对于“处理类型”，选择“已启用远程函数模块”。

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="“注册源”选项 - 已启用远程函数模块" border="true":::

3. 导航到“源代码”选项卡。可以通过两种方式来部署函数的代码：

   a. 在主菜单中，上传文本文件 [Z\_MITI\_DOWNLOAD](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) 文件。 为此，请依次选择“实用工具”"、“更多实用工具”、“上传/下载”和“上传”。

   b. 或者，打开文件，复制其内容并粘贴到“源代码”区域。

4. 导航到“导入”选项卡并创建以下参数：

   a.  P\_AREA TYPE DD02L-TABNAME (Optional = True)

   b.  *P\_LOCAL\_PATH TYPE STRING* (Optional = True)

   c.  *P\_LANGUAGE TYPE L001TAB-DATA DEFAULT \'E\'*

   d.  *ROWSKIPS TYPE SO\_INT DEFAULT 0*

   e.  *ROWCOUNT TYPE SO\_INT DEFAULT 0*

   > [!Note]
   > 为以上所有参数选择“传递值”

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="“注册源”选项 -“导入”参数" border="true":::

5. 导航到“表”选项卡，并定义以下内容：

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="“注册源”选项 -“表”选项卡" border="true":::

6. 导航到“例外”选项卡，并定义以下例外：`E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="“注册源”选项 -“例外”选项卡" border="true":::

7. 保存函数（按 Ctrl+S 或选择“函数模块”，然后在主菜单选择“保存”）。

8. 选择工具栏上的“激活”图标 (Ctrl+F3) 并在对话框窗口中选择“继续”按钮。 如果出现提示，则应选择要随主函数模块一起激活的生成的包含。

### <a name="testing-the-function"></a>测试函数

完成前面的所有步骤后，请按照以下步骤测试函数：

1. 打开 Z\_MITI\_DOWNLOAD 函数模块。

2. 依次选择“函数模块”和“测试”，然后从主菜单中选择“测试函数模块”（或按 F8）。

3. 将本地文件系统上的文件夹的路径输入参数 P\_LOCAL\_PATH，然后按工具栏上的“执行”图标（或按 F8）。

4. 如果必须下载或更新包含元数据的文件，请将相关区域的名称放入 P\_AREA 字段。 函数完成工作后，P\_LOCAL\_PATH 参数中指示的文件夹必须包含多个包含元数据的文件。 可在 P\_AREA 字段中指定的文件模拟区域的名称。

此函数将完成执行，如果在与 SAP S/4HANA 或 ECC 服务器建立了高速网络连接的计算机上启动该函数，将以快得多的速度下载元数据。

## <a name="next-steps"></a>后续步骤

- [注册并扫描 SAP ECC 源](register-scan-sapecc-source.md)
- [注册并扫描 SAP S/4HANA 源](register-scan-saps4hana-source.md)
