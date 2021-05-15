---
title: 使用针对 Visual Studio Code 的 Azure Data Lake 工具
description: 了解如何使用针对 Visual Studio Code 的 Azure Data Lake 工具创建、测试和运行 U-SQL 脚本。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: 40e3ce17e036312e7c3fdee95fcb42d06f5845e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751353"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>使用针对 Visual Studio Code 的 Azure Data Lake 工具

在本文中，了解可以如何使用针对 Visual Studio Code (VS Code) 的 Azure Data Lake 工具创建、测试和运行 U-SQL 脚本。 这些信息也包含在以下视频中：

[![视频播放器：针对 Visual Studio 的 Azure Data Lake 工具](media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png)](https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode")

## <a name="prerequisites"></a>必备条件

适用于 VS Code 的 Azure Data Lake 工具支持 Windows、Linux 和 macOS。 U-SQL 本地运行和本地调试仅适用于 Windows。

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)

对于 MacOS 和 Linux：

- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)
- [Mono 5.2.x](https://www.mono-project.com/download/)

## <a name="install-azure-data-lake-tools"></a>安装 Azure Data Lake 工具

安装必备组件后，即可安装用于 VS Code 的 Azure Data Lake 工具。

### <a name="to-install-azure-data-lake-tools"></a>安装 Azure Data Lake 工具的步骤

1. 打开 Visual Studio Code。
2. 在左窗格中选择“扩展”。 在搜索框中输入“Azure Data Lake 工具”。
3. 选择“Azure Data Lake 工具”旁的“安装”。

   ![选择安装 Data Lake 工具](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

   数秒后，“安装”按钮会变为“重载”。
4. 选择“重载”，激活“Azure Data Lake 工具”扩展。
5. 选择“重载窗口”以进行确认。 在“扩展”窗格中，可以看到“Azure Data Lake 工具” 。

## <a name="activate-azure-data-lake-tools"></a>激活 Azure Data Lake 工具

创建 .usql 文件，或打开现有的 .usql 文件以激活扩展。

## <a name="work-with-u-sql"></a>处理 U-SQL

若要处理 U-SQL，需打开 U-SQL 文件或文件夹。

### <a name="to-open-the-sample-script"></a>打开示例脚本的步骤

打开命令面板 (Ctrl+Shift+P)，并输入 **ADL: Open Sample Script**。 这会打开此示例的另一个实例。 还可以对此实例编辑、配置和提交脚本。

### <a name="to-open-a-folder-for-your-u-sql-project"></a>打开 U-SQL 项目文件夹的方法

1. 在 Visual Studio Code 中，选择“文件”菜单，并选择“打开文件夹”。
2. 指定文件夹，并选择“选择文件夹”。
3. 选择“文件”菜单，并选择“新建”。 一个 Untilted-1 文件会添加到项目。
4. 在 Untitled-1 文件中输入以下代码：

   ```usql
   @departments  =
       SELECT * FROM
           (VALUES
               (31,    "Sales"),
               (33,    "Engineering"),
               (34,    "Clerical"),
               (35,    "Marketing")
           ) AS
                 D( DepID, DepName );
   ```

   OUTPUT @departments     TO "/Output/departments.csv" USING Outputters.Csv();

    脚本创建具有 /output 文件夹中某些数据的 departments.csv 文件。

5. 在打开的文件夹中，将文件命名为 **myUSQL.usql**，并保存。

### <a name="to-compile-a-u-sql-script"></a>编译 U-SQL 脚本的方法

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入“ADL: Compile Script”。 编译结果显示在“输出”窗口中。 也可以右键单击脚本文件，并选择“ADL: Compile Script”编译 U-SQL 作业。 编译结果显示在“输出”窗格中。

### <a name="to-submit-a-u-sql-script"></a>提交 U-SQL 脚本的方法

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入“ADL: Submit Job”。 也可以右键单击脚本文件，并选择“ADL: Submit Job”。

提交 U-SQL 作业后，提交日志会显示在 VS Code 的“输出”窗口中。 作业视图显示在右侧窗格中。 如果提交成功，还会显示作业 URL。 可在 Web 浏览器中打开该作业 URL，跟踪作业实时状态。

在作业视图的“摘要”选项卡上，可以看到作业详细信息。 主要功能包括重新提交脚本、复制脚本和在门户中打开脚本。 在作业视图的“数据”选项卡上，可以参考输入文件、输出文件和资源文件。 可将文件下载到本地计算机。

![作业视图中的“摘要”选项卡](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

![作业视图中的“数据”选项卡](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

### <a name="to-set-the-default-context"></a>设置默认上下文

如果没有为文件分别设置参数，可将要应用此设置的默认上下文设置为所有脚本文件。

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入“ADL: Set Default Context”。 或者，右键单击脚本编辑器，然后选择“ADL: 设置默认上下文”。
3. 选择帐户、数据库和所需架构。 设置保存到 xxx_settings.json 配置文件。

   ![帐户、数据库和架构设置为默认上下文](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

### <a name="to-set-script-parameters"></a>设置脚本参数

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入“ADL: Set Script Parameters”。
3. 将打开 xxx_settings.json，其中显示了以下属性：

   - **account**：Azure 订阅下需要编译和运行 U-SQL 作业的 Azure Data Lake Analytics 帐户。 需要先配置此计算机帐户，然后才能编译和运行 U-SQL 作业。
   - **database**：帐户下的数据库。 默认为 **master**。
   - **schema**：数据库下的架构。 默认为 **dbo**。
   - **optionalSettings**：
        - **priority**：优先级范围是 1 到 1000，1 是最高优先级。 默认值为“1000”。
        - **degreeOfParallelism**：并行度范围是 1 到 150。 默认值是 Azure Data Lake Analytics 帐户中允许的最大并行度。

   ![JSON 文件的内容](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)

> [!NOTE]
> 如果未设置默认上下文，则保存配置后，帐户、数据库和架构信息会显示在对应 .usql 文件左下角的状态栏上。

### <a name="to-set-git-ignore"></a>将 Git 设为忽略

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入 ADL: Set Git Ignore。

   - 如果 VS Code 工作文件夹中没有 .gitIgnore 文件，则会在该文件夹中创建一个名为 .gitIgnore 的文件。 默认情况下会将四个项（usqlCodeBehindReference、usqlCodeBehindGenerated、.cache 和 obj）添加到该文件。 可以按需进行更多更新。
   - 如果 VS Code 工作文件夹中已有一个 .gitIgnore 文件，则该工具会向 .gitIgnore 文件中添加四个项（usqlCodeBehindReference、usqlCodeBehindGenerated、.cache、obj）（如果该文件中尚未包括这四个项）。

   ![.gitIgnore 文件中的项](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="work-with-code-behind-files-c-sharp-python-and-r"></a>处理代码隐藏文件：C Sharp、Python 和 R

Azure Data Lake 工具支持多种自定义代码。 有关说明，请参阅[在 Visual Studio Code 中使用 Python、R 和 C# 开发用于 Azure Data Lake Analytics 的 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)。

## <a name="work-with-assemblies"></a>处理程序集

有关开发程序集的信息，请参阅[为 Azure Data Lake Analytics 作业开发 U-SQL 程序集]()。

可以使用 Data Lake 工具在 Data Lake Analytics 目录中注册自定义代码程序集。

### <a name="to-register-an-assembly"></a>注册程序集的方法

可以通过 ADL: Register Assembly 或 ADL: Register Assembly (Advanced) 命令注册程序集。

### <a name="to-register-through-the-adl-register-assembly-command"></a>通过 ADL: Register Assembly 命令注册

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入 **ADL: Register Assembly**。
3. 指定本地程序集路径。
4. 选择 Data Lake Analytics 帐户。
5. 选择数据库。

门户在浏览器中打开并显示程序集注册过程。  

触发 ADL: Register Assembly 命令的另一种便利方法是在文件资源管理器中右键单击 .dll 文件。

### <a name="to-register-through-the-adl-register-assembly-advanced-command"></a>通过 ADL: Register Assembly (Advanced) 命令注册

1. 按 Ctrl+Shift+P 打开命令面板。
2. 输入“ADL: Register Assembly (Advanced)”。
3. 指定本地程序集路径。
4. 会显示 JSON 文件。 检查并根据需要编辑程序集依赖项和资源参数。 指令会显示在“输出”窗口中。 若要继续进行程序集注册，请保存 (Ctrl+S) JSON 文件。

   ![使用程序集依赖项和资源参数的 JSON 文件](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)

>[!NOTE]
>
>- Azure Data Lake 工具会自动检测 DLL 是否具有任何程序集依赖项。 依赖项在检测到之后会显示在 JSON 文件中。
>- 可以在程序集注册过程中上传 DLL 资源（例如，.txt、.png 和 .csv）。

触发 ADL: Register Assembly (Advanced) 命令的另一种方法是在文件资源管理器中右键单击 .dll 文件。

以下 U-SQL 代码演示如何调用程序集。 在示例中，程序集名称是 test。

```usql
REFERENCE ASSEMBLY [test];
@a =
    EXTRACT
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string
    FROM @"Sample/SearchLog.txt"
    USING Extractors.Tsv();
@d =
    SELECT DISTINCT Region
    FROM @a;
@d1 =
    PROCESS @d
    PRODUCE
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();
OUTPUT @d1
    TO @"Sample/SearchLogtest.txt"
    USING Outputters.Tsv();
```

## <a name="use-u-sql-local-run-and-local-debug-for-windows-users"></a>使用适用于 Windows 用户的 U-SQL 本地运行和本地调试

在将代码发布到 Data Lake Analytics 之前，U-SQL 本地运行可测试本地数据并在本地验证脚本。 可以使用本地调试功能在将代码提交到 Data Lake Analytics 之前完成以下任务：

- 调试 C# 代码隐藏。
- 逐步执行代码。
- 在本地验证脚本。

本地运行和本地调试功能仅适用于 Windows 环境，在 macOS 和基于 Linux 的操作系统上不受支持。

有关本地运行和本地调试的说明，请参阅[使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试](data-lake-tools-for-vscode-local-run-and-debug.md)。

## <a name="connect-to-azure"></a>连接到 Azure

必须首先接到 Azure 帐户，才能在 Data Lake Analytics 中编译和运行 U-SQL 脚本。

<a id="sign-in-by-command"></a>

### <a name="to-connect-to-azure-by-using-a-command"></a>使用命令连接到 Azure

1. 按 Ctrl+Shift+P 打开命令面板。

2. 输入 **ADL: Login**。 登录信息将显示在右下角。

   ![输入登录命令](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

   ![有关登录和身份验证的通知](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3. 选择“复制并打开”打开[登录网页](https://aka.ms/devicelogin)。 将代码粘贴到框中，然后选择“继续”。

    ![登录网页](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png)  

4. 按照说明从网页登录。 连接后，“VS Code”窗口左下角的状态栏上会显示 Azure 帐户名称。

> [!NOTE]
>
> - 如果没有注销，则 Data Lake 工具下次自动登录。
> - 如果帐户已启用双重身份验证，我们建议使用手机身份验证，而不要使用 PIN 码。

若要注销，请输入命令 **ADL: Logout**

### <a name="to-connect-to-azure-from-the-explorer"></a>从浏览器连接到 Azure

展开“AZURE DATALAKE”，选择“登录到 Azure”，然后按照[使用命令连接到 Azure](#sign-in-by-command)中的步骤 3 和步骤 4 进行操作。

![资源管理器中的“登录到 Azure”选项](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

无法从资源管理器注销。 若要注销，请参阅[使用命令连接到 Azure](#sign-in-by-command)。

## <a name="create-an-extraction-script"></a>创建提取脚本

可以使用 ADL: Create EXTRACT Script 命令或从 Azure Data Lake 资源管理器创建 .csv、.tsv 和 .txt 文件提取脚本。

### <a name="to-create-an-extraction-script-by-using-a-command"></a>使用命令创建提取脚本

1. 按 Ctrl+Shift+P 打开命令面板，然后输入 ADL: Create EXTRACT Script。
2. 指定 Azure 存储文件完整路径并按 Enter 键。
3. 选择一个帐户。
4. 对于 .txt 文件，请选择一个用于提取文件的分隔符。

![创建提取脚本的过程](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

提取脚本是根据条目生成的。 对于不能检测列的脚本，请从两个选项中选择一个。 否则，只会生成一个脚本。

![创建提取脚本的结果](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

### <a name="to-create-an-extraction-script-from-the-explorer"></a>从资源管理器创建提取脚本

创建提取脚本的另一个方法是通过 Azure Data Lake Store 或 Azure Blob 存储的 .csv、.tsv 或 .txt 文件上的右键（快捷）菜单。

![快捷菜单的“Create EXTRACT Script”命令](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="next-steps"></a>后续步骤

- [在 VS Code 中使用 Python、R 和 C Sharp 开发用于 Azure Data Lake Analytics 的 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [使用 Visual Studio Code 进行 U-SQL 本地运行和本地调试](data-lake-tools-for-vscode-local-run-and-debug.md)
- [教程：Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
- [教程：使用针对 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)