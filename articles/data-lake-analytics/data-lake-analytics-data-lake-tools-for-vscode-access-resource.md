---
title: 通过 Data Lake 工具访问资源
description: 了解如何使用 Azure Data Lake 工具来访问 Azure Data Lake Analytics 资源。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754606"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>通过 Azure Data Lake 工具访问资源

你可以轻松地使用 Azure Data Tools 命令或 VS Code 中的操作访问 Azure Data Lake Analytics 资源。

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>通过命令与 Azure Data Lake Analytics 集成

可以访问 Azure Data Lake Analytics 资源来列出帐户、访问元数据和查看分析作业。

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>列出 Azure 订阅下 Azure Data Lake Analytics 帐户的步骤

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入 **ADL: List Accounts**。 帐户随即显示在“输出”窗格中。

### <a name="to-access-azure-data-lake-analytics-metadata"></a>访问 Azure Data Lake Analytics 元数据

1. 按 Ctrl+Shift+P，并输入 **ADL: List Tables**。
2. 选择其中一个 Data Lake Analytics 帐户。
3. 选择其中一个 Data Lake Analytics 数据库。
4. 选择其中一个架构。 可以看到表的列表。

### <a name="to-view-azure-data-lake-analytics-jobs"></a>查看 Azure Data Lake Analytics 作业的步骤

1. 打开命令面板 (Ctrl+Shift+P)，并选择“ADL: Show Jobs”。
2. 选择 Data Lake Analytics 或本地帐户。
3. 等待显示帐户的作业列表.
4. 从作业列表中选择作业。 Data Lake 工具将在右侧窗格中打开作业视图，并在 VS Code 输出中显示一些信息。

    ![作业列表](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>通过命令与 Azure Data Lake Store 集成

可以使用 Azure Data Lake Store 相关的命令来执行以下操作：

- [浏览 Azure Data Lake Store 资源](#list-the-storage-path)
- [预览 Azure Data Lake Store 文件](#preview-the-storage-file)
- 在 VS Code 中将文件直接上传到 Azure Data Lake Store
- 在 VS Code 中从 Azure Data Lake Store 直接下载文件

### <a name="list-the-storage-path"></a>列出存储路径

### <a name="to-list-the-storage-path-through-the-command-palette"></a>通过命令面板列出存储路径

1. 右键单击脚本编辑器，然后选择“ADL: 列出路径”。
2. 在列表中选择文件夹，或者选择“输入路径”或“从根路径浏览”。 （我们将使用“输入路径”作为示例。）
3. 选择 Data Lake Analytics 帐户。
4. 浏览到或输入存储文件夹路径（例如，/output/）。  

命令面板根据输入内容列出路径信息。

![存储路径结果](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

列出相对路径的一种更便利的方法是通过快捷菜单。

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>通过快捷菜单列出存储路径

右键单击路径字符串并选择“列出路径”。

![快捷菜单上的“列出路径”](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>预览存储文件

1. 右键单击脚本编辑器，然后选择“ADL: 预览文件”。
2. 选择 Data Lake Analytics 帐户。
3. 输入 Azure 存储文件路径（例如，/output/SearchLog.txt）。

文件在 VS Code 中打开。

![预览存储文件的步骤和结果](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

预览文件的另一种方法是，通过脚本编辑器中文件完整路径或文件相对路径上的快捷菜单。

### <a name="upload-a-file-or-folder"></a>上传文件或文件夹

1. 右键单击脚本编辑器，然后选择“上传文件”或“上传文件夹”。
2. 如果选择“上传文件”，则选择一个或多个文件；如果选择“上传文件夹”，则选择整个文件夹。 然后，选择“上传”。
3. 在列表中选择存储文件夹，或者选择“输入路径”或“从根路径浏览”。 （我们将使用“输入路径”作为示例。）
4. 选择 Data Lake Analytics 帐户。
5. 浏览到或输入存储文件夹路径（例如，/output/）。
6. 选择“选择当前文件夹”，指定上传目标。

![上传文件或文件夹的步骤和结果](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

将文件上传到存储的另一种方法是通过脚本编辑器中文件完整路径或文件相对路径上的快捷菜单。

可以[监视上传状态](#check-storage-tasks-status)。

### <a name="download-a-file"></a>下载文件

可以通过 ADL: Download File 或 ADL: Download File (Advanced) 命令来下载文件。

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>通过 ADL: Download File (Advanced) 命令下载文件

1. 右键单击脚本编辑器，然后选择“下载文件(高级)”。
2. VS Code 显示 JSON 文件。 可以输入文件路径，以便同时下载多个文件。 说明将显示在 " **输出** " 窗口中。 若要继续下载文件，请保存（按 Ctrl+S）JSON 文件。

    ![附带文件下载路径的 JSON 文件](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

“输出”窗口显示文件下载状态。

![有下载状态的输出窗口](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

可以[监视下载状态](#check-storage-tasks-status)。

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>通过 ADL: Download File 命令下载文件

1. 右键单击脚本编辑器，选择“下载文件”，然后从“选择文件夹”对话框选择目标文件夹。

1. 在列表中选择文件夹，或者选择“输入路径”或“从根路径浏览”。 （我们将使用“输入路径”作为示例。）

1. 选择 Data Lake Analytics 帐户。

1. 浏览到或输入存储文件夹路径（例如，/output/），然后选择要下载的文件。

![下载文件的步骤和结果](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

下载存储文件的另一种方法是通过脚本编辑器中文件完整路径或文件相对路径上的快捷菜单。

可以[监视下载状态](#check-storage-tasks-status)。

### <a name="check-storage-tasks-status"></a>检查存储任务的状态

上传和下载状态显示在状态栏上。 选择状态栏，然后状态将出现在“输出”选项卡上。

![状态栏和输出详细信息](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>通过资源管理器与 Azure Data Lake Analytics 集成

登录后，在“AZURE DATALAKE”下，左侧窗格会列出 Azure 帐户的所有订阅。

![Data Lake 资源管理器](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics 元数据导航

扩展 Azure 订阅。 在“U-SQL 数据库”节点下，可以浏览 U-SQL 数据库并查看“架构”、“凭据”、“程序集”、“表”和“索引”等文件夹。

### <a name="data-lake-analytics-metadata-entity-management"></a>Data Lake Analytics 元数据实体管理

展开“U-SQL 数据库”。 可通过右键单击相应节点，然后选择快捷菜单上的“用于创建的脚本”，创建数据库、架构、表、表类型、索引或统计。 在打开的脚本页上，按需编辑脚本。 然后，右键单击并选择“ADL: 提交作业”来提交作业。

完成创建项后，右键单击节点，然后选择“刷新”以显示该项。 还可以通过右键单击并选择“删除”来删除项。

![Data Lake 资源管理器快捷菜单上的“用于创建的脚本”命令](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![新项的脚本页](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics 程序集注册

可以通过右键单击“程序集”节点并选择“注册程序集”，在相应数据库中注册程序集。

![程序集节点快捷菜单上的“注册程序集”命令](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>通过资源管理器与 Azure Data Lake Store 集成

浏览到“Data Lake Store”：

- 可以右键单击文件夹节点，然后使用快捷菜单上的“刷新”、“删除”、“上传”、“上传文件夹”、“复制相对路径”和“复制完整路径”命令。

   ![Data Lake 资源管理器中文件夹节点的快捷菜单命令](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- 可以右键单击文件节点，然后使用快捷菜单上的“预览”、“下载”、“删除”、“创建 EXTRACT 脚本”（仅适用于 CSV、TSV 和 TXT 文件）、“复制相对路径”和“复制完整路径”命令。

   ![Data Lake 资源管理器中文件节点的快捷菜单命令](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>通过资源管理器与 Azure Blob 存储集成

浏览到 Blob 存储：

- 可以右键单击该 blob 容器节点，然后使用快捷菜单上的“刷新”、“删除 Blob 容器”和“上传 Blob”命令。

   ![Blob 存储下 blob 容器节点的快捷菜单命令](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- 可以右键单击文件夹节点，然后使用快捷菜单上的“刷新”和“上传 Blob”命令。

   ![Blob 存储下文件夹节点的快捷菜单命令](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- 可以右键单击文件节点，然后使用快捷菜单上的“预览/编辑”、“下载”、“删除”、“创建 EXTRACT 脚本”（仅适用于 CSV、TSV 和 TXT 文件）、“复制相对路径”和“复制完整路径”命令。

    ![Blob 存储下文件节点的快捷菜单命令](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>在门户中打开 Data Lake 资源管理器

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入 Open Web Azure Storage Explorer，或者右键单击脚本编辑器中的相对路径或完整路径，并选择“打开 Web Azure 存储资源管理器”。
3. 选择 Data Lake Analytics 帐户。

Data Lake 工具会在 Azure 门户中打开 Azure 存储路径。 可以从 Web 查找路径和预览文件。

## <a name="additional-features"></a>其他功能

针对 VS Code 的 Data Lake 工具支持以下功能：

- **IntelliSense 自动完成**：在弹出窗口中围绕关键字、方法和变量等项显示建议。 不同图标表示不同类型的对象：

  - Scala 数据类型
  - 复杂数据类型
  - 内置 UDT
  - .NET 集合与类
  - C# 表达式
  - 内置 C# UDF、UDO 和 UDAAG
  - U-SQL 函数
  - U-SQL 开窗函数

    ![IntelliSense 对象类型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Data Lake Analytics 元数据上的 IntelliSense 自动完成**： Data Lake 工具在本地下载 Data Lake Analytics 元数据信息。 IntelliSense 功能自动填充 Data Lake Analytics 元数据的对象。 这些对象包括数据库、架构、表、视图、表值函数、过程和 C# 程序集。

  ![IntelliSense 元数据](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense 错误标记**：Data Lake 工具使用下划线标出 U-SQL 和 C# 编辑错误。
- **语法突出显示**：Data Lake 工具使用不同颜色来区分变量、关键字、数据类型和函数等项。

    ![不同颜色的语法](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> 我们建议升级到针对 Visual Studio 的 Azure Data Lake 工具 2.3.3000.4 或更高版本。 以前的版本不再可以下载，现已弃用。  

## <a name="next-steps"></a>后续步骤

- [在 VS Code 中使用 Python、R 和 C Sharp 开发用于 Azure Data Lake Analytics 的 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试](data-lake-tools-for-vscode-local-run-and-debug.md)
- [教程：Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
- [教程：使用针对 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)